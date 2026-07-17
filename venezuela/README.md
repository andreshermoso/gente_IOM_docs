# GENTE — Venezuela Operational Context

> *This document explains the humanitarian situation that GENTE was designed to address, the specific information management challenges of disaster response in Venezuela, and the gap between standard humanitarian IM tooling and what the field reality demands.*

---

## The June 24, 2026 earthquake

On June 24, 2026, a major earthquake struck Venezuela, causing widespread structural collapse, infrastructure failure, and population displacement across multiple states. The event triggered one of the most complex humanitarian emergencies in Venezuela's recent history — compounding an already severe socioeconomic crisis that had been unfolding for over a decade.

The earthquake's immediate effects included:

- Collapse and severe damage of residential structures, particularly informal housing in hillside communities (*barrios*) whose construction lacks seismic standards
- Disruption of road networks, cutting off communities from emergency services and supply chains
- Failure of water distribution systems in affected municipios
- Power grid damage extending and intensifying an already chronic pattern of rolling blackouts (*administración de carga*)
- Displacement of populations — some into spontaneous collective centres (schools, churches, public buildings), others into host family arrangements or open spaces
- Disruption of telecommunications infrastructure at exactly the moment when coordination most requires it

The International Organization for Migration (IOM) activated emergency response operations through its Venezuela Country Office in Caracas, deploying Displacement Tracking Matrix (DTM) assessments, Camp Coordination and Camp Management (CCCM) support, and Information Management (IM) resources to map the displacement situation and support the humanitarian coordination architecture.

---

## Venezuela's baseline: a compounded emergency

Understanding why standard humanitarian IM tools fail in Venezuela requires understanding the baseline conditions into which the earthquake struck.

### Chronic infrastructure fragility

Venezuela's electrical grid has operated under severe strain for years. Rolling blackouts — locally known as *administración de carga* — affect most of the country on a rotating schedule, with some states experiencing 12–20 hours of power cuts per day even before the earthquake. The earthquake worsened this significantly in affected areas.

For humanitarian IM operations, this means:

- Laptop batteries are the primary power source, not the backup
- Any system requiring continuous connectivity to a cloud server will fail unpredictably and repeatedly
- Charging cycles for field phones and equipment must be planned around power availability windows
- Portable power stations are not a convenience — they are operational infrastructure

### Degraded internet connectivity

Where internet exists in Venezuela, it is characterised by:

- Low bandwidth (1–5 Mbps is common in non-urban areas)
- High latency and packet loss
- Frequent disconnections
- Cost and availability constraints for SIM data in affected communities

Cloud-based humanitarian tools — Power BI dashboards, cloud KoboToolbox instances, Google Drive collaboration, online situation report templates — degrade or fail entirely under these conditions. The earthquake made this worse in affected zones.

### Data sovereignty and protection concerns

Venezuela presents a specific data protection context that humanitarian organizations must navigate carefully. Collecting and transmitting data about displaced persons, vulnerability profiles, and community locations through cloud infrastructure raises Do No Harm concerns that are amplified by:

- The sensitivity of displacement information for affected populations
- The importance of keeping beneficiary data within organizationally controlled systems
- The requirement to comply with IOM's data protection principles even when operating under emergency conditions

An offline-first architecture is not only operationally pragmatic in Venezuela — it is the responsible approach to humanitarian data governance in this context.

### The human geography of Venezuelan displacement

Venezuela's displacement patterns in earthquake-affected areas reflect the country's particular social geography:

**Barrios and informal settlements** occupy steep hillsides in and around major cities. Built informally over decades, often without seismic engineering, these communities suffer disproportionate structural damage in earthquakes. They are also the hardest to access for emergency services and the last to appear on formal administrative maps. Field enumerators working these areas operate on foot, with intermittent phone signal, collecting data on paper or offline forms.

**Dispersed displacement** is a distinctive feature of the Venezuelan context. Unlike camp-based displacement common in other humanitarian emergencies, Venezuelan affected populations tend to move to relatives, friends, or community spaces — dispersed across urban and peri-urban areas in ways that make standard camp-based CCCM tools less applicable. Tracking this population requires flexible, community-level data collection rather than site-by-site enumeration.

**Internal migration corridors** were already active before the earthquake, with significant population movement between states driven by economic conditions. The earthquake intersected with and disrupted these mobility patterns, making population tracking more complex.

**Language and local knowledge** matter enormously for data quality. Venezuelan Spanish has a rich vocabulary of regional place names, local landmarks, and colloquial terms for infrastructure and community spaces that generic AI prompts and international humanitarian tools do not capture. A field note referencing *"el sector de los bloques cerca del terminal"* requires understanding of Venezuelan urban geography to geocode correctly.

---

## The information management gap

### What standard IM workflows assume

Standard humanitarian IM workflows — as documented in IOM DTM methodology, CCCM IM guidance, and OCHA coordination frameworks — assume:

1. Field enumerators can submit forms to a cloud server in near-real time
2. IM coordinators can access online dashboards and mapping platforms
3. Situation reports can be produced using cloud-connected templates
4. Data flows from field to hub to coordination platform with manageable latency

These assumptions hold in many humanitarian contexts. They do not hold in Venezuela after the June 2026 earthquake.

### What actually happens without an offline solution

Without an offline IM platform, the field reality in Venezuela becomes:

| Step | Standard assumption | Venezuela earthquake reality |
|---|---|---|
| Form submission | KoboCollect syncs to cloud within seconds | Sync fails; data queues on phone for hours or days |
| Data aggregation | IM coordinator pulls from cloud dashboard | No dashboard available; coordinator waits for USB transfers or WhatsApp messages |
| Triage and prioritization | Automated field flags in cloud platform | Manual review of WhatsApp messages and voice notes |
| Situation report | Cloud template with live data | Manual copy-paste from spreadsheets, hours after data collection |
| Map update | Dashboard auto-refreshes | Coordinator manually updates a shared Google MyMaps — when internet allows |
| Coordination meeting | Live map briefing from dashboard | Printed map from two days ago |

The result is an operational picture that is always hours or days old, assembled manually from fragmented sources, by an IM coordinator whose time is consumed by data wrangling rather than analysis.

**This is the gap GENTE is designed to close.**

### What changes with GENTE

With GENTE deployed at a field coordination hub:

| Step | With GENTE |
|---|---|
| Form submission | KoboCollect syncs to local server over Wi-Fi hotspot — no internet needed |
| Data aggregation | Pipeline runs continuously; new submissions processed within 60 seconds |
| Triage and prioritization | Local AI classifies severity, medical need, and access constraints in Spanish |
| Situation report | QGIS print layout exports PDF in under 2 minutes |
| Map update | Triage points appear on operational map within 10 seconds of confirmation |
| Coordination meeting | Live QGIS map on projector or screen; current as of last confirmed submission |

The IM coordinator's time shifts from data assembly to data analysis — from answering "what information do we have" to answering "what does this information tell us and what should we do."

---

## IOM's role in Venezuela and the DTM mandate

IOM has operated in Venezuela for decades, supporting migration management, protection, and humanitarian response. In the context of the June 2026 earthquake response, IOM's mandate includes:

**Displacement Tracking Matrix (DTM):** Systematic collection, processing, and dissemination of information on the location, number, and needs of displaced populations. In the earthquake response, DTM assessments document displacement sites, population estimates, and priority needs at the community and site level.

**Camp Coordination and Camp Management (CCCM):** In the Venezuelan context, this extends to collective centres (schools, churches, sports facilities) hosting displaced families. CCCM IM responsibilities include maintaining a master list of sites, coordinating service delivery information across clusters, and ensuring that no site falls through the information gap.

**Information Management support to the humanitarian coordination system:** IOM's IM unit produces the maps, dashboards, factsheets, and situation reports that enable the entire humanitarian coordination architecture — UN agencies, NGOs, government counterparts, and donors — to make decisions based on a shared operational picture.

GENTE was designed to support all three of these functions under the specific connectivity and infrastructure constraints of the Venezuela earthquake response.

---

## Alignment with IOM IM standards and methodology

GENTE was built with explicit reference to IOM's published DTM methodology and CCCM IM guidance:

**Data collection:** The `damage_survey_es.xlsx` XLSForm mirrors the DTM site assessment form structure, covering shelter integrity, population count, displacement reason, and priority needs — adapted for rapid earthquake damage assessment in Venezuelan administrative geography.

**Data quality:** The `needs_review` gate in the pipeline — requiring human confirmation of every AI triage output before it reaches the operational map — reflects IOM's principle that automated processing is a tool to support, not replace, IM coordinator judgment.

**Reporting outputs:** The QGIS print layouts are designed to match the visual style and content requirements of IOM situation reports and DTM factsheets: north arrow, scale bar, legend, date stamp, source attribution, and a data limitations note.

**Terminology:** Field forms and AI prompts use IOM standard terminology (*estado de necesidad*, *desplazamiento interno*, *albergue colectivo*, *punto de tránsito*) rather than generic humanitarian vocabulary, reducing the translation burden on Venezuelan field staff.

**Coordination:** The SpatiaLite schema includes a `sites` table compatible with the CCCM master site list format used in IOM coordination platforms, enabling future data migration to institutional systems when connectivity is restored.

---

## The people GENTE is designed to serve

Behind every data point in a GENTE triage map is a family in a collapsed building, a pregnant woman on a blocked road, a community cut off from water, an elderly person who cannot walk to a distribution point.

Venezuelan communities affected by the 2026 earthquake are not abstract data sources. They are people navigating the intersection of a sudden disaster and a decade of accumulated hardship — people who have already shown extraordinary resilience in the face of economic collapse, migration, and chronic infrastructure failure.

The information system exists to serve them. Every minute that the IM coordinator spends wrestling with a spreadsheet instead of analyzing field data is a minute in which a critical need goes unmet, a resource goes to the wrong location, or a family falls through the coordination gap.

GENTE's entire architecture — offline-first, Spanish-language AI, fast triage, human-confirmed map — is oriented toward one outcome: getting the right information to the right person in time to make a difference.

---

## Limitations and what this project does not claim

GENTE is a proof-of-concept. It demonstrates an architectural approach and a depth of operational understanding. It is not:

- A production-ready system validated for deployment without institutional review
- A replacement for IOM's institutional IM platforms and methodology
- A tool that eliminates the need for trained, experienced IM coordinators
- A solution to the underlying infrastructure challenges of the Venezuelan context

What it does claim is this: the architectural approach is sound, the technology stack is proven, the operational problem is real, and the gap between current IM practice and what is possible with offline AI-powered tooling is large enough to matter.

The people of Venezuela deserve better information systems in their worst moments. GENTE is one person's contribution toward that — offered openly, built honestly, and designed to be improved by anyone who picks it up.

---

## References and acknowledgements

**IOM DTM methodology:** [dtm.iom.int](https://dtm.iom.int)  
**KoboToolbox humanitarian standard:** [kobotoolbox.org](https://www.kobotoolbox.org)  
**OCHA CCCM cluster guidance:** [cccmcluster.org](https://cccmcluster.org)  
**Venezuela administrative boundaries:** [geoBoundaries project](https://www.geoboundaries.org)  
**OpenStreetMap Venezuela:** [download.geofabrik.de](https://download.geofabrik.de/south-america/venezuela.html)  
**Llama 3.1 model:** [llama.meta.com](https://llama.meta.com)  
**Ollama:** [ollama.com](https://ollama.com)  
**QGIS:** [qgis.org](https://qgis.org)

---

*Built in Caracas, July 2026.*  
*For the gente.*
