# GENTE — AVRR
### Geospatial Emergency Network Training Engine
#### *Assisted Voluntary Return and Reintegration variant · IOM Venezuela*
#### *Inteligencia al servicio de nuestra gente*

> **Proof-of-concept offline humanitarian IM platform with a dedicated module for Assisted Voluntary Return and Reintegration (AVRR) field operations — onsite documentation verification, migrant case tracking, reception assistance workflows, and migration corridor monitoring.**  
> This variant is tailored to the operational context of the **Asistente de Apoyo en Terreno (AVRR)** position — Job ID 22252, IOM Venezuela Country Office.

→ *Core repository with full documentation and long-term vision:* [andreshermoso/gente](https://github.com/andreshermoso/gente)

---

## Migration as the operational center

GENTE was designed for the Venezuela earthquake response, but its foundational premise — that reliable information about people in movement is the prerequisite for every other humanitarian intervention — is equally the foundation of IOM's migration mandate.

More than 7.9 million Venezuelans have been displaced worldwide, making this one of the largest external displacement crises in modern history. The June 2026 earthquake added an acute internal displacement layer to a chronic external migration crisis that has been building for over a decade. For IOM's AVRR program in Venezuela, this means that the people arriving at airports and bus terminals are not only earthquake-affected internally displaced persons. They are migrants returning after years abroad — some voluntarily, some under pressure — who carry fragmented documentation histories, complex vulnerability profiles, and urgent needs that must be identified, recorded, and acted upon quickly at the point of reception.

The IOM Venezuela Country Office's AVRR program operates at exactly this intersection: migration management and humanitarian response, at the moment of arrival or departure, with a person in front of you, a form that must be completed correctly, and a case that must be tracked from that moment forward. GENTE AVRR was built to support that work.

---

## The AVRR operational challenge: documentation at the point of movement

Onsite documentation verification is the most consequential step in any AVRR operation. At an airport reception desk or a land border terminal, the field assistant must:

- Confirm the identity of the returning migrant against travel documents and IOM case records
- Verify that informed consent forms are signed, complete, and correctly matched to the individual
- Check that benefit authorization forms are accurate before any assistance is delivered
- Identify documentation gaps that could delay the case or trigger protection concerns
- Flag vulnerability indicators — health conditions, unaccompanied minors, signs of trafficking, disability, protection risks — that require immediate escalation
- Record all of the above accurately, quickly, and in a way that feeds into the IOM case management system

In Venezuela's current operational environment, two factors make this more difficult than the standard AVRR procedure assumes. First, many returning migrants have spent years without renewing their Venezuelan documentation, or have lost documents during displacement. Second, the connectivity conditions that make cloud-based case verification possible — stable internet at reception points, real-time access to central IOM databases — cannot be assumed in earthquake-affected areas or at secondary terminals and land crossings.

GENTE AVRR addresses both challenges: it provides offline-capable documentation verification support, and it is designed around the specific documentation vulnerabilities of Venezuelan returning migrants.

---

## Architecture overview

```
┌──────────────────────────────────────────────────────────────────┐
│  RECEPTION POINT (airport / bus terminal / land crossing)        │
│                                                                  │
│  📱 KoboCollect (Android)                                        │
│     AVRR intake form — bilingual (ES/EN)                         │
│     Document checklist — cédula / passport / consent / benefit   │
│     Vulnerability screening — 12 indicator flags                 │
│     Photo capture — document scan support                        │
│                                                                  │
│  Works fully offline — syncs over Wi-Fi hotspot to hub          │
└──────────────────────────────┬───────────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────────┐
│  FIELD COORDINATION HUB (IOM field office / mobile command)      │
│                                                                  │
│  avrr_pipeline.py                                               │
│  │                                                              │
│  ├── doc_verifier.py     → document completeness check          │
│  │   Required fields, consent match, benefit authorization      │
│  │   Missing document flags → generates follow-up task          │
│  │                                                              │
│  ├── vulnerability_screener.py → 12-indicator assessment        │
│  │   Health / disability / unaccompanied minor /                │
│  │   trafficking indicators / protection concern                │
│  │   Threshold breach → immediate supervisor alert              │
│  │                                                              │
│  ├── case_tracker.py     → SpatiaLite case database             │
│  │   Individual case records linked to migration corridor        │
│  │   Assistance delivery log — pre-departure / post-arrival     │
│  │   Status tracking: registered → verified → assisted → closed │
│  │                                                              │
│  └── field_reporter.py   → daily AVRR field report             │
│      Persons assisted / documentation gaps /                     │
│      vulnerability flags / operational incidents                 │
└──────────────────────────────────────────────────────────────────┘
```

---

## AVRR documentation verification module

The `doc_verifier.py` module implements the documentation checklist for each AVRR operation type:

### Returning migrant (arrival assistance)

| Document | Required | Action if missing |
|---|---|---|
| Venezuelan cédula or passport | Yes | Flag — escalate to protection officer |
| IOM case reference number | Yes | Flag — contact IOM case manager |
| Signed informed consent (original) | Yes | Cannot proceed — obtain or escalate |
| Benefit authorization form | Yes | Cannot deliver assistance without |
| Medical clearance (if flagged) | Conditional | Flag if health indicator triggered |
| Minor consent / guardian authorization | Conditional | Required if person under 18 |

### Pre-departure assistance

| Document | Required | Action if missing |
|---|---|---|
| Valid travel document | Yes | Cannot proceed — refer to documentation support |
| Voluntary return declaration | Yes | Must be signed in presence of field assistant |
| Destination country entry requirements | Verify | Checklist by destination country |
| Reintegration package acknowledgment | Yes | Signed copy retained by IOM |

All checklist results are recorded in the case record with timestamp, field assistant ID, and a free-text observations field for context the checklist cannot capture.

---

## Vulnerability screening: 12-indicator framework

The vulnerability screening module implements a structured 12-indicator assessment aligned with IOM's protection approach and the humanitarian Do No Harm framework. The field assistant completes this assessment during intake; the system flags any triggered indicator for immediate supervisor review.

```
HEALTH AND PHYSICAL WELLBEING
  [ ] Visible signs of acute illness or injury
  [ ] Self-reported medical condition requiring immediate attention
  [ ] Apparent nutritional deficit (particularly in children)

PROTECTION CONCERNS
  [ ] Unaccompanied or separated minor (under 18 without guardian)
  [ ] Indicators of trafficking or exploitation (recruitment, control, deception)
  [ ] Signs of gender-based violence or domestic abuse
  [ ] Person traveling under apparent duress or coercion

ADMINISTRATIVE VULNERABILITY
  [ ] Complete absence of identity documentation
  [ ] Discrepancy between stated identity and documents presented
  [ ] Case flagged in IOM pre-departure records for follow-up

PSYCHOSOCIAL
  [ ] Apparent severe psychological distress or disorientation
  [ ] Person expresses fear of return or safety concern at destination
```

Any single triggered indicator generates an immediate alert to the supervising AVRR coordinator. Two or more triggered indicators generate a protection escalation flag. All assessments are recorded with the field assistant ID, timestamp, and notes.

The system never displays vulnerability assessment results to non-authorized personnel. Access is restricted to the case management team and protection officers.

---

## Migration corridor monitoring

GENTE AVRR tracks case origin and destination at the municipio level, building an operational picture of migration corridors that complements IOM's DTM flow monitoring data.

Each case record includes:
- Last residence before departure (estado / municipio)
- Country and city of residence during migration
- Intended destination after return assistance
- Whether the return was voluntary, semi-voluntary (under regularization pressure), or involuntary
- Migration driver (earthquake displacement, economic, family reunification, regularization deadline)

Over the course of an AVRR operational period, this data produces a corridor map showing where returning migrants are coming from, where they are going, and what combination of drivers is shaping movement — directly supporting IOM's mandate to promote orderly and humane management of migration.

---

## AVRR database schema

```sql
-- AVRR case registry
CREATE TABLE avrr_cases (
    case_id             TEXT PRIMARY KEY,          -- VEN-AVRR-2026-000001
    iom_case_ref        TEXT UNIQUE,               -- IOM internal reference
    case_type           TEXT CHECK(case_type IN (
                          'voluntary_return',
                          'family_reunification',
                          'resettlement',
                          'post_arrival_assistance'
                        )),
    reception_point     TEXT,                      -- airport / terminal / crossing
    arrival_date        DATE,
    field_assistant_id  TEXT,
    origin_country      TEXT,
    origin_city         TEXT,
    destination_estado  TEXT,
    destination_municipio TEXT,
    migration_driver    TEXT,
    status              TEXT DEFAULT 'registered', -- registered → verified → assisted → closed
    created_at          DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_updated        DATETIME,
    geometry            GEOMETRY                   -- reception point GPS
);

-- Individual persons linked to cases (family units)
CREATE TABLE avrr_persons (
    person_id           TEXT PRIMARY KEY,
    case_id             TEXT REFERENCES avrr_cases(case_id),
    relationship        TEXT,                      -- principal / spouse / child / dependent
    first_name          TEXT NOT NULL,
    surname             TEXT NOT NULL,
    dob                 DATE,
    sex                 TEXT,
    nationality         TEXT,
    doc_type            TEXT,
    doc_number          TEXT,
    doc_verified        INTEGER DEFAULT 0,
    consent_signed      INTEGER DEFAULT 0,
    benefit_authorized  INTEGER DEFAULT 0,
    is_minor            INTEGER DEFAULT 0,
    guardian_present    INTEGER DEFAULT 0
);

-- Documentation verification log
CREATE TABLE avrr_doc_checks (
    check_id            TEXT PRIMARY KEY,
    person_id           TEXT REFERENCES avrr_persons(person_id),
    checked_at          DATETIME DEFAULT CURRENT_TIMESTAMP,
    assistant_id        TEXT,
    checklist_results   TEXT,                      -- JSON: per-document pass/fail/missing
    missing_docs        TEXT,                      -- JSON array of missing items
    observations        TEXT,
    all_clear           INTEGER DEFAULT 0
);

-- Vulnerability screening
CREATE TABLE avrr_vulnerability (
    screen_id           TEXT PRIMARY KEY,
    person_id           TEXT REFERENCES avrr_persons(person_id),
    screened_at         DATETIME DEFAULT CURRENT_TIMESTAMP,
    assistant_id        TEXT,
    indicators_triggered TEXT,                     -- JSON array of triggered indicators
    indicator_count     INTEGER DEFAULT 0,
    escalation_level    TEXT CHECK(escalation_level IN (
                          'none', 'supervisor_alert', 'protection_escalation'
                        )),
    supervisor_notified INTEGER DEFAULT 0,
    notes               TEXT                       -- RESTRICTED: protection officer access only
);

-- Assistance delivery log
CREATE TABLE avrr_assistance (
    assistance_id       TEXT PRIMARY KEY,
    case_id             TEXT REFERENCES avrr_cases(case_id),
    person_id           TEXT REFERENCES avrr_persons(person_id),
    assistance_type     TEXT,                      -- transport / cash / nfi / medical_referral / reintegration
    delivery_date       DATE,
    delivery_point      TEXT,
    amount              REAL,
    currency            TEXT DEFAULT 'USD',
    authorization_ref   TEXT,                      -- benefit authorization form reference
    delivered_by        TEXT,                      -- assistant ID
    receipt_signed      INTEGER DEFAULT 0
);

-- Field incident log
CREATE TABLE avrr_incidents (
    incident_id         TEXT PRIMARY KEY,
    reported_at         DATETIME DEFAULT CURRENT_TIMESTAMP,
    reporter_id         TEXT,
    incident_type       TEXT,                      -- operational / security / protection / medical
    location            TEXT,
    description         TEXT,
    severity            TEXT CHECK(severity IN ('Info','Minor','Serious','Critical')),
    reported_to         TEXT,                      -- supervisor / protection / security / medical
    follow_up_required  INTEGER DEFAULT 0,
    resolved            INTEGER DEFAULT 0
);

-- Full audit trail
CREATE TABLE avrr_audit_log (
    log_id              TEXT PRIMARY KEY,
    log_timestamp       DATETIME DEFAULT CURRENT_TIMESTAMP,
    operator_id         TEXT NOT NULL,
    action              TEXT NOT NULL,
    table_name          TEXT NOT NULL,
    record_id           TEXT NOT NULL,
    field_changed       TEXT,
    old_value           TEXT,
    new_value           TEXT
);
```

---

## Daily AVRR field report

`pipeline/field_reporter.py` generates the daily operational report that the field team submits to the AVRR coordinator:

```
══════════════════════════════════════════════════════════════════
IOM VENEZUELA — AVRR DAILY FIELD REPORT
Reception Point: Aeropuerto Internacional Simón Bolívar
Date: 2026-07-20  |  Report generated: 18:45
══════════════════════════════════════════════════════════════════

CASES ASSISTED TODAY
  Total cases received:              14
  Voluntary return (arrival):         9
  Family reunification:               3
  Post-arrival assistance:            2

PERSONS ASSISTED
  Total persons:                     31
  Adults:                            22
  Minors (under 18):                  9  ← 4 accompanied / 5 unaccompanied

DOCUMENTATION STATUS
  All documents verified:            27  (87%)
  Pending document follow-up:         4  (13%)
  Consent forms complete:            31  (100%)
  Benefit authorizations confirmed:  29

VULNERABILITY SCREENING
  Supervisor alerts triggered:        3
  Protection escalations:             1  ⚠ OPEN — protection officer assigned
  Health referrals made:              2

ORIGIN COUNTRIES TODAY
  Colombia: 8  |  Peru: 6  |  Chile: 5  |  Ecuador: 4  |  Other: 8

INCIDENTS
  Operational incidents:              0
  Security incidents:                 0
  Protection incidents:               1  (see escalation above)

OBSERVATIONS
  Increased volume of returns from Peru — regularization deadline effect.
  3 cases with expired cédulas — referred to documentation support unit.
══════════════════════════════════════════════════════════════════
```

---

## Connection to the broader GENTE platform

GENTE AVRR is part of the same platform family that supports DTM displacement tracking, CCCM site monitoring, and IMS individual registration. Its case data is designed to interface with the broader GENTE data ecosystem:

- **AVRR ↔ DTM:** Returning migrant origins and destinations feed into the DTM flow monitoring picture, adding the voluntary return dimension to the overall displacement and mobility dataset
- **AVRR ↔ IMS:** Individual records in AVRR cases are structured for linkage with IOM's IMS registration database, enabling continuity of case history across programs
- **AVRR ↔ CCCM:** Cases requiring temporary accommodation are linked to the CCCM master site list, so that bed availability and service coverage data is accessible at the moment a referral is needed

---

## Long-term vision: migration knowledge for Venezuela

Venezuela's migration crisis will not be resolved in the next nine months. The AVRR program is not only a return assistance operation — it is a window into the lived experience of Venezuelan migration: why people left, what they encountered abroad, why they are returning, and what they need to rebuild. Each case, documented carefully, is a data point in a larger picture that Venezuela's institutions, civil society, and international partners need to understand in order to design durable solutions.

The long-term GENTE vision includes a migration knowledge module that converts AVRR operational data — anonymized, aggregated, protected — into policy-relevant analysis: corridor trends, reintegration outcome tracking, documentation barrier patterns, and vulnerability profile evolution over time. This is not a tool for this nine-month contract. It is the direction in which this work, done carefully and consistently, can eventually point.

For now, GENTE AVRR is focused on what matters most in the next operational day: every person who arrives at a reception point in Venezuela deserves to have their documentation verified correctly, their vulnerability assessed respectfully, and their case recorded accurately — so that nothing falls through, no one is missed, and no assistance goes to the wrong person or fails to reach the right one.

---

## Related repositories

| Repository | Role variant |
|---|---|
| [andreshermoso/gente](https://github.com/andreshermoso/gente) | Core platform — full documentation and vision |
| [andreshermoso/gente_DTM](https://github.com/andreshermoso/gente_DTM) | Senior IM Associate — DTM (22086) |
| [andreshermoso/gente_CCCM](https://github.com/andreshermoso/gente_CCCM) | Senior IM Associate — CCCM (22097) |
| [andreshermoso/gente_IMS](https://github.com/andreshermoso/gente_IMS) | Senior IMS Registration Associate (22094) |

---

## About the author

**Andres Gabriel Hermoso Castillo**  
Systems Analyst | DataOps Architect | IM Specialist  
Caracas, Bolivarian Republic of Venezuela  
andres.hermoso@gmail.com | +58 412 701 0980

Developed in July 2026 in response to the Venezuela earthquake and as part of an application for the Asistente de Apoyo en Terreno (AVRR) position with IOM's Venezuela Country Office (Job ID 22252). The AVRR module reflects a conviction that field operations and information management are not separate functions — every document verified at a reception point, every vulnerability flag raised, and every case correctly recorded is an act of both protection and information governance.

---

## License

MIT License — free to use, adapt, and deploy in humanitarian contexts.

---

*"Cada persona que llega merece ser recibida con dignidad, documentada con precisión, y acompañada con respeto."*  
*(Every person who arrives deserves to be received with dignity, documented accurately, and accompanied with respect.)*
