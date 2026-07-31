<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>

# GENTE — DTM
### Geospatial Emergency Network Training Engine
#### *Displacement Tracking Matrix variant · IOM Venezuela earthquake response*
#### *Inteligencia al servicio de nuestra gente*

> **Proof-of-concept offline humanitarian IM platform built specifically to support IOM Displacement Tracking Matrix (DTM) operations in connectivity-degraded disaster environments.**  
> This variant is tailored to the responsibilities of the **Senior Information Management Associate (DTM)** position — Job ID 22086, IOM Venezuela Country Office.

→ *Core repository with full documentation and long-term vision:* [andreshermoso/gente](https://github.com/andreshermoso/gente)

---

## Why this exists

IOM's DTM mandate in the June 2026 Venezuela earthquake response requires systematic collection, processing, and dissemination of information on the location, number, and needs of displaced populations. That mandate assumes IM tools are reachable. In Venezuela — where rolling blackouts (*administración de carga*) can last 12–20 hours a day and internet bandwidth collapses precisely when emergency traffic peaks — that assumption fails.

GENTE DTM is the answer to a specific operational question: **how does a Senior IM Associate lead DTM data collection, quality assurance, and reporting when the tools that workflow normally depends on are unavailable?**

The answer is an offline-first pipeline: KoboCollect on field phones, a locally hosted KoboToolbox server over a Wi-Fi hotspot, a local AI model classifying reports in Venezuelan Spanish, and QGIS producing situation reports and factsheets — all running on a single laptop with no internet connection.

---

## DTM workflow — what this platform supports

The Senior IM Associate (DTM) role requires end-to-end responsibility for DTM data flows. GENTE DTM maps to each responsibility directly:

| DTM responsibility | GENTE DTM component |
|---|---|
| Design and manage data collection forms | `forms/dtm_site_assessment_es.xlsx` — XLSForm aligned with DTM Round methodology, in Venezuelan Spanish |
| Lead and supervise field enumerator teams | `docs/enumerator_guide_es.md` — Spanish-language field protocol and data quality standards |
| Ensure data quality and validate submissions | `pipeline/qa_validator.py` — automated field-level checks; `needs_review` gate for AI output |
| Process and analyze DTM datasets | `pipeline/kobo_to_qgis.py` — continuous ETL from KoboToolbox to SpatiaLite |
| Produce maps, dashboards, factsheets | `qgis/dtm_project.qgz` — pre-configured layouts for DTM Round factsheets and situation maps |
| Disseminate findings to coordination partners | PDF/PNG export from QGIS print layouts; no internet required for production |
| Manage IM flows between field, hub, and regional | SpatiaLite database as single source of truth; USB sneakernet sync between hubs |

---

## DTM data collection: what the form captures

The `forms/dtm_site_assessment_es.xlsx` XLSForm covers IOM's standard DTM site assessment indicators, adapted for rapid earthquake damage assessment in Venezuelan administrative geography:

**Location and identification**
- GPS coordinates (auto-captured by KoboCollect)
- Estado → Municipio → Parroquia (Venezuelan administrative hierarchy)
- Site name and type (collective centre / spontaneous site / host family / transit point)
- Site ID (compatible with IOM master site list format)

**Population data**
- Estimated total displaced population
- Breakdown by gender and age group (children / adults / elderly)
- Arrival date and displacement origin
- Intended duration of stay

**Shelter and structural conditions**
- Structure type and ownership
- Damage level (1–5 scale: none / minor / moderate / severe / collapsed)
- Overcrowding assessment

**Priority needs assessment**
- Water, sanitation, and hygiene (WASH) status
- Food security indicators
- Medical and health service access
- Protection concerns
- Free-text field: *"Describa la situación con sus propias palabras"* → AI classification input

**Access and coordination**
- Road access status (open / restricted / blocked)
- Active organizations present at site
- Last verified date

---

## Architecture overview

```
┌──────────────────────────────────────────────────────────────────┐
│  FIELD TEAM (no connectivity required)                           │
│                                                                  │
│  📱 KoboCollect → local Wi-Fi → KoboToolbox (Docker, offline)    │
│                                                                  │
│  Enumerators collect DTM site assessments in Spanish             │
│  GPS captured automatically on each submission                   │
└──────────────────────────────┬───────────────────────────────────┘
                               │ JSON via local API (poll every 60s)
                               ▼
┌──────────────────────────────────────────────────────────────────┐
│  DTM COMMAND HUB (Senior IM Associate workstation)               │
│                                                                  │
│  kobo_to_qgis.py                                                 │
│  │                                                               │
│  ├── qa_validator.py   → field-level data quality checks         │
│  │                                                               │
│  ├── ai_classifier.py  → Ollama (llama3.1:8b, local)             │
│  │   Extracts from free-text: priority need / protection flag    │
│  │   / site condition / access status / population estimate      │
│  │   Input: Venezuelan Spanish  |  Output: structured JSON       │
│  │                                                               │
│  └── db_writer.py      → SpatiaLite (dtm_venezuela.sqlite)       │
│                               │                                  │
│                     ┌─────────┴──────────┐                       │
│                     ▼                    ▼                       │
│              QGIS (offline)        dtm_reporter.py               │
│              Operational map       CSV / Excel export            │
│              DTM factsheets        for coordination partners     │
└──────────────────────────────────────────────────────────────────┘
```

---

## DTM-specific database schema

The SpatiaLite database (`dtm_venezuela.sqlite`) is organized around IOM DTM Round methodology:

```sql
-- DTM assessment rounds
CREATE TABLE dtm_rounds (
    round_id        TEXT PRIMARY KEY,         -- e.g. 'VEN-EQ-R001'
    round_name      TEXT,
    start_date      DATE,
    end_date        DATE,
    geographic_scope TEXT,
    lead_im         TEXT
);

-- Master site list (CCCM-compatible)
CREATE TABLE dtm_sites (
    site_id         TEXT PRIMARY KEY,         -- IOM standard site ID format
    site_name       TEXT NOT NULL,
    site_type       TEXT,                     -- collective_centre | spontaneous | host_family | transit
    estado          TEXT,
    municipio       TEXT,
    parroquia       TEXT,
    first_assessed  DATE,
    last_assessed   DATE,
    status          TEXT DEFAULT 'active',    -- active | closed | merged
    geometry        GEOMETRY
);

-- Site assessments per round
CREATE TABLE dtm_assessments (
    assessment_id   TEXT PRIMARY KEY,
    submission_id   TEXT UNIQUE,
    site_id         TEXT REFERENCES dtm_sites(site_id),
    round_id        TEXT REFERENCES dtm_rounds(round_id),
    assessed_at     DATETIME,
    enumerator_id   TEXT,
    population_total INTEGER,
    pop_children    INTEGER,
    pop_adults      INTEGER,
    pop_elderly     INTEGER,
    shelter_damage  INTEGER CHECK(shelter_damage BETWEEN 1 AND 5),
    wash_status     TEXT,
    food_status     TEXT,
    medical_access  TEXT,
    road_access     TEXT CHECK(road_access IN ('open','restricted','blocked')),
    protection_flag INTEGER DEFAULT 0,
    primary_need    TEXT,
    field_note      TEXT,                     -- original Spanish free text
    ai_need_class   TEXT,                     -- AI-extracted primary need
    ai_confidence   REAL,
    needs_review    INTEGER DEFAULT 1,        -- human QA gate
    reviewed_by     TEXT,
    reviewed_at     DATETIME,
    geometry        GEOMETRY                  -- point from GPS capture
);

-- Inter-round displacement flow tracking
CREATE TABLE dtm_flow_monitoring (
    flow_id         TEXT PRIMARY KEY,
    assessment_date DATE,
    origin_municipio TEXT,
    destination_municipio TEXT,
    estimated_flow  INTEGER,
    displacement_reason TEXT,
    data_source     TEXT,
    confidence      TEXT CHECK(confidence IN ('verified','estimated','reported'))
);
```

---

## QGIS layouts for DTM outputs

The `qgis/dtm_project.qgz` includes four pre-configured print layouts matching IOM DTM standard products:

**1. DTM Round Situation Map**
A4 landscape — all assessed sites symbolized by population size and priority need, state boundaries, road network, legend, IOM logo placeholder, round number, date stamp, data source note and limitation disclaimer.

**2. DTM Site Factsheet**
A4 portrait — single-site detail card: population breakdown chart, needs matrix, shelter damage indicator, access status, assessment history timeline, enumerator name and date.

**3. Population Flow Map**
A4 landscape — origin/destination arrows sized by estimated flow volume, administrative boundaries, displacement reason legend.

**4. Multi-Round Trend Dashboard**
A3 landscape — side-by-side comparison of two assessment rounds: population change, new sites, closed sites, priority need evolution.

All layouts export to PDF and PNG. No internet required for production.

---

## Sample DTM AI classification

**Field note input** (from enumerator at a spontaneous site in Petare, Caracas):

```
"Hay como 80 familias debajo del puente, muchos son de La Dolorita que 
quedó destruida. Los niños están sin comer desde ayer, hay una señora 
con bebé recién nacido sin atención médica. No ha llegado nadie oficial."
```

**AI classification output:**

```json
{
  "estimated_families": 80,
  "displacement_origin": "La Dolorita",
  "site_type": "spontaneous",
  "primary_need": "Food",
  "secondary_need": "Medical",
  "protection_flag": true,
  "protection_detail": "Newborn without medical attention",
  "organization_presence": false,
  "ai_confidence": 0.91
}
```

**What the Senior IM Associate sees:** A new orange point (food priority) on the operational map at the bridge coordinates, flagged for protection review, with estimated population 80 families, zero organization presence. This escalates automatically to the protection cluster column in the coordination dashboard.

---

## Enumerator supervision tools

A Senior IM Associate managing field teams needs more than data collection — they need visibility into team performance and data quality in near-real time. GENTE DTM includes:

**`pipeline/team_monitor.py`** — produces a per-enumerator quality dashboard:
- Submissions per hour (identifies teams falling behind schedule)
- GPS accuracy distribution (flags submissions with poor coordinate quality)
- Field note length distribution (very short notes correlate with low AI confidence)
- Missing field rate by question (identifies form design problems)
- Duplicate submission detection

**`pipeline/qa_validator.py`** — automated field-level checks run before AI classification:
- GPS coordinates within Venezuela bounding box
- Population figures within plausible range for site type
- Required fields complete
- Assessment date not in future
- Site ID format valid

Submissions failing validation are quarantined in a `qa_failed` table with a specific error code, rather than silently entering the operational dataset.

---

## Quickstart

**Prerequisites:** Docker, Docker Compose, Python 3.11+, QGIS 3.x, Ollama

```bash
git clone https://github.com/andreshermoso/gente_DTM.git
cd gente_DTM
docker-compose -f docker/docker-compose.yml up -d
pip install -r requirements.txt
ollama pull llama3.1:8b
python pipeline/kobo_to_qgis.py --sample --mode dtm
```

Open `qgis/dtm_project.qgz`. Ten synthetic DTM site assessments appear across the Venezuela map, color-coded by priority need.

---

## Long-term DTM training vision

The DTM variant of GENTE carries a training dimension that extends well beyond the immediate earthquake response. Every DTM Round conducted in Venezuela generates field knowledge — about site typologies, enumerator error patterns, AI classification edge cases, coordination gaps — that currently evaporates after the operational period ends.

The roadmap includes a DTM knowledge base module that converts after-action reviews into searchable lessons learned, generates scenario-based training exercises from real field incidents, and builds a library of Venezuela-specific DTM case studies for enumerator training and quality assurance calibration.

The long-term goal is that every future DTM deployment in Venezuela — earthquake, flood, economic displacement, or other trigger — starts with accumulated institutional knowledge rather than from zero.

---

## Related repositories

| Repository | Role variant |
|---|---|
| [gente](https://github.com/andreshermoso/gente) | Core platform — full documentation and vision |
| [gente_CCCM](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_CCCM) | Senior IM Associate — CCCM (22097) |
| [gente_IMS](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_IMS) | Senior IMS Registration Associate (22094) |
| [gente_AVRR](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_AVRR) | Assistant - AVRR (22252) |
| [gente_R](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_R) | Resilience — institutional memory |

---

## About the author

**Andres Gabriel Hermoso Castillo**  
Systems Analyst | DataOps Architect | IM Specialist  
Caracas, Bolivarian Republic of Venezuela  
andres.hermoso@gmail.com | +58 412 701 0980

Developed in July 2026 in response to the Venezuela earthquake and as part of an application for the Senior Information Management Associate (DTM) position with IOM's Venezuela Country Office (Job ID 22086).

---

## License

MIT License — free to use, adapt, and deploy in humanitarian contexts.

---
<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>


*"Cada punto en el mapa es una familia. Cada familia merece ser contada."*  
*(Every point on the map is a family. Every family deserves to be counted.)*
