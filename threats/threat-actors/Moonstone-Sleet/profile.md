# Threat Actor Profile: Moonstone Sleet (Storm-1789)

## Identity

| Field | Detail |
|---|---|
| **Scoundrel #** | 034 |
| **Primary Name** | Moonstone Sleet |
| **Former Tracking Name** | Storm-1789 |
| **Type** | State-Sponsored — Dual-Mission (Espionage + Revenue Generation) |
| **Origin** | North Korea (DPRK) — assessed state-aligned |
| **Active Since** | Early 2023 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **Mandate** | **Dual:** Intelligence collection AND revenue generation — rare combination |
| **Primary Sectors Targeted** | **Defense Industrial Base (DIB)**, Aerospace, Software/IT, Education |
| **Ransomware** | FakePenny — custom; $6.6M BTC demand (first observed April 2024) |
| **Microsoft Attribution** | May 28, 2024 (initial); active intelligence updates through 2025–2026 |
| **Previous Code Overlap** | Diamond Sleet (Lazarus Group sub-cluster) — now operates independently |
| **Admiralty Grade** | A1 — confirmed by Microsoft Threat Intelligence; CFR cyber operations tracker |
| **ATT&CK** | T1566.002, T1195.002, T1204.002, T1059, T1003.001, T1486, T1585, T1591, T1078, T1057 |

---

## Overview

Moonstone Sleet is the most operationally inventive North Korean threat actor profiled in this repository — and the most directly relevant to aerospace and defense targets. Microsoft confirmed in May 2024 that Moonstone Sleet **deployed ransomware against a defense technology company** and **compromised both a drone technology firm and an aircraft parts manufacturer** — all within a four-month window. This is not a peripheral A&D risk. This actor has specifically targeted the defense industrial base with espionage followed by ransomware.

What makes Moonstone Sleet genuinely unusual — even within the DPRK cyber program — is its **dual mandate**: collecting intelligence *and* generating revenue from the same operations. It begins with long-duration espionage access, collects credentials and intellectual property, and then — when the target is of sufficient value — deploys FakePenny ransomware and demands millions in Bitcoin. The same compromise serves two missions.

**The DeTankWar gambit:** In February 2024, Moonstone Sleet launched what may be the most creative initial access lure in the history of nation-state cyber operations: a fully functional, professionally developed tank battle video game. The game — DeTankWar — had a working download site, player registration, a public Twitter presence, and game mechanics. It also silently loaded a custom malware loader called YouieLoad when launched. Moonstone Sleet distributed it by posing as a blockchain startup seeking developer partnerships.

**The fake company ecosystem:** Moonstone Sleet builds complete, operational fake companies — with websites, employee personas, social media presence, and email campaigns — to approach targets. It has operated at least two documented fake companies (StarGlow Ventures and C.C. Waterfall), runs malicious npm packages as "skills tests," sends trojanized developer tools via LinkedIn, and pursues legitimate employment at software companies.

**The lineage:** When first observed in 2023, Moonstone Sleet extensively reused Diamond Sleet (Lazarus Group sub-cluster) code and techniques. It has since shifted to bespoke infrastructure — but the organizational capability and tradecraft clearly descend from Lazarus Group's multi-decade operational experience.

---

## Attack Campaigns

### Campaign 1 — Trojanized PuTTY (August 2023)
**Target:** Software developers and IT professionals via LinkedIn, Telegram, and freelancing platforms

Moonstone Sleet delivers a ZIP archive containing two files:
- `putty.exe` — trojanized version of the legitimate PuTTY terminal emulator
- `url.txt` — contains an IP address and password

If the target enters the provided IP and password into the PuTTY application, it decrypts an embedded payload and begins a multi-stage infection chain:

```
Stage 1 — Trojanized PuTTY
  Decrypts and executes Stage 2 payload
       ↓
Stage 2 — SplitLoader installer/dropper
  Writes SplitLoader DLL to disk
  Drops two encrypted files
  Executes SplitLoader via scheduled task or registry Run key
       ↓
Stage 3 — SplitLoader
  Decrypts and combines the two dropped files → creates next-stage PE
       ↓
Stage 4 — Trojan loader
  Awaits compressed/encrypted PE from C2
  Decompresses, decrypts, and executes final payload
```

---

### Campaign 2 — Malicious npm Packages
**Target:** Software developers via freelancing websites, LinkedIn

Moonstone Sleet delivers malicious npm packages inside fake "technical skills assessment" ZIP files, sent via fake companies. When the malicious package loads, it uses `curl` to connect to an actor-controlled IP and drops additional payloads including:
- SplitLoader
- LSASS credential theft tools

Microsoft collaborated with GitHub to identify and remove associated repositories.

---

### Campaign 3 — DeTankWar / The Malicious Game (February 2024+)
**Target:** Blockchain companies, software developers, gaming/crypto communities

The most audacious delivery mechanism documented in this repository. Moonstone Sleet built a **fully functional downloadable tank battle game** — DeTankWar (also known as DeFiTankWar, DeTankZone, TankWarsZone) — complete with:
- Working game mechanics (clone/reimagining of DeFiTankLand)
- Player registration system (username, password, invite code)
- Public-facing website: `detankwar[.]com` and `defitankzone[.]com`
- Multiple X (Twitter) personas for the game and the fake company behind it
- Professional-grade marketing materials

**Distribution:** Moonstone Sleet approached targets via messaging platforms and email, posing as a game developer seeking investment or collaboration. Used fake company **C.C. Waterfall** as the contact identity.

**Infection:** When the target launches `delfi-tank-unity.exe`, malicious DLLs bundled with the game are loaded. These drop **YouieLoad** — a custom malware loader that:
- Loads malicious payloads into memory (fileless)
- Creates malicious Windows services
- Performs network and user discovery
- Collects browser data
- For high-value compromised devices: enables hands-on-keyboard follow-on operations and credential theft

---

### Campaign 4 — FakePenny Ransomware (April 2024+)
**Target:** Organizations previously compromised via other Moonstone Sleet campaigns

Two months after the initial compromise of a defense technology company (December 2023), Moonstone Sleet deployed a custom ransomware variant: **FakePenny**.

| Attribute | Detail |
|---|---|
| **Payload** | Loader + encryptor components |
| **Encryption** | File-level encryption |
| **Ransom demand** | **$6.6 million USD in Bitcoin** |
| **Ransom note** | Closely overlaps with **NotPetya's ransom note** (Seashell Blizzard / Sandworm) |
| **Context** | Deployed against defense technology company after espionage phase |

**Why FakePenny is significant:**
- **First confirmed Moonstone Sleet ransomware deployment** — indicates expanding capability
- **$6.6M demand** is dramatically higher than all previous DPRK ransomware ($100K range: WannaCry, H0lyGhost)
- **NotPetya-matching ransom note** — either deliberate false flag (pointing to Russia/Sandworm) or shared tradecraft with Seashell Blizzard
- Deployed **after** intelligence collection, maximizing both espionage value and extortion potential from the same access

---

### Campaign 5 — Fake Companies (Ongoing since January 2024)

Moonstone Sleet operates at least two fully built fake company identities:

**StarGlow Ventures** (January–April 2024)
- Posing as legitimate software development company
- Email campaigns targeting thousands of organizations in education and software sectors
- Emails contained: compliments on the target's work, collaboration/support offers, **1×1 tracking pixel** to log which targets engaged
- Link to dummy unsubscribe page on StarGlow Ventures domain
- Purpose: Relationship establishment and target mapping

**C.C. Waterfall** (Ongoing)
- Posing as IT consulting firm
- Targeted higher education; claimed to be hiring developers or seeking collaboration
- Also used to distribute DeTankWar game (dual-purpose campaign vehicle)
- Combination of created websites, fake employee personas, and social media accounts

---

### Campaign 6 — IT Worker Infiltration
Moonstone Sleet has pursued legitimate software development employment at multiple companies — consistent with Jasper Sleet's (#032) documented IT worker scheme. In Moonstone Sleet's case, Microsoft notes this may serve both revenue generation AND gaining direct insider access for espionage purposes.

---

## Confirmed Defense Sector Targeting

| Target | Sector | Timeline | Method | Impact |
|---|---|---|---|---|
| Defense technology company | DIB | Dec 2023 (access) → Apr 2024 (ransomware) | Unknown initial vector | Credentials + IP stolen; FakePenny ransomware deployed; $6.6M demand |
| Drone technology company | Aerospace/DIB | April 2024 | DeTankWar / social engineering | Compromise confirmed |
| Aircraft parts manufacturer | Aerospace/DIB | May 2024 | Unknown | Compromise confirmed |

**This actor has specifically targeted A&D.** Three confirmed DIB/aerospace compromises in a five-month window.

---

## Malware Inventory

| Malware | Type | Notes |
|---|---|---|
| **SplitLoader** | Multi-stage loader | Stage 1–3 in trojanized PuTTY chain; drops encrypted payloads; executes via scheduled task or registry Run key |
| **YouieLoad** | Custom malware loader | DeTankWar game payload; loads payloads in memory (fileless); creates malicious services; performs network/user discovery and browser data collection |
| **FakePenny** | Custom ransomware | Loader + encryptor; $6.6M BTC demand; NotPetya-matching ransom note |
| **Comebacker** (reused) | Backdoor/loader | Originally Diamond Sleet malware; reused early in Moonstone Sleet operations |

---

## Microsoft Defender Detections

| Detection Name | Component |
|---|---|
| `Behavior:Win64/PennyCrypt` | FakePenny ransomware behavior |
| `TrojanDropper:Win32/SplitLoader` | SplitLoader Stage 2/3 dropper |
| `TrojanDropper:Win64/YouieLoad` | YouieLoad game-delivered loader |
| `HackTool:Win32/Mimikatz` | Credential theft post-compromise |
| `Ransomware-linked threat actor detected` | Behavioral — Moonstone Sleet activity |
| `Suspicious access to LSASS service` | Credential dumping activity |
| `Malicious credential theft tool execution detected` | Post-compromise credential theft |

---

## Threat Connections

🔗 **Connects to Scoundrel #003 — Lazarus Group (DPRK)**
Moonstone Sleet originated with strong overlaps with Diamond Sleet — a Lazarus Group sub-cluster. It extensively reused Comebacker malware code and Diamond Sleet techniques before shifting to independent infrastructure. The organizational capability, tool inheritance, and state alignment trace directly back to the Lazarus Group umbrella.

🔗 **Connects to Scoundrel #032 — Jasper Sleet**
Both actors use employment fraud and fake company tactics to gain access. Moonstone Sleet specifically pursues IT worker infiltration at software companies — the same scheme Jasper Sleet runs at scale. Microsoft notes the overlap explicitly, suggesting possible shared tooling or direction within the DPRK cyber program.

🔗 **Connects to Scoundrel #023 — Kimsuky (APT43)**
Both operate within the DPRK intelligence collection mission. Kimsuky focuses on government/think tanks/nuclear; Moonstone Sleet focuses on defense industrial base and software supply chain. Parallel tracks under the same state mandate.

🔗 **Connects to Scoundrel #002 — Stardust Chollima**
DPRK financial operations — Stardust Chollima and Moonstone Sleet both generate revenue for the regime, though via different methods (crypto heists vs. ransomware + IT worker fraud).

---

## MITRE ATT&CK Mapping

| Phase | Technique | ID |
|---|---|---|
| Resource Development | Establish fake company accounts (websites, social media, personas) | T1585.001 |
| Resource Development | Develop malicious npm packages and tools | T1587.001 |
| Initial Access | Spearphishing link (DeTankWar game download, PuTTY ZIP) | T1566.002 |
| Initial Access | Compromise software supply chain (malicious npm) | T1195.002 |
| Initial Access | Malicious file execution (user launches game/tool) | T1204.002 |
| Initial Access | Valid accounts (IT worker fraud) | T1078 |
| Execution | Scheduled task (SplitLoader persistence) | T1053.005 |
| Persistence | Registry Run key (SplitLoader persistence) | T1547.001 |
| Persistence | Create malicious Windows services (YouieLoad) | T1543.003 |
| Discovery | Network and user discovery (YouieLoad services) | T1018, T1057 |
| Credential Access | LSASS memory dump (npm package campaign) | T1003.001 |
| Collection | Browser data collection (YouieLoad) | T1185 |
| Impact | Data encrypted for impact (FakePenny) | T1486 |

---

## A&D Relevance

**CRITICAL — Direct confirmed targeting of defense industrial base.**

Moonstone Sleet is the only threat actor in this repository with **confirmed, specific attacks against defense technology companies, drone manufacturers, and aircraft parts suppliers** in its documented history. This is not theoretical sector risk — it is documented, confirmed compromise.

The dual espionage + ransomware mandate is particularly dangerous for A&D:
1. The espionage phase collects classified designs, credentials, personnel data, and program information
2. The ransomware phase disrupts operations and demands payment
3. Both phases may occur simultaneously — the organization is being robbed of its IP while also being extorted

**For defense contractors:** Any software developers receiving job offers, game collaboration requests, or technical assessment packages from unknown blockchain companies should be treated as potential Moonstone Sleet lures.

**For security teams:** The YouieLoad fileless loader and SplitLoader multi-stage chain are designed to evade signature-based detection. Behavioral monitoring for unauthorized service creation, LSASS access, and fileless payload execution are the detection priorities.

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Train developers to treat all unsolicited npm packages, game downloads, and technical assessments as attack vectors** — Moonstone Sleet specifically targets developers |
| 🔴 HIGH | **Sandbox all external code and software** before execution on developer machines — including "skills tests" from unknown parties |
| 🔴 HIGH | **Alert on LSASS memory access** outside approved security tools |
| 🔴 HIGH | **Monitor for unauthorized scheduled task or Windows service creation** — SplitLoader and YouieLoad both create persistence artifacts |
| 🟡 HIGH | **Review npm package supply in CI/CD pipelines** — malicious packages with legitimate names are Moonstone Sleet's signature delivery |
| 🟡 HIGH | **Block execution of unsigned DLLs** from game/media application directories via WDAC/AppLocker |
| 🟡 MEDIUM | **Employee training on fake company solicitations** — block tracking pixels; verify company legitimacy before engaging with email campaigns |
| 🟡 MEDIUM | **Screen job applicants** for IT and software roles against DPRK IT worker indicators (overlaps with Jasper Sleet #032 profile) |

---

## References

- [Microsoft Threat Intelligence — Moonstone Sleet emerges as new North Korean threat actor (May 28, 2024)](https://www.microsoft.com/en-us/security/blog/2024/05/28/moonstone-sleet-emerges-as-new-north-korean-threat-actor-with-new-bag-of-tricks/)
- [Help Net Security — Moonstone Sleet: A new North Korean threat actor (May 29, 2024)](https://www.helpnetsecurity.com/2024/05/29/moonstone-sleet-north-korean-threat-actor/)
- [CFR Cyber Operations Tracker — Moonstone Sleet](https://www.cfr.org/cyber-operations/moonstone-sleet)
- [Rewterz — Microsoft Attributes FakePenny Ransomware with Moonstone Sleet (2024)](https://www.rewterz.com/threat-advisory/microsoft-attributes-new-fakepenny-ransomware-with-north-korean-threat-actors-active-iocs)

---

*Profile created: 2026-05-03 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
