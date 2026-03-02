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
---

## 🔑 Key Takeaways

- A WAF is only effective if traffic is **forced through it** — direct access to the app (port 8080) must be firewall-blocked
- Default Linux package managers don't always install files where they claim — always verify with `dpkg -L` and `find`
- ELK Stack on constrained RAM requires explicit JVM heap tuning — defaults will OOM your system
- Real-world security work is 50% configuration and 50% troubleshooting

---

## 🚀 How to Reproduce

```bash
# 1. Clone this repo
git clone https://github.com/YOUR_USERNAME/security-sim-lab.git
cd security-sim-lab

# 2. Start DVWA
cd dvwa && docker-compose up -d

# 3. Start Honeypot
cd ../honeypot && docker-compose up -d

# 4. Install and configure ModSecurity WAF
sudo apt install apache2 libapache2-mod-security2 -y
sudo git clone https://github.com/coreruleset/coreruleset.git /etc/modsecurity/crs
sudo cp /etc/modsecurity/crs/crs-setup.conf.example /etc/modsecurity/crs/crs-setup.conf
# Edit /etc/modsecurity/modsecurity.conf → SecRuleEngine On
sudo systemctl restart apache2

# 5. Start ELK Stack
cd ../elk && docker-compose up -d

# 6. Access Kibana at http://localhost:5601
```

> ⚠️ This lab is for educational purposes only. Run in an isolated environment.
