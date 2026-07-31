<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>

# GENTE — CCCM
### Geospatial Emergency Network Training Engine
#### *Camp Coordination and Camp Management variant · IOM Venezuela earthquake response*
#### *Inteligencia al servicio de nuestra gente*

> **Proof-of-concept offline humanitarian IM platform built specifically to support IOM Camp Coordination and Camp Management (CCCM) operations in connectivity-degraded disaster environments.**  
> This variant is tailored to the responsibilities of the **Senior Information Management Associate (CCCM)** position — Job ID 22097, IOM Venezuela Country Office.

→ *Core repository with full documentation and long-term vision:* [andreshermoso/gente](https://github.com/andreshermoso/gente)

---

## Why this exists

CCCM information management in Venezuela's June 2026 earthquake response faces a structural contradiction: the work of coordinating collective centres, maintaining master site lists, and producing multi-cluster dashboards depends on continuous data flows — but the disaster environment that generates the need for CCCM also destroys the connectivity that standard IM tools require.

GENTE CCCM resolves that contradiction. It gives the Senior IM Associate (CCCM) a complete, offline-capable platform to maintain the master site list, collect and validate site monitoring data, coordinate information flows with cluster partners, and produce the dashboards, factsheets, and situation reports that drive CCCM operational decisions — all from a single laptop with no internet connection.

Venezuela's displacement pattern adds a specific complication. Unlike camp-based emergencies where CCCM tools are designed to operate, Venezuelan affected populations tend to disperse into collective centres, spontaneous sites, host family arrangements, and urban open spaces simultaneously. The master site list is not a stable registry — it is a continuously updated operational picture that requires flexible, community-level data collection and rapid quality assurance across heterogeneous site types.

**GENTE CCCM is designed for that operational reality.**

---

## CCCM workflow — what this platform supports

The Senior IM Associate (CCCM) role carries end-to-end responsibility for site-level IM and inter-cluster coordination. GENTE CCCM maps to each responsibility directly:

| CCCM IM responsibility | GENTE CCCM component |
|---|---|
| Create and maintain master site list | `db/cccm_sites` table — authoritative site registry with full attribute history |
| Design and manage site monitoring forms | `forms/cccm_site_monitoring_es.xlsx` — XLSForm aligned with CCCM monitoring indicators |
| Validate and quality-assure incoming data | `pipeline/qa_validator.py` — field-level checks + human review gate |
| Produce and update cluster dashboards | `qgis/cccm_project.qgz` — pre-configured CCCM dashboard layouts |
| Coordinate IM with other clusters | `pipeline/cluster_exporter.py` — standardized exports for Shelter, WASH, Health, Food clusters |
| Issue site-level alerts and escalations | `pipeline/alert_engine.py` — threshold-based alerts for overcrowding, service gaps, closures |
| Maintain inter-cluster service tracking | `db/cccm_services` table — organization presence and service delivery per site per date |
| Disseminate factsheets and sitreps | QGIS print layouts — PDF/PNG export, no internet required |

---

## The master site list: GENTE CCCM's central artifact

In CCCM operations, the master site list is the single most important information product. It is the shared reference that enables all clusters — Shelter, WASH, Health, Food Security, Protection — to coordinate service delivery without duplication or gap. Maintaining its accuracy, currency, and completeness under field conditions is the core IM challenge.

GENTE CCCM treats the master site list not as a spreadsheet but as a living spatial database with full attribute history, change tracking, and multi-cluster service visibility.

**Every site record contains:**

```
IDENTIFICATION
  site_id          TEXT    IOM standard format (VEN-EQ-CS-0001)
  site_name        TEXT    common name used by affected population
  site_type        TEXT    collective_centre | spontaneous | host_family | transit | open_space
  opening_date     DATE
  status           TEXT    active | at_capacity | closed | merged_into

LOCATION
  estado           TEXT    Venezuelan state
  municipio        TEXT
  parroquia        TEXT
  address_ref      TEXT    local landmark reference ("frente al liceo", "debajo del puente")
  latitude         REAL    GPS decimal degrees
  longitude        REAL    GPS decimal degrees

POPULATION (updated each monitoring visit)
  pop_total        INTEGER
  pop_children     INTEGER
  pop_women        INTEGER
  pop_men          INTEGER
  pop_elderly      INTEGER
  pop_with_disability INTEGER
  pop_pregnant     INTEGER

SITE CONDITIONS (updated each monitoring visit)
  site_manager     TEXT    organization managing the site
  overcrowding     TEXT    none | mild | moderate | severe
  shelter_quality  TEXT    adequate | inadequate | critical
  last_monitored   DATE

CLUSTER SERVICE TRACKING (updated per cluster visit)
  wash_present     BOOLEAN
  wash_org         TEXT
  health_present   BOOLEAN
  health_org       TEXT
  food_present     BOOLEAN
  food_org         TEXT
  protection_present BOOLEAN
  protection_org   TEXT
  shelter_present  BOOLEAN
  shelter_org      TEXT
```

The spatial column enables all site records to be displayed simultaneously on the QGIS operational map, filtered by any attribute, and exported as a georeferenced layer for cluster partners.

---

## Architecture overview

```
┌──────────────────────────────────────────────────────────────────┐
│  FIELD MONITORS (no connectivity required)                       │
│                                                                  │
│  📱 KoboCollect → local Wi-Fi → KoboToolbox (Docker, offline)    │
│                                                                  │
│  Site monitors complete CCCM monitoring form at each visit       │
│  GPS captured; service presence checkboxes; free-text notes      │
└──────────────────────────────┬───────────────────────────────────┘
                               │ JSON via local API (poll every 60s)
                               ▼
┌──────────────────────────────────────────────────────────────────┐
│  CCCM COMMAND HUB (Senior IM Associate workstation)              │
│                                                                  │
│  kobo_to_qgis.py                                                 │
│  │                                                               │
│  ├── qa_validator.py    → completeness + consistency checks      │
│  │                                                               │
│  ├── ai_classifier.py   → Ollama (llama3.1:8b, local)            │
│  │   Extracts from free text: protection concern / service gap   │
│  │   / overcrowding signal / site closure risk / population      │
│  │   estimate / emerging need                                    │
│  │   Input: Venezuelan Spanish  |  Output: structured JSON       │
│  │                                                               │
│  ├── db_writer.py       → SpatiaLite (cccm_venezuela.sqlite)     │
│  │                                                               │
│  ├── alert_engine.py    → threshold alerts (overcrowding,        │
│  │                         service gap >72h, new site, closure)  │
│  │                                                               │
│  └── cluster_exporter.py → standardized CSV/Excel per cluster.   │
│                               │                                  │
│                     ┌─────────┴──────────┐                       │
│                     ▼                    ▼                       │
│              QGIS (offline)        Cluster exports               │
│              CCCM dashboard        Shelter / WASH / Health /     │
│              Master site map       Food / Protection             │
└──────────────────────────────────────────────────────────────────┘
```

---

## CCCM-specific database schema

```sql
-- Master site list: authoritative site registry
CREATE TABLE cccm_sites (
    site_id             TEXT PRIMARY KEY,          -- VEN-EQ-CS-0001 format
    site_name           TEXT NOT NULL,
    site_type           TEXT NOT NULL,
    estado              TEXT,
    municipio           TEXT,
    parroquia           TEXT,
    address_ref         TEXT,                      -- local landmark reference
    opening_date        DATE,
    status              TEXT DEFAULT 'active',
    merged_into         TEXT REFERENCES cccm_sites(site_id),
    site_manager_org    TEXT,
    max_capacity        INTEGER,
    created_at          DATETIME DEFAULT CURRENT_TIMESTAMP,
    geometry            GEOMETRY
);

-- Site monitoring visits (one row per visit per site)
CREATE TABLE cccm_monitoring (
    monitoring_id       TEXT PRIMARY KEY,
    submission_id       TEXT UNIQUE,
    site_id             TEXT REFERENCES cccm_sites(site_id),
    monitored_at        DATETIME NOT NULL,
    monitor_name        TEXT,
    pop_total           INTEGER,
    pop_children        INTEGER,
    pop_women           INTEGER,
    pop_men             INTEGER,
    pop_elderly         INTEGER,
    pop_disability      INTEGER,
    pop_pregnant        INTEGER,
    overcrowding        TEXT CHECK(overcrowding IN ('none','mild','moderate','severe')),
    shelter_quality     TEXT CHECK(shelter_quality IN ('adequate','inadequate','critical')),
    field_note          TEXT,                      -- Spanish free text
    ai_protection_flag  INTEGER DEFAULT 0,
    ai_service_gap      TEXT,
    ai_closure_risk     INTEGER DEFAULT 0,
    ai_confidence       REAL,
    needs_review        INTEGER DEFAULT 1,
    reviewed_by         TEXT,
    reviewed_at         DATETIME
);

-- Cluster service presence tracking
CREATE TABLE cccm_services (
    service_id          TEXT PRIMARY KEY,
    site_id             TEXT REFERENCES cccm_sites(site_id),
    cluster             TEXT CHECK(cluster IN ('WASH','Health','Food','Protection','Shelter','Education','NFI')),
    organization        TEXT,
    service_description TEXT,
    start_date          DATE,
    end_date            DATE,                      -- NULL = currently active
    frequency           TEXT,                      -- daily | weekly | as_needed | one_off
    beneficiary_count   INTEGER,
    last_confirmed      DATE
);

-- Inter-cluster coordination log
CREATE TABLE cccm_coordination (
    coord_id            TEXT PRIMARY KEY,
    coord_date          DATE,
    meeting_type        TEXT,                      -- cluster_meeting | bilateral | field_visit
    clusters_present    TEXT,                      -- JSON array
    sites_discussed     TEXT,                      -- JSON array of site_ids
    action_items        TEXT,
    follow_up_date      DATE,
    recorded_by         TEXT
);

-- Site alerts
CREATE TABLE cccm_alerts (
    alert_id            TEXT PRIMARY KEY,
    site_id             TEXT REFERENCES cccm_sites(site_id),
    alert_type          TEXT,                      -- overcrowding | service_gap | closure_risk | protection | new_site
    severity            TEXT CHECK(severity IN ('Info','Warning','Critical')),
    triggered_at        DATETIME,
    description         TEXT,
    resolved_at         DATETIME,
    resolved_by         TEXT
);
```

---

## Alert engine: proactive site monitoring

A Senior IM Associate managing dozens of collective centres simultaneously cannot manually track every threshold. GENTE CCCM's `alert_engine.py` monitors the database continuously and generates structured alerts when conditions are met:

| Alert type | Trigger condition | Severity |
|---|---|---|
| Overcrowding | `pop_total > max_capacity × 1.2` | Warning |
| Severe overcrowding | `pop_total > max_capacity × 1.5` | Critical |
| Service gap — WASH | No WASH service confirmed in >72 hours | Warning |
| Service gap — Health | No Health service confirmed in >48 hours | Critical |
| Protection flag | AI detects protection keyword in field note | Critical |
| No recent monitoring | Site not visited in >5 days | Warning |
| Closure risk | AI classifies closure risk = true | Warning |
| New spontaneous site | New submission from unregistered GPS location | Info |
| Population spike | `pop_total` increase >30% since last visit | Warning |

Alerts appear as a notification layer in the QGIS dashboard and are exported daily to a `cccm_alerts_YYYYMMDD.csv` for sharing with the CCCM cluster coordinator via USB when internet is unavailable.

---

## Inter-cluster coordination exports

One of the most time-consuming tasks in CCCM IM is producing service gap analyses for cluster coordination meetings. GENTE CCCM automates this with `cluster_exporter.py`, which queries the `cccm_services` table and generates a standardized export for each humanitarian cluster:

```bash
python pipeline/cluster_exporter.py --cluster WASH --output /data/exports/
# Produces: wash_service_gaps_YYYYMMDD.xlsx
# Columns: site_id | site_name | municipio | pop_total |
#          wash_present | wash_org | days_since_confirmed | alert_level
```

Each cluster export is formatted for immediate use in coordination meetings: sorted by alert level, filtered to active sites only, with population figures from the most recent monitoring visit. No manual formatting required.

---

## QGIS layouts for CCCM outputs

The `qgis/cccm_project.qgz` includes five pre-configured print layouts matching standard CCCM information products:

**1. Master Site Map**
A3 landscape — all active sites symbolized by type and population size, service coverage overlay (color-coded by number of clusters present), state and municipio boundaries, road network, last-updated timestamp.

**2. Site Factsheet**
A4 portrait — single site: population breakdown bar chart, cluster service matrix (present / absent / unknown), alert history, monitoring visit log, site photos placeholder, contact information for site manager.

**3. Service Gap Dashboard**
A4 landscape — matrix view: sites as rows, clusters as columns, traffic-light coding (green = present, amber = unconfirmed >48h, red = gap >72h). Designed for CCCM cluster coordination meeting projection.

**4. Population Trend Map**
A4 landscape — choropleth by municipio showing population change between two monitoring rounds, with site-level point overlay.

**5. Weekly Situation Report Map**
A3 landscape — overview map for weekly sitrep: new sites (last 7 days), closed sites, critical alerts, total displaced population figure, source and date.

---

## Sample CCCM AI classification

**Field note input** (site monitor at Escuela Simón Bolívar, collective centre, Vargas state):

```
"El colegio está completamente lleno, están durmiendo en los pasillos y 
en el patio. No ha venido Cruz Roja desde hace 4 días. Hay una mujer que 
dice que su esposo la golpeó anoche y no quiere que la vean hablar conmigo."
```

**AI classification output:**

```json
{
  "overcrowding_signal": "severe",
  "service_gap": "Health",
  "service_gap_days_estimated": 4,
  "protection_flag": true,
  "protection_type": "GBV",
  "protection_confidence": 0.88,
  "closure_risk": false,
  "ai_confidence": 0.93
}
```

**What the Senior IM Associate sees:** A Critical alert fires immediately — GBV concern flagged for protection cluster escalation. A Warning alert fires for the 4-day health service gap. The site's QGIS symbol updates to red (critical). The protection cluster export for today's coordination meeting includes this site at the top of the list with the flag details.

The field note itself is never shared with clusters — only the structured classification. The raw text remains within the local database, accessible only to the IM coordinator, in compliance with Do No Harm data governance principles.

---

## Quickstart

**Prerequisites:** Docker, Docker Compose, Python 3.11+, QGIS 3.x, Ollama

```bash
git clone https://github.com/andreshermoso/gente_CCCM.git
cd gente_CCCM
docker-compose -f docker/docker-compose.yml up -d
pip install -r requirements.txt
ollama pull llama3.1:8b
python pipeline/kobo_to_qgis.py --sample --mode cccm
```

Open `qgis/cccm_project.qgz`. Fifteen synthetic collective centre monitoring records populate the master site map across three Venezuelan states, with pre-seeded alerts and service gap indicators.

---

## Long-term CCCM training vision

Every CCCM operation in Venezuela generates knowledge that currently disappears when the operation closes. Which site types lasted longest? Which coordination mechanisms worked? Which service gap patterns recurred? What protection indicators appeared earliest?

The CCCM training module on the roadmap converts after-action reviews from this and future operations into structured CCCM case studies, simulation exercises for IM coordinators, and site monitoring protocol improvements informed by real Venezuelan field experience. The goal is that the next CCCM IM coordinator deployed to Venezuela starts with a knowledge base built from every previous response — not from zero.

---

## Related repositories

| Repository | Role variant |
|---|---|
| [gente](https://github.com/andreshermoso/gente) | Core platform — full documentation and vision |
| [gente_DTM](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_DTM) | Senior IM Associate — DTM (22086) |
| [gente_IMS](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_IMS) | Senior IMS Registration Associate (22094) |
| [gente_AVRR](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_AVRR) | Assistant (22252) |
| [gente_R](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_R) | Resilience — institutional memory |

---

## About the author

**Andres Gabriel Hermoso Castillo**  
Systems Analyst | DataOps Architect | IM Specialist  
Caracas, Bolivarian Republic of Venezuela  
andres.hermoso@gmail.com | +58 412 701 0980

Developed in July 2026 in response to the Venezuela earthquake and as part of an application for the Senior Information Management Associate (CCCM) position with IOM's Venezuela Country Office (Job ID 22097).

---

## License

MIT License — free to use, adapt, and deploy in humanitarian contexts.

---
<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>


*"Ningún albergue debe caer entre las grietas de la coordinación."*  
*(No shelter site should fall through the gaps of coordination.)*
