<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>


# GENTE — Deployment Guide

> *This guide covers everything needed to prepare, install, and operate a GENTE command hub for offline field deployment in a disaster-affected area. It assumes a technically competent operator (field IM coordinator or IT support) performing the setup before deployment, ideally with internet access during the preparation phase.*

---

## Two phases of deployment

```
PHASE 1 — PREPARATION (internet required, done before going to the field)
─────────────────────────────────────────────────────────────────────────
Install OS → Pull Docker images → Pull Ollama model → Download map tiles
→ Load KoboToolbox form → Test full pipeline → Pack hardware

PHASE 2 — FIELD OPERATION (zero internet required)
─────────────────────────────────────────────────────────────────────────
Power on hub → Start Docker stack → Broadcast Wi-Fi → Collect forms
→ Run pipeline → Review triage → Export situation reports
```

All software downloads, container images, and map tiles must be completed in Phase 1. Once in the field, GENTE runs indefinitely without connectivity.

---

## Hardware checklist

### Recommended command hub configuration

| Component | Specification | Notes |
|---|---|---|
| **Laptop** | Any modern laptop with dedicated GPU | See GPU options below |
| **GPU** | NVIDIA RTX 4060 8GB (minimum) | Required for Ollama GPU inference |
| **RAM** | 32 GB DDR5 | 16 GB minimum; 32 GB recommended |
| **Storage** | 1 TB NVMe SSD | OS + Docker images + map tiles + database |
| **OS** | Ubuntu 24.04 LTS or Windows 11 + WSL2 | Linux preferred; WSL2 tested |
| **Battery** | EcoFlow River 2 Pro (768 Wh) or equivalent | 4–6 hours full operation per charge |
| **Wi-Fi** | Built-in Wi-Fi (hotspot mode) or USB adapter | Must support Access Point mode |
| **USB drives** | 2× 256 GB USB-A drives | Daily database backup + sneakernet sync |

### Apple Silicon alternative (recommended for mobility)

A MacBook Pro M3 Pro or M3 Max with 36–96 GB unified memory runs Llama 3.1 8B entirely on-device with no discrete GPU, on battery, silently, with no external power station needed for several hours. The unified memory architecture means larger models (34B, 70B) are viable for high-priority triage tasks.

```
MacBook Pro M3 Max (48 GB unified memory)
├── Ollama: llama3.1:70b-instruct-q4_K_M  ← excellent Spanish quality
├── KoboToolbox: Docker Desktop for Mac
├── QGIS: native ARM build
└── Battery life: ~6 hours under full load
```

### Field phone requirements (enumerators)

- Android 8.0+ or iOS 14+ with KoboCollect installed
- Minimum 2 GB free storage (form submissions accumulate offline)
- GPS enabled (required for coordinate capture)
- Power bank (10,000 mAh minimum) per enumerator

---

## Phase 1: Preparation (with internet)

### Step 1 — Install Docker and Docker Compose

**Ubuntu / Debian:**
```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo usermod -aG docker $USER
newgrp docker
```

**Windows (WSL2):**
Install Docker Desktop for Windows, enable WSL2 backend, then continue all steps inside your WSL2 Ubuntu terminal.

**macOS:**
Install Docker Desktop for Mac (Apple Silicon or Intel build as appropriate).

---

### Step 2 — Install NVIDIA drivers and CUDA (Linux GPU systems only)

```bash
# Check current driver
nvidia-smi

# If not installed:
sudo apt install -y nvidia-driver-535 nvidia-cuda-toolkit
sudo reboot

# Verify after reboot
nvidia-smi
# Should show GPU name, driver version, CUDA version
```

Install the NVIDIA Container Toolkit so Docker containers can access the GPU:
```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | \
  sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt update && sudo apt install -y nvidia-container-toolkit
sudo systemctl restart docker
```

---

### Step 3 — Clone GENTE and configure

```bash
git clone https://github.com/andreshermoso/gente.git
cd gente
cp config/config.example.py pipeline/config.py
```

Edit `pipeline/config.py` to set your local paths and preferences:
```python
# pipeline/config.py
KOBO_API_URL      = "http://localhost:8000"   # local KoboToolbox
KOBO_TOKEN        = "your-local-kobo-api-token"
OLLAMA_URL        = "http://localhost:11434"
OLLAMA_MODEL      = "llama3.1:8b"             # or llama3.1:70b on Apple Silicon
SPATIALITE_DB     = "/data/gente/disaster_triage.sqlite"
POLL_INTERVAL_SEC = 60
BACKUP_PATH       = "/media/usb_backup/"
LOG_LEVEL         = "INFO"
```

---

### Step 4 — Pull the KoboToolbox Docker stack

```bash
cd docker/
docker compose pull
# This downloads ~3–4 GB of container images
# Estimated time: 10–20 minutes on a standard connection
```

Verify all images are present:
```bash
docker images | grep kobo
# Should list: kobocat, kpi, koboform, postgres, mongo, redis, nginx
```

---

### Step 5 — Install Ollama and pull the AI model

```bash
# Install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Pull Llama 3.1 8B (quantized — ~5 GB download)
ollama pull llama3.1:8b

# Optional: pull larger model for Apple Silicon / high-RAM systems
ollama pull llama3.1:70b-instruct-q4_K_M   # ~42 GB

# Verify
ollama list
# Should show: llama3.1:8b   ID   5.0 GB   ...
```

---

### Step 6 — Install Python dependencies

```bash
cd gente/
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

`requirements.txt` includes:
```
requests==2.31.0        # KoboToolbox API calls
pyspatialite==0.1.1     # SpatiaLite database interface
click==8.1.7            # CLI for pipeline script
python-dotenv==1.0.0    # Environment variable management
schedule==1.2.1         # Polling scheduler
pytest==7.4.3           # Test runner
```

---

### Step 7 — Install QGIS

**Ubuntu:**
```bash
sudo apt install -y qgis qgis-plugin-grass
```

**macOS:**
Download the native ARM build from [qgis.org/downloads](https://qgis.org/downloads/).

**Windows:**
Use the OSGeo4W installer from [qgis.org](https://qgis.org).

Install the SpatiaLite GUI plugin inside QGIS:
`Plugins → Manage and Install Plugins → search "SpatiaLite" → Install`

---

### Step 8 — Download offline map tiles for Venezuela

GENTE uses vector tiles rather than raster tiles to minimize storage and enable full offline use.

```bash
# Download Venezuela state boundaries (GeoJSON, public domain)
mkdir -p qgis/basemaps/
wget -O qgis/basemaps/vzla_estados.geojson \
  "https://raw.githubusercontent.com/wmgeolab/geoBoundaries/main/releaseData/gbOpen/VEN/ADM1/geoBoundaries-VEN-ADM1.geojson"

# Download municipio boundaries
wget -O qgis/basemaps/vzla_municipios.geojson \
  "https://raw.githubusercontent.com/wmgeolab/geoBoundaries/main/releaseData/gbOpen/VEN/ADM2/geoBoundaries-VEN-ADM2.geojson"
```

For road network and building footprints (optional, larger download):
```bash
# Install osmium-tool
sudo apt install osmium-tool

# Download Venezuela OSM extract (~180 MB)
wget -O /tmp/venezuela.osm.pbf \
  "https://download.geofabrik.de/south-america/venezuela-latest.osm.pbf"

# Extract roads only
osmium tags-filter /tmp/venezuela.osm.pbf \
  w/highway -o qgis/basemaps/vzla_roads.osm.pbf

# Convert to GeoJSON for QGIS
ogr2ogr -f GeoJSON qgis/basemaps/vzla_roads.geojson \
  qgis/basemaps/vzla_roads.osm.pbf lines
```

---

### Step 9 — Initialise the SpatiaLite database

```bash
source .venv/bin/activate
python pipeline/db_writer.py --init
# Creates /data/gente/disaster_triage.sqlite with full schema
# Loads spatial metadata and Venezuela coordinate reference system (EPSG:4326)
```

---

### Step 10 — Load the XLSForm into local KoboToolbox

```bash
# Start KoboToolbox
cd docker/
docker compose up -d
# Wait ~60 seconds for all services to start

# Open browser: http://localhost:8000
# Log in with credentials set in docker-compose.yml
# Go to: Library → Upload form → select forms/damage_survey_es.xlsx
# Deploy the form → note the form ID (needed for config.py KOBO_FORM_ID)
```

---

### Step 11 — Full pipeline test (with sample data)

```bash
source .venv/bin/activate
python pipeline/kobo_to_qgis.py --sample --verbose
```

Expected output:
```
[INFO] GENTE pipeline starting...
[INFO] Loading 10 sample submissions from tests/sample_data.json
[INFO] Processing submission VEN-2026-001...
[INFO]   Sending field note to Ollama (llama3.1:8b)...
[INFO]   AI triage complete: severity=High, medical_need=True (confidence=0.91)
[INFO]   Inserted point: triage_points id=1, needs_review=1
[INFO] Processing submission VEN-2026-002...
...
[INFO] Pipeline complete. 10 points written to disaster_triage.sqlite.
[INFO] Open qgis/gente_project.qgz to view triage map.
```

Open `qgis/gente_project.qgz`. Ten grey and colored points should appear across the Venezuela map. If they do, the stack is working correctly.

---

### Step 12 — Run the automated test suite

```bash
pytest tests/ -v
```

All tests should pass before field deployment. If any fail, do not deploy.

---

### Step 13 — Pre-deployment checklist

Print and complete this checklist before packing:

```
HARDWARE
[ ] Laptop charged to 100%
[ ] Portable power station charged to 100%
[ ] 2× USB drives formatted and labelled (BACKUP_A / BACKUP_B)
[ ] Field phone power banks charged
[ ] KoboCollect installed and tested on all enumerator phones

SOFTWARE
[ ] Docker images verified offline (docker images)
[ ] Ollama model verified offline (ollama list)
[ ] SpatiaLite database initialized and writable
[ ] QGIS project opens and shows Venezuela basemaps
[ ] Pipeline test passed with sample data (--sample flag)
[ ] config.py KOBO_TOKEN and KOBO_FORM_ID set correctly
[ ] Wi-Fi hotspot tested on target hardware

FORMS
[ ] damage_survey_es.xlsx uploaded to local KoboToolbox
[ ] Form deployed and accessible to test KoboCollect install
[ ] GPS capture tested in field conditions (not just indoors)
[ ] All enumerator phones connected to local hotspot successfully

DATA GOVERNANCE
[ ] Laptop full-disk encryption enabled (BitLocker / LUKS)
[ ] Strong hotspot passphrase set (rotate daily in field)
[ ] USB backup drives encrypted
[ ] Local KoboToolbox admin password set (change from default)
```

---

## Phase 2: Field operation

### Starting the stack each day

```bash
# 1. Start KoboToolbox
cd ~/gente/docker && docker compose up -d

# 2. Start Ollama (if not running as a service)
ollama serve &

# 3. Start the pipeline (runs continuously, polls every 60 seconds)
cd ~/gente
source .venv/bin/activate
python pipeline/kobo_to_qgis.py --live

# 4. Open QGIS
qgis qgis/gente_project.qgz &
```

### Broadcasting the field Wi-Fi hotspot

**Ubuntu (NetworkManager):**
```bash
nmcli device wifi hotspot \
  ifname wlan0 \
  ssid "GENTE-CAMPO" \
  password "your-daily-passphrase"
```

Share the SSID and passphrase with enumerators. They connect KoboCollect to this hotspot to sync submissions.

### Daily backup procedure

```bash
# Run at end of each operational day
cp /data/gente/disaster_triage.sqlite \
   /media/BACKUP_A/disaster_triage_$(date +%Y%m%d).sqlite

# Rotate: alternate between BACKUP_A and BACKUP_B drives
# Store one drive physically separate from the laptop at all times
```

### Exporting situation reports from QGIS

```
1. Open qgis/gente_project.qgz
2. Review and confirm any pending triage points (needs_review=1 → grey)
3. Go to: Project → Layouts → Situation Report Map
4. Update the date/time text box to current date/time
5. Layout → Export as PDF → save to /data/gente/reports/sitrep_YYYYMMDD.pdf
6. Share PDF via WhatsApp, USB, radio coordination, or printed briefing
```

### Shutting down safely

```bash
# Stop the pipeline
Ctrl+C in pipeline terminal

# Stop KoboToolbox stack
cd ~/gente/docker && docker compose stop

# Stop Ollama
pkill ollama

# Run backup before powering off
cp /data/gente/disaster_triage.sqlite /media/BACKUP_A/...

# Power off laptop
sudo shutdown now
```

---

## Troubleshooting

### KoboToolbox containers not starting

```bash
docker compose logs kobocat
# Common cause: PostgreSQL not ready yet — wait 60 seconds and retry
docker compose restart
```

### Ollama not using GPU

```bash
ollama run llama3.1:8b "test"
# Check output for: "gpu layers: 33" (should not say 0)

# If GPU not detected:
sudo systemctl restart docker
# Verify: nvidia-smi shows GPU processes after starting ollama
```

### Pipeline not detecting new submissions

```bash
# Check KoboToolbox API is reachable locally
curl http://localhost:8000/api/v2/assets/ \
  -H "Authorization: Token your-token"

# Check KOBO_TOKEN in config.py matches the token shown in:
# KoboToolbox → Account Settings → API Token
```

### QGIS not showing new points

```
Layer → Right-click triage_points → Refresh
# or
Layer → Properties → Source → Refresh interval → set to 30 seconds
```

### Enumerator phones cannot connect to hotspot

```bash
# Verify hotspot is broadcasting
nmcli connection show --active | grep Hotspot

# Check phone is within ~30 metres
# Verify password is entered correctly (case-sensitive)
# Restart hotspot:
nmcli connection down GENTE-CAMPO
nmcli device wifi hotspot ssid "GENTE-CAMPO" password "passphrase"
```

---

## Security hardening

```bash
# Enable full disk encryption (Ubuntu — do during OS install)
# If post-install:
sudo apt install ecryptfs-utils
ecryptfs-migrate-home -u $USER

# Set firewall: allow local network only
sudo ufw enable
sudo ufw allow from 192.168.0.0/24   # local hotspot subnet only
sudo ufw deny from any

# Change KoboToolbox default credentials immediately after install
# Go to: http://localhost:8000/accounts/login/
# Admin → Users → Change password
```

---

## Resource usage at steady state

| Process | CPU | RAM | GPU VRAM |
|---|---|---|---|
| KoboToolbox (Docker stack) | ~5% | ~2 GB | 0 |
| Ollama (idle) | ~1% | ~500 MB | ~6 GB loaded |
| Ollama (active inference) | ~80% | ~500 MB | ~6 GB |
| QGIS (open) | ~5% | ~800 MB | 0 |
| Pipeline script (polling) | ~1% | ~150 MB | 0 |
| **Total** | **~10–90%** | **~4.5 GB** | **~6 GB** |

A system with 16 GB RAM and 8 GB GPU VRAM handles the full stack comfortably. During Ollama inference (every ~60 seconds per submission batch), CPU spikes to ~80% for 3–5 seconds, then returns to idle.

---
<img align="left" width="10%" alt="gente" alt="gentecolor" src="https://github.com/user-attachments/assets/44fa3eac-48f4-4e06-aab6-cdd3c0837372" />
<br>


*See [`docs/architecture.md`](architecture.md) for the system design rationale behind these deployment decisions.*  
*See [`docs/venezuela-context.md`](venezuela-context.md) for the operational context this deployment guide was written for.*
