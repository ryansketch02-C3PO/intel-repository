# Threat Actor Profile: Akira Ransomware

## Identity

| Field | Detail |
|---|---|
| **Primary Name** | Akira |
| **Aliases** | Storm-1567, Howling Scorpius, Punk Spider, Gold Sahara |
| **Type** | Cybercriminal — Ransomware-as-a-Service (RaaS) |
| **Origin** | Unknown (Russian-speaking; Conti lineage suspected) |
| **Active Since** | March 2023 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **Advisory** | CISA/FBI/HHS/Europol AA24-109A (updated November 13, 2025) |

---

## Overview

Akira emerged in March 2023 and rapidly became one of the most prolific ransomware operations of the 2023–2025 period. By late September 2025, the group had extorted an estimated **$244.17 million USD** from victims across North America, Europe, and Australia. They operate as a Ransomware-as-a-Service (RaaS), recruiting affiliates in exchange for a cut of ransom proceeds.

The group bears strong Conti DNA — code overlap in string obfuscation, encryption routines, and file extension exclusion lists; shared financial infrastructure (confirmed cross-wallet transactions to Conti-affiliated addresses); and TTP similarities suggest former Conti members or affiliates played a founding role. Akira is not to be confused with an unrelated ransomware variant of the same name that briefly appeared in 2017.

Akira has two distinct payload lines:
- **Akira / Akira_v2** — C++ (Windows) and Linux/ESXi encryptors; appends `.akira` extension
- **Megazord** — Rust-based Windows encryptor (introduced August 2023); appends `.powerranges` extension

Both can be deployed simultaneously against different architectures in a single campaign.

---

## Target Profile

**Preferred sectors:**
- Manufacturing (critical)
- Education / Academia
- Information Technology / MSPs
- Healthcare & Public Health
- Financial Services
- Food & Agriculture
- Small and medium-sized businesses (SMBs) — primary focus, but large enterprises also targeted

**Geography:** Primarily US, UK, Canada, Australia, New Zealand; some European victims (including Finland — Tietoevry, Jan 2024; French — Peugeot Motocycles, Jan 2026)

**Hypervisor environments:** Specifically targeted; ESXi and Hyper-V are priority encryption targets

---

## Tactics, Techniques, and Procedures (TTPs)

### Initial Access
- **VPN exploitation without MFA** — signature vector; specifically targets Cisco ASA/FTD products
  - CVE-2020-3259 (Cisco — info disclosure)
  - CVE-2023-20269 (Cisco ASA — auth bypass)
  - CVE-2024-40766 (SonicWall — improper access control)
  - CVE-2024-37085 (VMware ESXi — auth bypass)
- **Veeam Backup & Replication exploitation:**
  - CVE-2023-27532 (missing authentication)
  - CVE-2024-40711 (deserialization — RCE)
- **Compromised VPN credentials** — purchased from Initial Access Brokers (IABs)
- **Password spraying** via SharpDomainSpray (T1110.003)
- **SSH tunneling** through router IP addresses (T1021.004)
- **Spearphishing** with malicious attachments and links (T1566.001, T1566.002)

### Persistence & Discovery
- Create domain accounts (T1136.002) — named `itadm` in documented incidents
- Kerberoasting (credential extraction from LSASS — T1003.001)
- Credential dumping: **Mimikatz**, **LaZagne**
- NTDS.dit + SYSTEM hive extraction for domain admin compromise (T1003.002, T1003.003)
- Network discovery: **SoftPerfect**, **Advanced IP Scanner**, **NetScan**, **AdFind**, **BloodHound/SharpHound**
- Domain reconnaissance: `net` commands, `nltest`, `Get-ADComputer`, `Get-ADUser`

### Privilege Escalation
- Exploit Veeam.Backup.MountService.exe (CVE-2024-40711)
- Pass-the-Hash (T1550.002)
- Kerberoasting → domain admin access

### Defense Evasion
- Kill AV/EDR via **PowerTool** (exploits Zemana AntiMalware driver — BYOVD)
- **POORTRY** (signed vulnerable driver) + **STONETOP** loader (BYOVD — T1068)
- Uninstall EDR systems entirely (T1562.001)
- Disable Windows Defender real-time monitoring
- Run malware inside a **virtual machine** to hide from host-based detection (T1564.006)
- Masquerading (T1036); obfuscation via Base64/XOR encoding
- 7-zip archive compression (T1027.015)

### Lateral Movement
- RDP (T1021.001), SSH (T1021.004)
- **Impacket wmiexec.py** (T1047)
- **MobaXterm**, **AnyDesk**, **LogMeIn**, **RustDesk** (T1219)
- Cobalt Strike (C2 + lateral movement)

### C2
- Tunneling via **Ngrok** (T1572) — encrypted, bypasses perimeter
- **Cloudflare Tunnel**
- **SystemBC** SOCKS5 proxy (Coroxy)
- AnyDesk, RustDesk, MobaXterm as persistent remote access

### Exfiltration (BEFORE encryption)
- **WinRAR**, **FileZilla**, **WinSCP**, **7-zip**, **RClone**
- Exfil to **Mega.nz** cloud storage (T1537, T1567.002)
- SFTP, FTP
- Ngrok tunnels for covert exfil

**Speed:** In documented incidents, data exfiltration occurred in as little as **2 hours** from initial access.

### Impact — Encryption
- **Hybrid encryption:** ChaCha20 (file encryption) + RSA-4096 (key encryption) — Windows variant
- **Linux variant:** AES, CAMELLIA, DES, IDEA (symmetric); keys generated using timestamp seeds (nanosecond precision, 1,500 SHA-256 rounds) — brute-forced by researcher with 16× RTX 4090 GPUs in ~10 hours
- Volume Shadow Copy deletion: `powershell.exe -Command "Get-WmiObject Win32_Shadowcopy | Remove-WmiObject"` (T1490)
- ESXi encryptor arguments: `--encryption_path`, `--share_file`, `--encryption_percent`, `--fork`
- Target files are `icacls`-modified to grant full access before encryption

### Extortion Model
- **Double extortion:** steal data, then encrypt, then threaten leak
- Ransom demands: **$200,000 – $4,000,000+ USD** (Bitcoin)
- Ransom note contains unique victim code; victim contacts operators via Tor .onion chat
- **No initial ransom demand on-network** — operators withhold amount until victim makes contact
- Dedicated Leak Site (DLS) on Tor; stolen data distributed via torrent links (not direct hosted download — mirrors Cl0p's technique)
- Known to **phone victim organizations** directly to pressure payment (FBI reporting)

---

## Notable Incidents

| Date | Victim | Detail |
|---|---|---|
| Dec 2023 | Finnish NCSC | Cisco CVE-2023-20269 campaign; wiped NAS devices |
| Jan 2024 | Tietoevry (Finland) | Cloud hosting provider; significant downstream impact |
| 2024–2025 | Multiple US healthcare orgs | HHS flagged as top-targeted sector |
| Various | SMBs globally | Hundreds of victims across 2023–2025 per CISA/FBI data |

---

## Connections to Existing Profiles

- **Conti** — Suspected predecessor. Code overlap (ChaCha algo, string obfuscation, file extension exclusion), shared financial wallet infrastructure, TTP similarities. Former Conti members/affiliates assessed as probable founders/operators.
- **#021 Payouts King** — Conti → Black Basta → Payouts King is a parallel lineage; Akira → same Conti origin, different branch. Shared talent pool ancestry.
- **#020 BlackCat/ALPHV** — Same generation of "post-Conti" RaaS operations; share the "professional criminal enterprise" model inherited from Conti.
- **#015 LockBit** — Operated concurrently in the same affiliate market; some IAB and affiliate overlap reported.
- **#018 Cl0p** — Both used torrent-based leak site distribution (distinctive shared tactic). Parallel operation in the same criminal ecosystem.

---

## Indicators of Compromise

See `iocs.md` for full IOC listing.

---

## MITRE ATT&CK Summary

| Tactic | Key Techniques |
|---|---|
| Initial Access | T1078, T1133, T1190, T1566.001, T1566.002, T1110.003 |
| Execution | T1059.001, T1059.003, T1059.005, T1047 |
| Persistence | T1136.002, T1547.001, T1547.009 |
| Privilege Escalation | T1068, T1078, T1550.002 |
| Defense Evasion | T1027, T1027.015, T1036, T1562.001, T1564.002, T1564.006 |
| Credential Access | T1003.001, T1003.002, T1003.003 |
| Discovery | T1016, T1018, T1057, T1082, T1083, T1087.001, T1087.002 |
| Lateral Movement | T1021.001, T1021.004, T1570 |
| C2 | T1090, T1572, T1219 |
| Exfiltration | T1020, T1029, T1041, T1048, T1537, T1567.002 |
| Impact | T1486, T1489, T1490, T1491.001, T1531, T1657 |

---

## Defensive Mitigations

1. 🔴 **MFA on ALL VPN endpoints** — their #1 entry point. Hardware key preferred.
2. 🔴 **Patch Cisco ASA/FTD immediately** — CVE-2020-3259, CVE-2023-20269 are actively exploited
3. 🔴 **Patch Veeam Backup & Replication** — CVE-2023-27532, CVE-2024-40711
4. 🔴 **Patch SonicWall** — CVE-2024-40766
5. 🔴 **Alert on `itadm` account creation** — signature persistence action
6. 🔴 **Detect and block BYOVD patterns** (PowerTool / Zemana driver abuse; POORTRY)
7. 🟡 **Block Ngrok and Cloudflare Tunnel on enterprise networks** where not authorized
8. 🟡 **Monitor Rclone outbound traffic** — primary exfil tool
9. 🟡 **Protect VSS / block `vssadmin delete shadows`** — anti-ransomware baseline
10. 🟡 **Restrict AnyDesk, LogMeIn, RustDesk** to authorized use only; alert on unexpected RMM
11. 🟢 **Offline immutable backups** — tested regularly
12. 🟢 **BloodHound your own environment** before attackers do

---

## Intelligence Grading

| Field | Value |
|---|---|
| **Intel Grade** | A1 |
| **TLP** | TLP:WHITE |
| **Last Updated** | 2026-05-14 |
| **Primary Sources** | CISA/FBI/HHS AA24-109A (Nov 2025), Arctic Wolf Labs, Qualys, Blackpoint Cyber, HHS HC3, Picus Security, Arete IR April 2026, Check Point Q1 2026 Ransomware Report |

---

## 📡 Intelligence Update — 2026-05-14

### Q1 2026: Second Most Active Ransomware Group; 41% Market Share with Qilin/LockBit/Gentlemen

Check Point Research's **State of Ransomware Q1 2026** report (May 11, 2026) confirmed Akira as the **second most active ransomware group** in Q1 2026, behind Qilin. Akira, Qilin, LockBit, and The Gentlemen together accounted for **41% of all 2,122 Q1 victims** posted on data leak sites.

Akira was the **#1 group for every month from July 2025 through March 2026** (dethroned by Qilin in April 2026). The group maintained consistent victim volumes throughout Q1 with heavy targeting of **Consumer Goods** (23.9%, +9.8 pp above baseline) and **Industrial Manufacturing** (17.8%, +6.7 pp above baseline) — consistent with an economically optimized disruption model.

### New TTP: BYOVD with rwdrv.sys and hlpdrv.sys (Confirmed April 2026)

Arete IR confirmed Akira has adopted **Bring Your Own Vulnerable Driver (BYOVD)** techniques in multiple Q3 2025 and Q1–Q2 2026 engagements:

- **rwdrv.sys** — vulnerable driver loaded via DLL side-loading
- **hlpdrv.sys** — second vulnerable driver used in same BYOVD chain

Akira leverages these drivers to **gain kernel-level access and disable security processes** (EDR/AV) before ransomware deployment. This technique bypasses user-mode endpoint protection and renders traditional AV/EDR ineffective if not defended at the driver/kernel level.

*Note: DragonForce was also observed using the same vulnerable drivers in overlapping engagements, suggesting shared affiliate tradecraft or tooling exchange.*

### New TTP: Microsoft Teams IT Help Desk Impersonation (Resurgent, 2026)

Akira (along with Payouts King ransomware group) has resurrected the **Microsoft Teams IT help desk social engineering** tactic originally pioneered by Black Basta and Cactus in 2024–2025:

1. Threat actors create external Microsoft Teams accounts impersonating IT or help desk staff
2. Reach out to employees via **cross-tenant Teams chat** claiming to resolve a security incident
3. Trick employees into installing remote access tools (**Quick Assist**, AnyDesk, etc.)
4. Use remote access to gain initial foothold and conduct data theft or ransomware deployment
5. **Rclone** utility used for data exfiltration to cloud storage

Microsoft has observed multiple intrusions following this exact chain linked to Akira. Organizations should brief employees that legitimate IT help desk staff will **never contact users via external Teams tenant accounts** to request remote access installation.
