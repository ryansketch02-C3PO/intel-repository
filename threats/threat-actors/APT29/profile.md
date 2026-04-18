# 🕵️ APT29 — Threat Actor Profile

**Scoundrel #009**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | APT29 |
| **Aliases** | Cozy Bear, Midnight Blizzard, NOBELIUM, The Dukes, Dark Halo, UNC2452, Cloaked Ursa, CozyDuke, BlueBravo, StellarParticle, SolarStorm, Blue Kitsune, UNC3524, Iron Hemlock, YTTRIUM |
| **MITRE ATT&CK ID** | [G0016](https://attack.mitre.org/groups/G0016/) |
| **Type** | Nation-State APT |
| **Origin** | Russian Federation |
| **Sponsor** | SVR — Sluzhba Vneshney Razvedki (Foreign Intelligence Service) |
| **Active Since** | 2008 (possibly earlier) |
| **Status** | 🔴 Highly Active |
| **Motivation** | Long-term strategic intelligence collection in support of Russian foreign & security policy |
| **Threat Level** | 🔴 CRITICAL |
| **Admiralty Grade** | A1 (confirmed attribution by US, UK, EU, and Five Eyes intelligence agencies) |
| **TLP** | TLP:WHITE |

---

## Overview

APT29 is arguably the **most sophisticated and disciplined** state-sponsored threat actor on the planet. Operated by Russia's Foreign Intelligence Service (SVR), they don't break things — they quietly sit inside networks for months or years, silently vacuuming up everything that matters: diplomatic cables, defense contracts, government email, cloud tokens, source code.

Where their GRU cousins (APT28, Sandworm) tend to make noise — hack-and-leak operations, destructive wiper attacks — APT29 is the ghost. They've been inside networks that never knew they were there. Their hallmark is **patience, stealth, and operational security** that puts most red teams to shame.

The SolarWinds supply chain compromise (2020) remains their magnum opus: a trojanized software update delivered to 18,000+ organizations, including 9 US federal agencies. They sat inside for months before FireEye accidentally uncovered it while investigating their own breach.

In 2024–2026, APT29 has pivoted hard into **cloud identity attacks** — stealing OAuth tokens, abusing delegated admin privileges, and living inside Microsoft 365 without touching disk. They've also resumed large-scale spear-phishing of European diplomatic entities and executed a major SCADA supply chain attack against European energy infrastructure.

---

## Primary Targets

- **Government & Diplomacy** — foreign ministries, embassies, think tanks, NATO entities
- **Defense & Intelligence** — defense contractors, intelligence-adjacent organizations, military research
- **Critical Infrastructure** — energy utilities, SCADA/ICS systems (increasingly post-2024)
- **Technology & Cloud** — cloud service providers, IT vendors, software supply chains
- **Healthcare & Pharma** — COVID vaccine research (2020), biodefense R&D
- **Academia & NGOs** — research institutions, policy organizations, election-adjacent groups

**Geographic Focus:** United States (primary), Europe (UK, Germany, France, Netherlands, Poland, Czech Republic), NATO members broadly, Ukraine post-2022.

---

## Signature Campaigns

| Campaign | Year | Description |
|---|---|---|
| **DNC Hack** | 2015–2016 | Compromised Democratic National Committee networks; operated alongside APT28 |
| **COVID-19 Vaccine Theft** | 2020 | Targeted UK, US, Canadian vaccine researchers (AstraZeneca, Moderna, NIH) |
| **SolarWinds / SUNBURST** | 2020 | Trojanized SolarWinds Orion update; breached 18,000+ orgs incl. 9 US agencies |
| **Microsoft Corporate Breach** | 2024 | Accessed Microsoft senior leadership email and corporate source code repositories |
| **RDP Spearphishing Wave** | Oct 2024 | Signed malicious RDP files sent to 10,000+ targets across 100+ orgs in gov, defense, NGO |
| **GRAPELOADER European Diplomat Campaign** | 2025 | Fake wine-tasting invites used to deliver GRAPELOADER downloader to EU diplomatic targets |
| **EcoStruxure SCADA Supply Chain / VoltDrop** | Jan–Feb 2026 | Compromised Schneider Electric update server; deployed VoltDrop backdoor in 47 European energy utilities |

---

## TTPs (MITRE ATT&CK)

### Initial Access
| ID | Technique |
|---|---|
| T1566.001 | Spearphishing Attachment (malicious documents, signed RDP files, HTML smuggling) |
| T1566.002 | Spearphishing Link (ROOTSAW dropper delivery, credential harvest pages) |
| T1190 | Exploit Public-Facing Application (Citrix, Pulse Secure VPN, Zimbra, JetBrains TeamCity) |
| T1195.002 | Supply Chain Compromise (SolarWinds 2020, EcoStruxure 2026) |
| T1110.003 | Password Spraying (cloud account targeting) |
| T1078.004 | Valid Accounts: Cloud Accounts (compromised OAuth/service principal credentials) |

### Execution & Persistence
| ID | Technique |
|---|---|
| T1059.001 | PowerShell (primary scripting engine) |
| T1047 | Windows Management Instrumentation (WMI for lateral exec) |
| T1053.005 | Scheduled Task/Job (persistence; cleaned up after establishing remote access) |
| T1098 | Account Manipulation (cloud account backdooring, delegate email permissions) |
| T1136 | Create Account (backdoor cloud accounts for persistence) |
| T1547.001 | Boot or Logon Autostart: Registry Run Keys |

### Credential Access
| ID | Technique |
|---|---|
| T1110.003 | Brute Force: Password Spraying |
| T1528 | Steal Application Access Token (OAuth token theft) |
| T1550 | Use Alternate Authentication Material (stolen cloud tokens, SAML forgery) |
| T1557 | Adversary-in-the-Middle (FOGGYWEB/MAGICWEB for ADFS token interception) |
| T1539 | Steal Web Session Cookie |

### Defense Evasion
| ID | Technique |
|---|---|
| T1027 | Obfuscated Files or Information (steganography in C2 comms) |
| T1090.002 | Proxy: External Proxy (residential proxies matching victim geolocation) |
| T1656 | Impersonation (infrastructure mimics legitimate victim services) |
| T1070 | Indicator Removal (tool cleanup after access established; timestomping) |
| T1218 | System Binary Proxy Execution (mshta for script execution) |
| T1665 | Hide Infrastructure (hostname masking, residential IP matching) |

### Command & Control
| ID | Technique |
|---|---|
| T1071.001 | Application Layer Protocol: Web (HTTPS C2 over legitimate services) |
| T1102 | Web Service (Dropbox, OneDrive, Notion, Twitter/X for C2 comms — HAMMERTOSS) |
| T1090.003 | Proxy: Multi-hop (Tor for RDP/SMB tunneling) |
| T1568 | Dynamic Resolution (DGA-generated subdomains for C2) |

### Collection & Exfiltration
| ID | Technique |
|---|---|
| T1114.002 | Email Collection: Remote (Exchange Web Services API; mailbox delegation abuse) |
| T1213 | Data from Information Repositories (SharePoint, code repos) |
| T1041 | Exfiltration Over C2 Channel |

---

## Malware Arsenal

| Tool | Type | Description |
|---|---|---|
| **SUNBURST** | Backdoor (trojanized DLL) | SolarWinds supply chain implant; masqueraded as legitimate Orion traffic |
| **SUNSPOT** | Build process injector | Intercepted SolarWinds build pipeline to insert SUNBURST |
| **TEARDROP** | Memory-only dropper | Delivered Cobalt Strike Beacon post-SUNBURST |
| **GRAPELOADER** | Downloader/fingerprinting | New 2025 tool; used in European diplomat phishing campaign |
| **WINELOADER** | Second-stage implant | Delivered by ROOTSAW/ICEBEAT dropper in 2024 diplomatic campaigns |
| **ROOTSAW / EnvyScout** | HTML smuggler / dropper | Initial delivery mechanism; victim fingerprinting |
| **FOGGYWEB** | ADFS backdoor | Silently exfiltrates ADFS server tokens; survives reimaging |
| **MAGICWEB** | ADFS DLL | Manipulates authentication claims for persistent access |
| **CozyDuke** | Modular backdoor | Core platform; downloads/executes modules from C2 |
| **MiniDuke / FatDuke** | Backdoor family | "Duke" malware family; Assembly-based; used 2008–present variants |
| **QUIETEXIT** | Reverse SSH tunnel | Installed on opaque network devices (SAN arrays, load balancers) |
| **VoltDrop** | Firmware backdoor | 2026 — operates at Schneider Electric Modicon PLC firmware level |
| **Cobalt Strike Beacon** | Commercial RAT | Used post-compromise for lateral movement |
| **HAMMERTOSS** | C2 framework | Uses Twitter/GitHub for covert C2 comms via steganography |

---

## Connection Webs

| Actor | Relationship |
|---|---|
| **APT28 (Scoundrel #006)** | Both attributed to Russia; operated simultaneously inside DNC networks in 2015–2016. APT28 = GRU; APT29 = SVR. Separate chains of command, occasionally overlapping targets. |
| **Sandworm (Scoundrel #007)** | Also GRU-attributed (different unit). Sandworm destroys; APT29 collects. Sometimes share victim environments. |

---

## Defensive Recommendations

1. **Phishing-resistant MFA** — hardware keys or number-matching; APT29 has bypassed SMS/push MFA
2. **Cloud identity hardening** — conditional access, impossible travel alerts, token lifetime limits
3. **ADFS monitoring** — detect FOGGYWEB/MAGICWEB via unexpected DLL loads and anomalous token issuance
4. **Software supply chain audit** — verify code signing certificates, hash-verify updates before deployment
5. **Mailbox delegation auditing** — alert on ApplicationImpersonation grants and unexpected EWS API access
6. **Residential proxy detection** — flag logins from ISPs atypical for your user base
7. **Endpoint visibility on network appliances** — SAN arrays, load balancers (APT29 installs QUIETEXIT here)

---

## References

- [MITRE ATT&CK G0016](https://attack.mitre.org/groups/G0016/)
- [CISA Advisory AA24-057A — SVR Cloud Tactics](https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-057a)
- [Check Point Research — GRAPELOADER Campaign (2025)](https://research.checkpoint.com/2025/apt29-phishing-campaign/)
- [Microsoft MSTIC — Midnight Blizzard RDP Campaign (Oct 2024)](https://www.microsoft.com/en-us/security/blog/2024/10/29/midnight-blizzard-conducts-large-scale-spear-phishing-campaign-using-rdp-files/)
- [DarkRelay — APT29 Operations & IOCs (2026)](https://www.darkrelay.com/post/apt29-midnight-blizzard-operations-iocs-detection-strategies)
- [KENSAI — VoltDrop SCADA Campaign (2026)](https://kensai.app/ar/blog/russian-apt-midnight-blizzard-targets-european-energy-sector-march-2026)

---

*Last Updated: 2026-04-06 | Maintained by C3PO Intel*
