# Evil Corp (Indrik Spider)

**Scoundrel #040**

| Field | Value |
|-------|-------|
| **Aliases** | Evil Corp, Indrik Spider, GOLD DRAKE, DEV-0243, Dridex Gang, Maksim Yakubets Group |
| **Ransomware Brands (rebrands to evade OFAC)** | BitPaymer → WastedLocker → Hades → Phoenix CryptoLocker → PayloadBIN → Macaw Locker → LockBit (affiliate) |
| **Type** | Cybercriminal — Financial Fraud + RaaS + FSB-Affiliated |
| **Origin** | Russia (Moscow; CIS-aligned) |
| **Sponsor** | Criminal enterprise with **confirmed FSB ties** — protected and tasked by Russian intelligence |
| **Status** | 🔴 ACTIVE — Operations continuing under evolving ransomware brands; Dridex botnet persistent |
| **Active Since** | ~2007 (financial fraud); ~2017 (ransomware) |
| **Threat Level** | 🔴 HIGH — OFAC-sanctioned; FSB nexus; $5M DOJ bounty on leader; confirmed A&D-adjacent targeting |
| **Sector Focus** | Finance · Healthcare · Government · Critical Infrastructure · Manufacturing · Technology |
| **MITRE ATT&CK Group** | G0119 |
| **Admiralty Grade** | A1 — DOJ indictment (2019), OFAC sanctions, NCA/NCSC corroboration, extensive FBI reporting |

---

## TL;DR

Evil Corp is arguably the most sophisticated criminal organization operating under Russian state protection. Led by **Maksim "aqua" Yakubets** — a flamboyant Moscow car enthusiast who reportedly drove a customized camouflage Lamborghini with "THIEF" on the license plate while the DOJ was building a case against him — the group has operated since 2007, pivoting from Zeus malware to Dridex banking trojans to an ever-shifting series of ransomware brands.

The key differentiator: **OFAC sanctions imposed in December 2019** make Evil Corp the only major threat actor where paying their ransom demand is a potential **federal crime** for US persons. This created a bizarre operational problem for their victims — and an even more bizarre solution from Evil Corp: constantly rebrand the ransomware so it can't be attributed to them, letting victims pay without legal risk. They used **LockBit's infrastructure** for exactly this purpose.

Yakubets reportedly moonlights as an **FSB asset** — his father-in-law is a former FSB senior officer, and DOJ court documents allege he worked on classified FSB projects. This isn't a Russian government-tolerated criminal operation. It's a criminal operation that sometimes does intelligence work on the side.

---

## Leadership

### Maksim Yakubets — "aqua"
- **DOJ indictment**: December 2019, W.D. Pennsylvania
- **Charges**: Conspiracy to commit bank fraud, wire fraud, computer hacking, money laundering
- **Bounty**: **$5 million** — largest ever offered for a cybercriminal at time of indictment (since surpassed)
- **Known lifestyle**: Moscow-based; luxury cars (Lamborghini with "THIEF" plate); married into FSB family
- **FSB nexus**: DOJ indictment alleges Yakubets worked for FSB on classified assignments; father-in-law **Eduard Benderskiy** is a retired FSB senior officer who used connections to obstruct law enforcement
- Still at large in Russia. Zero prospect of extradition.

### Igor Turashev — "Nintutu"
- Yakubets' second-in-command; IT director of Evil Corp operations
- Also indicted December 2019, W.D. Pennsylvania
- Managed Dridex botnet infrastructure; handled technical operations
- OFAC-sanctioned

### Additional Sanctioned Members (Oct 2024 expansion)
UK NCA, US OFAC, and Australian authorities coordinated to sanction **16 total Evil Corp-linked individuals** by 2024, including:
- Aleksandr Ryzhenkov (WastedLocker operator; later linked to LockBit affiliate activity)
- Maksim Yakubets' family members with financial facilitation roles

---

## Origins & Evolution

### Zeus / Jabber Zeus Era (2007–2014)
- Evil Corp operators began in the **Zeus malware ecosystem** — banking trojans targeting financial credentials
- Participated in **Jabber Zeus** (2009–2010): an early cybercriminal organization that combined Zeus with organized money mule networks; US DOJ Operation Trident Breach (2010) disrupted but didn't eliminate the group
- Developed **Bugat** (aka Cridex) — early precursor malware

### Dridex Dominance (2014–Present)
- Launched **Dridex** (aka Bugat v5, Cridex) — sophisticated banking trojan with modular architecture
- Dridex became one of the most prolific financial malware families in history
- Estimated **$100M+ in financial losses** in US alone; **$500M+ globally** (FBI estimate)
- Operated a massive **botnet** used for credential theft, banking fraud, and as a delivery platform for subsequent malware
- UK NCA and US DOJ/FBI Operation Onymous and subsequent actions disrupted but never eliminated Dridex
- **Dridex is still active as of 2026** — persistent, updated, still deployed for initial access

### BitPaymer — Entry Into Ransomware (2017)
- Evil Corp introduced **BitPaymer** ransomware in 2017
- Targeted large enterprises; demanded $1M+ ransoms (pioneering "big game hunting")
- Major victim: **UK National Health Service (NHS)** — August 2017; significant disruption

### The OFAC Problem — and the Solution (2019–Present)

**December 2019**: US Treasury OFAC designated Evil Corp as a Specially Designated National (SDN).
- This created a legal prohibition: US persons cannot pay ransoms to OFAC-sanctioned entities without Treasury authorization
- Ransomware insurance carriers immediately flagged — could not reimburse payments to Evil Corp

**Evil Corp's response**: Rebrand constantly. If the ransomware can't be attributed to Evil Corp, victims can pay.

| Year | Ransomware Brand | Notes |
|------|-----------------|-------|
| 2017–2019 | BitPaymer | Original; abandoned after OFAC designation |
| 2020 | WastedLocker | New name, same operators; Garmin attack |
| 2020 | Hades | Rolled out quickly after WastedLocker attribution |
| 2021 | Phoenix CryptoLocker | Further rebrand |
| 2021 | PayloadBIN | Short-lived; quickly attributed |
| 2021 | Macaw Locker | Used against Olympus, Sinclair Broadcast |
| 2022–2024 | **LockBit affiliate** | Used LockBit RaaS specifically to obscure Evil Corp attribution; Aleksandr Ryzhenkov confirmed as LockBit affiliate |

The LockBit connection is particularly significant: Evil Corp affiliates used LockBit's infrastructure not because it was better, but because **LockBit is not OFAC-sanctioned**, enabling victims to pay without legal exposure. LockBit (#015) was, knowingly or not, serving as a sanctions-evasion vehicle.

---

## Key Operations

### 🎮 Garmin — July 2020 ⭐
- **WastedLocker** deployed against Garmin — the GPS/navigation company whose products include **aviation navigation systems** (Garmin avionics are in millions of aircraft)
- **Reported ransom**: $10 million
- **Impact**: Garmin Connect went dark; flyGarmin (aviation navigation service) offline; pilots unable to download updated navigation databases
- **Confirmed payment**: Garmin reportedly obtained a WastedLocker decryption tool — implication being ransom was paid, possibly through third-party intermediary to avoid OFAC
- Aviation safety implications: Garmin systems are in cockpits; loss of flyGarmin database updates is a flight safety issue
- **OFAC complication**: Payment to Evil Corp = sanctions violation; Garmin never confirmed payment

### 📺 Sinclair Broadcast Group — October 2021
- **Macaw Locker** deployed against Sinclair — one of the largest US local TV broadcasters
- Disrupted live broadcasts across multiple markets
- Confirmed Macaw Locker later attributed to Evil Corp by Mandiant

### 🏭 Olympus Corporation — September 2021
- **Macaw Locker** hit Olympus — medical device manufacturer
- Disrupted IT systems across EMEA region

### UK Financial Sector Dridex Campaign (Ongoing)
- NCA estimates Evil Corp's Dridex campaigns cost UK financial institutions **£20M+**
- Persistent targeting of UK banks, building societies, accounting firms

### US Government / Defense-Adjacent Targeting
- Multiple confirmed Dridex infections at US defense contractors and government agencies (FBI Private Industry Notification, 2020)
- Not primary targets, but opportunistic — Dridex's broad deployment means it lands on sensitive networks

---

## Tradecraft & Tools

### Dridex — The Persistent Engine
- Modular banking trojan / backdoor; 12+ years of continuous development
- **Initial delivery**: Malicious Word/Excel macro documents (phishing); evolved to use Emotet as loader
- **Modules**: Web inject (credential theft), screenshot capture, keylogger, network propagation, VNC backdoor
- **C2**: Domain generation algorithm (DGA) + P2P botnet architecture (resilient takedown)
- Still deployed as initial access → credential theft → ransomware pre-positioning

### Ransomware Toolchain (WastedLocker as representative)
- Custom-built; not based on leaked source code (unlike many groups)
- Targets: network shares, removable drives, local files
- Encryption: AES-256 (file) + RSA-4096 (key)
- Appends `.garminwasted` (Garmin) or custom extensions based on target
- Shadow copy deletion, backup disruption (standard)
- Moves laterally via stolen domain admin credentials before deploying

### Full Intrusion Chain
```
Phishing email (malicious Office doc)
    ↓
Dridex / SocGholish loader
    ↓
Cobalt Strike beacon
    ↓
Domain enumeration (BloodHound, ADFind)
    ↓
Domain admin credential theft (Mimikatz / DCSync)
    ↓
Data exfiltration (Rclone → MEGA)
    ↓
Ransomware deployment (domain-wide via GPO or PsExec)
```

### Living Off the Land
- Cobalt Strike (primary post-exploitation)
- Mimikatz (credential theft)
- BloodHound / SharpHound (AD enumeration)
- PsExec / WMI (lateral movement)
- Rclone (exfiltration to cloud)
- AnyDesk / TeamViewer (persistence)
- ADFind (domain reconnaissance)

---

## MITRE ATT&CK Mapping

| Tactic | Technique ID | Technique |
|--------|-------------|-----------|
| Initial Access | T1566.001 | Phishing: Spearphishing Attachment |
| Initial Access | T1566.002 | Phishing: Spearphishing Link |
| Execution | T1059.001 | PowerShell |
| Execution | T1059.003 | Windows Command Shell |
| Execution | T1204.002 | User Execution: Malicious File |
| Persistence | T1547.001 | Boot or Logon Autostart: Registry Run Keys |
| Persistence | T1543.003 | Create/Modify System Process: Windows Service |
| Defense Evasion | T1027 | Obfuscated Files or Information |
| Defense Evasion | T1562.001 | Impair Defenses: Disable or Modify Tools |
| Defense Evasion | T1036 | Masquerading |
| Credential Access | T1003.001 | OS Credential Dumping: LSASS |
| Credential Access | T1558.003 | Kerberoasting |
| Discovery | T1069.002 | Domain Groups Discovery |
| Discovery | T1087.002 | Account Discovery: Domain Account |
| Lateral Movement | T1021.002 | SMB/Windows Admin Shares |
| Lateral Movement | T1021.001 | RDP |
| Collection | T1074.002 | Data Staged: Remote Data Staging |
| Exfiltration | T1567.002 | Exfiltration to Cloud Storage (MEGA) |
| Impact | T1486 | Data Encrypted for Impact |
| Impact | T1490 | Inhibit System Recovery |

---

## OFAC / Legal Landscape

### Why This Matters Operationally

Evil Corp is the **only major ransomware group where paying the ransom may itself be a federal crime** for US persons.

**31 CFR Part 501** — OFAC regulations prohibit transactions with SDN-listed entities without Treasury authorization.

**Practical implications for incident response:**
1. If your IR team suspects Evil Corp ransomware (Dridex delivery chain, WastedLocker/Hades/Macaw artifacts), **halt ransom negotiations** pending OFAC review
2. Cyber insurance carriers may refuse to reimburse OFAC-prohibited payments
3. Request an OFAC license if payment is deemed necessary — rarely granted
4. FBI strongly recommends notifying FBI *before* any ransom payment in potential Evil Corp cases

**OFAC Guidance (Oct 2020):** Treasury issued advisory specifically addressing ransomware payments and OFAC compliance — prompted largely by Evil Corp's persistent rebranding.

---

## Sanctions Timeline

| Date | Action |
|------|--------|
| Dec 2019 | OFAC SDN designation — Evil Corp, Yakubets, Turashev + 5 others; $5M DOJ bounty for Yakubets |
| Oct 2020 | OFAC advisory on ransomware payments; Evil Corp as primary case study |
| Mar 2022 | UK NCA sanctions 7 additional Evil Corp members |
| Feb 2023 | Joint UK/US/EU advisory on Evil Corp rebranding to LockBit |
| Oct 2024 | UK NCA + OFAC + Australian DFAT expand sanctions to 16 total individuals; Aleksandr Ryzhenkov (LockBit affiliate) named |

---

## Intelligence Update — 2026-05-11

**Source grade: A1 | Analyst: C3PO**

As of May 2026:

- **Dridex botnet remains active** — periodic campaigns delivering updated loaders; no confirmed full takedown
- **Aleksandr Ryzhenkov** (sanctioned Oct 2024 as Evil Corp + LockBit affiliate) remains the key active operator to track; post-LockBit disruption (Feb 2024), Ryzhenkov's activities have shifted to other RaaS platforms
- Evil Corp operators are likely operating through **RansomHub affiliates** (before RansomHub went dark April 2025) and potentially current active platforms
- **Aviation exposure**: Garmin attack demonstrated that Evil Corp's financial motivation combined with broad Dridex deployment creates real risk to aviation-adjacent infrastructure — any org running avionics software, navigation databases, or flight planning systems should treat Dridex as a tier-1 threat
- The OFAC sanctions architecture remains the most important compliance consideration: organizations in defense industrial base must ensure IR retainers include OFAC-certified legal counsel familiar with ransomware payment legality

---

*Profile created: 2026-05-11 | Analyst: C3PO | Admiralty Grade: A1*
