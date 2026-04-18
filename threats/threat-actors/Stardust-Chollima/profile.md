# 🇰🇵 Threat Actor Profile: Stardust Chollima

**TLP:** WHITE  
**Admiralty Grade:** A2  
**Status:** 🔴 Active  
**Last Updated:** 2026-03-31  
**Profile Author:** C3PO Intel Platform  

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | Stardust Chollima |
| **Aliases** | BlueNoroff, APT38, BeagleBoyz, TA444, NICKEL GLADSTONE, BlackAlicanto, Copernicium, Sapphire Sleet |
| **Nation-State Nexus** | Democratic People's Republic of Korea (DPRK) |
| **Parent Organization** | Bureau 121, Reconnaissance General Bureau (RGB) |
| **Relationship** | Subgroup / financial arm of Lazarus Group |
| **Active Since** | At least 2015 (possibly earlier under Lazarus umbrella) |
| **Motivation** | Financial — regime currency generation; sanctions evasion for DPRK weapons programs |
| **Actor Type** | Nation-state sponsored / financially motivated APT |
| **Classification** | CrowdStrike: Targeted Intrusion Adversary |

---

## Strategic Context

Stardust Chollima exists for one purpose: **generate hard currency for the DPRK regime.** International sanctions cut North Korea off from SWIFT in 2017 and constrained legitimate financial channels. In response, Bureau 121 tasked this unit with funding the regime through large-scale financial cyber theft — primarily targeting banks, crypto exchanges, and fintech companies.

Assessed by CrowdStrike as likely representing an element of Bureau 121 based on U.S. government public disclosures. Shares development resources (and possibly personnel) with other Chollima-cluster actors, evidenced by shared tooling frameworks and similar compilation artifacts (notably Microsoft Visual Studio 6 — used across WannaCry, Hawup, and TwoPence artifacts).

---

## Targeting

**Primary focus:** Financial theft — not espionage, not destruction. Every operation is oriented around stealing money or cryptocurrency to fund the regime.

### Target Sectors
- Banks and financial institutions (SWIFT endpoints)
- Cryptocurrency exchanges and DeFi platforms
- Web3 / blockchain companies
- Fintech startups
- Casinos and online gambling operators
- ATM networks
- Venture capital firms (impersonated for luring targets)

### Geographic Focus
- South Korea, Japan, United States, Canada, Western Europe
- Latin America (targeting since at least mid-2017)
- Global — opportunistic where crypto/fintech infrastructure exists

### Notable Victim Targeting Logic
- Prefers **small-to-medium crypto startups** — less security investment, high-value wallets
- Targets employees with access to crypto wallets (especially MetaMask browser extension users)
- Actively hunts **macOS users** in finance/crypto — significant shift from Windows-only operations

---

## Campaigns & Notable Operations

### SnatchCrypto (2021–2022)
Targeted crypto startups globally by impersonating venture capital firms. Sent macro-enabled Word documents or malicious ZIP files disguised as contracts/business files. Post-compromise: keylogger + screenshot monitoring for weeks/months, then replaced MetaMask Chrome extension with malicious version to intercept and redirect outgoing crypto transactions at the moment of transfer — draining wallets in a single click.

### RustBucket Campaign (2023)
macOS-focused campaign deploying the RustBucket backdoor. Used malicious PDF viewer lures and ISO/VHD file delivery for initial malware execution. Targeted finance and cryptocurrency sectors.

### SpectralBlur Backdoor (Late 2023 – Early 2024)
Deployed SpectralBlur, a macOS backdoor with data exfiltration and remote execution capability. Continued active use through early 2024.

### Hidden Risk Campaign (2024)
Phishing emails delivering fake PDF payloads to compromise Web3 professionals. Impersonated known external contacts to establish trust before delivering payloads.

### NimDoor Campaign (2024)
Used fake Zoom SDK updates to deliver NimDoor RAT — highlighting evolving macOS-centric delivery mechanisms and exploitation of trusted software update channels.

### Deepfake Zoom Campaign (March–June 2025)
**Most sophisticated social engineering evolution to date.** Active since at least March 2025. Two documented incidents (Huntress + Field Effect research):

**Incident 1 — Canadian Gambling Firm (May 2025):**
Attacker impersonated a known external contact, set up a Zoom call. During the call, victim experienced "audio issues" and pop-ups. Attacker convinced victim to run a "Zoom audio repair" AppleScript — malicious script with dangerous commands buried below blank lines. Script connected to `zoom-tech[.]us` (registered April 14, 2025), downloaded second-stage payload via curl/zsh. Credential prompt then exfiltrated locally stored credentials before cleaning traces.

**Incident 2 — Crypto Foundation:**
Attacker sent Calendly link for Google Meet; URL redirected to attacker-controlled fake Zoom domain. Weeks later, victim joined a group Zoom call populated with **deepfakes of known senior leadership and external contacts**. Victim couldn't unmute; deepfakes directed them to download a "required Zoom extension." Payload included macOS malware with multiple implants, AppleScript-based keylogger, screen capture, and clipboard harvesting. Specifically searched for crypto wallets on the system.

---

## Malware & Tooling

| Tool | Platform | Purpose |
|---|---|---|
| **TwoPence Framework** | Windows | Core implant development framework — multiple shared implants; code-protected with Enigma Protector |
| **RustBucket** | macOS | Backdoor — initial access + persistence; Rust-based |
| **ObjCShellz** | macOS | Shell command execution backdoor; Objective-C |
| **SpectralBlur** | macOS | Full-featured backdoor — file ops, shell exec, C2 comms |
| **NimDoor RAT** | macOS | Remote access trojan delivered via fake Zoom SDK updates |
| **Hawup** | Windows | Shared framework with LABYRINTH CHOLLIMA lineage |
| **Hermes Ransomware** | Windows | Used in SWIFT heist operations (FEIB, Oct 2017) — attributed by open-source |
| **Custom MetaMask replacement** | Chrome | Malicious browser extension — intercepts and redirects crypto transactions |
| **AppleScript payloads** | macOS | Social engineering delivery; obfuscated with blank lines |

### Code Artifact Note
Multiple Stardust Chollima tools, WannaCry samples, and Hawup variants compiled with **Microsoft Visual Studio 6** — extremely rare nearly 20 years post-release. Suggests shared development environment or code library across DPRK Chollima-cluster actors.

---

## TTPs — MITRE ATT&CK Mapping

### Initial Access
| Technique | ID | Notes |
|---|---|---|
| Phishing: Spearphishing Attachment | T1566.001 | Macro-enabled Word docs, ISO/VHD files, fake contracts |
| Phishing: Spearphishing Link | T1566.002 | Calendly/Google Meet lures → fake Zoom domains |
| Drive-by Compromise | T1189 | Strategic web compromises targeting banking networks |

### Execution
| Technique | ID | Notes |
|---|---|---|
| Command and Scripting Interpreter: AppleScript | T1059.002 | Core delivery mechanism in 2025 Zoom campaigns |
| User Execution: Malicious File | T1204.002 | Fake Zoom extensions, malicious audio repair tools |
| Exploitation for Client Execution | T1203 | PDF viewer exploits |

### Persistence
| Technique | ID | Notes |
|---|---|---|
| Browser Extensions | T1176 | Malicious MetaMask replacement |
| Boot/Logon Autostart | T1547 | Multiple persistence mechanisms across platforms |

### Defense Evasion
| Technique | ID | Notes |
|---|---|---|
| Obfuscated Files or Information | T1027 | Enigma Protector on Windows tools; blank-line obfuscation in AppleScript |
| Indicator Removal: Clear Command History | T1070.003 | Session history erasure post-compromise |
| Masquerading | T1036 | Fake Zoom domains, VC firm impersonation, deepfake video |

### Credential Access
| Technique | ID | Notes |
|---|---|---|
| Input Capture: Keylogging | T1056.001 | Keylogger deployed post-compromise for extended surveillance |
| Credentials from Password Stores | T1555 | Browser credential harvesting |

### Collection
| Technique | ID | Notes |
|---|---|---|
| Screen Capture | T1113 | Screenshot collection during recon phase |
| Clipboard Data | T1115 | Clipboard harvesting in 2025 Zoom campaign |
| Data from Local System | T1005 | Crypto wallet file hunting |

### Impact
| Technique | ID | Notes |
|---|---|---|
| Financial Theft | T1657 | Primary mission — crypto wallet draining, SWIFT fraud |
| Resource Hijacking | T1496 | In some operations |

---

## Infrastructure & OpSec

- Uses **typosquatted and newly registered domains** impersonating Zoom, financial institutions, and VC firms
- Registered `zoom-tech[.]us` April 14, 2025 — used in May 2025 attacks
- Multi-stage payload delivery (AppleScript → curl → zsh → second stage) to avoid detection
- **Password-protected executables** and **secure deletion functions** for operational cleanup
- Extended dwell time — monitors victims for weeks/months before executing financial theft
- Uses **deepfake video technology** in live video calls (2025+ campaigns)
- Shares infrastructure elements with other DPRK Chollima-cluster actors

---

## Relationship to Other DPRK Actors

| Actor | Relationship |
|---|---|
| Lazarus Group | Parent umbrella; shared resources and development |
| LABYRINTH CHOLLIMA | Shares Hawup framework lineage; technical overlap |
| SILENT CHOLLIMA | Earlier attempts at revenue generation; no direct tooling overlap identified |
| RICOCHET CHOLLIMA | Possible shared code library (VS6 compiler artifact) |
| GOLDEN CHOLLIMA / PRESSURE CHOLLIMA | Related financially-motivated subgroups split from LABYRINTH CHOLLIMA |
| WannaCry | Technical overlap with TwoPence/Hawup; shared development resources assessed |

---

## Aerospace & Defense Relevance

**Relevance: LOW-MODERATE (Indirect)**

Stardust Chollima is not known to directly target aerospace or defense firms. However:
- DPRK weapons programs are the **end beneficiary** of stolen funds — every successful theft potentially funds missile and nuclear development
- Defense contractors with crypto/fintech holdings or partnerships are potential secondary targets
- The deepfake Zoom technique is **sector-agnostic** — any executive-level target is vulnerable
- Any company with significant cryptocurrency treasury holdings could be targeted regardless of sector

---

## Defensive Priorities

1. **macOS endpoint security** — most current campaigns target macOS users specifically; ensure robust macOS EDR coverage
2. **Verify Zoom domains** before running any scripts from a call — legitimate Zoom will never ask you to run an AppleScript
3. **Strict browser extension controls** — audit Chrome/Firefox extensions regularly; monitor for MetaMask or wallet extensions installed from local directory (Developer Mode = red flag)
4. **Block ISO/VHD file delivery via email** — common delivery vector
5. **Deepfake awareness training** — if someone on a video call can't be heard but keeps directing you to download something, hang up and call them directly on a known number
6. **Domain monitoring** — watch for newly registered domains mimicking Zoom, VC firms, or your own brand
7. **Crypto wallet protection** — hardware wallets, strict extension auditing, transaction verification via separate channel

---

## Intelligence Sources

| Source | URL | Date |
|---|---|---|
| CrowdStrike — Adversary of the Month | https://www.crowdstrike.com/en-us/blog/meet-crowdstrikes-adversary-of-the-month-for-april-stardust-chollima/ | 2018 (original profile) |
| CrowdStrike — Adversary Directory | https://www.crowdstrike.com/adversaries/stardust-chollima/ | 2025 |
| Huntress — Threat Library | https://www.huntress.com/threat-library/threat-actors/stardust-chollima | 2026-01-01 |
| Deepwatch — Deepfake Zoom Analysis | https://www.deepwatch.com/labs/cyber-intel-brief-june-19-25-2025/ | 2025-07-08 |
| Security Boulevard — Deepfake Zoom Campaign | https://securityboulevard.com/2025/06/n-korean-group-bluenoroff-uses-deepfake-zoom-calls-in-crypto-scams/ | 2025-06-26 |
| PolySwarm — 2024 DPRK Recap | https://blog.polyswarm.io/2024-recap-north-korean-threat-actor-activity | 2024-12-13 |
| Kaspersky — SnatchCrypto Analysis | https://www.kaspersky.com/about/press-releases/snatch-that-crypto-bluenoroff-threat-actor-drains-cryptocurrency-startups-accounts | 2022 |
| SecurityBrief — CrowdStrike LABYRINTH Split | https://securitybrief.com.au/story/crowdstrike-splits-labyrinth-chollima-into-three-units | 2026-01-30 |
| Malpedia — Actor Entry | https://malpedia.caad.fkie.fraunhofer.de/actor/stardust_chollima | Reference |
