# GENTE-R
## Geospatial Emergency Network Training Engine — Resilience

> *The institutional memory and long-term preparedness component of the GENTE humanitarian platform ecosystem.*

**Inteligencia al servicio de nuestra gente — más allá de la emergencia**

---

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  GENTE ECOSYSTEM — FIVE VARIANTS, ONE MISSION                              ║
║                                                                            ║
║  GENTE-DTM    Understanding population movements                           ║
║  GENTE-CCCM   Supporting displaced populations and humanitarian coord.     ║
║  GENTE-IMS    Managing critical humanitarian information and registration  ║
║  GENTE-AVRR   Supporting voluntary return and sustainable reintegration    ║
║  GENTE-R  ►   Preserving institutional knowledge · Building resilience     ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

→ *Core repository:* [andreshermoso/gente](https://github.com/andreshermoso/gente)  
→ *Full ecosystem:* DTM · CCCM · IMS · AVRR · **R (this repository)**

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Humanitarian Context](#2-humanitarian-context)
3. [Problem Statement](#3-problem-statement)
4. [Strategic Vision](#4-strategic-vision)
5. [Platform Architecture](#5-platform-architecture)
6. [Operational Objectives](#6-operational-objectives)
7. [Long-Term Sustainability](#7-long-term-sustainability)
8. [Training Philosophy](#8-training-philosophy)
9. [Deployment Considerations](#9-deployment-considerations)
10. [Future Roadmap](#10-future-roadmap)
11. [Repository Structure](#11-repository-structure)
12. [About the Project](#12-about-the-project)

---

## 1. Executive Summary

GENTE-R is the fifth and most strategically distinctive variant of the GENTE platform ecosystem. While its four sister variants — DTM, CCCM, IMS, and AVRR — are operational tools designed to support humanitarian response during an active emergency, GENTE-R addresses a fundamentally different and arguably more important challenge: **what happens after the emergency ends and international support diminishes**.

The platform is built around a single strategic question:

> **How can humanitarian knowledge survive long after humanitarian funding has disappeared?**

GENTE-R is not a field data collection tool. It is not a dashboard or a situation report generator. It is a **humanitarian resilience architecture** — a system for capturing, preserving, structuring, and continuously building the institutional knowledge that makes communities more capable of responding to the next crisis, independently of whether international humanitarian organizations are still present.

Its core functions are:

- **Knowledge capture:** Systematically extracting lessons learned, after-action reviews, standard operating procedures, and operational observations from every humanitarian response operation conducted in Venezuela
- **Knowledge preservation:** Maintaining a structured, searchable, and continuously updated repository of humanitarian preparedness knowledge that does not disappear when a project cycle ends
- **Knowledge transfer:** Converting operational field experience into training content, simulation exercises, certification curricula, and community preparedness programs that build local capacity at scale
- **Knowledge evolution:** Ensuring that each successive emergency response starts with what previous responses learned, rather than from zero

The philosophical premise is simple but consequential: emergency preparedness should never be treated as a temporary project. It must become a continuously evolving national capability.

GENTE-R is the component of the GENTE ecosystem designed to make that transformation possible.

---

## 2. Humanitarian Context

### 2.1 Venezuela's compounded crisis landscape

Venezuela's humanitarian situation in 2026 is the product of over a decade of compounding crises rather than a single catastrophic event. Understanding GENTE-R requires understanding this context — because the platform's long-term resilience mission is shaped by the specific vulnerabilities that make Venezuela's recovery particularly fragile.

**The displacement crisis at scale**

More than 7.9 million Venezuelans have been displaced worldwide, making this one of the largest external displacement crises in modern history. According to the UNHCR and the R4V Coordination Platform, approximately one in four Venezuelans has left the country. This exodus has reshaped the demographic composition of the country: the people who remain are disproportionately elderly, very young, or unable to migrate — the populations most vulnerable in any disaster scenario.

**The June 2026 earthquake**

The June 24, 2026 earthquake struck a country already operating at the margins of institutional capacity. Infrastructure that might have been resilient under normal conditions was not. Emergency response systems that might have activated automatically required international support that took time to mobilize. Communities that might have self-organized effectively lacked the preparedness training and institutional memory to do so with speed and coherence.

The earthquake did not create Venezuela's vulnerability. It revealed it.

**The structural vulnerabilities that predate and survive the earthquake**

Venezuela's emergency response capacity is constrained by a set of structural factors that will persist long after the earthquake's immediate humanitarian impact has been addressed:

*Infrastructure fragility:* Chronic underinvestment and years of rolling blackouts (*administración de carga*) have left power, water, and communications infrastructure unable to withstand even moderate shocks. Recovery and reconstruction will take years, not months.

*Institutional capacity gaps:* Public health surveillance has been severely compromised. The national epidemiological bulletin has been suppressed for over eleven years. Emergency response protocols that should be maintained as living institutional documents have in many cases not been updated, tested, or trained against in years.

*Demographic pressure:* The departure of millions of Venezuelans — including many trained professionals, healthcare workers, and educators — has created knowledge gaps in precisely the sectors most critical to disaster preparedness and response.

*Socioeconomic fragility:* Large-scale displacement of newly affected persons can rapidly outpace regional supply chains, available housing, and local labor markets. The social cohesion that enables community self-organization in emergencies has been strained by years of economic hardship and political tension.

### 2.2 The global humanitarian funding landscape

Venezuela's crisis does not exist in isolation. It competes for attention, funding, and operational capacity with a global humanitarian landscape under unprecedented pressure.

The factors currently shaping that landscape include:

**Escalating armed conflicts and geopolitical instability** across multiple regions are generating simultaneous large-scale displacement emergencies that compete for the same pool of international humanitarian funding. Every new crisis that captures global attention is a crisis that draws resources away from existing ones.

**Climate change-driven emergencies** — severe droughts, catastrophic floods, and extreme weather events — are increasing in both frequency and severity. The El Niño phenomenon alone has the potential to affect global food production and food security in ways that will generate secondary humanitarian needs at massive scale.

**Emerging public health threats** continue to surface. The European wildfire crisis of July 2026, which exposed millions of people to dangerous concentrations of PM2.5 airborne particles capable of penetrating deep into the respiratory system, is a recent example of a non-traditional disaster category with significant health impact. Zoonotic diseases and epidemic outbreaks — as the June 2026 Federación Médica Venezolana communiqué on a lethal cardiorespiratory pathology in Varinas and Anzoátegui states illustrates — remain a constant and largely unpredictable risk.

**Global supply chain disruptions** continue to affect humanitarian logistics and procurement, adding cost and complexity to operations that are already underfunded.

**Rising xenophobia and social tensions** in host countries are affecting the quality and durability of protection for displaced Venezuelan populations abroad, and creating pressure for return before conditions at home are adequate to receive them.

Together, these factors create a humanitarian funding environment in which the trajectory of support to any individual crisis is almost certain to decline over time, regardless of the scale of need that persists on the ground.

### 2.3 The funding cliff and its consequences

History provides consistent evidence of a pattern that humanitarian practitioners call the **funding cliff**: the point at which international attention shifts to a new emergency, humanitarian appeals go undersubscribed, and operational capacity is reduced precisely when long-term recovery work requires sustained investment.

The most challenging phase of a humanitarian crisis frequently begins after international attention has moved elsewhere. Long-term recovery — housing reconstruction, livelihood restoration, institutional capacity building, social reintegration, mental health support, documentation regularization — may continue for years or decades after emergency operations have concluded.

For Venezuela specifically, the funding cliff presents a particular risk. The structural vulnerabilities described above mean that recovery will be slow and uneven. The communities most affected by the earthquake are not necessarily the communities with the greatest local capacity to rebuild. And the institutional memory that would enable effective local response to future emergencies — floods, droughts, disease outbreaks, secondary displacement events — is not currently being systematically captured, preserved, or transferred.

This is the gap that GENTE-R is designed to fill.

---

## 3. Problem Statement

### 3.1 The knowledge evaporation problem

Every humanitarian response operation generates knowledge. Field teams learn which approaches work and which do not. Coordinators develop an understanding of local power structures, community decision-making processes, and institutional relationships that cannot be read in a handbook. IM specialists discover which data collection methods yield reliable results under Venezuelan field conditions and which produce noise. Protection officers develop frameworks for identifying vulnerability indicators that are calibrated to the specific social context of affected communities.

This knowledge is invaluable. And in the current humanitarian operating model, it almost entirely evaporates when a project cycle ends.

After-action reviews are conducted inconsistently, if at all. Lessons learned documents are filed in organizational repositories that are rarely consulted. Staff rotate to other operations, taking their operational judgment with them. Institutional memory is stored in the heads of individuals rather than in systems, processes, and training materials that can be accessed by the next person who faces the same challenge.

The result is that each successive humanitarian response in Venezuela — and there will be successive responses — begins with less cumulative knowledge than it should. Teams relearn lessons that were already learned. Approaches that did not work are tried again. Communities that have already been through this process are asked to repeat it from the beginning.

**This is not a resource allocation problem. It is a knowledge management problem.** And it is largely invisible because the cost of not capturing knowledge is diffuse and delayed, while the cost of capturing it is immediate and concrete.

### 3.2 The capacity dependency problem

International humanitarian organizations operating in Venezuela provide services that Venezuela's own institutions are currently unable to provide at the required scale and quality. This is the legitimate and necessary function of organizations like IOM, UNHCR, OCHA, and their operational partners.

The problem is structural dependency: when operational capacity is provided by international organizations operating on finite project cycles, the capacity does not automatically transfer to local institutions when the project ends. Communities become dependent on a form of support whose continuation is determined by funding decisions made in donor capitals rather than by the needs of the communities themselves.

**The question is not whether international humanitarian organizations should provide capacity.** They should, and they do so effectively. The question is whether that capacity is being transferred — systematically, intentionally, and at sufficient scale — to local institutions, civil society organizations, community leaders, healthcare workers, educators, and volunteers who will still be present when international operational presence is reduced.

Current humanitarian operating models are not optimized for this transfer. They are optimized for operational delivery during the emergency phase, which is appropriate given the urgency of that phase. But the transfer of capacity requires a different kind of investment: in training content development, in simulation-based learning, in SOP documentation, in knowledge repository maintenance, and in the institutional relationships that enable local actors to keep building after international actors have left.

### 3.3 The preparedness gap

Venezuela's recent experience demonstrates the cost of insufficient preparedness at the moment of crisis. Communities that have been through structured preparedness training, that have practiced their response protocols in simulation exercises, and that have local teams capable of mobilizing quickly are demonstrably more resilient than communities that have not.

The preparedness gap — the difference between the preparedness that exists and the preparedness that would be needed to respond effectively to the next crisis — is currently large and growing. Each year that passes without a systematic national investment in preparedness is a year in which the gap widens.

Closing this gap requires more than periodic training programs. It requires a continuously evolving knowledge ecosystem: one that captures new lessons as they are learned, incorporates them into updated training content, makes that content accessible to a broad base of local actors, and tracks the development of local preparedness capacity over time.

That is the systems-level gap that GENTE-R addresses.

---

## 4. Strategic Vision

### 4.1 The foundational principle

> **Emergency preparedness should never be treated as a temporary project. It must become a continuously evolving national capability.**

This principle is the foundation of GENTE-R's design. Every architectural decision, every feature, every data structure in the platform flows from it.

A temporary project ends when funding ends. A national capability grows over time regardless of funding cycles. The distinction is not rhetorical — it has concrete implications for how knowledge is captured, how training is structured, how local actors are engaged, and how the platform evolves.

### 4.2 The transformation cycle

GENTE-R is designed around a four-stage transformation cycle that converts operational experience into durable resilience:

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   OPERATIONAL EXPERIENCE        INSTITUTIONAL KNOWLEDGE             │
│                                                                     │
│   Field observations     ──►    Lessons learned repository          │
│   Incident reports       ──►    SOP library                         │
│   After-action reviews   ──►    Best practices database             │
│   Case studies           ──►    Searchable knowledge base           │
│                                                                     │
│              ▼                              ▼                       │
│                                                                     │
│   TRAINING CONTENT              LOCAL CAPACITY                      │
│                                                                     │
│   Simulation scenarios   ──►    Community preparedness              │
│   Training curricula     ──►    Local responder networks            │
│   Certification programs ──►    Institutional resilience            │
│   E-learning modules     ──►    Self-sustaining knowledge growth    │
│                                                                     │
│              ▲______________________________________________▲        │
│                       Continuous feedback loop                      │
└─────────────────────────────────────────────────────────────────────┘
```

The cycle is continuous. Every local response operation generates new field experience that enters the knowledge repository as lessons learned. Those lessons update the training content. Updated training content reaches a broader base of local actors. Those actors become more capable, and their experiences in future response operations generate new lessons that re-enter the cycle.

Over time, the accumulated knowledge grows. Local capacity deepens. The dependency on international operational presence decreases — not because international organizations have withdrawn, but because local capability has grown to the point where it can carry more of the load independently.

### 4.3 The resilience ecosystem model

GENTE-R does not operate in isolation. It is the institutional memory layer of an ecosystem in which all five GENTE variants contribute knowledge and draw on it:

**From operational variants to GENTE-R:**

Every DTM displacement assessment produces field observations about community mobility patterns, infrastructure access, and local governance capacity that belong in the lessons learned repository. Every CCCM site monitoring visit generates knowledge about what site management approaches work under Venezuelan conditions. Every IMS registration operation reveals patterns in documentation gaps and vulnerability profiles that should inform future preparedness planning. Every AVRR reception operation produces insights about the reintegration needs and barriers facing returning migrants that should shape the reintegration support knowledge base.

**From GENTE-R to operational variants:**

The lessons learned repository improves future DTM assessment methodology. Updated SOPs derived from field experience make CCCM coordination more efficient. Training materials calibrated to Venezuelan conditions make IMS registration teams more capable. Reintegration best practices preserved in the knowledge base enable better AVRR outcomes.

The five variants form a learning system, not just a tool set.

### 4.4 What success looks like in ten years

A successful GENTE-R implementation would, over a ten-year horizon, produce measurable outcomes across three dimensions:

**Knowledge dimension:** A continuously maintained, searchable repository containing thousands of indexed lessons learned, hundreds of validated SOPs, and a growing library of case studies — accessible to humanitarian practitioners, local responders, researchers, and policymakers in Venezuela and across the region.

**Capacity dimension:** A network of trained local responders, community volunteers, healthcare workers, and educators across Venezuela's earthquake-affected and disaster-prone regions — capable of activating coherent, protocol-guided response within hours of an emergency, without waiting for international operational capacity to mobilize.

**Institutional dimension:** A set of national institutions — civil protection agencies, health ministries, municipal governments, universities, NGOs — that have incorporated GENTE-R's training content, SOPs, and simulation methodology into their own institutional programs, creating knowledge propagation channels that operate independently of the GENTE project.

If, ten years from now, Venezuela experiences another major earthquake and the response is faster, better coordinated, more effectively targeted at the most vulnerable, and less dependent on international operational presence than the June 2026 response was — and if that improvement can be traced in part to the knowledge ecosystem that GENTE-R built — then the platform will have achieved its purpose.

---

## 5. Platform Architecture

### 5.1 System overview

GENTE-R is a knowledge management and training platform with five integrated modules:

```
┌─────────────────────────────────────────────────────────────────────────┐
│  GENTE-R PLATFORM ARCHITECTURE                                          │
│                                                                         │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐     │
│  │  MODULE 1        │  │  MODULE 2        │  │  MODULE 3        │     │
│  │  Knowledge       │  │  SOP Library     │  │  Training        │     │
│  │  Repository      │  │  & Procedures    │  │  Content Engine  │     │
│  │                  │  │                  │  │                  │     │
│  │  Lessons learned │  │  Living SOPs     │  │  Scenarios       │     │
│  │  Case studies    │  │  Field protocols │  │  Simulations     │     │
│  │  Best practices  │  │  Checklists      │  │  Curricula       │     │
│  │  Historical data │  │  Decision trees  │  │  Assessments     │     │
│  └────────┬─────────┘  └────────┬─────────┘  └────────┬─────────┘     │
│           │                     │                      │               │
│           └─────────────────────┼──────────────────────┘               │
│                                 │                                       │
│                    ┌────────────▼────────────┐                         │
│                    │   RAG KNOWLEDGE ENGINE   │                         │
│                    │   (Ollama + local LLM)   │                         │
│                    │                          │                         │
│                    │   Semantic search        │                         │
│                    │   Context-aware Q&A      │                         │
│                    │   Content generation     │                         │
│                    │   Gap identification     │                         │
│                    └────────────┬────────────┘                         │
│                                 │                                       │
│           ┌─────────────────────┼──────────────────────┐               │
│           │                     │                      │               │
│  ┌────────▼─────────┐  ┌────────▼─────────┐  ┌────────▼─────────┐    │
│  │  MODULE 4        │  │  MODULE 5        │  │  DATA FEEDS      │    │
│  │  Crisis          │  │  Capacity        │  │                  │    │
│  │  Intelligence    │  │  Tracker         │  │  GENTE-DTM       │    │
│  │  Monitor         │  │                  │  │  GENTE-CCCM      │    │
│  │                  │  │  Local responder │  │  GENTE-IMS       │    │
│  │  Social media    │  │  registry        │  │  GENTE-AVRR      │    │
│  │  Signal detect.  │  │  Training logs   │  │  External APIs   │    │
│  │  Misinformation  │  │  Cert. tracking  │  │  (offline-ready) │    │
│  │  Early warning   │  │  Coverage maps   │  │                  │    │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.2 Module 1 — Knowledge Repository

The Knowledge Repository is GENTE-R's foundational component. It is a structured, searchable database of humanitarian preparedness knowledge derived from field operations, academic research, regional best practices, and accumulated institutional experience.

#### 5.2.1 Knowledge taxonomy

All entries in the repository are classified according to a four-level taxonomy:

```
LEVEL 1 — DOMAIN
  Emergency response / Migration management / Public health / 
  Protection / Logistics / Information management / Community resilience

LEVEL 2 — PHASE
  Preparedness / Early warning / Immediate response / 
  Recovery / Reconstruction / Resilience building

LEVEL 3 — CONTEXT
  Earthquake / Flood / Epidemic / Displacement / 
  AVRR / CCCM / DTM / IMS / Multi-hazard

LEVEL 4 — APPLICABILITY
  National / Regional / Municipal / Community / Household
```

Every knowledge entry receives a taxonomy classification, enabling semantic search across multiple dimensions simultaneously.

#### 5.2.2 Entry types

The repository contains six categories of knowledge entries:

**Lessons learned:** Structured post-operation observations in a standardized format:
- What was planned vs. what happened
- Why the difference occurred
- What was the impact of the difference
- What should be done differently in future operations
- Who is responsible for implementing the change
- How the change should be tracked

**Case studies:** Narrative accounts of specific interventions, decisions, or challenges drawn from GENTE operational data, anonymized and structured for educational use. Case studies are the primary raw material for simulation-based training scenarios.

**Best practices:** Validated approaches that have demonstrated effectiveness under Venezuelan field conditions, with evidence of outcomes and guidance on the conditions under which the approach is applicable.

**Negative examples:** Documented approaches that did not work, with analysis of why they failed. Negative examples are as educationally valuable as best practices — arguably more so — and are systematically underrepresented in humanitarian knowledge repositories that prioritize success narratives.

**Technical references:** Standards documents, SOPs, IOM methodological guidance, and external technical references relevant to Venezuelan emergency preparedness, tagged and indexed for rapid retrieval.

**Historical records:** Anonymized, aggregated operational datasets from GENTE variants — displacement flows, registration records, CCCM site histories, AVRR corridor data — preserved for longitudinal analysis and research use.

#### 5.2.3 Database schema

```sql
-- Core knowledge entries table
CREATE TABLE knowledge_entries (
    entry_id          TEXT PRIMARY KEY,           -- GNTR-2026-LL-000001
    entry_type        TEXT NOT NULL CHECK(entry_type IN (
                        'lesson_learned', 'case_study', 'best_practice',
                        'negative_example', 'technical_reference', 'historical_record'
                      )),
    title             TEXT NOT NULL,
    summary           TEXT NOT NULL,              -- 150-word structured summary
    full_content      TEXT NOT NULL,              -- full markdown content
    domain_l1         TEXT,                       -- taxonomy level 1
    domain_l2         TEXT,                       -- taxonomy level 2
    domain_l3         TEXT,                       -- taxonomy level 3
    domain_l4         TEXT,                       -- taxonomy level 4
    source_operation  TEXT,                       -- which GENTE variant generated this
    source_date       DATE,
    author_id         TEXT,                       -- contributor ID (anonymized)
    validation_status TEXT DEFAULT 'pending' CHECK(validation_status IN (
                        'pending', 'reviewed', 'validated', 'superseded'
                      )),
    validated_by      TEXT,
    validated_at      DATETIME,
    applicability     TEXT,                       -- geographic / contextual scope
    related_entries   TEXT,                       -- JSON array of related entry IDs
    search_vector     TEXT,                       -- pre-computed search terms
    access_level      TEXT DEFAULT 'public' CHECK(access_level IN (
                        'public', 'humanitarian_staff', 'restricted'
                      )),
    language          TEXT DEFAULT 'es',          -- es / en / bilingual
    created_at        DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_updated      DATETIME,
    view_count        INTEGER DEFAULT 0,
    usefulness_rating REAL                        -- community rating 0-5
);

-- Version history for living documents
CREATE TABLE knowledge_versions (
    version_id        TEXT PRIMARY KEY,
    entry_id          TEXT REFERENCES knowledge_entries(entry_id),
    version_number    INTEGER NOT NULL,
    content_snapshot  TEXT NOT NULL,
    changed_by        TEXT,
    change_reason     TEXT,
    changed_at        DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Cross-references between entries
CREATE TABLE knowledge_links (
    link_id           TEXT PRIMARY KEY,
    entry_a           TEXT REFERENCES knowledge_entries(entry_id),
    entry_b           TEXT REFERENCES knowledge_entries(entry_id),
    link_type         TEXT CHECK(link_type IN (
                        'contradicts', 'supports', 'updates', 
                        'depends_on', 'see_also', 'derived_from'
                      )),
    link_note         TEXT
);
```

### 5.3 Module 2 — SOP Library and Procedures

The SOP Library maintains the living document repository of standard operating procedures for Venezuelan emergency preparedness and humanitarian response. Unlike static reference documents, the SOPs in this library are version-controlled, feedback-enabled, and continuously updated as field experience accumulates.

#### 5.3.1 SOP categories

```
EMERGENCY RESPONSE SOPs
  ER-001  Community early warning activation protocol
  ER-002  Evacuation coordination — urban barrio context
  ER-003  Search and rescue coordination with local teams
  ER-004  Mass casualty triage — resource-constrained settings
  ER-005  Emergency communications when digital infrastructure fails
  ER-006  First 72 hours coordination — before humanitarian orgs arrive

POPULATION TRACKING SOPs
  PT-001  Community headcount methodology — offline
  PT-002  Displacement site registration — rapid intake
  PT-003  Family tracing and reunification — initial steps
  PT-004  Vulnerable population identification and prioritization

COORDINATION SOPs
  CO-001  Multi-agency coordination in absence of established cluster
  CO-002  Local government liaison — protocol and escalation
  CO-003  Volunteer management and deployment
  CO-004  Information sharing across organizations — data protection

SUPPLY AND LOGISTICS SOPs
  SL-001  Community needs assessment — rapid methodology
  SL-002  Distribution point setup and crowd management
  SL-003  Supply chain workarounds — disrupted environment
  SL-004  Cash assistance delivery — security protocols

PUBLIC HEALTH SOPs
  PH-001  Epidemic early warning — community surveillance
  PH-002  Disease outbreak response — initial containment steps
  PH-003  Water quality monitoring — field methods
  PH-004  Mental health and psychosocial support — first contact

PROTECTION SOPs
  PR-001  GBV indicator identification — community setting
  PR-002  Child protection — unaccompanied minor protocol
  PR-003  Human trafficking indicators — field identification
  PR-004  Sensitive information handling — field conditions
```

#### 5.3.2 SOP structure (standardized)

Every SOP in the library follows a standardized eight-section structure:

```markdown
## SOP [CODE] — [TITLE]
**Version:** X.X  |  **Status:** Active / Under review / Superseded  
**Language:** ES / EN / Bilingual  |  **Last validated:** YYYY-MM-DD

### Purpose
One paragraph describing what this SOP enables and why it exists.

### Scope
Who should use this SOP, in what contexts, and under what conditions it applies.

### Prerequisites
What must be in place before this SOP can be executed.

### Step-by-step procedure
Numbered steps. Each step includes: what to do, how to verify completion,
and what to do if the step cannot be completed as described.

### Decision tree
[Flowchart for key decision points in the procedure]

### Common errors and how to avoid them
Derived from field experience and lessons learned entries.
Each error includes: what it looks like, why it happens, how to prevent it.

### Version history
Date | Version | Change | Changed by | Reason

### Related entries
Links to relevant lessons learned, case studies, and training scenarios.
```

### 5.4 Module 3 — Training Content Engine

The Training Content Engine is the platform component that converts knowledge repository content into training materials. It serves three delivery formats: facilitator-led simulation exercises, self-paced e-learning modules, and structured training curricula for certification programs.

#### 5.4.1 Simulation scenario library

Simulation scenarios are the highest-value training format in emergency preparedness — they require participants to make decisions under realistic conditions, revealing gaps in knowledge and coordination that cannot be identified through lecture-based training alone.

GENTE-R's simulation scenarios are derived directly from real events documented in the knowledge repository. Each scenario is anonymized and generalized sufficiently to protect the privacy of individuals and organizations involved, while retaining the operational realism that makes the scenario educationally valuable.

**Scenario structure:**

```yaml
scenario_id: VEN-SIM-EQ-001
title: "Colapso estructural en barrio urbano — Primeras 6 horas"
category: earthquake_response
duration_minutes: 120
participants: 8-15
difficulty: intermediate
prerequisites:
  - ER-001  # Early warning protocol
  - ER-006  # First 72 hours coordination

context_brief: |
  Son las 14:30 del martes. Un sismo de 6.2 grados acaba de sacudir 
  el área metropolitana de Caracas. [Continues for 300 words of 
  operational context derived from June 2026 earthquake field reports]

injects:
  - time: T+00:00
    type: initial_report
    content: "Reports of structural collapse in Petare sector..."
  - time: T+00:15
    type: information_update
    content: "Communications partially restored. 3 schools affected..."
  - time: T+00:45
    type: complication
    content: "Power grid failure in Miranda state. Medical teams unable..."
  - time: T+01:30
    type: escalation
    content: "Unaccompanied minors identified at improvised shelter..."
  - time: T+02:15
    type: resolution_opportunity
    content: "Municipal civil protection arrives. Coordination required..."

decision_points:
  - dp_01: "How do you prioritize search and rescue versus registration?"
  - dp_02: "A vulnerable individual needs medical attention but no transport..."
  - dp_03: "Conflicting information from two reliable sources about..."

debrief_guide: |
  Key learning points to surface during debrief:
  1. The tension between speed and documentation accuracy in first hours
  2. Communication failure protocols and workarounds
  3. Protection screening under time pressure
  4. Coordination with informal community structures

real_event_reference: GNTR-2026-CS-0047  # case study this is derived from
```

#### 5.4.2 E-learning modules

Self-paced e-learning modules allow knowledge to reach practitioners who cannot attend facilitator-led sessions — community volunteers, healthcare workers in remote areas, local government staff with limited availability for structured training.

Modules are designed to run offline, on low-specification Android devices, in the absence of internet connectivity. This is a non-negotiable design constraint for any training system intended for use in Venezuela's current infrastructure environment.

**Module structure:**
- Estimated duration: 20–45 minutes per module
- Format: Text + images + audio narration (Spanish) + knowledge check questions
- Offline delivery: downloaded once, runs without connectivity
- Progress tracking: local SQLite log, synced to hub when connectivity available
- Completion certificate: generated locally on completion

#### 5.4.3 Certification curricula

GENTE-R defines three certification levels for local emergency preparedness practitioners:

**Level 1 — Community Preparedness Volunteer (CPV)**
Target audience: Community leaders, neighborhood association members, local volunteers  
Duration: 8 hours (can be delivered in two half-day sessions)  
Content: Basic emergency response, family preparedness planning, community early warning, first aid fundamentals, how to access and report to formal response structures  
Assessment: Written and practical assessment, pass threshold 70%  
Certificate validity: 2 years (renewal requires one refresher module)

**Level 2 — Local Emergency Coordinator (LEC)**  
Target audience: Municipal civil protection staff, healthcare workers, teachers, religious leaders with coordination roles  
Duration: 24 hours (three full-day sessions)  
Prerequisites: Level 1 CPV certification or equivalent  
Content: Multi-agency coordination, situation reporting, volunteer management, vulnerable population identification, basic IMS registration, supply chain basics, communications protocols  
Assessment: Written examination + facilitated simulation exercise  
Certificate validity: 3 years (renewal requires updated simulation exercise)

**Level 3 — Emergency Response Specialist (ERS)**  
Target audience: Professional emergency responders, NGO field coordinators, health ministry emergency response staff  
Duration: 80 hours (10-day intensive or equivalent modular delivery)  
Prerequisites: Level 2 LEC certification or demonstrated equivalent experience  
Content: Full GENTE platform ecosystem, advanced coordination methodology, after-action review facilitation, training design and delivery, institutional capacity building, resilience assessment  
Assessment: Portfolio assessment + capstone simulation exercise + peer evaluation  
Certificate validity: 5 years (renewal requires continuing professional development log)

### 5.5 Module 4 — Crisis Intelligence Monitor

The Crisis Intelligence Monitor addresses the information gap that made Venezuela's June 2026 earthquake response more difficult than it needed to be: the absence of a systematic capability to monitor publicly available information signals during emergencies, validate them, and incorporate the relevant ones into the operational picture.

This module is one of the most technically distinctive components of GENTE-R. It applies AI-assisted signal detection to the stream of publicly available information — social media, news sources, official government communications, NGO situation reports — to identify emerging crisis signals, prioritize verified information, suppress misinformation, and surface early warning indicators.

#### 5.5.1 Signal categories monitored

```
EMERGENCY SIGNALS
  Structural collapse reports (location, scale, access status)
  Infrastructure failure (power, water, road, communications)
  Population movement (unexpected displacement, gathering points)
  Security incidents affecting humanitarian access

HEALTH SIGNALS
  Disease outbreak indicators (cluster reports, unusual mortality)
  Healthcare facility capacity alerts
  Water contamination reports
  Nutritional crisis signals

SOCIAL SIGNALS
  Community tension indicators
  Misinformation propagation patterns
  Xenophobia incidents affecting displaced populations
  Social media mobilization (both constructive and harmful)

LOGISTICAL SIGNALS
  Supply chain disruption reports
  Road access changes
  Weather event forecasts with operational implications
  Border crossing status changes
```

#### 5.5.2 Signal validation pipeline

All signals detected by the monitor pass through a four-stage validation pipeline before being incorporated into operational products:

```
STAGE 1 — DETECTION
  Automated monitoring of configured sources
  AI-assisted classification of signal type and relevance
  Confidence scoring based on source reliability and corroboration

STAGE 2 — CROSS-REFERENCING
  Match against known operational data (DTM, CCCM, IMS feeds)
  Geographic validation (does reported location make operational sense?)
  Temporal validation (is this consistent with the event timeline?)

STAGE 3 — HUMAN REVIEW
  All signals above relevance threshold reviewed by analyst
  Confirmation, rejection, or flagging for further investigation
  Integration with operational picture or escalation to coordinator

STAGE 4 — DISSEMINATION
  Validated signals incorporated into situation reports
  Rejected signals logged with rejection reason (learning record)
  High-priority signals trigger immediate coordinator alert
```

The human review gate is absolute. No AI-classified signal reaches an operational product without explicit human confirmation.

### 5.6 Module 5 — Capacity Tracker

The Capacity Tracker maintains a longitudinal record of local emergency preparedness capacity development across Venezuela's affected regions. It answers the question that no current system answers systematically: **is local capacity actually growing over time?**

```sql
-- Local responder registry
CREATE TABLE local_responders (
    responder_id      TEXT PRIMARY KEY,
    responder_type    TEXT CHECK(responder_type IN (
                        'community_volunteer', 'healthcare_worker',
                        'educator', 'local_government', 'ngo_staff',
                        'religious_leader', 'civil_protection'
                      )),
    estado            TEXT,
    municipio         TEXT,
    parroquia         TEXT,
    certification_level TEXT,                    -- CPV / LEC / ERS
    cert_date         DATE,
    cert_expiry       DATE,
    training_hours    INTEGER DEFAULT 0,
    simulations_completed INTEGER DEFAULT 0,
    active_status     TEXT DEFAULT 'active',
    last_contact      DATE
    -- No personally identifying information stored
    -- Responder ID is a pseudonymous local identifier
);

-- Training activity log
CREATE TABLE training_activities (
    activity_id       TEXT PRIMARY KEY,
    activity_type     TEXT,                      -- module / simulation / certification
    content_id        TEXT,                      -- module or scenario ID
    location          TEXT,                      -- estado / municipio
    delivery_date     DATE,
    participants      INTEGER,
    completion_rate   REAL,
    facilitator_id    TEXT,
    assessment_results TEXT,                     -- JSON: pass/fail by participant type
    feedback_summary  TEXT,
    content_gaps_identified TEXT                 -- what participants struggled with
);

-- Coverage map data
CREATE TABLE coverage_assessment (
    assessment_id     TEXT PRIMARY KEY,
    assessment_date   DATE,
    estado            TEXT,
    municipio         TEXT,
    certified_cpv     INTEGER DEFAULT 0,
    certified_lec     INTEGER DEFAULT 0,
    certified_ers     INTEGER DEFAULT 0,
    estimated_population INTEGER,
    coverage_ratio    REAL,                      -- certified responders per 1000 population
    coverage_level    TEXT CHECK(coverage_level IN (
                        'critical_gap', 'insufficient', 'minimal', 
                        'adequate', 'strong'
                      )),
    priority_rank     INTEGER,
    notes             TEXT
);
```

The Coverage Assessment table enables the generation of a **national preparedness coverage map** — a QGIS-rendered visualization showing, at the municipio level, the ratio of trained and certified local responders to population. This map is the primary accountability tool for the GENTE-R program: it makes the preparedness gap visible, trackable, and comparable over time.

---

## 6. Operational Objectives

GENTE-R's operational objectives are organized across three time horizons: immediate (within the first year of deployment), medium-term (years two through five), and long-term (years five through ten and beyond).

### 6.1 Immediate objectives (Year 1)

**Knowledge capture from active operations:**
- Integrate data feeds from GENTE-DTM, CCCM, IMS, and AVRR to begin populating the lessons learned repository
- Conduct structured after-action reviews with IOM field teams from the earthquake response operation
- Document a minimum of 50 validated lessons learned entries from the June 2026 response
- Publish an initial SOP library of 20 priority SOPs covering the most critical gaps identified in the earthquake response

**Initial training deployment:**
- Develop and pilot five simulation scenarios derived from June 2026 earthquake response field data
- Deliver Level 1 Community Preparedness Volunteer training to a minimum of 500 participants across three affected estados
- Establish baseline Capacity Tracker data for all affected municipios

**Crisis Intelligence Monitor baseline:**
- Configure and deploy the signal monitoring pipeline for Venezuelan social media sources and official information channels
- Document the signal detection and validation methodology
- Establish the human review workflow and train the analyst team

### 6.2 Medium-term objectives (Years 2–5)

**Knowledge ecosystem growth:**
- Reach 500+ validated knowledge repository entries across all entry types
- Complete SOP library covering all 28 identified procedure categories
- Establish a peer review process for knowledge validation involving Venezuelan academic institutions and civil society organizations
- Publish the first annual Venezuela Emergency Preparedness State-of-Knowledge report

**Training program scaling:**
- Certify minimum 5,000 Level 1 CPVs across Venezuela's earthquake-affected and high-risk regions
- Certify minimum 500 Level 2 LECs, prioritizing municipios with critical coverage gaps
- Establish Level 3 ERS program in partnership with at least one Venezuelan university
- Develop offline e-learning module library of 30+ modules deliverable on low-specification Android devices

**Institutional integration:**
- Engage a minimum of three Venezuelan national institutions (civil protection, health ministry, education ministry) to formally incorporate GENTE-R training content into their institutional programs
- Support at least two Venezuelan universities in developing emergency preparedness academic content drawing on the knowledge repository
- Establish a community of practice network connecting certified local responders across Venezuela

**Sustainability transition:**
- Transfer primary knowledge repository maintenance to a Venezuelan institutional partner
- Establish a volunteer knowledge contributor network capable of sustaining repository growth independent of IOM operational presence
- Document the platform's full technical architecture and operational methodology sufficiently to enable independent replication

### 6.3 Long-term objectives (Years 5–10+)

**National resilience infrastructure:**
- Achieve adequate or strong preparedness coverage (minimum one certified LEC per 500 population) in all Venezuela's high-risk municipios
- Maintain a living knowledge repository recognized as the primary reference for Venezuelan emergency preparedness by national and regional actors
- Establish GENTE-R as a model for humanitarian resilience knowledge platforms, with at least two replication projects underway in other Latin American contexts

**Knowledge preservation beyond funding:**
- Ensure the knowledge repository is maintained by Venezuelan institutional partners regardless of international humanitarian funding availability
- Establish open-access publication of non-restricted repository content to ensure knowledge cannot be lost through institutional closure or project termination
- Create the conditions under which local communities can develop, update, and share preparedness knowledge independently of external technical support

---

## 7. Long-Term Sustainability

### 7.1 The sustainability challenge

Every humanitarian knowledge platform faces the same fundamental sustainability risk: it is built with project funding, and project funding ends. When the project ends, the platform is either maintained by an institutional successor or it is not maintained at all — in which case the knowledge it contains gradually becomes inaccessible, outdated, and eventually lost.

GENTE-R's architecture is explicitly designed to resist this outcome. Sustainability is not an afterthought — it is a primary design constraint that shapes every major architectural decision.

### 7.2 The open-source foundation

GENTE-R is built entirely on open-source components. No proprietary software licenses are required to run, maintain, or extend the platform. This means that when project funding ends, the platform can continue to operate without ongoing licensing costs — provided that the institutional host has the technical capacity to maintain it.

The technology stack is chosen specifically for its longevity and maintainability:

| Component | Technology | Rationale |
|---|---|---|
| Knowledge database | SQLite / SpatiaLite | Zero-dependency, single-file, no server required |
| AI/LLM inference | Ollama + Llama 3.x | Fully offline, open-weight, runs on consumer hardware |
| Web interface | Python FastAPI + vanilla HTML/CSS | Minimal dependencies, maintainable by a single developer |
| Offline e-learning | Progressive Web App | Runs in any browser, no app store dependency |
| GIS visualization | QGIS | Free, open-source, widely used in humanitarian sector |
| Version control | Git + GitHub | Free hosting for public repositories |

### 7.3 The open knowledge principle

All non-restricted content in the GENTE-R knowledge repository is published under a Creative Commons Attribution license (CC BY 4.0). This means:

- Any Venezuelan institution can freely use, adapt, and build on the repository content
- If IOM's operational presence in Venezuela is reduced or concluded, the knowledge remains accessible
- Venezuelan universities, civil society organizations, and government agencies can incorporate the content into their own programs without requiring ongoing engagement with the GENTE project
- Regional replication in other Latin American contexts is explicitly encouraged and facilitated

### 7.4 The institutional partnership model

Long-term sustainability requires Venezuelan institutional partners who have incorporated GENTE-R's knowledge and training programs into their own institutional DNA — not as users of a service, but as co-owners of a shared knowledge infrastructure.

The target institutional partnerships for GENTE-R are:

**National institutions:** The Ministry of Interior and Justice (civilian protection), the Ministry of Health (epidemiological surveillance and emergency health response), and the Ministry of Education (community preparedness integration into school curricula)

**Academic institutions:** Venezuelan universities with programs in social work, emergency management, public health, or related fields — potential Level 3 ERS certification delivery partners and research collaborators for the knowledge repository

**Civil society organizations:** Venezuelan NGOs and community organizations with established networks in earthquake-affected and disaster-prone areas — primary delivery channels for Level 1 CPV training at community scale

**Municipal governments:** Local government bodies in high-risk municipios — primary institutional hosts for Capacity Tracker data and natural leaders of local preparedness programs

### 7.5 The community knowledge contributor model

The most durable form of knowledge sustainability is community ownership. GENTE-R is designed to enable Venezuelan practitioners — not just IOM staff — to contribute knowledge to the repository.

A structured community contribution program includes:
- Simplified lesson learned submission forms accessible on mobile devices
- A peer review process that validates community contributions without requiring IOM staff involvement
- Recognition and incentive mechanisms for active contributors
- Training on effective knowledge documentation for Level 2 and Level 3 certified practitioners

Over time, as the certified practitioner network grows, the repository becomes increasingly self-sustaining — fed by the experiences of the same local responders who use it.

---

## 8. Training Philosophy

### 8.1 The experiential learning principle

Adult learning research consistently demonstrates that the most effective learning — particularly for high-stakes operational skills — occurs through structured experience rather than passive information transfer. You do not learn to coordinate an emergency response by reading about it. You learn by doing it, making mistakes in a safe environment, receiving feedback, and doing it again.

GENTE-R's training philosophy is built on this principle. Every knowledge entry in the repository is a potential training scenario. Every SOP is a potential practical exercise. Every lesson learned is a reflection prompt that helps practitioners apply past experience to future challenges.

The platform is not a library. It is a learning system.

### 8.2 The contextual calibration principle

Generic humanitarian training materials — developed for global deployment and applicable to any crisis in any context — are less effective than materials calibrated to the specific operational environment where they will be used.

GENTE-R's training content is explicitly calibrated to Venezuela:

- Simulation scenarios are derived from Venezuelan field data, not hypothetical generic emergencies
- SOPs reference Venezuelan administrative geography (estados, municipios, parroquias), local infrastructure, and institutional structures
- Case studies use Venezuelan Spanish, Venezuelan cultural references, and Venezuelan community dynamics
- The AI components of the platform understand Venezuelan Spanish vocabulary, nicknames, regional terminology, and geographic references

This calibration is not a limitation — it is a strength. A Venezuelan community volunteer learning to respond to an earthquake scenario set in Caracas's barrios will transfer that learning more effectively to a real event than one trained on a scenario set in a generic urban environment.

### 8.3 The breadth principle

A persistent debate in emergency preparedness investment concerns the allocation between depth and breadth: is it better to invest in a small number of highly specialized professional responders, or in a large network of community-level volunteers with more basic capabilities?

GENTE-R's position is that this is a false choice. Both are necessary, and they complement rather than substitute for each other.

The certification pyramid (CPV → LEC → ERS) is specifically designed to enable both simultaneously:

- Level 1 CPV training is low-cost, short-duration, and scalable to thousands of community participants
- Level 2 LEC training develops the local coordination capacity that professional responders depend on to be effective
- Level 3 ERS training develops the specialist practitioners who can facilitate the CPV and LEC programs, maintain the knowledge repository, and lead future after-action reviews

The three levels form a self-reinforcing system. CPVs are the community foundation. LECs are the coordination layer. ERSs are the institutional backbone. Each level is more valuable because the others exist.

### 8.4 The generational continuity principle

Emergency preparedness knowledge should not have to be rebuilt from scratch every fifteen to twenty years as one generation of practitioners retires and a new generation enters the field. The knowledge should transfer — through training programs, through documented SOPs, through simulation exercises built on real events, and through the mentorship relationships that the GENTE-R certified practitioner network enables.

The knowledge repository is the institutional memory that bridges generations. The certification program is the transfer mechanism. And the community of practice network — connecting CPVs, LECs, and ERSs across Venezuela's affected regions — is the social infrastructure through which tacit knowledge, which cannot be fully captured in documents, passes from experienced practitioners to newer ones.

---

## 9. Deployment Considerations

### 9.1 Technical requirements

GENTE-R is designed for deployment in Venezuela's constrained infrastructure environment. The base configuration runs on a single laptop with no internet connectivity.

**Minimum system requirements:**

| Component | Minimum | Recommended |
|---|---|---|
| CPU | Intel i5 / AMD Ryzen 5 | Intel i7 / AMD Ryzen 7 |
| RAM | 16 GB | 32 GB |
| Storage | 500 GB SSD | 2 TB NVMe SSD |
| GPU VRAM | 8 GB (Ollama LLM inference) | 16–24 GB |
| OS | Ubuntu 24.04 LTS | Ubuntu 24.04 LTS |
| Power | Laptop battery + UPS | Portable power station (768 Wh+) |
| Connectivity | None required (fully offline) | Optional: local Wi-Fi hotspot |

**Apple Silicon alternative:** A MacBook Pro M3 Pro/Max (36–96 GB unified memory) is an excellent mobile deployment option — runs Llama 3.1 70B on battery, silently, without external power, and with superior energy efficiency for extended field deployment.

### 9.2 Phased deployment model

GENTE-R deployment follows a four-phase model:

**Phase 0 — Foundation (Months 1–3):**
Initial setup, knowledge repository seeding from June 2026 earthquake response data, SOP library v1.0 publication, first five simulation scenarios, Crisis Intelligence Monitor configuration and baseline testing.

**Phase 1 — Pilot (Months 4–6):**
Level 1 CPV training delivered to 200 participants across one affected municipio. Lessons learned from pilot training incorporated into content. Capacity Tracker baseline established. First after-action review conducted using GENTE-R methodology.

**Phase 2 — Scale (Months 7–18):**
Full Level 1 CPV program across affected municipios. Level 2 LEC program launched. Knowledge repository reaches 200+ validated entries. Community contributor program launched. First institutional partnership agreements signed.

**Phase 3 — Sustainability transition (Months 19–36):**
Level 3 ERS program launched with university partner. Primary repository maintenance transferred to Venezuelan institutional partner. Open-access publication of repository content. Community of practice network operational.

### 9.3 Data governance and protection

GENTE-R handles knowledge derived from humanitarian operations — including case studies and historical data that may contain sensitive information about vulnerable individuals and communities. The following data governance principles apply absolutely:

**Anonymization:** No personally identifying information is stored in the knowledge repository. Case studies and historical records are anonymized before entry, removing names, document numbers, specific addresses, and any other information that could identify individuals.

**Aggregation thresholds:** Statistical data derived from GENTE operational variants is reported only at the aggregation level at which individual identification is impossible (minimum municipio level for most indicators).

**Access control:** Repository entries are classified by access level (public / humanitarian staff / restricted). Restricted entries contain information about protection incidents, vulnerability assessments, or community security that should not be publicly accessible.

**Right to removal:** Individuals or communities who have contributed information to the system through GENTE operational variants retain the right to request removal of their information from any knowledge product. This right is documented, tracked, and honored.

### 9.4 Language and accessibility

All GENTE-R training content is produced bilingually (Spanish and English) where resources permit, with Spanish as the primary language. Spanish-only production is acceptable for all community-facing content. English-only is not acceptable for any content intended for Venezuelan community audiences.

E-learning modules include Spanish audio narration to serve participants with limited reading fluency. Visual content is designed to be comprehensible without text for participants with low literacy.

---

## 10. Future Roadmap

### Version 0.1 (Current — proof of concept)
Knowledge repository schema and seed data from June 2026 earthquake response. Five simulation scenarios. Initial SOP library (20 SOPs). Capacity Tracker baseline schema. Crisis Intelligence Monitor configuration framework.

### Version 0.2
RAG (Retrieval-Augmented Generation) implementation connecting Ollama to the knowledge repository — enabling natural language queries of the knowledge base in Spanish. First offline e-learning module. Capacity coverage map in QGIS.

### Version 0.3
Community knowledge contributor portal — web interface for certified practitioners to submit lesson learned entries directly to the repository. Peer review workflow implementation. Level 1 CPV curriculum complete and delivered.

### Version 0.4
Simulation scenario generation assistant — uses the LLM to draft new simulation scenarios from case study entries, reducing the time required to convert field experience into training content. Level 2 LEC curriculum complete.

### Version 0.5
Crisis Intelligence Monitor operational — social media monitoring, signal classification, validation pipeline, and analyst review workflow. Integration with GENTE-DTM, CCCM, IMS, and AVRR data feeds for cross-referencing.

### Version 0.6
Mobile e-learning delivery — Progressive Web App for offline module delivery on Android devices. Progress tracking and certificate generation. Level 3 ERS curriculum complete.

### Version 1.0
Full platform operational. Institutional partnership agreements in place. Community of practice network launched. Open-access repository published. First annual Venezuela Emergency Preparedness State-of-Knowledge report released.

### Version 2.0 (Regional expansion)
Platform adapted for replication in other Latin American humanitarian contexts. Multilingual support extended. Regional knowledge-sharing protocol established with partner organizations.

---

## 11. Repository Structure

```
gente_R/
├── README.md                          ← this document
├── docs/
│   ├── architecture.md                ← technical system design
│   ├── deployment.md                  ← setup and deployment guide
│   ├── data-governance.md             ← data protection framework
│   ├── training-philosophy.md         ← extended training methodology
│   └── sustainability-model.md        ← institutional sustainability framework
├── knowledge/
│   ├── schema/
│   │   └── knowledge_db.sql           ← complete database schema
│   ├── seed/
│   │   ├── lessons_learned_2026.json  ← June 2026 earthquake seed entries
│   │   └── best_practices_latam.json  ← regional best practices
│   └── taxonomy/
│       └── classification_system.yml  ← full taxonomy definition
├── sop_library/
│   ├── emergency_response/            ← ER-001 through ER-006
│   ├── population_tracking/           ← PT-001 through PT-004
│   ├── coordination/                  ← CO-001 through CO-004
│   ├── supply_logistics/              ← SL-001 through SL-004
│   ├── public_health/                 ← PH-001 through PH-004
│   └── protection/                    ← PR-001 through PR-004
├── training/
│   ├── simulations/
│   │   ├── scenarios/                 ← YAML scenario files
│   │   ├── facilitator_guides/        ← PDF facilitator guides
│   │   └── debrief_templates/         ← structured debrief forms
│   ├── curricula/
│   │   ├── CPV_level1/                ← Community Preparedness Volunteer
│   │   ├── LEC_level2/                ← Local Emergency Coordinator
│   │   └── ERS_level3/                ← Emergency Response Specialist
│   └── elearning/
│       └── modules/                   ← offline PWA module packages
├── pipeline/
│   ├── knowledge_ingester.py          ← ETL from GENTE variants to repository
│   ├── rag_engine.py                  ← Ollama RAG interface
│   ├── signal_monitor.py              ← Crisis Intelligence Monitor
│   ├── capacity_tracker.py            ← local responder tracking
│   ├── coverage_mapper.py             ← QGIS coverage map generator
│   └── report_generator.py            ← annual state-of-knowledge report
├── web/
│   ├── app.py                         ← FastAPI web interface
│   ├── templates/                     ← HTML templates
│   └── static/                        ← CSS, JS, assets
├── qgis/
│   └── resilience_coverage.qgz        ← QGIS project: capacity coverage map
├── docker/
│   └── docker-compose.yml             ← full stack deployment
└── tests/
    ├── test_knowledge_schema.py
    ├── test_rag_engine.py
    └── test_signal_monitor.py
```

---

## 12. About the Project

### The fifth variant

GENTE-R is the fifth and final variant of the GENTE humanitarian platform ecosystem. Unlike its four operational siblings — DTM, CCCM, IMS, and AVRR — it does not collect data, manage sites, register individuals, or facilitate returns. It does something harder and more durable: it ensures that everything learned from doing those things is preserved, structured, transferred, and continuously built upon.

The four operational variants answer the question *what is happening now and what do we need to do about it?* GENTE-R answers a different question: *what did we learn, and how do we make sure we never have to learn it again from scratch?*

### The collective vision

Together, the five GENTE variants form a unified humanitarian preparedness ecosystem centered on a single principle: **The Human Geography of Venezuelan Displacement**.

Their collective purpose extends beyond emergency response. It is to transform humanitarian experience into preparedness, preparedness into knowledge, and knowledge into resilience. If international assistance is eventually reduced, preparedness must remain. If humanitarian operations conclude, institutional learning must continue. If future emergencies arise — as history strongly suggests they will — local communities should never be required to begin again from zero.

That is the most valuable long-term outcome the GENTE platform could aspire to achieve.

### About the author

**Andres Gabriel Hermoso Castillo**  
Systems Analyst | DataOps Architect | IM Specialist  
Caracas, Bolivarian Republic of Venezuela  
andres.hermoso@gmail.com | +58 412 701 0980

GENTE-R was conceived in July 2026 in Caracas, as the global humanitarian funding landscape was entering a period of unprecedented competition for limited resources, and as Venezuela was simultaneously facing the acute impact of the June 2026 earthquake and the chronic fragility that preceded it. The platform represents a conviction that has guided my professional work for thirty years: that the most important information system is the one that makes the next crisis easier to navigate than the last one. Every lesson captured, every SOP written, every simulation delivered, and every local responder certified is a contribution toward a Venezuela that does not have to start from zero when the next emergency arrives.

---

## License

MIT License — free to use, adapt, and deploy.  
Knowledge repository content: Creative Commons Attribution 4.0 (CC BY 4.0).  
Training materials: Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0).

Regional replication is explicitly encouraged. If you are adapting GENTE-R for another Latin American or global humanitarian context, please open an issue or reach out directly. Every replication teaches something that makes the original better.

---

*"La memoria humanitaria no debería evaporarse cuando termina la financiación. Debería crecer."*  
*(Humanitarian memory should not evaporate when funding ends. It should grow.)*

---

*Built in Caracas, July 2026. For the gente — today, and for the next emergency.*
