# Kimsuky (APT43)

## Overview
| Field | Details |
|-------|---------|
| **Also Known As** | APT43, Black Banshee, Emerald Sleet, Springtail, TA427, Velvet Chollima, HIDDEN COBRA |
| **Origin** | North Korea 🇰🇵 |
| **Sponsor** | Reconnaissance General Bureau (RGB), DPRK government |
| **Active Since** | 2012–2013 |
| **Threat Level** | 🔴 HIGH |
| **Primary Motivation** | Espionage / Intelligence Collection |
| **MITRE ATT&CK Group** | G0094 |

## Mission
Kimsuky is tasked by the North Korean regime with **global intelligence gathering** — specifically targeting foreign policy experts, think tanks, government entities, and academic institutions for intelligence on nuclear policy, Korean peninsula issues, and sanctions. They steal information to support DPRK strategic decision-making, not to cause disruption.

## Primary Targets
- Think tanks and policy institutes (US, South Korea, Japan)
- Government officials and diplomats
- Academic researchers (nuclear policy, Korean peninsula, international relations)
- Defense research institutes
- Media organizations
- Cryptocurrency and blockchain sector
- North Korean defectors and human rights organizations

## Aerospace & Defense Relevance
While Kimsuky's primary focus is political/policy intelligence, they have targeted:
- **Defense research institutes** in South Korea and the US
- **Job search sites in the defense industry** (used as lure infrastructure)
- **Defense contractors** via spearphishing disguised as recruitment
- DPRK sanctions enforcement entities — critical for understanding how North Korea funds weapons programs

## Tactics, Techniques & Procedures (TTPs)

### Initial Access
- **Spearphishing** (T1566.001 / T1566.002) — primary vector; highly tailored lures
- **Quishing** (T1660) — malicious QR codes in emails bypassing email security controls (active as of 2025–2026)
- **ClickFix** — tricking victims into manually executing PowerShell commands
- **Watering hole attacks** (T1189)
- **Malicious browser extensions** via Chrome Web Store (T1185)
- Torrent site malware distribution

### Execution
- **BabyShark** — VBS-based malware, primary tool; executes via mshta.exe
- **HappyDoor** — VMP-protected backdoor; collects system info, keystrokes, screenshots, audio
- **QuasarRAT** — used as final payload in ClickFix campaigns
- **RftRAT / Amadey** — newer payload delivery tools
- **PowerShell** (T1059.001) — fileless execution, in-memory operation
- **AutoIt scripts** (HncUpdateTray.exe) — information theft
- Malicious LNK files disguised as legitimate software

### Persistence
- Scheduled task registration (T1053.005)
- Registry modifications for autostart (T1547)
- **GREASE malware** — adds Windows admin account + enables RDP (T1021.001)
- HWP default file association hijacking (T1546.001)
- PHP web shells on compromised domains with "Dinosaur" code references (T1505.003)

### Credential Theft & Collection
- **MECHANICAL** — PowerShell-based keylogger
- **Session token theft and replay** (T1550.004) — MFA bypass via quishing
- **Nirsoft SniffPass** — network credential sniffing (T1040)
- **PHProxy** — web traffic interception
- Chrome extension cookie/password theft
- Email auto-forward rule creation (T1114.003)

### Exfiltration
- Data encrypted with RC4 (MD5 hash key) then RSA before exfiltration
- Primary exfil channel: **email** (C2 via actor-controlled mail servers)
- Cloud services used: **Dropbox**, **Proton Drive**, **Google Drive** as C2 channels
- MEGA links used for payload delivery

### Defense Evasion
- **VMProtect** on HappyDoor — hinders analysis
- Multilingual PDF manuals to avoid sandbox detection
- Obfuscated/reversed PowerShell code
- Legitimate software (Bandizip) bundled with malware
- Mobile device pivot via QR codes — bypasses corporate EDR/email inspection

## Notable Campaigns
| Year | Campaign | Target | Method |
|------|----------|--------|--------|
| 2018 | STOLEN PENCIL | Academia | GREASE malware, RDP |
| 2018 | Chrome Web Store | Various | Malicious browser extension |
| 2020+ | COVID lures | Think tanks, gov | BabyShark via spearphishing |
| 2024 | DMARC abuse | Policy experts | Email spoofing |
| 2025 | Quishing campaign | US think tanks, NGOs | Malicious QR codes, session token theft |
| 2025 | ClickFix campaign | SK diplomats, security experts | PowerShell/VBS execution |
| 2025 | HappyDoor | SK government, media, education | Bandizip-disguised installer |
| 2025 | MEGA/Dropbox C2 | NK activists | LNK + PowerShell via Dropbox |

## Infrastructure Patterns
- Registers domains recently (often 2022–2023 vintage at time of use)
- Registrant countries: Bulgaria, Bahamas, Iceland, US
- ISPs: 24Shells, ColoCrossing, Hostwinds, Nexeon Technologies, OVH, Leaseweb Asia Pacific
- Creates subdomains mimicking Google, Yahoo, Microsoft 365, Okta, VPN portals
- Infrastructure spread across South Korea, China, Vietnam
- Shares IP ranges with **Lazarus Group** infrastructure (connected operations)

## Connections to Other Threat Actors
- **Lazarus Group (#003)** — both operate under RGB; share infrastructure (IP ranges confirmed), share tooling patterns, and coordinate on DPRK strategic objectives
- **Andariel** — fellow RGB sub-group; overlapping targets in Korean peninsula operations
- **Konni** — fellow DPRK-nexus group; parallel targeting of developers and government

## Admiralty Scale Rating
- **Source Reliability: A** (CISA, FBI, NSA joint advisories; multiple vendor confirmations)
- **Information Credibility: 1** (Confirmed by multiple independent sources)

## References
- [CISA/FBI/CNMF Advisory AA20-301A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-301a)
- [FBI FLASH AC-000001-MW — Quishing Campaign (Jan 2026)](https://www.ic3.gov/CSA/2026/260108.pdf)
- [AhnLab ASEC APT Trends July 2025](https://asec.ahnlab.com/en/89875/)
- [AhnLab ASEC APT Trends January 2026](https://asec.ahnlab.com/en/92627/)
- [Gurucul DPRK Infrastructure Analysis (Dec 2025)](https://partner.gurucul.com/articles/ThreatResearch/Inside-DPRK-Operations-New-Lazarus-and-26-12-2025)
- [MITRE ATT&CK G0094](https://attack.mitre.org/groups/G0094/)
- [ThreatFox Kimsuky IOCs](https://threatfox.abuse.ch/browse/malware/win.kimsuky/)

*Profile created: 2026-04-16 | Next review: 2026-07-16*
