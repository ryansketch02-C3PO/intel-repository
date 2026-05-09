# Conti Ransomware Group (WIZARD SPIDER)

**Scoundrel #038**

| Field | Value |
|-------|-------|
| **Aliases** | Conti, WIZARD SPIDER, Ryuk successor, Gold Ulrick, DEV-0146 |
| **Type** | Cybercriminal — RaaS + Direct Operations |
| **Origin** | Russia (St. Petersburg / CIS-aligned) |
| **Sponsor** | Criminal enterprise; aligned with Russian state interests (protected, not directed) |
| **Status** | ❌ DEFUNCT — Officially dissolved May 2022 |
| **Active Period** | 2019–2022 (ransomware ops); TrickBot/WIZARD SPIDER lineage back to 2016 |
| **Threat Level** | 🔴 HIGH — Defunct as org; operators actively running Akira, Black Basta, BlackCat, Royal/BlackSuit |
| **Sector Focus** | Healthcare, Government, Critical Infrastructure, Education, Manufacturing, Finance |
| **MITRE ATT&CK Group** | G0092 |
| **Admiralty Grade** | A1 — FBI, CISA, NCSC, Europol, extensive Conti Leaks corroboration |

---

## TL;DR

Conti was the most dominant ransomware operation of 2020–2022, responsible for at least **$150M in confirmed ransom payments** (FBI estimate; likely far higher) and hundreds of attacks on critical infrastructure globally. Operated by WIZARD SPIDER — a highly professional, corporate-structured criminal syndicate out of Russia — Conti ran a full RaaS platform with HR, management, developers, and negotiators. When a Ukrainian member leaked **160,000 internal chat logs and full source code** in February 2022 after Conti publicly supported Russia's invasion of Ukraine, the group became the most thoroughly documented criminal organization in cybersecurity history. Conti officially shut down in May 2022 — but its operators didn't retire. They spun off into Akira, Black Basta, BlackCat/ALPHV, Royal/BlackSuit, Quantum, and Karakurt. **Conti never died — it fractured and multiplied.**

---

## Origins & Evolution

### WIZARD SPIDER / TrickBot Era (2016–2019)
- WIZARD SPIDER emerged from the **Dyre banking trojan** ecosystem (~2016)
- Developed **TrickBot** as their primary initial access malware — modular, banking-focused, evolved into a full-service initial access broker platform
- Deployed **Ryuk ransomware** starting 2018 in partnership with another group (initially separate; later merged under WIZARD SPIDER umbrella)
- Ryuk campaigns established the "big game hunting" model: target large enterprises, demand millions (not thousands) in ransom
- **Target profile**: Hospitals, city governments, schools — anything with low tolerance for downtime and deep pockets

### Conti Launch (2019–2020)
- Conti ransomware introduced ~2019 as Ryuk's successor; written in C++
- Key innovation: **multi-threaded encryption** using Windows Restart Manager to unlock files before encrypting — significantly faster than competitors
- Launched full **RaaS platform** — affiliates receive ~70% cut; core team keeps 30%
- Operated as a **legitimate business structure**: HR dept, employee onboarding, salary reviews, vacation policy, code review, customer service for ransom negotiations
- Known to pay developers $1,500–$2,000/month (competitive for Russian IT market)
- **By 2021**: ~350–400 staff (per leaked comms); operated like a mid-size tech company

### Dominance (2020–2022)
- Most prolific ransomware gang globally through 2020–2022
- **#1 most active ransomware group** in 2021 (surpassing REvil, LockBit in victim count)
- US CISA/FBI issued **Alert AA21-265A** (September 2021): warned of 400+ US victim orgs, including 16+ in healthcare/first responder sector
- Key tactic shift: moved from simple encryption to **double extortion** (encrypt + exfiltrate + threaten to publish on "Conti News" leak site)
- Maintained leak site on Tor: `continewsnv5otx5kaoje7krkto2qbu3gtqef22mnr7eaxw3y6ncz3ad.onion` (now offline)

---

## Key Operations

### Irish Health Service Executive (HSE) — May 2021 ⭐ LANDMARK
- **Impact**: Took down entire national healthcare IT system of Ireland
- Initial access via phishing; deployed BazarLoader → Cobalt Strike → Conti encryptor
- **Demanded $20M ransom** — Irish government refused to pay
- Healthcare system operated on paper for weeks; patient care severely disrupted; chemotherapy and radiology appointments cancelled
- **Decryptor provided for free** after massive public pressure (PR move — Conti claimed they "don't attack hospitals"; then immediately attacked hospitals again)
- Total recovery cost: **€500M+** (Irish government estimate)
- One of the most disruptive healthcare cyberattacks in history

### Costa Rica Government — April–May 2022 🌎 NATIONAL EMERGENCY
- Attacked Costa Rican **Ministry of Finance** — encrypted tax and customs systems weeks before a new presidential administration took office
- New President Rodrigo Chaves declared a **national emergency** — first nation-state to do so over a ransomware attack
- Conti demanded $20M; escalated to $10M; never paid
- Follow-on attack by **Hive** (separate group) hit Costa Rica's social security system simultaneously — suspected coordination
- **Intended to force state capitulation** — Conti publicly threatened to destabilize the country and called on Costa Ricans to pressure their government
- Attack widely interpreted as politically motivated; timed to maximize chaos

### US Healthcare During COVID-19 (2020–2021)
- **October 2020**: FBI/CISA/HHS joint advisory warned of imminent Conti threat to US hospitals during COVID surge
- Hit dozens of US hospitals simultaneously; one of the worst ransomware moments for healthcare sector
- Notable victims: Universal Health Services (UHS), Scripps Health, Broward County Public Schools

### Other Notable Victims
| Victim | Date | Demand | Notes |
|--------|------|--------|-------|
| JVCKenwood | Sep 2021 | $7M | 1.5TB data stolen |
| Graff Jewellers (UK) | Oct 2021 | Undisclosed | Celebrity client data leaked |
| Shutterfly | Dec 2021 | Undisclosed | Active over holiday weekend |
| City of Tulsa, OK | May 2021 | Undisclosed | Police citations leaked |
| Serologix | 2021 | Undisclosed | Blood testing company |
| Nordex Group (wind turbines) | Mar 2022 | Undisclosed | Energy sector OT proximity |

---

## The Conti Leaks — February 2022 💥

**The most significant involuntary OSINT dump in ransomware history.**

### What Happened
- **February 25, 2022**: One day after Russia invaded Ukraine, Conti published a statement declaring full support for Russia and threatening cyber retaliation against any Western entities opposing Russia
- A Ukrainian member (or members) of Conti — furious at the statement — created a Twitter account `@ContiLeaks` and began dumping internal data
- **Total leak**:
  - ~160,000 internal **Jabber chat logs** (Jan 2021–Feb 2022)
  - Full **source code** for Conti ransomware encryptor, decryptor, and builder
  - Full **TrickBot source code** (partial)
  - Internal organizational charts, salary records, HR documents
  - Bitcoin wallet transaction logs
  - Negotiation transcripts with victims
  - Technical infrastructure details

### What Was Revealed
- **Corporate structure**: Named roles — Stern (CEO/boss), Mango (HR/manager), Target, Reshaev, Buza, Bentley (developers), Salamandra (OSINT), and dozens more
- **Salaries**: Developers paid $1,500–$2,000/month; managers $2,000–$5,000/month; all in BTC
- **Revenue**: $180M+ in one year alone (per internal records)
- **Russian state proximity**: Internal chats reference FSB contacts, apparent foreknowledge of law enforcement operations, discussion of bribery
- **Business processes**: Employee onboarding emails, code review systems, vacation policy, performance reviews
- **Bitcoin**: Wallets holding tens of millions; tracked by blockchain analysts to numerous exchanges

### Impact of the Leak
- Enabled **extensive attribution** and sanctions against named individuals
- Source code immediately repurposed by other groups — Conti locker used as basis for multiple new ransomware variants
- **Conti News leak site went dark** within weeks
- Group formally announced shutdown May 2022, attributing decision to "the name being too toxic"

---

## Tradecraft & Tools

### Initial Access
| Method | Tool/Technique | Notes |
|--------|---------------|-------|
| Phishing | TrickBot | Primary delivery for years; modular backdoor |
| Phishing | BazarLoader (BazarBackdoor) | Stealthier successor to TrickBot; used for big game targets |
| Phishing | IcedID | Purchased access from other IABs |
| IAB purchase | Various | Paid for initial access from specialized brokers |
| VPN exploitation | Fortinet CVE-2018-13374, CVE-2020-12812 | Used for network-level initial access |

### Execution & Lateral Movement
- **Cobalt Strike** — primary post-exploitation framework; all TTPs
- **PowerShell Empire** (early campaigns)
- **Mimikatz** — credential harvesting; DCSync
- **BloodHound / SharpHound** — AD enumeration
- **ADFind** — AD query tool
- **PsExec / WMI / SMB** — lateral movement
- **Kerberoasting** — service account credential theft

### Persistence
- Scheduled tasks, services, registry run keys
- WMI subscriptions for persistence on key servers
- Custom backdoors deployed for long-dwell operations

### Exfiltration
- **Rclone** — configured to cloud storage (MEGA, Dropbox) for data exfiltration
- **FileZilla** — FTP exfiltration in some cases
- **MEGAsync** — direct cloud sync

### Ransomware Encryptor
- Written in C++; compiled as PE binary
- **Multi-threaded encryption** using Windows I/O Completion Ports
- Uses **Windows Restart Manager API** to unlock files before encrypting
- Encryption: ChaCha20 (file-level) with RSA-4096 (key protection)
- **Speed**: One of the fastest encryptors tested — could encrypt 660GB of files in ~4 minutes in testing
- Ransom note filename: `CONTI_README.txt`
- Ransom note string: `"All of your files are currently encrypted by CONTI RANSOMWARE"`
- Self-deletes after execution

### Anti-Detection
- Built-in AV process killer targeting ~160 security product process names
- Windows shadow copy deletion via `vssadmin delete shadows /all /quiet`
- Backup catalog destruction: `wbadmin delete catalog -quiet`
- Windows Defender bypass via PowerShell: `Set-MpPreference -DisableRealtimeMonitoring $true`

---

## MITRE ATT&CK Mapping

| Tactic | Technique ID | Technique |
|--------|-------------|-----------|
| Initial Access | T1566.001 | Phishing: Spearphishing Attachment |
| Initial Access | T1566.002 | Phishing: Spearphishing Link |
| Initial Access | T1133 | External Remote Services (VPN exploitation) |
| Execution | T1059.001 | PowerShell |
| Execution | T1059.003 | Windows Command Shell |
| Execution | T1047 | Windows Management Instrumentation |
| Persistence | T1053.005 | Scheduled Task/Job |
| Persistence | T1543.003 | Create or Modify System Process: Windows Service |
| Privilege Escalation | T1078 | Valid Accounts |
| Privilege Escalation | T1068 | Exploitation for Privilege Escalation |
| Defense Evasion | T1027 | Obfuscated Files or Information |
| Defense Evasion | T1562.001 | Impair Defenses: Disable or Modify Tools |
| Credential Access | T1003.001 | OS Credential Dumping: LSASS Memory |
| Credential Access | T1558.003 | Steal or Forge Kerberos Tickets: Kerberoasting |
| Discovery | T1082 | System Information Discovery |
| Discovery | T1069 | Permission Groups Discovery (BloodHound) |
| Discovery | T1083 | File and Directory Discovery |
| Lateral Movement | T1021.002 | Remote Services: SMB/Windows Admin Shares |
| Lateral Movement | T1021.006 | Remote Services: Windows Remote Management |
| Collection | T1074.001 | Data Staged: Local Data Staging |
| Exfiltration | T1567.002 | Exfiltration Over Web Service: Exfiltration to Cloud Storage |
| Impact | T1486 | Data Encrypted for Impact |
| Impact | T1490 | Inhibit System Recovery |
| Impact | T1489 | Service Stop |

---

## Shutdown & Legacy

### Official Disbandment — May 2022
- Conti officially announced dissolution in late May 2022
- Leak site taken offline; negotiation portal shut down
- Stated reason: "The brand Conti is too toxic"
- **Reality**: Group reorganized rather than retired

### The Conti Diaspora — Active Threats Today

| Splinter Group | Status | Repo Entry |
|---------------|--------|-----------|
| **Black Basta** | ❌ Disrupted (2025) — key affiliates at PayoutsKing | #021 (PayoutsKing) |
| **Akira** | 🔴 ACTIVE — #1 or #2 globally | #025 |
| **BlackCat / ALPHV** | ❌ Disrupted (Mar 2024) — affiliates migrated | #020 |
| **Royal / BlackSuit** | 🔴 ACTIVE (rebranded to BlackSuit) | Referenced in #025, #028 |
| **Quantum / Zeon** | Mostly absorbed into other groups | — |
| **Karakurt** | Extortion-only arm; partially active | — |
| **DragonForce** | 🔴 ACTIVE — Conti code lineage | #033 |

**Bottom line**: Nearly every major ransomware operation active in 2024–2026 traces direct lineage — through code, personnel, or tradecraft — back to Conti.

---

## Sanctions & Law Enforcement Actions

| Date | Action |
|------|--------|
| Feb 2023 | UK/US sanctions against 7 named Conti members (incl. Vitaly Kovalev / Stern) |
| May 2023 | US State Dept: $10M reward for info on Conti leadership |
| Sep 2023 | US DOJ indicts Mikhail Matveev (aka Wazawaka) — active Conti affiliate |
| Nov 2023 | Europol/FBI: Arrests of Conti/TrickBot developers in Ukraine and Eastern Europe |
| 2022–ongoing | OFAC: Numerous bitcoin wallets sanctioned; exchanges penalized for processing Conti ransom payments |

---

## Intelligence Update — 2026-05-09

**Source grade: A1 | Analyst: C3PO**

Conti remains relevant not as an active operator but as the **root node of the modern ransomware ecosystem**. As of May 2026:

- **Akira** (#025) remains among the top 3 most prolific ransomware groups globally; Conti operator fingerprints visible in tooling and TTPs
- **DragonForce** (#033) explicitly reuses Conti encryptor source code (confirmed via binary comparison)
- **PayoutsKing** (#021) is staffed by former Black Basta operators — Black Basta was a direct Conti spinoff
- The **Conti Leaks** source code continues to be repurposed by new entrants — at least 5 new ransomware variants in 2024–2025 use modified Conti encryptors
- CISA/MS-ISAC updated TTPs advisory in early 2026 specifically noting Conti-lineage tradecraft still active across descendant groups

**Assessments:**
- Probability that former Conti core operators remain active: **HIGH (A1)** — confirmed through Akira attribution
- Probability of Conti brand resurrection: **LOW** — brand permanently toxic post-leaks; operators prefer fresh branding
- Threat to A&D sector: **MODERATE** — Conti itself focused on healthcare/government; Akira/Black Basta descendants have hit defense contractors

---

*Profile created: 2026-05-09 | Analyst: C3PO | Admiralty Grade: A1*
