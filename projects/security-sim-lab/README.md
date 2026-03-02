# 🛡️ Enterprise Security Simulation Lab

A hands-on cybersecurity project simulating a small-scale enterprise environment — built, attacked, defended, and monitored end-to-end on a single Kali Linux machine using Docker.

---

## 📋 Project Overview

| Detail | Value |
|---|---|
| **Platform** | Kali Linux · 8GB RAM |
| **Date** | February 2026 |
| **Type** | Offensive + Defensive Security Simulation |
| **Status** | ✅ Completed |

---

## 🏗️ Architecture

```
[Kali Attacker Tools]
       │
       │  Nmap / Metasploit / curl
       ▼
[UFW Firewall]  ──blocks──▶  [Blocked Traffic]
       │
       ▼
[Apache + ModSecurity WAF :80]   ──logs──▶  [modsec_audit.log]
       │                                            │
  blocks SQLi/XSS                                   ▼
       │                                     [Logstash :5044]
       ▼                                            │
[DVWA :8080]  [Dionaea Honeypot]                    ▼
                                        [Elasticsearch :9200]
                                                    │
                                                    ▼
                                           [Kibana :5601]
```

**All components run as Docker containers on a shared bridge network (`172.18.0.0/24`).**

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| Target App | DVWA (Damn Vulnerable Web Application) |
| Honeypot | Dionaea |
| WAF | Apache + ModSecurity + OWASP CRS v4 |
| Firewall | UFW |
| Monitoring | ELK Stack (Elasticsearch 8.11, Logstash, Kibana) |
| Attack Tools | Nmap, Metasploit Framework |

---

## ✅ What Was Done

### 1 — Deploy Environment
- DVWA deployed via Docker, accessible on port 8080
- Dionaea honeypot configured on ports 21, 23, 445, 1433
- All containers connected via a shared Docker bridge network

### 2 — Attack Simulation
- **Nmap** host discovery, service/OS fingerprinting, and vulnerability scanning across the Docker subnet
- **Metasploit** HTTP login brute-force module against DVWA
- **Manual SQL Injection** — `1' OR '1'='1` and UNION-based payloads via DVWA UI and curl
- **XSS attempt** — `<script>alert(1)</script>` via GET parameter
- **Honeypot probing** — Nmap and FTP connection attempts captured by Dionaea

### 3 — Defence Configuration
- ModSecurity WAF configured in `SecRuleEngine On` (enforcement mode)
- OWASP CRS v4 rules deployed — blocked all SQLi and XSS payloads with **HTTP 403**
- UFW default-deny inbound policy with explicit allow rules for ports 22, 80, 5601
- Port 8080 (direct DVWA access) explicitly denied to prevent WAF bypass

### 4 — Monitoring
- ELK Stack deployed via Docker Compose with RAM-limited JVM settings
- Logstash ingested Apache access logs and ModSecurity audit logs
- **136 security events** indexed in Elasticsearch
- Kibana dashboard built with 4 panels: Requests Over Time, Top Attack Types, Top Blocked IPs, HTTP Response Codes

### 5 — Key Events Confirmed in Logs
- **Rule 942100** — SQL Injection detected via libinjection (`fingerprint: s&sos`, `ARGS:id`)
- **Rule 949110** — Inbound Anomaly Score Exceeded (score: 5, threshold: 5) → request denied
- **Rule 980170** — Anomaly score correlation report (SQLI=5, XSS=0, COMBINED=5)

---

## 🐛 Troubleshooting Log

This section documents real issues encountered and resolved during the project — included because working through these is part of the learning.

### Issue 1 — WAF Returned 302 Instead of 403

**Symptom:** `curl` SQL injection test returned `HTTP/1.1 302 Found` instead of a 403 block.

**Root cause:** DVWA redirects unauthenticated requests to `/login.php` before any page logic runs. ModSecurity never saw the payload.

**Fix:** Re-ran the test with an authenticated session cookie:
```bash
curl -c /tmp/dvwa_cookies.txt \
  -d "username=admin&password=password&Login=Login" \
  http://localhost/login.php

curl -b /tmp/dvwa_cookies.txt \
  "http://localhost/vulnerabilities/sqli/?id=1'+OR+'1'='1&Submit=Submit"
```
Result: **HTTP 403 confirmed.**

---

### Issue 2 — OWASP CRS Rules Directory Not Found

**Symptom:** `/etc/apache2/modsecurity-crs/rules/` did not exist despite the package being installed.

**Root cause:** The `modsecurity-crs` Kali package (v3.3.8) registers the path `/etc/modsecurity/crs/` in `dpkg -L` but never actually creates it — a known Kali packaging bug.

**Fix:** Cloned the CRS rules directly from GitHub:
```bash
sudo git clone https://github.com/coreruleset/coreruleset.git \
     /etc/modsecurity/crs

sudo cp /etc/modsecurity/crs/crs-setup.conf.example \
        /etc/modsecurity/crs/crs-setup.conf
```
Then updated `security2.conf` to point to the correct path and ran `sudo apachectl configtest` → `Syntax OK`.

---

### Issue 3 — Logstash Pipeline Crashing on Startup

**Symptom:** Logstash exited immediately with:
```
LogStash::ConfigurationError: GeoIP Filter in ECS-Compatibility mode requires
a `target` when `source` is not an `ip` sub-field
```

**Root cause:** The `geoip { source => "clientip" }` filter is incompatible with ECS v8 mode (default in Logstash 8.x), which requires a `target` field when the source is not a standard ECS IP sub-field.

**Fix:** Removed the `geoip` block from `logstash.conf` entirely. GeoIP enrichment is not essential for this lab.

---

### Issue 4 — Logstash Consuming Too Much RAM

**Symptom:** System memory pressure with Elasticsearch and Logstash both running; containers becoming unresponsive.

**Root cause:** Default Logstash JVM heap is `-Xms1g -Xmx1g`. On an 8GB system already running Elasticsearch at 512MB, this left insufficient memory for the host OS.

**Fix:** Added memory constraints to `docker-compose.yml`:
```yaml
logstash:
  environment:
    - LS_JAVA_OPTS=-Xms256m -Xmx256m
  mem_limit: 512m
```

---

### Issue 5 — Kibana Data View Showing "No Matching Indices"

**Symptom:** Creating a data view with pattern `security-logs-*` in Kibana returned "The index pattern you entered doesn't match any data streams, indices, or index aliases."

**Root cause:** Logstash was crashing before shipping any data (due to Issue 3 above), so no index existed in Elasticsearch yet.

**Fix:** After fixing `logstash.conf`, restarted the stack and generated traffic:
```bash
curl http://localhost:9200/_cat/indices?v
# Confirmed: security-logs-2026.02.25 exists with 136 documents
```

---

### Issue 6 — Kibana Dashboard Panels Showing "No Results Found"

**Symptom:** All four dashboard panels empty despite data existing in Elasticsearch.

**Root cause:** Visualisations were configured with ECS v8 field names (`source.address.keyword`, `http.request.method.keyword`) which don't exist in the documents. The grok parser produces different field names from the `COMBINEDAPACHELOG` pattern.

**Fix:** Inspected actual document fields with:
```bash
curl "http://localhost:9200/security-logs-*/_search?pretty&size=1"
```
Then updated each visualisation to use the real field names: `clientip.keyword`, `verb.keyword`, `response.keyword`, `request.keyword`.

---

## 🔑 Key Takeaways

- A WAF is only effective if traffic is **forced through it** — direct access to the app (port 8080) must be firewall-blocked
- Default Linux package managers don't always install files where they claim — always verify with `dpkg -L` and `find`
- ELK Stack on constrained RAM requires explicit JVM heap tuning — defaults will OOM your system
- Real-world security work is 50% configuration and 50% troubleshooting

---

## 🚀 How to Reproduce

### Only Prerequisite
**Docker + Docker Compose** must be installed on your system.
→ [Get Docker](https://docs.docker.com/get-docker/)

Everything else — DVWA, Honeypot, WAF, and the full ELK Stack — runs inside containers. No `apt install`, no manual Apache setup, no host-level changes required.

---

### Step 1 — Clone the Repo and Set Up Config Files

```bash
git clone https://github.com/YOUR_USERNAME/security-sim-lab.git
cd security-sim-lab
```

Create the Logstash pipeline config file at `logstash/logstash.conf`:

```bash
mkdir -p logstash
cat > logstash/logstash.conf << 'EOF'
input {
  file {
    path => "/var/log/waf/access.log"
    start_position => "beginning"
    type => "apache_access"
  }
  file {
    path => "/var/log/waf/modsec_audit.log"
    start_position => "beginning"
    type => "modsecurity"
  }
}

filter {
  if [type] == "apache_access" {
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
    date {
      match => [ "timestamp", "dd/MMM/yyyy:HH:mm:ss Z" ]
    }
  }
  if [type] == "modsecurity" {
    grok {
      match => { "message" => "\[id \"%{NUMBER:rule_id}\"\]" }
      tag_on_failure => []
    }
    grok {
      match => { "message" => "\[msg \"%{DATA:attack_message}\"\]" }
      tag_on_failure => []
    }
    grok {
      match => { "message" => "\[uri \"%{DATA:attacked_uri}\"\]" }
      tag_on_failure => []
    }
    if "Access denied" in [message] {
      mutate { add_field => { "action" => "BLOCKED" } }
    } else if "Warning" in [message] {
      mutate { add_field => { "action" => "DETECTED" } }
    }
    if "SQLi" in [message] or "SQL Injection" in [message] {
      mutate { add_field => { "attack_type" => "SQL Injection" } }
    } else if "XSS" in [message] {
      mutate { add_field => { "attack_type" => "XSS" } }
    }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "security-logs-%{+YYYY.MM.dd}"
  }
}
EOF
```

---

### Step 2 — Create the Unified docker-compose.yml

Create `docker-compose.yml` in the root of the repo:

```yaml
services:

  # ── Vulnerable Web App ──────────────────────────────
  dvwa:
    image: vulnerables/web-dvwa
    ports:
      - "8080:80"
    networks:
      - simnet

  # ── Honeypot ────────────────────────────────────────
  dionaea:
    image: dinotools/dionaea
    ports:
      - "21:21"
      - "23:23"
      - "445:445"
      - "1433:1433"
    networks:
      - simnet

  # ── WAF (ModSecurity + OWASP CRS + Reverse Proxy) ───
  waf:
    image: owasp/modsecurity-crs:apache
    ports:
      - "80:80"
    environment:
      - PARANOIA=1
      - ANOMALY_INBOUND=5
      - BACKEND=http://dvwa:80
      - ERRORLOG=/var/log/waf/modsec_audit.log
      - ACCESSLOG=/var/log/waf/access.log
    volumes:
      - waf_logs:/var/log/waf
    depends_on:
      - dvwa
    networks:
      - simnet

  # ── Elasticsearch ────────────────────────────────────
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - ES_JAVA_OPTS=-Xms512m -Xmx512m
    ports:
      - "9200:9200"
    networks:
      - simnet

  # ── Logstash ─────────────────────────────────────────
  logstash:
    image: docker.elastic.co/logstash/logstash:8.11.0
    environment:
      - LS_JAVA_OPTS=-Xms256m -Xmx256m
    volumes:
      - ./logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
      - waf_logs:/var/log/waf:ro
    depends_on:
      - elasticsearch
      - waf
    networks:
      - simnet

  # ── Kibana ───────────────────────────────────────────
  kibana:
    image: docker.elastic.co/kibana/kibana:8.11.0
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - simnet

volumes:
  waf_logs:

networks:
  simnet:
    driver: bridge
```

---

### Step 3 — Start the Entire Lab

```bash
docker-compose up -d
```

Wait about **60–90 seconds** for Elasticsearch to fully initialise before Logstash and Kibana connect.

---

### Step 4 — Verify Everything is Running

```bash
docker-compose ps
# All 6 services should show status: running

curl http://localhost:9200/_cluster/health?pretty
# "status" should be "green" or "yellow"
```

---

### Step 5 — Access the Lab

| Service | URL | Credentials |
|---|---|---|
| DVWA (target app) | http://localhost:8080 | admin / password |
| WAF-proxied DVWA | http://localhost:80 | admin / password |
| Kibana dashboard | http://localhost:5601 | none required |
| Elasticsearch API | http://localhost:9200 | none required |

On first DVWA visit, click **"Create / Reset Database"** on the setup page.

---

### Step 6 — Generate Attack Traffic & View in Kibana

```bash
# Authenticate with DVWA
curl -c /tmp/cookies.txt \
  -d "username=admin&password=password&Login=Login" \
  http://localhost/login.php

# SQL Injection — should be blocked with HTTP 403
curl -i -b /tmp/cookies.txt \
  "http://localhost/vulnerabilities/sqli/?id=1'+OR+'1'='1&Submit=Submit"

# XSS attempt — should be blocked with HTTP 403
curl -i -b /tmp/cookies.txt \
  "http://localhost/?test=<script>alert(1)</script>"
```

Then open Kibana at `http://localhost:5601`:
1. Go to **Stack Management → Data Views → Create data view**
2. Pattern: `security-logs-*`, timestamp: `@timestamp`
3. Go to **Dashboard** and create visualisations using fields: `action.keyword`, `attack_type.keyword`, `clientip.keyword`

---

### Tear Down

```bash
docker-compose down -v
```

> ⚠️ This lab is for **educational purposes only**. Run in an isolated environment. Never expose these ports to the internet.
