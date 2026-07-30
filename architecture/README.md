<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>

# GENTE — System Architecture

> *This document describes the architectural decisions behind GENTE, the rationale for each component choice, and how the system behaves under the specific constraints of a disaster-affected field environment in Venezuela.*

---

## Design philosophy

GENTE is built around three non-negotiable constraints derived directly from the Venezuela earthquake operational environment:

1. **No internet dependency at runtime.** Every component must function with zero outbound connectivity after initial setup. Cloud-dependent tools fail precisely when disasters occur.

2. **Minimal hardware footprint.** The entire stack must run on a single laptop or small ruggedized server that can be transported in a backpack, powered by a portable battery station during blackouts.

3. **Zero data egress.** Population displacement and vulnerability data collected from affected communities must never leave the physical device. Humanitarian Do No Harm principles and Venezuelan data governance requirements demand this absolutely.

Every technology choice in GENTE flows from these three constraints, not from preference or convenience.

---

## System layers

```
┌──────────────────────────────────────────────────────────────────────┐
│  LAYER 4 — PRESENTATION                                              │
│  QGIS operational map | PDF situation reports | CSV exports          │
└──────────────────────────────────┬───────────────────────────────────┘
                                   │
┌──────────────────────────────────▼───────────────────────────────────┐
│  LAYER 3 — SPATIAL DATABASE                                          │
│  SpatiaLite (disaster_triage.sqlite)                                 │
│  Tables: triage_points | sites | households | infrastructure         │
└──────────────────────────────────┬───────────────────────────────────┘
                                   │
┌──────────────────────────────────▼───────────────────────────────────┐
│  LAYER 2 — AI TRIAGE ENGINE                                          │
│  Ollama (local) + Llama 3.1 8B                                       │
│  Input: Spanish field notes | Output: structured JSON                │
└──────────────────────────────────┬───────────────────────────────────┘
                                   │
┌──────────────────────────────────▼───────────────────────────────────┐
│  LAYER 1 — DATA COLLECTION                                           │
│  KoboToolbox (Docker) + KoboCollect (Android/iOS)                    │
│  Form: damage_survey_es.xlsx (earthquake, Spanish)                   │
└──────────────────────────────────┬───────────────────────────────────┘
                                   │
┌──────────────────────────────────▼───────────────────────────────────┐
│  LAYER 0 — FIELD NETWORK                                             │
│  Local Wi-Fi hotspot (no internet) | USB sync (fallback)             │
│  Field devices: Android phones with KoboCollect installed            │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Component deep-dive

### Layer 0 — Field network

**Local Wi-Fi hotspot**

The command hub (laptop or server) broadcasts a password-protected Wi-Fi access point. Field workers within range connect their phones and KoboCollect syncs form submissions directly to the local KoboToolbox instance. No router, no internet gateway, no ISP dependency.

Range: standard Wi-Fi covers approximately 30–50 metres indoors, 100+ metres in open outdoor conditions — sufficient for a field base camp, displacement site, or coordination hub.

**USB sync fallback**

When Wi-Fi is unavailable (field workers in remote areas, phone battery constraints), KoboCollect supports exporting submissions as files that can be transferred via USB cable or USB drive to the command hub and imported into the local KoboToolbox instance. This is the "sneakernet" fallback and is critical for Venezuela's terrain-fragmented operational zones.

---

### Layer 1 — Data collection: KoboToolbox + KoboCollect

**Why KoboToolbox**

KoboToolbox is the global standard for humanitarian data collection. It is used by IOM, UNHCR, OCHA, WFP, and hundreds of NGOs. Field workers trained by any of these organizations already know KoboCollect. GENTE deliberately uses it rather than building a custom form application, because:

- Zero retraining cost for field enumerators
- XLSForm standard (`.xlsx`) means forms are auditable, translatable, and shareable
- Offline collection is a core KoboCollect feature — forms work without connectivity
- IOM's own DTM operations use KoboToolbox

**Docker deployment**

KoboToolbox runs in Docker Compose on the command hub. The `kobocat`, `kpi`, `postgresql`, and `mongo` containers form the complete KoboToolbox stack. Initial container images are pulled once before field deployment (internet required at setup time only). After that, the stack starts and runs indefinitely offline.

The form (`forms/damage_survey_es.xlsx`) covers:
- GPS coordinates (auto-captured by phone)
- Administrative location (estado → municipio → parroquia)
- Structure type and damage level (1–5 scale)
- Number of persons affected / displaced
- Open text field: *"Describa la situación con sus propias palabras"* (Describe the situation in your own words)
- Infrastructure status: water, electricity, road access, communications
- Immediate needs: medical, food, shelter, WASH

The open text field is the primary input to the AI triage engine.

---

### Layer 2 — AI triage engine: Ollama + Llama 3.1 8B

**Why a local LLM instead of a rules engine**

Structured dropdown fields in a KoboToolbox form capture factual data reliably. But the operational reality of disaster response is that the most important information arrives as unstructured narrative:

> *"El puente cedió y hay gente atrapada arriba, los carros de bomberos no pueden pasar, hay una señora embarazada entre los heridos"*
> *(The bridge gave way and people are trapped above, fire trucks cannot pass, there is a pregnant woman among the injured)*

A rules engine cannot extract the clinical priority (obstetric emergency), the infrastructure blockage (bridge collapse), and the access constraint (emergency vehicles blocked) from that sentence. A local LLM can, reliably, in under 3 seconds.

**Why Llama 3.1 8B specifically**

| Model | VRAM required | Spanish quality | Latency (RTX 4060) | Chosen |
|---|---|---|---|---|
| Llama 3.1 8B (Q4_K_M) | 6 GB | Good | ~3 seconds | ✅ |
| Llama 3.1 70B (Q4_K_M) | 40 GB | Excellent | ~25 seconds | Roadmap |
| Mistral 7B | 5 GB | Moderate | ~2 seconds | Fallback |
| Gemma 2 9B | 7 GB | Good | ~4 seconds | Alternative |

Llama 3.1 8B at Q4_K_M quantization fits in 6 GB of GPU VRAM, leaving headroom on an RTX 4060 (8 GB). Its Spanish-language capability is sufficient for standard Venezuelan field report vocabulary. The model is open-weight and can be used without internet after the initial `ollama pull`.

**The triage prompt**

The AI prompt in `pipeline/ai_triage.py` is written in Spanish and instructs the model to extract exactly the fields needed for the SpatiaLite schema:

```
Eres un asistente de gestión de información humanitaria.
Analiza el siguiente reporte de campo en español venezolano
y extrae SOLAMENTE un objeto JSON con estos campos:

{
  "severity_level": "Critical | High | Medium | Low",
  "infrastructure_status": "Collapsed | Damaged | Accessible | Unknown",
  "medical_need": true | false,
  "obstetric_emergency": true | false,
  "blocked_access": true | false,
  "estimated_affected": <integer or null>,
  "location_reference": "<text or null>",
  "primary_need": "Medical | Shelter | Food | WASH | Search_Rescue | Unknown",
  "confidence": <float 0.0-1.0>
}

No incluyas texto adicional. Solo el JSON.
Reporte: {field_note}
```

The prompt enforces JSON-only output, which allows reliable parsing without post-processing heuristics.

**Human QA requirement**

All AI triage output is flagged with a `needs_review` boolean set to `true` by default. A human IM coordinator reviews and confirms (or overrides) each AI classification before it enters the operational map layer. This is a hard architectural constraint — GENTE never places an unreviewed point on an operational map used for resource allocation decisions.

---

### Layer 3 — Spatial database: SpatiaLite

**Why SpatiaLite instead of PostgreSQL/PostGIS**

| | SpatiaLite | PostGIS |
|---|---|---|
| Installation | Zero (SQLite extension) | PostgreSQL server required |
| File format | Single `.sqlite` file | Database server + files |
| QGIS compatibility | Native, no configuration | Requires connection setup |
| Backup / transfer | `cp file.sqlite /usb/` | `pg_dump` + restore |
| Concurrent writes | Single process | Full ACID, multi-process |

For a single-operator field deployment, SpatiaLite's simplicity is a decisive advantage. Backing up the entire operational database is a single file copy to a USB drive. QGIS connects to it natively without a connection string.

**Schema**

```sql
-- Primary triage events table
CREATE TABLE triage_points (
    id              INTEGER PRIMARY KEY AUTOINCREMENT,
    submission_id   TEXT UNIQUE NOT NULL,
    submitted_at    DATETIME NOT NULL,
    latitude        REAL NOT NULL,
    longitude       REAL NOT NULL,
    estado          TEXT,
    municipio       TEXT,
    parroquia       TEXT,
    severity_level  TEXT CHECK(severity_level IN ('Critical','High','Medium','Low')),
    infrastructure  TEXT,
    medical_need    INTEGER DEFAULT 0,    -- boolean
    blocked_access  INTEGER DEFAULT 0,    -- boolean
    primary_need    TEXT,
    affected_count  INTEGER,
    field_note      TEXT,                 -- original Spanish text
    ai_confidence   REAL,
    needs_review    INTEGER DEFAULT 1,    -- boolean: human QA required
    reviewed_by     TEXT,
    reviewed_at     DATETIME,
    geometry        GEOMETRY             -- SpatiaLite spatial column
);

-- Displacement sites (DTM / CCCM)
CREATE TABLE sites (
    site_id         TEXT PRIMARY KEY,
    site_name       TEXT,
    site_type       TEXT,               -- collective_centre | spontaneous | host_family
    estado          TEXT,
    municipio       TEXT,
    population      INTEGER,
    capacity        INTEGER,
    water_access    INTEGER DEFAULT 0,
    sanitation      INTEGER DEFAULT 0,
    electricity     INTEGER DEFAULT 0,
    last_updated    DATETIME,
    geometry        GEOMETRY
);
```

---

### Layer 4 — Presentation: QGIS

**Operational map**

The QGIS project (`qgis/gente_project.qgz`) pre-loads:

- Venezuela state boundaries (GeoJSON, public domain)
- Municipio boundaries for affected states
- Road network (OpenStreetMap extract, offline)
- `triage_points` layer from SpatiaLite (auto-refreshes)
- `sites` layer from SpatiaLite

Points are symbolized by `severity_level`:
- 🔴 **Critical** — red, large circle
- 🟠 **High** — orange, medium circle
- 🟡 **Medium** — yellow, small circle
- 🟢 **Low** — green, small circle
- ⚪ **Needs review** — grey, pulsing (unconfirmed AI output)

**Print layouts**

Two pre-configured QGIS print layouts:
1. **Situation Report Map** — A4 landscape, legend, north arrow, scale bar, IOM logo placeholder, date/time stamp
2. **Site Factsheet** — A4 portrait, single-site detail, population indicators, needs matrix

Both export to PDF and PNG for WhatsApp distribution, radio operators, and printed briefings.

---

## Data flow: end to end

```
1. Field enumerator opens KoboCollect on Android phone (offline)
2. Completes damage_survey_es.xlsx form, including GPS capture and free-text note
3. Moves within Wi-Fi range of command hub
4. KoboCollect syncs submission to local KoboToolbox (Docker) — ~2 seconds
5. kobo_to_qgis.py polls KoboToolbox API every 60 seconds
6. New submission detected → field note extracted
7. ai_triage.py sends field note to Ollama (local) → structured JSON returned
8. db_writer.py inserts point into SpatiaLite with needs_review=1
9. QGIS auto-refreshes triage_points layer → grey point appears on map
10. IM coordinator reviews AI classification, confirms or overrides → needs_review=0
11. Point changes color on map according to confirmed severity_level
12. Coordinator exports situation report PDF for coordination meeting
```

Total time from form submission to map point: **under 10 seconds** (Wi-Fi sync + AI triage + database write + QGIS refresh).

---

## Security considerations

- KoboToolbox local instance is not exposed to any network outside the local Wi-Fi hotspot
- Wi-Fi hotspot uses WPA2 with a strong passphrase rotated daily
- SpatiaLite database file is encrypted at rest using SQLCipher (planned v0.2)
- No submission data is logged, cached, or transmitted outside the local machine
- QGIS project does not include any cloud tile layer connections
- Physical device security (full-disk encryption, power-off when unattended) is mandatory

---

## Failure modes and mitigations

| Failure | Impact | Mitigation |
|---|---|---|
| Laptop battery dies | All operations halt | Portable power station (required hardware) |
| GPU unavailable | AI triage fails | Fallback to CPU inference (slower, ~45s/report) |
| KoboToolbox Docker crash | No new submissions accepted | `docker-compose restart` restores in ~30s |
| SpatiaLite corruption | Data loss | Daily backup to encrypted USB drive |
| Field phone battery dies | No new form submissions | Spare power banks; paper form fallback |
| QGIS crash | Map unavailable | Reopen project; SpatiaLite data is persistent |

---
<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>


*See [`docs/deployment.md`](deployment.md) for step-by-step installation and field setup instructions.*  
*See [`docs/venezuela-context.md`](venezuela-context.md) for the operational context that shaped these design decisions.*
