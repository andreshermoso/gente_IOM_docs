# GENTE — IMS
### Geospatial Emergency Network Training Engine
#### *Information Management System Registration variant · IOM Venezuela earthquake response*
#### *Inteligencia al servicio de nuestra gente*

> **Proof-of-concept offline humanitarian registration and IMS platform built specifically to support individual and household registration operations in connectivity-degraded disaster environments.**  
> This variant is tailored to the responsibilities of the **Senior IMS Registration Associate** position — Job ID 22094, IOM Venezuela Country Office.

→ *Core repository with full documentation and long-term vision:* [andreshermoso/gente](https://github.com/andreshermoso/gente)

---

## Why this exists

Individual and household registration is the most data-sensitive operation in humanitarian response. It is also the one most dependent on system reliability — a registration system that loses records, produces duplicates, or becomes unavailable mid-operation causes direct harm to the people it is designed to serve. Families registered twice receive assistance that should go to families not yet registered. Families missed entirely fall through.

In Venezuela's June 2026 earthquake response, the connectivity conditions that make cloud-based registration systems unreliable are not edge cases — they are the baseline. Rolling blackouts (*administración de carga*) lasting 12–20 hours a day, bandwidth too degraded for reliable API calls, and the data sovereignty concerns inherent in transmitting vulnerable population records through cloud infrastructure all point toward the same architectural requirement: registration must work fully offline, with rigorous deduplication, and with zero tolerance for data loss.

GENTE IMS is designed for that requirement. It provides the Senior IMS Registration Associate with a locally hosted, SQL-backed individual registration system with built-in deduplication logic, configurable field workflows, bilingual (Spanish/English) interface support, SOP documentation tooling, and the data export formats required for migration to IOM's institutional IMS platforms when connectivity is restored.

---

## IMS Registration workflow — what this platform supports

The Senior IMS Registration Associate role combines system configuration, database management, data quality assurance, team training, and SOP development. GENTE IMS maps to each responsibility directly:

| IMS Registration responsibility | GENTE IMS component |
|---|---|
| Configure and maintain registration system | `config/registration_config.yml` — field definitions, validation rules, workflow stages |
| Design registration data entry forms | `forms/household_registration_es.xlsx` — XLSForm; `forms/individual_registration_es.xlsx` |
| Implement deduplication logic | `pipeline/dedup_engine.py` — probabilistic matching across name, DOB, origin, biographic fields |
| Manage and clean registration database | `pipeline/db_manager.py` — merge, archive, flag, audit trail tools |
| Ensure data quality and validation | `pipeline/qa_validator.py` — completeness, consistency, referential integrity checks |
| Train registration staff on system use | `docs/registration_sop_es.md` — bilingual SOP; `docs/data_entry_guide_es.md` |
| Produce registration statistics and reports | `pipeline/stats_reporter.py` — daily registration figures, demographic breakdowns, coverage maps |
| Support integration with IOM IMS platforms | `pipeline/ims_exporter.py` — IOM-compatible XML/CSV export for system migration |
| Develop and maintain SOPs | `docs/` — structured SOP library with version control |

---

## The deduplication problem in Venezuelan earthquake registration

Deduplication is the central technical challenge of humanitarian registration in Venezuela's post-earthquake context, for reasons specific to this operational environment:

**Pre-existing population mobility.** Venezuela's prolonged socioeconomic crisis produced significant internal migration before the earthquake. Many affected persons have moved multiple times, hold documents with addresses that no longer reflect their location, and may have registered with different organizations in different locations over the preceding years.

**Document loss.** Earthquake-affected families frequently lose identity documents in building collapses. Registration without documents — relying on biographic and biometric data — increases the risk of inconsistent name spellings, date of birth approximations, and other fields that make exact-match deduplication fail.

**Multi-site registration.** Displaced families may visit multiple registration points before settling. Without real-time deduplication across sites — impossible when sites lack connectivity — the same family can be registered at three locations before the data is ever consolidated.

**Name variation in Venezuelan Spanish.** Venezuelan naming conventions include double first names, maternal and paternal surnames, common nicknames used in place of legal names, and regional spelling variations (*Yolanda* / *Jolanda*, *Jesús* / *Jesus*, *María* / *Maria*) that break exact-match logic.

GENTE IMS addresses these challenges with a probabilistic deduplication engine that scores potential duplicate pairs across multiple weighted fields, surfaces matches above a configurable threshold for human review, and maintains a full audit trail of every merge, split, and override decision.

---

## Architecture overview

```
┌──────────────────────────────────────────────────────────────────┐
│  REGISTRATION POINTS (no connectivity required)                  │
│                                                                  │
│  📱 KoboCollect → local Wi-Fi → KoboToolbox (Docker, offline)   │
│                                                                  │
│  Registration staff enter household and individual data          │
│  Photo capture for visual verification (optional)                │
│  Document scan or manual transcription                           │
└──────────────────────────────┬───────────────────────────────────┘
                               │ JSON via local API (real-time)
                               ▼
┌──────────────────────────────────────────────────────────────────┐
│  IMS COMMAND HUB (Senior IMS Registration Associate workstation) │
│                                                                  │
│  registration_pipeline.py                                        │
│  │                                                              │
│  ├── qa_validator.py      → field-level validation on intake    │
│  │   Required fields, format checks, DOB plausibility,         │
│  │   document number format, coordinate bounds                  │
│  │                                                              │
│  ├── dedup_engine.py      → probabilistic duplicate detection   │
│  │   Weighted scoring: name + DOB + origin + family composition │
│  │   Threshold-based: auto-merge | human review | clear        │
│  │   Full audit trail of every decision                        │
│  │                                                              │
│  ├── db_manager.py        → SpatiaLite (ims_venezuela.sqlite)  │
│  │   Households | Individuals | Documents | Audit log          │
│  │                                                              │
│  ├── stats_reporter.py    → daily registration statistics       │
│  │   Total registered | New today | Pending review |           │
│  │   Demographic breakdown | Coverage by municipio             │
│  │                                                              │
│  └── ims_exporter.py      → IOM IMS-compatible export          │
│                              XML / CSV / Excel                   │
│                              Ready for institutional migration   │
└──────────────────────────────────────────────────────────────────┘
```

---

## Registration database schema

The IMS database is the most sensitive artifact in the GENTE IMS system. It is designed with data minimization, referential integrity, and full audit trail as primary constraints — not as afterthoughts.

```sql
-- Household registry: the primary registration unit
CREATE TABLE ims_households (
    household_id        TEXT PRIMARY KEY,          -- VEN-EQ-HH-000001 format
    registration_date   DATE NOT NULL,
    registration_point  TEXT,                      -- site or location of registration
    registration_staff  TEXT,                      -- enumerator ID
    estado              TEXT,
    municipio           TEXT,
    parroquia           TEXT,
    displacement_origin TEXT,                      -- where family came from
    displacement_reason TEXT,                      -- earthquake | economic | combined
    current_location    TEXT REFERENCES cccm_sites(site_id),  -- FK to CCCM master site
    shelter_type        TEXT,                      -- collective_centre | host | spontaneous | unknown
    head_of_household   TEXT REFERENCES ims_individuals(individual_id),
    household_size      INTEGER,
    has_documentation   INTEGER DEFAULT 0,
    dedup_status        TEXT DEFAULT 'pending',    -- pending | clear | merged_into | flagged
    merged_into_hh      TEXT REFERENCES ims_households(household_id),
    notes               TEXT,
    created_at          DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_updated        DATETIME,
    geometry            GEOMETRY                   -- registration point GPS
);

-- Individual registry: one row per person
CREATE TABLE ims_individuals (
    individual_id       TEXT PRIMARY KEY,          -- VEN-EQ-IN-000001 format
    household_id        TEXT REFERENCES ims_households(household_id),
    relationship_to_hoh TEXT,                      -- head | spouse | child | parent | other
    first_name          TEXT NOT NULL,
    second_name         TEXT,
    first_surname       TEXT NOT NULL,
    second_surname      TEXT,
    nickname            TEXT,                      -- used for dedup matching
    sex                 TEXT CHECK(sex IN ('M','F','Other','Unknown')),
    dob_exact           DATE,                      -- if document available
    dob_estimated       TEXT,                      -- year or year+month if no document
    dob_confidence      TEXT CHECK(dob_confidence IN ('exact','estimated','unknown')),
    nationality         TEXT DEFAULT 'Venezuelan',
    doc_type            TEXT,                      -- cedula | passport | none | unknown
    doc_number          TEXT,
    doc_verified        INTEGER DEFAULT 0,
    phone               TEXT,
    vulnerability_flags TEXT,                      -- JSON: pregnant | disability | unaccompanied_minor | medical
    dedup_status        TEXT DEFAULT 'pending',
    dedup_score         REAL,                      -- highest match score seen
    dedup_matched_to    TEXT REFERENCES ims_individuals(individual_id),
    created_at          DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_updated        DATETIME
);

-- Document tracking
CREATE TABLE ims_documents (
    doc_id              TEXT PRIMARY KEY,
    individual_id       TEXT REFERENCES ims_individuals(individual_id),
    doc_type            TEXT,
    doc_number          TEXT,
    issuing_authority   TEXT,
    issue_date          DATE,
    expiry_date         DATE,
    verified_by         TEXT,
    verified_at         DATETIME,
    scan_path           TEXT                       -- local file path if scanned
);

-- Deduplication review queue
CREATE TABLE ims_dedup_queue (
    review_id           TEXT PRIMARY KEY,
    entity_type         TEXT CHECK(entity_type IN ('household','individual')),
    candidate_a         TEXT NOT NULL,             -- ID of first candidate
    candidate_b         TEXT NOT NULL,             -- ID of potential duplicate
    match_score         REAL NOT NULL,             -- 0.0–1.0 composite score
    score_breakdown     TEXT,                      -- JSON: per-field scores
    auto_action         TEXT,                      -- merge | flag | clear (if above/below threshold)
    review_status       TEXT DEFAULT 'pending',    -- pending | reviewed | auto_processed
    reviewer            TEXT,
    reviewer_decision   TEXT,                      -- merge | not_duplicate | defer
    reviewed_at         DATETIME,
    created_at          DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Full audit trail: every data change recorded
CREATE TABLE ims_audit_log (
    log_id              TEXT PRIMARY KEY,
    log_timestamp       DATETIME DEFAULT CURRENT_TIMESTAMP,
    operator            TEXT NOT NULL,
    action              TEXT NOT NULL,             -- INSERT | UPDATE | DELETE | MERGE | SPLIT | EXPORT
    table_name          TEXT NOT NULL,
    record_id           TEXT NOT NULL,
    field_changed       TEXT,
    old_value           TEXT,
    new_value           TEXT,
    reason              TEXT
);
```

The audit log is append-only and records every INSERT, UPDATE, DELETE, MERGE, SPLIT, and EXPORT operation with the operator name, timestamp, and reason. It cannot be modified after writing — it is the accountability chain that makes the registration data trustworthy.

---

## Deduplication engine: design and logic

`pipeline/dedup_engine.py` implements a weighted probabilistic matching approach suited to the Venezuelan registration context:

### Field weights

| Field | Weight | Match method |
|---|---|---|
| First surname | 0.25 | Jaro-Winkler distance (handles spelling variation) |
| First name + second name | 0.20 | Jaro-Winkler + nickname lookup table |
| Date of birth | 0.20 | Exact match (0 or 1); partial credit for year-only match (0.5) |
| Second surname | 0.15 | Jaro-Winkler |
| Document number | 0.15 | Exact match only (0 or 1) — high confidence when present |
| Displacement origin | 0.05 | Exact municipio match |

**Composite score = Σ (field weight × field match score)**

### Decision thresholds

| Score range | Action | Rationale |
|---|---|---|
| ≥ 0.95 | Auto-flag as probable duplicate → human review | High confidence; human confirms before merge |
| 0.80 – 0.94 | Queue for human review | Likely duplicate; operator decision required |
| 0.60 – 0.79 | Log as possible match; no action | Low confidence; monitor across future registrations |
| < 0.60 | Clear — no duplicate detected | Proceed to registration |

**No automatic merges.** Every deduplication decision that results in a record merge requires explicit human confirmation by the Senior IMS Registration Associate or designated operator. The dedup engine surfaces candidates; humans decide. This is a hard architectural constraint, not a configuration option.

### Venezuelan name normalization

Before scoring, all name fields pass through a normalization layer that handles:
- Accent stripping for comparison (*José* → *jose*, *María* → *maria*)
- Common nickname mapping (*Pepe* → *José*, *Cheo* → *Sergio*, *Conchita* → *Concepción*)
- Compound first name handling (*José Luis* matched against *José* and *Luis* separately)
- Common spelling variants (*Yolanda* / *Jolanda*, *Xiomara* / *Siomara*)

The nickname mapping table (`config/vzla_nicknames.csv`) is Venezuela-specific and extensible — registration staff can add new mappings as they encounter them in the field.

---

## System configuration: `registration_config.yml`

A Senior IMS Registration Associate configures the registration system through a single YAML file, without writing code. Key configurable parameters:

```yaml
registration:
  operation_name: "Venezuela Earthquake Response 2026"
  operation_code: "VEN-EQ-2026"
  id_prefix_household: "VEN-EQ-HH"
  id_prefix_individual: "VEN-EQ-IN"
  id_sequence_start: 1
  registration_points:
    - id: RP001
      name: "Centro de Registro Petare"
      site_id: VEN-EQ-CS-0012
    - id: RP002
      name: "Centro de Registro La Dolorita"
      site_id: VEN-EQ-CS-0019

deduplication:
  auto_flag_threshold: 0.95
  review_queue_threshold: 0.80
  nickname_table: config/vzla_nicknames.csv
  require_human_confirmation: true           # cannot be set to false

validation:
  require_dob: false                         # allow estimated DOB
  require_document: false                    # allow undocumented registration
  require_gps: true
  max_household_size: 25                     # flag for review if exceeded
  min_dob_year: 1920

export:
  ims_format: "IOM_IMS_v4"
  export_encoding: "UTF-8"
  include_audit_trail: true
  encrypt_export: true
```

This configuration-first design means the system can be adapted to a new operation, a new country, or a new registration point by editing the YAML file — not by modifying Python code. The Senior IMS Registration Associate owns the configuration; developers are not required for operational adjustments.

---

## SOP library structure

A core responsibility of the Senior IMS Registration Associate is developing and maintaining standard operating procedures for registration staff. GENTE IMS includes a `docs/sop/` directory with a structured SOP template library:

```
docs/sop/
├── SOP-IMS-001_registration_intake_es.md       ← step-by-step data entry protocol
├── SOP-IMS-002_document_verification_es.md     ← cedula / passport verification steps
├── SOP-IMS-003_undocumented_registration_es.md ← protocol for persons without documents
├── SOP-IMS-004_dedup_review_es.md              ← how to review and decide dedup queue
├── SOP-IMS-005_data_correction_es.md           ← how to correct errors with audit trail
├── SOP-IMS-006_daily_backup_es.md              ← USB backup procedure
├── SOP-IMS-007_export_and_handover_es.md       ← IMS migration export procedure
└── SOP-IMS-008_vulnerability_flagging_es.md    ← identifying and flagging vulnerable individuals
```

Each SOP is written in plain Venezuelan Spanish, structured as numbered steps, and designed to be printed and laminated for field registration desks. Version numbers and approval dates are embedded in each file header for change management.

---

## Daily registration statistics report

`pipeline/stats_reporter.py` produces a daily registration summary that the Senior IMS Registration Associate shares with the operation coordinator and cluster leads:

```
═══════════════════════════════════════════════════════
VENEZUELA EARTHQUAKE RESPONSE — REGISTRATION STATISTICS
Operation: VEN-EQ-2026 | Date: 2026-07-15 | 18:00 hrs
═══════════════════════════════════════════════════════

CUMULATIVE TOTALS
  Households registered:     1,847
  Individuals registered:    6,293
  Pending dedup review:         34
  Confirmed duplicates:         89  (1.4% duplicate rate)

TODAY'S ACTIVITY
  New households:              127
  New individuals:             438
  Dedup reviews completed:      21
  Merges confirmed:              8

DEMOGRAPHIC BREAKDOWN
  Children (0–17):           2,104  (33.4%)
  Adults (18–59):            3,618  (57.5%)
  Elderly (60+):               571  ( 9.1%)
  Female:                    3,298  (52.4%)
  Male:                      2,995  (47.6%)

VULNERABILITY FLAGS
  Pregnant women:              112
  Persons with disability:     203
  Unaccompanied minors:         17  ⚠ PRIORITY FOLLOW-UP
  Medical needs flagged:        89

REGISTRATION BY ESTADO
  Miranda:                   2,841  (45.1%)
  Vargas:                    1,987  (31.6%)
  Caracas D.C.:              1,465  (23.3%)

DOCUMENTATION STATUS
  With valid document:       4,102  (65.2%)
  Without document:          2,191  (34.8%)

DATA QUALITY
  QA failures today:             3  (0.7% of new records)
  Fields missing (required):     0
═══════════════════════════════════════════════════════
```

This report is generated as both a plain-text file (for radio operators and printed distribution) and a structured JSON file (for integration with coordination dashboards when connectivity is restored).

---

## IOM IMS export

When operational connectivity is restored — or at the end of the emergency phase — the GENTE IMS database must be migrated to IOM's institutional IMS platform. `pipeline/ims_exporter.py` produces a complete, encrypted export in IOM IMS v4-compatible format:

```bash
python pipeline/ims_exporter.py \
  --operation VEN-EQ-2026 \
  --format IOM_IMS_v4 \
  --include-audit-trail \
  --encrypt \
  --output /media/USB_EXPORT/ims_export_20260715.xml.enc
```

The export includes all household and individual records, the complete deduplication decision log, the full audit trail, and a data quality summary. The receiving IOM IMS administrator can verify record counts, review dedup decisions, and import with confidence that the data provenance is fully documented.

---

## Quickstart

**Prerequisites:** Docker, Docker Compose, Python 3.11+, QGIS 3.x, Ollama

```bash
git clone https://github.com/andreshermoso/gente_IMS.git
cd gente_IMS
docker-compose -f docker/docker-compose.yml up -d
pip install -r requirements.txt
python pipeline/registration_pipeline.py --sample --records 50
```

The sample run generates 50 synthetic household registrations, including 6 intentional near-duplicate pairs at varying match scores, to demonstrate the deduplication review queue. Open `qgis/ims_project.qgz` to see registration coverage by municipio.

---

## Long-term IMS training vision

Registration data collected during the Venezuela earthquake response contains operational lessons that no future registration manual can replicate: which name normalization rules mattered most, which vulnerability flags identified the highest-need families, how duplicate rates varied by registration point and time of day, which SOPs needed revision after the first week.

The IMS training module on the GENTE roadmap converts these operational observations into a Venezuela-specific registration training curriculum: case studies from real deduplication decisions (anonymized), SOP revision history with documented rationale, and simulation exercises built from the actual data patterns of the 2026 response. The goal is that the next Senior IMS Registration Associate deployed to Venezuela inherits not just a system but a knowledge base.

---

## Related repositories

| Repository | Role variant |
|---|---|
| [andreshermoso/gente](https://github.com/andreshermoso/gente) | Core platform — full documentation and vision |
| [andreshermoso/gente_DTM](https://github.com/andreshermoso/gente_DTM) | Senior IM Associate — DTM (22086) |
| [andreshermoso/gente_CCCM](https://github.com/andreshermoso/gente_CCCM) | Senior IM Associate — CCCM (22097) |

---

## About the author

**Andres Gabriel Hermoso Castillo**  
Systems Analyst | DataOps Architect | IM Specialist  
Caracas, Bolivarian Republic of Venezuela  
andres.hermoso@gmail.com | +58 412 701 0980

Developed in July 2026 in response to the Venezuela earthquake and as part of an application for the Senior IMS Registration Associate position with IOM's Venezuela Country Office (Job ID 22094). The deduplication engine design draws on 30 years of experience in database administration, data quality management, and ETL pipeline design across enterprise and humanitarian contexts.

---

## License

MIT License — free to use, adapt, and deploy in humanitarian contexts.

---

*"Cada persona merece ser contada una sola vez — y ninguna debe quedar sin contar."*  
*(Every person deserves to be counted once — and none should go uncounted.)*
