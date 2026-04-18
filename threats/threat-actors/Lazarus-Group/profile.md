# 🕵️ Lazarus Group — Threat Actor Profile

**Scoundrel #003**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | Lazarus Group |
| **Aliases** | Hidden Cobra, ZINC, Diamond Sleet, Labyrinth Chollima, Guardians of Peace, APT-C-26, Group 77, Appleworm, TEMP.Hermit, 414 Liaison Office (internal DPRK name) |
| **MITRE ATT&CK ID** | [G0032](https://attack.mitre.org/groups/G0032/) |
| **Type** | Nation-State APT (umbrella group) |
| **Origin** | Democratic People's Republic of Korea (DPRK) |
| **Sponsor** | Reconnaissance General Bureau (RGB), 3rd Bureau / Lab 110 |
| **Active Since** | 2009 |
| **Status** | 🔴 Highly Active |
| **Motivation** | Espionage · Financial theft · Sabotage · Sanctions evasion |
| **Threat Level** | 🔴 CRITICAL |
| **Admiralty Grade** | A1 (well-established, highly reliable) |
| **TLP** | TLP:WHITE |

---

## Overview

Lazarus Group is arguably the most versatile and dangerous state-sponsored threat actor on the planet. Unlike most APT groups that specialize in *either* espionage *or* financial crime, Lazarus does all three: **spy, steal, and destroy** — often in the same campaign.

Operated under North Korea's Reconnaissance General Bureau (RGB), the group functions as DPRK's primary offensive cyber instrument, conducting operations designed to:
- Circumvent UN sanctions through massive cryptocurrency and bank theft
- Conduct military and defense espionage (especially aerospace/defense)
- Engage in destructive sabotage when politically motivated

The "Lazarus Group" label is best understood as an **umbrella** for multiple coordinated sub-units including Andariel (South Korea/critical infra focus) and BlueNoroff/Stardust Chollima (financial crime). These units share tooling, infrastructure, and presumably personnel — all under RGB oversight.

---

## Sub-Groups

| Sub-Group | Focus | See Also |
|---|---|---|
| **Stardust Chollima** (BlueNoroff, APT38) | Financial crime — crypto, banks, SWIFT | [#002 Scoundrels Profile](https://discord.com/channels/1483619395375992844/1488665479189893202) |
| **Andariel** | South Korean critical infra, supply chain | — |
| **BeagleBoyz** | SWIFT/ATM attacks in developing nations | — |

---

## Primary Targets

- **Aerospace & Defense** — contractors, weapons systems researchers, defense engineers (Operation Dream Job)
- **Financial Sector** — banks, SWIFT endpoints, crypto exchanges, DeFi platforms
- **Government & Think Tanks** — foreign policy, nuclear programs, sanctions monitoring
- **Healthcare & Pharma** — COVID-19 research (WannaCry victims; vaccine R&D theft 2020)
- **Media & Entertainment** — Sony Pictures (2014)
- **Supply Chain** — software vendors, IT service providers

**Geographic Focus:** South Korea, United States, Israel, India, Europe, Southeast Asia, Latin America — effectively *global*.

---

## Signature Campaigns

| Campaign | Year | Description |
|---|---|---|
| **Operation Troy** | 2009–2012 | DDoS/espionage against South Korean government |
| **Sony Pictures Hack** | 2014 | Destructive wiper attack; ~100TB data stolen/leaked |
| **Bangladesh Bank Heist** | 2016 | $81M stolen via fraudulent SWIFT transactions |
| **WannaCry Ransomware** | 2017 | Global ransomware worm; ~150 countries, ~$4B damage |
| **Operation Dream Job** | 2019–present | Fake LinkedIn job lures targeting defense/aerospace engineers |
| **TraderTraitor / AppleJeus** | 2018–present | Trojanized crypto apps; ongoing crypto theft |
| **Operation North Star** | 2020 | Aerospace/defense targeting in US, UK, India |
| **Comebacker Aerospace Campaign** | 2025 | New backdoor variant targeting European A&D contractors |

---

## TTPs (MITRE ATT&CK)

### Initial Access
| ID | Technique |
|---|---|
| T1566.001 | Spearphishing Attachment |
| T1566.003 | Spearphishing via Service (LinkedIn) |
| T1195.002 | Supply Chain Compromise (software) |
| T1189 | Drive-by Compromise (watering hole) |

### Execution & Persistence
| ID | Technique |
|---|---|
| T1204.002 | User Execution: Malicious File |
| T1547.001 | Registry Run Keys / Startup Folder |
| T1053.005 | Scheduled Tasks |
| T1505.004 | Server Software Component: IIS |

### Defense Evasion
| ID | Technique |
|---|---|
| T1027.002 | Software Packing (Themida) |
| T1027.009 | Embedded Payloads (PNG-wrapped binaries) |
| T1553.002 | Code Signing (Sectigo RSA certs) |
| T1036.008 | Masquerading: Masquerade File Type |
| T1622 | Debugger Evasion |
| T1068 | Exploitation for Privilege Escalation (CVE-2024-38193, CVE-2024-21338) |

### Credential Access & Lateral Movement
| ID | Technique |
|---|---|
| T1110 | Brute Force |
| T1078 | Valid Accounts |
| T1550.002 | Pass the Hash |

### Exfiltration
| ID | Technique |
|---|---|
| T1041 | Exfiltration Over C2 Channel |
| T1567.002 | Exfiltration to Cloud Storage (Dropbox, OneDrive) |
| T1048.003 | Exfiltration via SMTP (SierraBravo-Two) |

### Impact
| ID | Technique |
|---|---|
| T1485 | Data Destruction (wiper malware) |
| T1486 | Data Encrypted for Impact (WannaCry, ransomware) |
| T1489 | Service Stop |

---

## Malware Arsenal

| Malware | Type | Notes |
|---|---|---|
| **BLINDINGCAN** | RAT | Defense/aerospace targeting |
| **COPPERHEDGE** | Backdoor | Defense industry ops |
| **Comebacker** | Backdoor | Aerospace targeting; 2025 active variant |
| **MISTPEN** | Backdoor | Operation Dream Job 2025 variant |
| **BURNBOOK** | Loader | Dream Job infrastructure |
| **DRATzarus** | RAT | Operation Dream Job |
| **Torisma** | Implant | Monitoring drives + RDP connections |
| **AppleJeus / TraderTraitor** | Trojanized apps | Cross-platform crypto theft |
| **FASTCash** | ATM malware | AIX-based ATM cash-out attacks |
| **WannaCry** | Ransomware Worm | Global, 2017 |
| **MATA Framework** | Modular RAT | Cross-platform (Win/Linux/macOS) |
| **KiloAlfa** | Keylogger | Long-term implant |

---

## Infrastructure Patterns

- Heavy use of **compromised third-party infrastructure** (hacked WordPress/SharePoint servers as C2)
- **Fake recruiter/company domains** mimicking real defense contractors
- **Code-signed malware** using legitimate Sectigo RSA certificates
- C2 communications encrypted with AES, XOR, custom ciphers
- Staging via cloud file services: Dropbox, OneDrive, GitHub

---

## Known IOCs

> See `iocs.md` for full indicator list.

**Notable malicious domains (Operation Dream Job / Comebacker 2025):**
- `birancearea[.]com`
- `hiremployee[.]com`
- `office-theme[.]com`

**CVEs actively exploited:**
- `CVE-2024-38193` — Windows AFD.sys zero-day (SYSTEM privesc)
- `CVE-2024-21338` — Windows kernel flaw (CVSS 7.8)
- `CVE-2018-4878` — Adobe Flash (legacy)

---

## Geopolitical Context

Lazarus operates to serve three core DPRK strategic imperatives:
1. **Sanctions circumvention** — estimated $3B+ stolen in crypto (2017–2023) to fund weapons programs
2. **Military intelligence** — weapons tech, aerospace engineering secrets, nuclear program data
3. **Political retaliation** — Sony Pictures (The Interview film), South Korean targets

The group reportedly has 6,000+ personnel in the broader DPRK cyber apparatus, with operators frequently stationed abroad (China, Southeast Asia).

---

## Connection Web

- ⛓️ **[#002 Stardust Chollima](https://discord.com/channels/1483619395375992844/1488665479189893202)** — Lazarus financial sub-unit; APT38/BlueNoroff; responsible for the Bangladesh Bank heist and ongoing crypto theft
- ⛓️ **Andariel** — South Korea-focused sub-unit; targets critical infrastructure and defense supply chain
- ⛓️ **BeagleBoyz** — SWIFT/ATM theft in developing nation banks

---

## Defense Recommendations

1. **Block macro execution** in Office documents — primary initial access vector
2. **Enforce strict LinkedIn/external messaging policies** for engineers with clearances
3. **Deploy EDR** capable of detecting multi-stage loaders (Themida-packed binaries)
4. **Monitor for MagicLine4NX, SumatraPDF, TightVNC** spawned from user directories (Dream Job TTPs)
5. **Patch CVE-2024-38193 and CVE-2024-21338** immediately if unpatched
6. **MFA on all privileged accounts** — pass-the-hash mitigations
7. **Network segment** development/engineering systems from corporate IT
8. **Threat hunt** for LNK files in startup folders, scheduled tasks executing remote XSL

---

## References

- [MITRE ATT&CK: Lazarus Group (G0032)](https://attack.mitre.org/groups/G0032/)
- [Mandiant / Google: APT38 Report](https://cloud.google.com/blog/topics/threat-intelligence/apt38-details-on-new-north-korean-regime-backed-threat-group)
- [CISA Advisory AA21-048A: AppleJeus](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-048a)
- [Orange Cyberdefense: Operation Dream Job 2025 Arsenal](https://lazarus.day/media/post/files/2025/11/21/Navigating_Operation_DreamJob_s_arsenal_1.pdf)
- [Picus Security: Lazarus TTPs & Timeline](https://www.picussecurity.com/resource/blog/lazarus-group-apt38-explained-timeline-ttps-and-major-attacks)
- [EurePoc: Advanced Persistent Threat Profile: Lazarus](https://eurepoc.eu/wp-content/uploads/2024/02/Advanced-Persistent-Threat-Profile-Lazarus-February-2024.pdf)
- [Radar/OffSeq: Comebacker Aerospace Campaign 2025](https://radar.offseq.com/threat/lazarus-group-targets-aerospace-and-defense-with-n-e1ff8123)

---

*Last Updated: 2026-04-01 | Profile by C3PO*
