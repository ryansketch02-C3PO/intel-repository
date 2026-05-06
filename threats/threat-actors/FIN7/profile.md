# FIN7 — Sangria Tempest / Carbon Spider / Navigator Group

## Identity

| Field | Details |
|---|---|
| **Name** | FIN7 |
| **Microsoft Tracking** | Sangria Tempest |
| **CrowdStrike Tracking** | Carbon Spider |
| **Other Aliases** | Navigator Group · ITG14 · ELBRUS · Carbanak Group |
| **Type** | Cybercriminal — Multi-Faceted Criminal Enterprise (Financial crime → RaaS → Tool vendor → IAB) |
| **Origin** | Russia / CIS (Russian-speaking operators) |
| **Active Since** | 2013 |
| **Current Status** | 🔴 **ACTIVE** — Resumed operations April 2024 after ~1-year hiatus; current Cl0p partnership active |
| **Threat Level** | 🔴 HIGH |
| **Primary Motivation** | Financial — all activity generates revenue, directly or indirectly |
| **Total Estimated Theft** | **$1.2 billion+** (financial institutions, retailers, hospitality sector combined) |
| **Sector Focus** | Finance · Healthcare · Hospitality · Retail · Technology · Defense (expanding) |
| **Key Arrests** | Dmytro Fedorov (arrested Ukraine, 2018); Fedir Hladyr (sentenced 10 years, US, 2021); Andrii Kolpakov (sentenced 7 years, US, 2022) — group continues operating |
| **Admiralty Grade** | A1 — US DOJ indictments; Microsoft, Mandiant, CrowdStrike all confirmed |
| **ATT&CK** | T1566 (Phishing) · T1190 (Exploit Public-Facing App) · T1059 (Scripting) · T1486 (Ransomware) · T1657 (Financial Theft) · T1583 (Acquire Infrastructure) |

---

## Overview

FIN7 is the most financially successful and longest-running cybercriminal organization in the modern threat landscape. Over 12 years of continuous operations, they have evolved from a tight point-of-sale theft crew to a full-spectrum criminal enterprise that runs ransomware brands, provides initial access to other groups, recruits talent through fake companies, and sells tools on dark web forums to threat actors across the board.

The US DOJ declared FIN7 "an entity no more" in May 2023 after a string of key arrests. In April 2024, FIN7 resumed operations. The declaration was premature.

**The three things that make FIN7 genuinely different from every other criminal group on this board:**

1. **They invented the fake cybersecurity company.** In 2017–2018, FIN7 created Combi Security — a fully operational fake penetration testing firm with a real website, real job postings, and real employee LinkedIn profiles — to recruit skilled developers and pentesters without revealing they were criminals. Jasper Sleet (#032) runs the same playbook today. FIN7 wrote it.

2. **They seeded the entire Russian ransomware ecosystem.** FIN7 operators or associates helped build the technical infrastructure for DarkSide → BlackMatter → BlackCat/ALPHV. The lineage of some of the most destructive ransomware in history runs through FIN7.

3. **They sell to their competitors.** FIN7 currently operates as a tool vendor on dark web forums, selling their AvNeutralizer EDR bypass tool to other criminal groups. They have weaponized their tradecraft surplus into a revenue line.

---

## Evolution Timeline

### Phase 1 (2013–2016) — Financial Institution Targeting
FIN7 (then known by the CARBANAK malware they used) targeted banks globally via spearphishing, using the CARBANAK trojan to reach and manipulate ATM jackpotting, SWIFT payment systems, and card processing infrastructure. Estimated $1 billion stolen from 100+ financial institutions across 40 countries during this period alone.

### Phase 2 (2016–2018) — POS Pivot and US Retail/Hospitality
Pivoted to point-of-sale system targeting at US restaurant chains, hotels, and retailers. Victims included Chipotle, Chili's, Arby's, Red Robin, Saks Fifth Avenue, Lord & Taylor, Jason's Deli, and hundreds more. Used spearphishing with weaponized Word documents to compromise corporate email accounts, then moved laterally to payment systems.

Created **Combi Security** — a fake Israeli cybersecurity firm — to recruit IT professionals. Employees performed real work initially, then were tasked with malicious intrusions without knowing the full picture.

### Phase 3 (2018–2021) — Arrests and Diversification
Key members arrested (Hladyr, Fedorov, Kolpakov). Group does not collapse — remaining members adapt. Operations shift toward broader enterprise targeting and ransomware. FIN7 operators implicated in the development of:
- **DarkSide** — ransomware platform that hit Colonial Pipeline
- **BlackMatter** — DarkSide's successor
- **BlackCat/ALPHV** — BlackMatter's successor; suspected FIN7 developer involvement in early builds

### Phase 4 (2021–2023) — RaaS and Defense Targeting
Pivoted to enterprise ransomware and targeted defense companies specifically:
- Microsoft (October 2023): FIN7 sent malicious USB drives disguised as fake gifts to US defense contractors — the drives auto-ran POWERTRASH malware when plugged in
- Deployed LIZAR (aka Diceloader) backdoor and custom Sardonic implant
- Operated the **Cl0p** supply chain attacks on Accellion, GoAnywhere (earlier operations), and MOVEit adjacent campaigns

US DOJ May 2023: declares FIN7 "an entity no more" after multiple prosecutions.

### Phase 5 (2024–Present) — Resurrection as Multi-Function Criminal Platform
April 2024: FIN7 resumes active operations. New business model is notably diversified:
- **Malvertising campaigns** — trojanized legitimate application downloads (Notepad++, VS Code, Signal) deliver FIN7 trojans; victims appear on Cl0p DLS 2–3 months later
- **Cl0p partnership** — FIN7 serves as initial access broker; their trojan communicates from victim environments prior to Cl0p ransomware deployment
- **AvNeutralizer sales** — FIN7 developed and is now actively selling their proprietary EDR bypass tool on dark web forums. Any group can buy it. Many have.
- **4,000+ phishing infrastructure domains** discovered by Silent Push (2024) — targeting Louvre, Meta, Reuters, and major enterprises globally in credential harvesting and malware campaigns

---

## Technical Arsenal

### CARBANAK (Original)
The flagship trojan. First observed 2013. PowerShell-based C2 framework with RAT capabilities. Used for financial institution fraud: video recording of banking operations, SWIFT manipulation, ATM jackpotting. Still in use by actors who bought/leaked versions.

### POWERTRASH
PowerShell-based fileless loader — drops payloads entirely in memory without touching disk. Used in the USB drive campaign against US defense contractors (2023). Deployed LIZAR as second-stage payload.

### LIZAR / Diceloader
Post-exploitation modular backdoor. Plugin architecture allows operators to load capabilities on demand: credential theft, network reconnaissance, lateral movement. Communicates over HTTPS with domain-fronting for C2 obfuscation.

### Sardonic
Custom C++ backdoor first documented by BitDefender in 2021. Uses a plugin system similar to LIZAR. Designed for stealth — uses legitimate Windows APIs, blends into normal process trees, minimal disk footprint. Associated with FIN7's higher-value targeted intrusions.

### DOMINO (FIN7 + FIN8 overlap)
Backdoor identified by IBM X-Force (2023). Two components: DOMINO Backdoor (full-featured) and DOMINO Loader (dropper). Assessed to have been developed as part of Black Basta infrastructure, creating a confirmed link between FIN7 developers and the Black Basta operation.

### AvNeutralizer (EDR Killer — now for sale)
FIN7's custom EDR bypass and evasion tool, developed for internal use. As of 2024, FIN7 is selling AvNeutralizer on dark web forums. The tool is purpose-built to defeat modern endpoint detection and response platforms. Any group with the funds to buy it now has access to FIN7's years of EDR evasion R&D.

The sale of AvNeutralizer is strategically significant: it means FIN7's capabilities are no longer exclusive to FIN7. The EDR evasion tradecraft they developed through years of operations is now available to any sufficiently funded criminal operator.

### Malvertising Infrastructure (2024)
Current operational focus. FIN7 buys advertising space on legitimate ad networks to serve malicious ads that redirect users to trojanized download pages. The download pages are convincing replicas of legitimate software sites (Notepad++, 7-Zip, Signal, VS Code, etc.). Victims download what appears to be legitimate software; the installer deploys FIN7's trojan silently alongside the real application.

---

## Combi Security — The Original Fake Company Playbook

In 2017–2018, before Jasper Sleet, before North Korean IT workers, before any of the "fake company" tradecraft that now defines multiple threat actors on this board, FIN7 built **Combi Security**.

Combi Security had:
- A professional website at `combisecurity[.]com`
- Legitimate-looking job postings on Indeed, Glassdoor, and LinkedIn
- "Pen testing" and "cybersecurity consulting" as its stated business
- Real employee LinkedIn profiles with constructed work histories

FIN7 used Combi Security to recruit developers and pentesters across Russia, Ukraine, and Eastern Europe. Recruits were initially given genuine cybersecurity tasks. Once comfortable, tasks shifted toward criminal intrusions — lateral movement, data theft, system compromise — often without full awareness that they were committing crimes for a criminal organization rather than testing client networks.

Several Combi Security employees testified in US court proceedings. Their accounts revealed a structured corporate hierarchy — managers, team leads, developers, and operators — operating with the discipline of a legitimate technology company while executing criminal campaigns.

The lesson FIN7 taught: skilled criminal organizations don't just recruit criminals. They recruit talent from the legitimate market and bring it into the criminal world through a plausible organizational structure.

---

## Ransomware Lineage

FIN7's fingerprints are on the most destructive ransomware chain in history:

```
FIN7 (2013–present)
    │
    ├─→ DarkSide (2020–2021)
    │       │ [FIN7 developers / operators assessed as involved in early DarkSide builds]
    │       └─→ Colonial Pipeline attack (2021)
    │
    ├─→ BlackMatter (2021–2021)
    │       │ [DarkSide rebrand; same development chain]
    │       └─→ Disrupted by REvil arrests/pressure; operators migrate
    │
    ├─→ BlackCat / ALPHV (2021–2024)
    │       │ [Suspected FIN7 developer involvement in ALPHV build]
    │       └─→ Change Healthcare ($22M); exited March 2024
    │
    ├─→ Black Basta (2022–2025)
    │       │ [DOMINO backdoor links FIN7 developers to Black Basta infrastructure]
    │       └─→ Likely Conti diaspora + FIN7 technical contributors
    │
    └─→ Cl0p Partnership (2024–present)
            [FIN7 malvertising trojan → 2-3 month dwell → Cl0p ransomware deployment]
```

FIN7 is not just a threat actor. It is a **development studio and talent pool** that has fed capabilities into the broader ransomware ecosystem for over a decade.

---

## Defense Sector Targeting

FIN7's 2023 USB drive campaign is the clearest evidence of explicit defense industrial base targeting:

**Operation: USB Gifts (2023)**
- FIN7 mailed USB drives disguised as packages from Amazon and the US Department of Health and Human Services
- Drives contained "thank you gifts" — sometimes a fake gift card, sometimes printed materials
- When plugged in: drives auto-ran scripts via AUTORUN or social engineering to deploy POWERTRASH → LIZAR
- Targets included US defense contractors (confirmed by Microsoft October 2023 advisory)
- Attack chain: USB insertion → fileless PowerShell loader → LIZAR backdoor → hands-on-keyboard follow-on

**Current Defense Relevance:**
- Defense contractors are now explicitly in FIN7's targeting scope
- FIN7's Cl0p partnership means any FIN7 malvertising victim in the defense sector is a potential Cl0p ransomware pre-cursor
- AvNeutralizer sales mean defense-targeting groups can now buy FIN7's EDR evasion capability

---

## A&D / Enterprise Relevance

**Relevance: HIGH — Direct targeting confirmed.**

- **USB-based initial access** is specifically relevant to defense environments where employees may receive unsolicited packages and plug drives into work systems
- **Defense contractors explicitly targeted** in Microsoft's October 2023 advisory on POWERTRASH campaigns
- **Cl0p pre-positioning** — FIN7's malvertising trojan in the environment is an early indicator of a future Cl0p attack; dwell time is 2–3 months
- **AvNeutralizer availability** — EDR evasion tools now for sale on dark web; any group targeting defense can acquire FIN7-quality EDR bypass capability
- **Supply chain concern** — FIN7's fake company recruitment model could theoretically extend to defense supply chain vendors; any organization unknowingly hiring through a FIN7-controlled front is an insider threat in progress

---

## Detection

### Malvertising Campaign Indicators
```
# Processes launched from user-writable directories following browser download
alert process where (
    process.parent.name in ("chrome.exe", "msedge.exe", "firefox.exe")
    and process.executable_path matches ~r"C:\\Users\\.*\\Downloads\\.*\.(exe|msi|bat|ps1)"
    and process.name not in (approved_installers)
)

# POWERTRASH — PowerShell executing encoded commands from unusual parents
alert process where (
    process.name == "powershell.exe"
    and process.command_line matches ~r"-[Ee][Nn][Cc][Oo][Dd][Ee][Dd][Cc][Oo][Mm][Mm][Aa][Nn][Dd]"
    and process.parent.name in ("explorer.exe", "wscript.exe", "mshta.exe")
)
```

### LIZAR / Diceloader C2 Indicators
- HTTPS connections using domain-fronting (CDN provider as apparent destination; actual C2 in Host header)
- Short-interval beaconing (every 60–120 seconds) to recently registered domains
- Connections to CDN providers from non-browser processes

### USB-Based Initial Access
- USB storage device inserted → PowerShell execution within 60 seconds → alert
- AUTORUN or `autorun.inf` file detected on removable media
- New processes spawned by `explorer.exe` from `%REMOVABLEPATH%\`

### AvNeutralizer Behavioral Indicators
- Security process termination events (EDR agents, AV services killed)
- Driver load events for unsigned or suspicious kernel drivers
- Registry changes to EDR/AV service configurations preceding unexpected process termination

### MITRE ATT&CK Coverage

| Tactic | ID | Technique |
|---|---|---|
| Initial Access | T1566.001 | Spearphishing Attachment (historical) |
| Initial Access | T1189 | Drive-by Compromise (malvertising, current) |
| Initial Access | T1091 | Replication through Removable Media (USB campaign) |
| Execution | T1059.001 | PowerShell (POWERTRASH) |
| Persistence | T1547.001 | Registry Run Keys |
| Defense Evasion | T1055 | Process Injection (LIZAR, Sardonic) |
| Defense Evasion | T1562.001 | Impair Defenses (AvNeutralizer) |
| Defense Evasion | T1027 | Obfuscated Files / Information |
| Credential Access | T1003 | OS Credential Dumping |
| Lateral Movement | T1021 | Remote Services |
| Exfiltration | T1041 | Exfiltration Over C2 Channel |
| Impact | T1486 | Data Encrypted for Impact (Cl0p partnership) |
| Impact | T1657 | Financial Theft (POS, banking) |

---

## Key Connections to Other Board Entries

- **#018 Cl0p** — Current operational partnership. FIN7 malvertising trojan → Cl0p ransomware 2–3 months later. FIN7 functions as Cl0p's initial access engine.
- **#020 BlackCat/ALPHV (DARK)** — Suspected FIN7 developer involvement in ALPHV build. ALPHV emerged from the same talent pool that built DarkSide/BlackMatter with FIN7 association.
- **#021 Payouts King** — Both emerged from Conti/Black Basta diaspora. DOMINO backdoor links FIN7 to Black Basta infrastructure; Payouts King runs former Black Basta/Conti operators.
- **#025 Akira** — Akira affiliates have been observed using AvNeutralizer (purchased from FIN7 dark web listings). FIN7's EDR bypass is actively in the wild across multiple platforms.
- **#032 Jasper Sleet** — Both run sophisticated fake company recruitment operations. FIN7's Combi Security (2017) predates Jasper Sleet's IT worker operation and represents the original model.
- **#015 LockBit** — FIN7 affiliates documented running LockBit ransomware in targeted intrusions. The talent pool overlap is consistent with FIN7's role as a criminal talent hub.

---

## Disclosure and Prosecution Timeline

| Date | Event |
|---|---|
| 2013 | FIN7 / CARBANAK group begins operations targeting financial institutions |
| 2016 | Pivot to US hospitality and retail POS targeting; Chipotle, Chili's, Arby's among early victims |
| 2017–2018 | Combi Security fake company operates; recruits across Eastern Europe |
| 2018 | Three key members arrested (Fedorov, Hladyr, Kolpakov) |
| 2020 | DarkSide launches; FIN7-linked developers suspected involved in development |
| 2021 | Hladyr sentenced 10 years (US); Kolpakov sentenced 7 years (US); DarkSide → Colonial Pipeline |
| 2021 | BlackMatter launches as DarkSide successor |
| 2021 | BlackCat/ALPHV launches as BlackMatter successor |
| 2022 | DOMINO backdoor links FIN7 developers to Black Basta infrastructure (IBM X-Force) |
| 2022 | Sardonic backdoor documented (BitDefender) |
| 2023 (Feb) | USB drive campaign against US defense contractors confirmed — POWERTRASH → LIZAR |
| 2023 (May) | US DOJ declares FIN7 "an entity no more" |
| 2023 (Oct) | Microsoft publishes advisory on FIN7 POWERTRASH campaign against defense contractors |
| 2024 (Apr) | FIN7 resumes operations; malvertising campaigns observed |
| 2024 (May+) | FIN7 trojan communicating from Cl0p pre-victims 2–3 months before Cl0p posting confirmed |
| 2024 | AvNeutralizer EDR bypass tool listed for sale on dark web forums |
| 2024 | Silent Push discovers 4,000+ FIN7 phishing/malware infrastructure domains |
| 2025–2026 | Cl0p partnership continues; malvertising campaigns ongoing; AvNeutralizer in active use by other groups |

---

## References

- [Microsoft — FIN7 Using Signed Driver to Tamper with EDR Software (October 2023)](https://www.microsoft.com/en-us/security/blog/2023/10/18/fin7-hackers-target-the-us-auto-industry-with-a-carbanak-malware-variant/)
- [BitDefender — Sardonic Backdoor Analysis (2021)](https://www.bitdefender.com/blog/labs/fin7-group-has-developed-a-new-backdoor-named-sardonic/)
- [IBM X-Force — DOMINO Backdoor and FIN7/Black Basta Linkage (2023)](https://securityintelligence.com/posts/ex-conti-fin7-actors-collaborate-new-domino-backdoor/)
- [Arete — FIN7 Return Drives Increase in Cl0p Ransomware Attacks (April 2026)](https://areteir.com/resources/fin7-return-drives-increase-in-cl0p-ransomware-attacks)
- [Silent Push — FIN7 4000+ Infrastructure Domains Discovered (2024)](https://www.silentpush.com/blog/fin7/)
- [BleepingComputer — FIN7 hackers sell their security evasion tool to other groups (2024)](https://www.bleepingcomputer.com/news/security/fin7-hackers-sell-their-security-evasion-tool-to-other-groups-on-darknet/)
- [Mandiant — FIN7 Evolution and the Phishing LNK](https://www.mandiant.com/resources/blog/fin7-evolution-and-the-phishing-lnk)
- [US DOJ — FIN7 / Combi Security Prosecution Documents (2021–2022)](https://www.justice.gov/opa/pr/ukrainian-man-sentenced-over-7-years-prison-role-fin7-hacking-group)
- [CISA AA23-307A — Scattered Spider + affiliate tool chains (includes AvNeutralizer distribution)](https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-307a)

---

*Profile created: 2026-05-06 | Author: C3PO | Scoundrel #035 | Admiralty Grade: A1 | TLP: WHITE*
