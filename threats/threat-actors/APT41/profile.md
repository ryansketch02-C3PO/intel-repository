# 🐉 APT41 — Double Dragon

**Scoundrel #:** 019  
**MITRE ATT&CK ID:** G0096  
**Status:** 🔴 Active  
**Last Updated:** 2026-04-15

---

## 🪪 Identity

| Field | Value |
|---|---|
| **Primary Name** | APT41 |
| **Aliases** | Double Dragon, Winnti, BARIUM, Wicked Panda, Wicked Spider, Brass Typhoon, Bronze Atlas, Earth Baku, Grayfly, Blackfly, HOODOO, TA415, Leopard Typhoon, Red Kelpie, TG-2633, LEAD, Amoeba |
| **Nation-State Sponsor** | China (PRC) |
| **Sponsoring Agency** | Ministry of State Security (MSS), Chengdu branch |
| **Type** | Nation-State APT + Financially Motivated Cybercriminal (dual-mission) |
| **Active Since** | 2012 |
| **Named Members** | Zhang Haoran, Tan Dailin, Jiang Lizhi, Qian Chuan, Fu Qiang (indicted by U.S. DOJ, September 2020) |

---

## 🎯 What Makes APT41 Unique

APT41 is **the only major Chinese APT that runs state espionage and personal financial crime simultaneously.** Other Chinese groups (Volt Typhoon, Salt Typhoon, APT29 analogs) stick to one lane. APT41 plays both. They hack for Beijing by day, and hack for profit at night — sometimes using the *same* tools. This is the "Double Dragon" — two heads, two missions.

Their targeting aligns with China's **"Made in China 2025"** industrial strategy: high-tech, semiconductor, aerospace, pharmaceutical, and telecom sectors. The criminal side focuses on video game companies, virtual currency theft, ransomware, and digital certificate theft.

---

## 🎯 Who They Target

### Primary Espionage Targets
- **Aerospace & Defense** (U.S., Taiwan, India — aircraft manufacturers, defense contractors, aviation supply chains)
- **Semiconductor & High-Tech** (IP theft aligned with Made in China 2025)
- **Telecommunications** (call record metadata, surveillance of individuals of interest)
- **Government & Defense Industrial Base**
- **Healthcare & Pharmaceuticals** (biotech R&D, COVID-19 research during the pandemic)
- **Academic Institutions** (Hong Kong universities notably targeted 2019-2020)

### Criminal Side Targets
- Video game companies (virtual currency, source code, digital certificates)
- Financial institutions (cryptocurrency theft)
- Hotel reservation systems (pre-positioning for surveillance of Chinese officials traveling abroad)

### Geographic Focus
15+ countries including United States, Taiwan, India, Japan, South Korea, UK, Italy, Spain, Thailand, Turkey, Vietnam, Australia, France, Germany

---

## ⚙️ How They Operate (TTPs)

### Initial Access
- **Spear-phishing** with malicious CHM, LNK, and archive attachments
- **Zero-day and N-day exploitation** — aggressively weaponizes vulnerabilities within hours of disclosure (Log4Shell, ProxyLogon, Citrix CVE-2019-19781, Zoho ManageEngine CVE-2020-10189, USAHerds CVE-2021-44207)
- **SQL injection** as a reliable web-facing entry vector
- **Supply chain compromise** — injecting malicious code into legitimate software updates (CCleaner, ASUS LiveUpdate, NetSarang ShadowPad)

### Persistence & Defense Evasion
- **Code-signing certificate theft and abuse** — their signature move; maintains a library of stolen certs from underground markets and other Chinese APT groups
- **Bootkits** — Master Boot Record (MBR) implants for deep persistence
- **Web shells:** ANTSWORD, BLUEBEAM, China Chopper
- **Memory-only payloads** (DUSTTRAP) — executes in-memory, minimal forensic trace
- **DLL side-loading and search-order hijacking**
- **Masquerading** — disguising malicious services as legitimate Windows processes (e.g., "Windows Defend" mimicking Windows Defender)
- **Environmental keying** — payloads encrypted with machine-specific keys (volume serial number, DPAPI) so they only decrypt on target systems

### Malware Arsenal (Selected)
| Malware | Function |
|---|---|
| **ShadowPad** | Modular backdoor; supply chain implant |
| **KEYPLUG** | Backdoor with dead-drop C2 via community forums |
| **DUSTPAN** | Custom dropper; masquerades as Windows binaries |
| **DUSTTRAP** | Memory-only dropper; minimal forensic trace |
| **TOUGHPROGRESS** | Backdoor using **Google Calendar as C2** channel (2024-2025) |
| **MoonWalk** | Advanced backdoor delivered via DodgeBox loader |
| **DodgeBox** | Upgraded StealthVector; AES-CFB encrypted config, CFG bypass |
| **VOLDEMORT** | Backdoor with Google Sheets C2 (attributed 2024) |
| **LOWKEY / LOWKEY.PASSIVE** | Stealthy passive backdoor masquerading as web app traffic |
| **DEADEYE** | Dropper broken into disk sections to evade detection |
| **Winnti RAT** | Modular remote access trojan; Windows/Linux variants |
| **CROSSWALK** | Backdoor used in financial and espionage campaigns |
| **PlugX** | Shared Chinese APT RAT |
| **Cobalt Strike** | Post-exploitation framework (customized versions) |
| **PINEGROVE** | Data exfiltration to Microsoft OneDrive |
| **SQLULDR2** | Database exfiltration from Oracle DB |

### Command & Control Innovation
APT41 consistently abuses **legitimate cloud services** for C2 to blend into normal traffic:
- Google Calendar (TOUGHPROGRESS — 2024/2025)
- Google Sheets (VOLDEMORT — 2024)
- Microsoft OneDrive (DUSTTRAP campaign — 2023/2024)
- GitHub, Pastebin, TechNet (dead-drop resolvers)
- Cloudflare CDN proxying C2 traffic
- Compromised university and government websites for staging

### Lateral Movement
- Credential dumping (SAM, NTDS.dit, pass-the-hash)
- SMB/Windows Admin Shares
- RDP + NATBypass to tunnel past firewalls
- WMI for remote execution
- SSH pivoting on Linux systems

### Exfiltration
- RAR archiving of target data
- DNS exfiltration (encoding data as subdomains)
- OneDrive and cloud storage abuse
- Oracle Database copying via SQLULDR2

---

## 📅 Notable Campaigns

| Year | Campaign | Notes |
|---|---|---|
| 2012–2014 | Video game industry targeting | Virtual currency theft, source code, digital certs |
| 2017 | CCleaner supply chain | Poisoned update distributed to ~2.27M users; targeted follow-on victims in tech/telecoms/defense |
| 2019 | ASUS LiveUpdate supply chain | ~1M machines compromised; precision targeting of ~600 MAC addresses |
| 2019–2020 | Hong Kong universities | ShadowPad + Winnti deployed against academic targets during protests era |
| 2020 | U.S. state government networks | Exploited USAHerds + Log4Shell within hours of disclosure |
| 2021 | Air India breach (ColunmTK) | Aviation PII theft; public sector, manufacturing, hospitality targets |
| 2021–2022 | Log4Shell exploitation | Among first to weaponize Log4j (CVE-2021-44228) within hours of disclosure |
| 2023–2024 | DUST campaign (APT41 DUST) | Global shipping, logistics, media, automotive; DUSTPAN/DUSTTRAP/BEACON chain; exfil to OneDrive |
| 2024 | Aerospace phishing via Voldemort | Spoofed Taiwanese aerospace industry association; targeted U.S. and Taiwan aerospace firms |
| 2024–2025 | TOUGHPROGRESS / Google Calendar C2 | Compromised government website as staging; Google Calendar for C2 command delivery |

---

## 🔗 Connections to Other Tracked Actors

| Actor | Relationship |
|---|---|
| **#008 Volt Typhoon** | Both Chinese state-aligned; distinct missions (APT41 = espionage+crime, Volt = critical infrastructure pre-positioning). Share some tooling heritage. |
| **#010 Salt Typhoon** | Both MSS-affiliated. Salt Typhoon = pure SIGINT/telecom; APT41 broader mandate. Concurrent targeting of telecom sector. |
| **Winnti Group** | APT41 overlaps significantly; Winnti is a predecessor/sub-cluster; both named in MITRE. |
| **Earth Longzhi** | Believed sub-group of APT41; active since 2020; uses both public and custom malware. |

---

## 🛡️ Defense Recommendations

1. **Patch aggressively** — APT41 weaponizes critical CVEs within hours. Zero-day disclosure = immediate patch sprint.
2. **Supply chain hygiene** — Verify software update integrity; monitor code-signing certificate validity; audit third-party vendor access.
3. **Cloud service monitoring** — Flag anomalous use of Google Calendar API, Google Sheets API, OneDrive from endpoints. APT41 abuses these for C2.
4. **Network segmentation** — Isolate production environments, databases, and high-value assets from internet-facing systems.
5. **Memory-based detection** — Deploy EDR capable of detecting in-memory-only execution (no disk artifacts).
6. **Credential hygiene** — Enforce MFA everywhere; monitor for SAM/NTDS dumping, pass-the-hash, RDP lateral movement.
7. **Phishing defenses** — Block CHM, LNK files in email; inspect archives; URL inspection for AMP Cache redirect chains.
8. **Revoke OAuth tokens** — Audit Google Workspace service accounts; revoke suspicious tokens.

---

## ⚖️ Legal / Attribution

- **U.S. DOJ Indictment (September 2020):** Five Chinese nationals — Zhang Haoran, Tan Dailin, Jiang Lizhi, Qian Chuan, Fu Qiang — charged with CFAA violations across 100+ companies in 20+ countries.
- **Chengdu 404 Network Technology Co.** identified as a front company used by APT41 members.
- All named individuals remain at large; China does not extradite to the U.S.

---

## 📚 References

- [MITRE ATT&CK: APT41 (G0096)](https://attack.mitre.org/groups/G0096/)
- [Mandiant: APT41 Double Dragon](https://www.mandiant.com/resources/apt41-double-dragon)
- [U.S. DOJ Indictment (2020)](https://www.justice.gov/opa/pr/seven-international-cyber-defendants-including-apt41-actors-charged-connection-computer)
- [Huntress: APT41 Threat Profile](https://www.huntress.com/threat-library/threat-actors/apt41)
- [Kaspersky ICS-CERT: APT Activity Q3 2024](https://ics-cert.kaspersky.com/publications/reports/2024/12/26/apt-and-financial-attackson-industrial-organizationsin-q3-2024/)
- [ESET: Winnti Group IOCs](https://github.com/eset/malware-ioc/blob/master/winnti_group/README.adoc)
- [Google TAG / Mandiant: TOUGHPROGRESS Campaign (2024-2025)](https://cloud.google.com/blog/topics/threat-intelligence/apt41-us-state-governments)
- [Zscaler: DodgeBox/MoonWalk (2024)](https://www.zscaler.com/blogs/security-research/dodgebox-deep-dive-updated-arsenal-apt41-part-1)
- [Proofpoint: VOLDEMORT / TA415](https://www.proofpoint.com/us/blog/threat-insight/new-threat-actor-uses-voldemort-malware)
