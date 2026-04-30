# Threat Actor Profile: RansomHub

## Identity

| Field | Detail |
|---|---|
| **Scoundrel #** | 030 |
| **Primary Name** | RansomHub |
| **Aliases** | Ransomhub, cyclops (suspected infrastructure), Knight (suspected predecessor) |
| **Type** | Cybercriminal — Ransomware-as-a-Service (RaaS) |
| **Origin** | Unknown (Eastern European operators suspected; affiliate base international) |
| **Active Period** | February 2, 2024 — April 1, 2025 (14 months) |
| **Status** | ⚠️ DARK — went offline April 1, 2025 without warning; affiliate base migrated to Qilin and DragonForce |
| **Threat Level** | 🔴 HIGH (historical; affiliate tradecraft persists across successor platforms) |
| **CISA Advisory** | AA24-242A — #StopRansomware: RansomHub (August 29, 2024, joint CISA/FBI/MS-ISAC/HHS) |
| **Sectors Targeted** | Healthcare, Water/Wastewater, IT, Government Services, Emergency Services, Food & Agriculture, Financial Services, Critical Manufacturing, Transportation, Communications |
| **Geographic Focus** | United States (primary); global |
| **Peak Activity** | Q2–Q4 2024 — dominated the RaaS landscape following ALPHV and LockBit disruptions |
| **ATT&CK** | T1190, T1078, T1133, T1486, T1490, T1069, T1057, T1082, T1083, T1567.002 |

---

## Overview

RansomHub was the dominant ransomware platform of 2024 — not because it was the most technically sophisticated, but because it arrived at exactly the right moment and offered the most operator-friendly terms in the market.

When the FBI dismantled LockBit's infrastructure in **Operation Cronos (February 2024)** and the ALPHV/BlackCat operation imploded in an exit scam after the **Change Healthcare attack (also February 2024)**, hundreds of skilled ransomware affiliates simultaneously became free agents. RansomHub — which had posted its first advertisements on criminal forums just **two days before the ALPHV exit scam** — was ready. It offered a **90/10 affiliate split** (the highest in the market), no restrictions on sector targeting (including hospitals, critical infrastructure, and government), and a professional affiliate panel with custom encryptors for Windows, Linux, ESXi, and macOS.

By August 2024, CISA had counted **210+ victims** across every critical infrastructure sector. High-profile names included **Halliburton**, **Christie's**, and RansomHub's own claim to possess data stolen in the Change Healthcare breach. By the time the platform went dark on April 1, 2025, the total was estimated at **500+ victims** — a remarkable volume for a 14-month operation.

RansomHub's disappearance was as abrupt and unexplained as its rise. No law enforcement action was announced. No exit scam was publicly confirmed, though affiliates reported being stiffed. The affiliate base migrated primarily to **Qilin** (which doubled its leak site volume in the weeks after) and **DragonForce**. The TTPs, tooling, and operators didn't disappear — they rebranded.

**The Knight connection:** RansomHub is widely assessed as a rebrand of the **Knight ransomware** operation. Knight's source code was listed for sale on the underground RAMP forum on **February 18, 2024** — 16 days after RansomHub's forum advertisements appeared, and the same week ALPHV collapsed. Arete and multiple researchers confirmed matching code structure between Knight and RansomHub encryptors.

**The nation-state angle:** RansomHub is notable for confirmed use by **North Korean threat actors** and operations by **Scattered Spider**-affiliated actors, demonstrating the platform's role as a shared criminal-as-a-service layer that state-adjacent and Western criminal groups could both leverage.

---

## Why RansomHub Mattered

RansomHub was the apex expression of the **affiliate-first RaaS model**:

1. **Best payout in the market** — 90% to affiliates. At peak, the only platforms offering comparable splits were smaller operations with lower victim counts.
2. **No rules** — Unlike LockBit (which nominally discouraged hospital attacks) or some platforms with geographic restrictions, RansomHub had no guardrails. Every sector was in scope.
3. **Cross-platform encryptor** — Windows, Linux, ESXi, and macOS variants. Affiliates could hit any environment.
4. **Absorbed the best talent** — When LockBit and ALPHV collapsed within weeks of each other, their most experienced affiliates had one obvious destination.
5. **Data-only extortion option** — RansomHub allowed affiliates to exfiltrate without encrypting, for victims where operational disruption wasn't achievable or desirable.

This is the playbook that Qilin, DragonForce, and LockBit 5.0 are now running. Understanding RansomHub is understanding the modern RaaS template.

---

## Encryptor Technical Profile

| Property | Detail |
|---|---|
| **Languages** | C++ and Go (cross-platform compilation) |
| **Encryption** | AES-256 for file content + Curve25519 (ECDH) for key exchange |
| **File extension** | First 6 characters of the Curve25519 public key |
| **Ransom note** | `README_[extension].txt` in each encrypted directory |
| **Execution** | Requires `-pass [SHA-256]` argument — will not encrypt without it |
| **Configuration** | JSON config decrypted at runtime from embedded blob |
| **Safe Mode** | `-safeboot` flag reboots into Windows Safe Mode (bypasses AV/EDR) |
| **VSS deletion** | Deletes Volume Shadow Copies pre-encryption |
| **Event log clearing** | Clears Windows event logs post-execution |
| **Intermittent encryption** | Large files partially encrypted (0x100000 byte blocks, skips 0x200000 bytes) — optimizes speed |
| **Exclusions** | Whitelisted system files/dirs to prevent unbootable systems |
| **Network shares** | Enumerates and encrypts mounted network shares |
| **VM handling** | `-skip-vm` flag to preserve specified VMs for operational reasons |

---

## Attack Chain

```
INITIAL ACCESS: Phishing | Exploited internet-facing vuln (Citrix, Fortinet, Ivanti, F5, Apache)
                Valid account credentials purchased from IABs
       ↓
RECON: ADRecon / SharpHound — map AD, find DCs, identify backup systems
       PowerShell, WMI for host enumeration
       ↓
LATERAL MOVEMENT: PsExec · RDP · WMI · Pass-the-Hash
       ↓
PERSISTENCE: Remote access tools (AnyDesk, Atera, Splashtop, ScreenConnect, Cobalt Strike)
             Valid account creation
       ↓
DEFENSE EVASION: Defender disable (registry) · Safeboot reboot (-safeboot flag) · BYOVD (some affiliates)
       ↓
EXFILTRATION: WinSCP · Rclone · AWS S3 CLI → attacker-controlled cloud storage
              (Data-only extortion option: stop here, skip encryption)
       ↓
DEPLOY: Execute RansomHub.exe -pass [SHA-256] across domain (PsExec/GPO)
        VSS deletion → AES-256 encryption → .README_.txt ransom note
       ↓
EXTORT: TOR-based negotiation portal · Double extortion (encrypt + data leak threat)
        Data published on RansomHub leak site if payment refused
```

**Exploited CVEs (documented by CISA AA24-242A and IR firms):**

| CVE | Product | CVSS |
|---|---|---|
| CVE-2023-3519 | Citrix NetScaler | 9.8 |
| CVE-2023-27997 | Fortinet FortiOS/FortiProxy | 9.8 |
| CVE-2023-46805 / CVE-2024-21887 | Ivanti Connect Secure | 8.2 / 9.1 |
| CVE-2023-48788 | Fortinet FortiClientEMS | 9.8 |
| CVE-2021-26084 | Atlassian Confluence | 9.8 |
| CVE-2023-22515 | Atlassian Confluence | 9.8 |
| CVE-2023-34362 | Progress MOVEit Transfer | 9.8 |
| CVE-2024-1709 | ConnectWise ScreenConnect | 10.0 |
| CVE-2022-47966 | Zoho ManageEngine | 9.8 |
| CVE-2023-4966 (Citrix Bleed) | Citrix NetScaler | 9.4 |

---

## Notable Victims

| Victim | Sector | Ransom Demand | Notes |
|---|---|---|---|
| Halliburton | Oil & Energy | Undisclosed | August 2024; operations disrupted; one of the highest-profile critical infrastructure hits of 2024 |
| Christie's | Art/Auction | ~$500K | Theft of 500,000+ client records; auction operations disrupted |
| Change Healthcare data | Healthcare | — | RansomHub claimed possession of data from the ALPHV/BlackCat breach; threatened to sell after ALPHV exit scammed its own affiliate |
| Kawasaki Motors Europe | Manufacturing | Undisclosed | 487 GB exfiltrated |
| Planned Parenthood (Montana) | Healthcare | $1M | 93 GB exfiltrated |
| Rite Aid | Retail/Pharmacy | Undisclosed | 10 GB customer data; 45M+ customer records at risk |
| Texas Dow Employees Credit Union | Financial | Undisclosed | |
| Patelco Credit Union | Financial | Undisclosed | 726,000 customers affected |
| Frontier Communications | Telecom | Undisclosed | 750,000 customer records; SEC disclosure required |
| Ann & Robert H. Lurie Children's Hospital | Healthcare | Undisclosed | Network taken offline; patient care impacted |
| 210+ critical infrastructure orgs | Multiple | Various | Per CISA AA24-242A (Aug 2024) |

---

## Affiliate Notable Activity

### Scattered Spider (#013)
Scattered Spider actors were documented using RansomHub as their ransomware payload of choice during 2024. Their social engineering capabilities (SIM swapping, help desk vishing, MFA fatigue) combined with RansomHub's cross-platform encryptor made for a particularly effective pairing against enterprise environments.

### North Korean Threat Actors (#003 — Lazarus-adjacent)
North Korean cybercriminals were documented deploying RansomHub as part of operations, consistent with the DPRK pattern of leveraging commercial RaaS platforms to monetize intrusions (also seen with Medusa, #029).

---

## Threat Connections

🔗 **Connects to Scoundrel #020 — BlackCat / ALPHV (disrupted)**
The direct predecessor in affiliate talent. When ALPHV exit-scammed its affiliates after Change Healthcare, the most experienced operators went to RansomHub. RansomHub even hosted the Change Healthcare stolen data on its leak site — a direct inheritance move that signaled to the market it was absorbing ALPHV's business.

🔗 **Connects to Scoundrel #015 — LockBit (disrupted/rebuilding)**
Operation Cronos (Feb 2024) freed LockBit's affiliate pool simultaneously with the ALPHV collapse. RansomHub absorbed the majority. When LockBit 5.0 relaunched in September 2025, it was competing against the same Qilin/DragonForce ecosystem that inherited RansomHub's affiliates — the circle completes.

🔗 **Connects to Scoundrel #013 — Scattered Spider**
Confirmed RansomHub payload deployers in 2024. Social engineering access + RansomHub encryptor = one of the most capable enterprise threat pairings of the year.

🔗 **Connects to Scoundrel #003 — Lazarus Group (DPRK)**
North Korean operators documented using RansomHub — consistent with DPRK's pattern of leveraging third-party RaaS platforms (also Medusa, #029) to monetize intrusions while maintaining plausible operational separation.

🔗 **Connects to Scoundrel #028 — APT73 / Bashe**
APT73 emerged from the LockBit post-Cronos diaspora. Its affiliate pool overlaps substantially with former RansomHub operators, and its targeting profile (business services, finance, healthcare) mirrors RansomHub's sector focus.

🔗 **Connects to Scoundrel #029 — Medusa / Storm-1175**
Qilin absorbed the majority of RansomHub's affiliate talent after April 2025. DragonForce absorbed the rest. The same operators running RansomHub playbooks are now executing under Qilin, DragonForce, and — in some cases — Medusa branding.

---

## Legacy & Current Threat

RansomHub is gone. Its **playbook is everywhere**.

The affiliates who made it the #1 ransomware platform of 2024 didn't retire — they migrated. Qilin's explosive growth from February to April 2025 (doubling its leak site volume in the weeks after RansomHub's disappearance) is the direct statistical signature of this migration. The same initial access techniques, the same lateral movement tools (AnyDesk, Atera, Rclone), the same exploitation of Citrix/Fortinet/Ivanti appliances — all documented in RansomHub IR engagements — are now appearing in Qilin, DragonForce, and LockBit 5.0 cases.

When you see a RansomHub-pattern attack today, it's running under a different flag.

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 HIGH | **Patch Citrix, Fortinet, Ivanti, and ConnectWise immediately** — the 10 CVEs in RansomHub's arsenal are still the RaaS entry point of choice across successor platforms |
| 🔴 HIGH | **Disable or restrict VPN/SSL appliance management interfaces** from internet exposure |
| 🔴 HIGH | **Monitor for unauthorized RMM tools** (AnyDesk, Atera, Splashtop, ScreenConnect) — RansomHub affiliates relied heavily on these |
| 🔴 HIGH | **Alert on Rclone + WinSCP running on servers** — primary exfil tools |
| 🟡 HIGH | **VSS monitoring** — alert on unexpected shadow copy deletion (`vssadmin delete shadows`, `wmic shadowcopy delete`) |
| 🟡 HIGH | **Safeboot registry modification** — alert on `HKLM\SYSTEM\CurrentControlSet\Control\SafeBoot\Minimal` changes via non-admin processes |
| 🟡 HIGH | **Network segmentation** — RansomHub affiliates moved laterally via PsExec/RDP; segment east-west traffic |
| 🟡 MEDIUM | **Credential hygiene** — 23% of RaaS attacks in 2025 started with IAB-sourced credentials. Monitor for login anomalies on VPN/Citrix/Fortinet appliances |
| 🟡 MEDIUM | **Immutable/offline backups** — data-only extortion option means some affiliates skip encryption entirely; backups don't help if data is already exfiltrated |

---

## References

- [CISA AA24-242A — #StopRansomware: RansomHub (August 29, 2024)](https://www.cisa.gov/newsevents/cybersecurity-advisories/aa24-242a)
- [Arete — Malware Spotlight: RansomHub Ransomware](https://areteir.com/resources/malware-spotlight-ransomhub-ransomware)
- [Symantec / Broadcom — RansomHub: New Ransomware-as-a-Service (2024)](https://symantec-enterprise-blogs.security.com/threat-intelligence/ransomhub-knight-ransomware)
- [GuidePoint Security — GRIT Ransomware Report Q1 2026](https://www.guidepointsecurity.com/resources/grit-ransomware-report-q1-2026/)
- [Netguardia — Mapping RaaS Affiliate Economics in 2026](https://netguardia.com/cybersecurity-intelligence/threat-intelligence/mapping-ransomware-as-a-service-affiliate-economics-in-2026/)

---

*Profile created: 2026-04-30 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
