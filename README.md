<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />

# GENTE

### Geospatial Emergency Network Training Engine
#### *Inteligencia al servicio de nuestra gente*

> **Proof-of-concept humanitarian information management and training platform designed for offline deployment in disaster-affected areas with degraded or absent internet connectivity.**  
> Built in response to the **June 24, 2026 Venezuela earthquake** — and designed to serve Venezuela's long-term emergency preparedness needs for decades beyond it.

---

## Vision

More than 7.9 million Venezuelans have been displaced worldwide as a result of prolonged socioeconomic and geopolitical crises — approximately one in four Venezuelans has left the country. The June 2026 earthquake did not create Venezuela's displacement challenge. It accelerated and intensified a dynamic that has been building for over a decade, driven by climate-related disasters, regional instability, and deep socioeconomic inequality.

GENTE was designed with that reality in mind. Its immediate purpose is to close the information management gap that opens when disasters strike and cloud-based tools fail. Its long-term purpose is more ambitious: to become the foundation of a national emergency preparedness knowledge platform that captures lessons learned from every response operation, converts field experience into training and simulation content, monitors publicly available information during crises, and builds the kind of institutional knowledge that makes Venezuelan communities more resilient — year over year, response after response.

> *GENTE is not merely a technology project. It is a long-term humanitarian initiative focused on preparedness, institutional learning, and the human geography of Venezuelan displacement.*

See [`venezuela/README.md`](venezuela/README.md) for the full humanitarian context
and [`GENTE-Resilience/README.md`](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_R) for the long-term vision.

---

## The problem this solves — now

When a major earthquake strikes Venezuela, three things collapse almost simultaneously: buildings, communications infrastructure, and the cloud-based tools that humanitarian coordinators depend on. Standard IM workflows — submitting KoboToolbox forms to cloud servers, running Power BI dashboards from the internet, coordinating via WhatsApp — become unavailable precisely when they are needed most.

Field teams are left with paper forms, verbal radio reports, and maps printed before the disaster. By the time data reaches a coordination hub, the operational picture is hours or days old.

**GENTE is designed to work in that gap.** It runs entirely on a local laptop or ruggedized field server — no internet required, no cloud dependency, no data ever leaving the physical device. Field workers collect damage assessments on their phones via KoboCollect (offline), sync to a local server over a Wi-Fi hotspot, and within seconds an AI model categorizes the report and places a color-coded point on a QGIS operational map.

---

## Architecture overview

```
┌─────────────────────────────────────────────────────────────────┐
│  FIELD (no connectivity required)                               │
│                                                                 │
│  📱 KoboCollect (phone) ──► local Wi-Fi ──► KoboToolbox         │
│                                              (Docker, offline)  │
└──────────────────────────────┬──────────────────────────────────┘
                               │ JSON via local API
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│  COMMAND HUB (local machine)                                    │
│                                                                 │
│  kobo_to_qgis.py                                                │
│  │                                                              │
│  ├── ai_triage.py ──► Ollama (llama3.1:8b, local)               │
│  │   Extract: severity / infrastructure / medical_need          │
│  │   Input language: Venezuelan Spanish                         │
│  │   Output: structured JSON                                    │
│  │                                                              │
│  └── db_writer.py ──► SpatiaLite database                       │
│                         disaster_triage.sqlite                  │
│                               │                                 │
│                               ▼                                 │
│                        QGIS (offline)                           │
│                        Operational map                          │
│                        Color-coded by severity                  │
└─────────────────────────────────────────────────────────────────┘
```

See [`architecture/README.md`](architecture/README.md) for a detailed component breakdown and design rationale.

---

## Core capabilities

| Capability | Description |
|---|---|
| **Offline-first** | Zero internet dependency. All components run on a single local machine or small LAN. |
| **AI-assisted classification** | Local LLM (Llama 3.1 8B via Ollama) classifies Spanish-language field reports into structured JSON: severity, infrastructure status, medical need. |
| **GIS integration** | Output writes directly to a SpatiaLite database monitored by QGIS — map updates in near-real time as reports arrive. |
| **KoboToolbox-native** | Uses KoboCollect (standard humanitarian data collection) — field workers need no retraining. |
| **Data sovereignty** | Zero bytes leave the device. All sensitive population data stays strictly within the physical hardware, complying with humanitarian Do No Harm principles. |
| **Bilingual** | Field notes ingested in Venezuelan Spanish; AI output and QGIS attributes in English or Spanish as configured. |
| **Venezuela-aware** | Prompt templates reference Venezuelan administrative divisions (estados, municipios), local landmarks, and regional Spanish terminology. |
| **Training-ready** | Field data and after-action observations feed into a growing knowledge base for scenario-based training and SOP development. |

---

## Alignment with IOM DTM and CCCM workflows

GENTE was designed with IOM's Displacement Tracking Matrix (DTM) and Camp Coordination and Camp Management (CCCM) information needs explicitly in mind:

- **DTM site assessments** → the earthquake damage survey XLSForm maps directly to DTM site condition indicators (shelter integrity, access, population count, priority needs)
- **CCCM master site lists** → the SpatiaLite database schema includes site ID, coordinates, population estimate, and service availability fields compatible with CCCM reporting templates
- **Situation reports** → QGIS layouts pre-configured to export PDF factsheets and PNG maps for operational coordination meetings
- **IMS Registration** → the database schema supports individual/household record linkage for future integration with IOM's IMS registration platform
- **Capacity building** → the Training component converts field observations into reusable SOPs, simulation exercises, and training curricula for local responders

---

## Quickstart (5 commands)

**Prerequisites:** Docker, Docker Compose, Python 3.11+, QGIS 3.x, Ollama

```bash
# 1. Clone the repository
git clone https://github.com/andreshermoso/gente.git
cd gente

# 2. Start the offline KoboToolbox + Ollama stack
docker-compose -f docker/docker-compose.yml up -d

# 3. Install Python dependencies
pip install -r requirements.txt

# 4. Pull the local AI model (one-time, ~5GB)
ollama pull llama3.1:8b

# 5. Run the pipeline against sample data
python pipeline/kobo_to_qgis.py --sample
```

Open `qgis/gente_project.qgz` in QGIS. The `triage_points` layer will populate with color-coded points from the sample data run.

---

## Sample output

**Input** (Spanish field note from KoboCollect form):

```
"Colapso parcial del puente sobre el Río Guaire. Paso interrumpido para 
vehículos de rescate. 2 heridos reportados en el sector Las Mercedes."
```

**AI classification output** (Ollama → structured JSON):

```json
{
  "severity_level": "High",
  "infrastructure_status": "Collapsed",
  "medical_need": true,
  "blocked_access": true,
  "location_reference": "Las Mercedes, Río Guaire",
  "confidence": 0.94
}
```

**QGIS result:** A red high-severity point appears at the reported coordinates, with popup attributes matching the JSON fields. The map updates within 3 seconds of form submission.

---

## Role-specific repositories

This repository is the canonical GENTE platform. Three focused variants have been prepared for specific IOM operational contexts — each shares the same core architecture while emphasizing the competencies and workflows most relevant to each role:

| Repo | IOM Role | Focus |
|---|---|---|
| [gente_DTM](https://github.com/andreshermoso/gente/tree/main/gente_DTM) | Senior IM Associate — DTM (22086) | Displacement tracking, field team supervision, population mobility, geospatial analysis |
| [gente_CCCM](https://github.com/andreshermoso/gente/tree/main/gente_CCCM) | Senior IM Associate — CCCM (22097) | Master site list management, inter-cluster coordination, collective centre monitoring |
| [gente_IMS](https://github.com/andreshermoso/gente/tree/main/gente_IMS) | Senior IMS Registration Associate (22094) | Individual registration, household deduplication, SQL schema design, system configuration |
| [gente_AVRR](https://github.com/andreshermoso/gente_IOM_docs/tree/main/gente_AVRR) | Assistant  — AVRR (22252) | Onsite documentation verification, migrant case tracking, reception assistance workflows, and migration corridor monitoring |

---

## Venezuela operational context

The June 24, 2026 earthquake caused widespread building collapse, road damage, and infrastructure failure across multiple Venezuelan states. The operational environment presents specific information management challenges:

- **Administración de carga** (rolling blackouts): power grids are unreliable, making sustained cloud connectivity impossible
- **Bandwidth degradation**: when internet does exist, it is frequently too slow for cloud GIS or data platform use
- **Data sensitivity**: population displacement data involving vulnerable groups requires strict local data governance
- **Language and geography**: field reports use Venezuelan Spanish, regional landmark references, and local administrative vocabulary not reliably handled by generic AI prompts
- **Pre-existing displacement**: the earthquake intersected with existing internal migration corridors shaped by years of socioeconomic crisis, complicating population tracking

See [`venezuela/README.md`](venezuela/README.md) for the full operational context, IM gap analysis, and long-term platform vision.

---

## Technology stack

| Component | Technology | Rationale |
|---|---|---|
| Data collection | KoboCollect + KoboToolbox (Docker) | UN/NGO humanitarian standard; works fully offline |
| AI classification | Ollama + Llama 3.1 8B (Q4_K_M) | Runs on consumer GPU (8GB VRAM); ~3s latency per report |
| Spatial database | SpatiaLite (SQLite + spatial extension) | Zero-dependency, single-file, natively QGIS-compatible |
| GIS platform | QGIS 3.x | Free, open-source; ArcGIS-comparable for field operations |
| Containerization | Docker Compose | Reproducible offline stack, one-command deployment |
| Language | Python 3.11 | Standard in humanitarian data tooling ecosystem |

---

## Hardware requirements

| Component | Minimum | Recommended |
|---|---|---|
| RAM | 16 GB | 32–64 GB |
| GPU VRAM | 8 GB (RTX 4060) | 16–24 GB (RTX 4090) |
| Storage | 500 GB NVMe SSD | 2 TB NVMe SSD (offline map tiles) |
| Power | Standard | Portable power station (EcoFlow/Jackery) for blackout continuity |

> **Apple Silicon alternative:** A MacBook Pro M-series with 64GB unified memory runs larger models (34B+) completely offline on battery — highly recommended for mobile field command hubs.

---

## Limitations and scope

GENTE is a **proof-of-concept** demonstrating the architectural approach for offline humanitarian IM and the long-term vision for a national preparedness knowledge platform. It is not production software and would require institutional vetting, security review, and field testing before operational deployment. Current limitations:

- AI model outputs require human QA review before operational use (hallucination risk on edge cases)
- KoboToolbox Docker deployment requires initial internet connectivity to pull container images (one-time setup before field deployment)
- QGIS project pre-loaded with publicly available Venezuela base layers only; satellite imagery tiles require separate offline download
- No multi-hub data synchronization (planned for v0.3: USB/SSD-based sneakernet sync protocol)
- The national training knowledge platform described in the vision is roadmap, not current implementation

---

## Roadmap

**v0.1 (current):** Core offline pipeline — KoboToolbox → Ollama → SpatiaLite → QGIS  
**v0.2:** Bilingual QGIS print layout templates (factsheet + situation report PDF)  
**v0.3:** Multi-hub sneakernet sync via encrypted SSD transfer protocol  
**v0.4:** Vision-Language Model integration for drone imagery damage classification  
**v0.5:** RAG-based knowledge base — after-action reviews → searchable lessons learned  
**v0.6:** Social media monitoring pipeline — crisis signal extraction and validation  
**v0.7:** Simulation content generator — field incidents → training scenarios and SOPs  
**v1.0:** Gentoo Linux optimized deployment image for ruggedized hardware

---

## About the author

**Andres Gabriel Hermoso Castillo**  
Systems Analyst | DataOps Architect | IM Specialist  
Caracas, Bolivarian Republic of Venezuela  
andres.hermoso@gmail.com | +58 412 701 0980  
[LinkedIn](https://linkedin.com)

30 years of experience in data architecture, ETL pipeline design, BI, and enterprise systems integration across Venezuela, the Caribbean, and Latin America. GENTE was conceived and developed in July 2026 in response to the Venezuela earthquake, as a contribution to improving information management capacity in the affected region and to the longer-term goal of building Venezuela's national emergency preparedness ecosystem.

---

## Acknowledgements

This project draws on the humanitarian technology ecosystem built by the following open-source communities and organizations:

- [KoboToolbox](https://www.kobotoolbox.org/) — the standard for humanitarian data collection
- [Ollama](https://ollama.com/) — local LLM inference
- [QGIS](https://qgis.org/) — open-source GIS platform
- [Meta / Llama](https://llama.meta.com/) — Llama 3.1 open-weight model
- [IOM DTM](https://dtm.iom.int/) — for publicly available DTM methodology documentation
- [UNHCR / R4V](https://www.r4v.info) — Venezuela displacement data and coordination framework

---

## License

MIT License — free to use, adapt, and deploy in humanitarian contexts.  
If you adapt GENTE for another disaster response context, please open a PR or reach out. Every deployment teaches something that makes the next one better.

---

*"La información salva vidas — pero solo si llega a tiempo."*  
*(Information saves lives — but only if it arrives in time.)*
