# junkielab-hermes-netops
AI-powered Network Operations, ISP Automation, Marketing Research, and Product Intelligence platform built on Hermes Agent, Docker, and Debian.

# Hermes Agent NetOps + Marketing Edition (JunkieLab)

## Tujuan

Membangun container Hermes Agent yang mampu:

* Network Automation
* MikroTik Management
* Cisco Management
* OLT Management (Huawei, ZTE, FiberHome, VSOL, C-Data, BDCOM, dll)
* SSH Automation
* SNMP Monitoring
* DNS Troubleshooting
* API Integration
* Product Scraping
* Market Research
* Data Collection
* Excel/CSV Export
* SEO Research
* AI-assisted Network Operations

---

# Struktur Direktori

```text
/opt/junkielab/
├── hermes-agent/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── data/
├── n8n/
├── 9router/
├── portainer/
└── backups/
```

---

# Alasan Menggunakan Dockerfile

Jangan melakukan:

```bash
docker exec -it hermes-agent bash
apt install xxx
```

Karena perubahan akan hilang saat:

* Rebuild image
* Update image
* Recreate container
* Restore backup

Semua dependency harus didefinisikan di Dockerfile agar dapat direproduksi kapan saja.

---

# Kemampuan Bawaan Hermes yang Sudah Ada

Tidak perlu di-install ulang:

```text
requests
httpx
netmiko
paramiko
napalm
scrapli
ncclient
pysnmp
routeros_api
jinja2
yaml
```

---

# Tool Linux Tambahan

## Network Automation

```text
openssh-client
sshpass
dnsutils
iputils-ping
netcat-openbsd
traceroute
mtr-tiny
jq
snmp
snmpd
snmptrapd
telnet
tftp-hpa
lftp
expect
tcpdump
whois
```

## Utility

```text
curl
wget
git
nano
vim-tiny
httpie
```

## Python Runtime

```text
python3
python3-pip
python3-venv
python3-dev
build-essential
libssl-dev
libffi-dev
```

## Browser Automation

```text
chromium
chromium-driver
fonts-noto
fonts-noto-color-emoji
fonts-liberation
```

---

# Python Library Tambahan

## Network Automation

```text
textfsm
ntc-templates
librouteros
rich
typer
```

## Product Scraping

```text
beautifulsoup4
scrapy
playwright
selenium
lxml
selectolax
```

## Data Processing

```text
pandas
openpyxl
```

## Marketing & SEO

```text
advertools
tldextract
rapidfuzz
dateparser
```

---

# Dockerfile

```dockerfile
FROM nousresearch/hermes-agent:latest

USER root

ENV DEBIAN_FRONTEND=noninteractive
ENV VIRTUAL_ENV=/opt/netops-venv
ENV PATH="/opt/netops-venv/bin:$PATH"

RUN apt-get update && apt-get install -y --no-install-recommends \
    openssh-client \
    sshpass \
    curl \
    wget \
    dnsutils \
    iputils-ping \
    netcat-openbsd \
    traceroute \
    mtr-tiny \
    jq \
    ca-certificates \
    git \
    nano \
    vim-tiny \
    python3 \
    python3-pip \
    python3-venv \
    python3-dev \
    build-essential \
    libssl-dev \
    libffi-dev \
    iproute2 \
    net-tools \
    telnet \
    whois \
    snmp \
    snmpd \
    snmptrapd \
    tftp-hpa \
    lftp \
    expect \
    tcpdump \
    httpie \
    chromium \
    chromium-driver \
    fonts-noto \
    fonts-noto-color-emoji \
    fonts-liberation \
    && rm -rf /var/lib/apt/lists/*

RUN python3 -m venv /opt/netops-venv && \
    pip install --no-cache-dir --upgrade pip setuptools wheel && \
    pip install --no-cache-dir \
    paramiko \
    netmiko \
    napalm \
    scrapli \
    ncclient \
    pysnmp \
    textfsm \
    ntc-templates \
    jinja2 \
    pyyaml \
    requests \
    httpx \
    routeros-api \
    librouteros \
    rich \
    typer \
    beautifulsoup4 \
    pandas \
    openpyxl \
    playwright \
    scrapy \
    lxml \
    selectolax \
    selenium \
    advertools \
    tldextract \
    rapidfuzz \
    dateparser

WORKDIR /opt/data

USER root
```

---

# Docker Compose

```yaml
services:
  hermes-agent:
    build:
      context: .
      dockerfile: Dockerfile

    image: junkielab/hermes-agent-netops:latest

    container_name: hermes-agent

    restart: unless-stopped

    network_mode: host

    command: gateway run

    dns:
      - 1.1.1.1
      - 8.8.8.8
      - 9.9.9.9

    environment:
      TZ: Asia/Jakarta
      HERMES_DASHBOARD: "0"

    volumes:
      - /opt/junkielab/hermes-agent/data:/opt/data
      - /var/run/docker.sock:/var/run/docker.sock

    cap_add:
      - NET_RAW
```

---

# Build dan Deploy

```bash
cd /opt/junkielab/hermes-agent

docker compose down

docker compose build --no-cache

docker compose up -d
```

---

# Verifikasi Instalasi

Masuk ke container:

```bash
docker exec -it hermes-agent bash
```

Cek tool:

```bash
which ssh
which sshpass
which dig
which nslookup
which snmpwalk
which telnet
which chromium
which jq
```

Cek library:

```bash
python - <<'PY'
mods = [
    "requests",
    "httpx",
    "bs4",
    "pandas",
    "openpyxl",
    "playwright",
    "scrapy",
    "netmiko",
    "paramiko",
    "napalm",
    "scrapli",
    "ncclient",
    "pysnmp",
    "routeros_api",
    "jinja2",
    "yaml"
]

for m in mods:
    try:
        __import__(m)
        print(f"OK {m}")
    except Exception as e:
        print(f"MISS {m}: {e}")
PY
```

---

# Best Practice

## Gunakan Docker untuk Semua Service

```text
Hermes Agent
n8n
9Router
Portainer
PostgreSQL
```

Host Debian hanya digunakan untuk:

```text
Docker Engine
Docker Compose
BTRFS Snapshot
SSH Management
```

---

## Snapshot BTRFS Sebelum Perubahan Besar

```bash
sudo btrfs subvolume snapshot -r / /.snapshots/pre-update-$(date +%Y%m%d-%H%M)
```

---

## Backup Data

```bash
sudo tar -czf \
junkielab-backup-$(date +%Y%m%d).tar.gz \
/opt/junkielab
```

---

# Roadmap Berikutnya

* Integrasi Hermes ↔ n8n
* Integrasi MikroTik API
* Integrasi Cisco SSH Automation
* Integrasi OLT Automation
* Inventory Perangkat
* Monitoring SNMP
* Product Scraper
* Marketplace Monitoring
* AI Network Engineer
* AI Marketing Assistant
* AI Product Research Assistant

Target akhir: membangun ekosistem JunkieLab AI Infrastructure yang mampu membantu operasional jaringan, otomasi ISP, pemasaran digital, dan analisis data dari satu platform Hermes Agent.
