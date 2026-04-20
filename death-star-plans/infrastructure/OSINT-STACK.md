# 🕵️ C3PO OSINT Stack — Setup & Reference

**Host:** [HOSTNAME] (Raspberry Pi 5, 8GB RAM, ARM64)
**OS:** Debian Bookworm / Linux 6.12.62
**Installed:** 2026-04-16
**Maintained by:** C3PO

> ⚖️ All usage governed by [LEGAL-POLICY.md](./LEGAL-POLICY.md) — passive recon only on third-party targets.

---

## Installed Tools

### 🕷️ SpiderFoot
- **Purpose:** Automated OSINT collection engine — 200+ modules covering domains, IPs, emails, usernames, threat intel feeds, paste sites, breach databases
- **Location:** `~/osint-stack/spiderfoot/`
- **Web UI:** `http://localhost:5001`
- **Start:** `python3 ~/osint-stack/spiderfoot/sf.py -l 127.0.0.1:5001`
- **⚠️ POLICY:** Always select **"Passive"** use case when creating scans on third-party targets. 199 passive modules available.
- **Best for:** Full automated recon on A&D vendor domains, threat actor infrastructure, breach detection

### 🌾 theHarvester
- **Purpose:** Fast email, subdomain, hostname enumeration from public sources
- **Location:** `~/osint-stack/theHarvester/`
- **Activate venv:** `source ~/osint-stack/theHarvester/venv/bin/activate`
- **Usage:** `theHarvester -d <domain> -b google,bing,linkedin`
- **Sources:** Google, Bing, LinkedIn, PGP key servers, DuckDuckGo, and more
- **Best for:** First-pass passive recon on A&D vendor domains

### 🔭 recon-ng
- **Purpose:** Modular web reconnaissance framework (Metasploit-style)
- **Location:** `~/osint-stack/recon-ng/`
- **Usage:** `python3 ~/osint-stack/recon-ng/recon-ng`
- **Best for:** Structured recurring queries, DNS recon, WHOIS pivoting

### 🧅 Tor + TorBot
- **Purpose:** Dark web access layer + .onion site crawler
- **Tor service:** Running as SOCKS5 proxy on `127.0.0.1:9050`
- **TorBot location:** `~/osint-stack/torbot/`
- **Tor status:** `systemctl status tor`
- **TorBot usage:** `python3 ~/osint-stack/torbot/main.py -u <.onion URL>`
- **⚠️ POLICY:** Public .onion pages only. No authentication bypass. No file downloads.
- **Best for:** Dark web forum and threat actor infrastructure monitoring

### 🔍 Shodan CLI
- **Purpose:** Search Shodan's pre-indexed database of internet-connected devices
- **Usage:** `shodan search <query>` | `shodan host <IP>`
- **Setup:** `shodan init <API_KEY>` (free key at shodan.io)
- **⚠️ POLICY:** Query Shodan's index only — do not use for active scanning
- **Best for:** Finding exposed A&D vendor assets, vulnerable services, OT/ICS devices

### 🗺️ Supporting Tools
- **nmap** — port/service scanning (authorized targets only)
- **whois** — domain registration lookups
- **dig** — DNS queries and zone enumeration

---

## Startup Script

```bash
bash ~/osint-stack/start-osint-stack.sh
```

Starts Tor + SpiderFoot web UI and prints full tool reference.

---

## Web-Based Tools (No Install Required)

| Tool | URL | Purpose |
|------|-----|---------|
| Intelligence X | https://intelx.io | Dark web + Tor + I2P + breach search |
| VirusTotal | https://virustotal.com | Malware, URL, domain, IP analysis |
| ThreatFox | https://threatfox.abuse.ch | IOC database (free API) |
| MalwareBazaar | https://bazaar.abuse.ch | Malware sample database (free API) |
| DeHashed | https://dehashed.com | Leaked credential search |
| DNSDumpster | https://dnsdumpster.com | DNS/subdomain recon |
| Shodan (web) | https://shodan.io | Internet device search |
| Censys | https://censys.io | Attack surface/certificate tracking |
| SecurityTrails | https://securitytrails.com | Historical DNS records |
| OSINT Framework | https://osintframework.com | Curated OSINT tool directory |
| Feedly TI | https://feedly.com/i/threat-intelligence | AI-powered threat feed aggregation |
| Have I Been Pwned | https://haveibeenpwned.com | Breach/credential exposure check |

---

## Dark Web Monitoring (Free)

1. **Tor** runs as SOCKS5 proxy on `127.0.0.1:9050`
2. **TorBot** crawls public .onion URLs through Tor
3. **Intelligence X** (free tier) provides indexed dark web search
4. **ThreatFox + MalwareBazaar** provide continuously updated IOC feeds

### Verify Tor is working
```bash
curl --socks5 127.0.0.1:9050 https://check.torproject.org/api/ip
```

---

## Typical Workflows

### Actor Infrastructure Recon (Passive)
```bash
# 1. Passive domain enumeration
source ~/osint-stack/theHarvester/venv/bin/activate
theHarvester -d <actor-domain> -b google,bing,linkedin,dnsdumpster
deactivate

# 2. DNS mapping
dig <domain> ANY

# 3. SpiderFoot web UI — select "Passive" use case
# Open http://localhost:5001 → New Scan → paste IOC → select Passive
```

### IOC Enrichment
```bash
# VirusTotal: https://virustotal.com/gui/search/<hash/ip/domain>
# ThreatFox: https://threatfox.abuse.ch/browse/
# MalwareBazaar: https://bazaar.abuse.ch/browse/
```

### A&D Vendor Exposure Check
```bash
# Shodan (query their index — not active scanning)
shodan search "org:Lockheed Martin"
shodan search "org:Boeing" has_vuln:true

# Censys web: search org certificates
# DNSDumpster: map vendor subdomain exposure
```

---

## A&D Vendor Watchlist

| # | Vendor |
|---|--------|
| 1 | Lockheed Martin |
| 2 | Boeing |
| 3 | Raytheon (RTX) |
| 4 | Northrop Grumman |
| 5 | General Dynamics |
| 6 | BAE Systems |
| 7 | L3Harris |
| 8 | Leidos |
| 9 | SAIC |
| 10 | Thales Group |
| 11 | GE Aerospace |
| 12 | Safran |
| 13 | Honeywell Aerospace |
| 14 | Airbus |
| 15 | Collins Aerospace (RTX) |

---

*Last updated: 2026-04-16 | Maintained by C3PO*
