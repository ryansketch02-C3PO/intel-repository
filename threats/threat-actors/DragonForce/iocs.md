# IOCs — DragonForce (Scoundrel #033)

> Sources: Anomali Threat Intelligence (Nov 2025), Ransomware.live, UK NCSC, Breachsense (2026)
> TLP: WHITE | Last updated: 2026-05-02

---

## ⚠️ IOC Note

DragonForce's white-label "cartel" model means attacks launched by its affiliates may appear under different group names. IOCs below include DragonForce-attributed activity; affiliate-specific IOCs (e.g. Scattered Spider social engineering infrastructure) are maintained in those actors' profiles.

---

## File System Indicators

| Indicator | Type | Notes |
|---|---|---|
| DragonForce encryptor binary (cross-platform) | Ransomware payload | Conti v3 + LockBit 3.0 lineage; Windows, Linux, ESXi variants |
| DragonForce ransom note | Ransom note | Filename pattern varies by affiliate brand (white-label); typically `README` or `DECRYPT` variant |
| PSEXESVC / PsExec staging artifacts | Lateral movement | PsExec used for domain-wide spread |

---

## BYOVD Drivers (EDR Kill)

> DragonForce affiliates use BYOVD technique to disable EDR at kernel level. Flag loading of these drivers outside approved vendor software.

| Driver | Vendor | Abuse Notes |
|---|---|---|
| Toshiba power management driver | Toshiba | Vulnerable signed driver; loads in non-Toshiba hardware contexts |
| Carbon Black Cloud Sensor updater (`upd.exe`) | VMware Carbon Black | Abused to disable CB sensor |
| Zemana Anti-Rootkit driver | Zemana | Used to kill security processes |
| Various BYOVD drivers from public lists | Multiple | Cross-reference with LOLDrivers.io database |

---

## Behavioral Indicators

| Indicator | Description | Confidence |
|---|---|---|
| Help desk password reset / MFA change followed by new device enrollment from unknown IP | Scattered Spider initial access pattern | 🔴 HIGH |
| RMM tool (AnyDesk, TeamViewer) installed by newly enrolled credential | Post-social-engineering persistence | 🔴 HIGH |
| `vssadmin delete shadows /all /quiet` | Pre-encryption VSS deletion | 🔴 HIGH |
| Signed-but-vulnerable driver loaded from non-standard path | BYOVD EDR killer | 🔴 HIGH |
| BYOVD driver loading → security process termination cascade | EDR kill chain | 🔴 HIGH |
| Bulk file extension change across multiple directories | Active ransomware encryption | 🔴 HIGH (active incident) |
| Large Rclone transfer to external cloud storage (>50GB) | Pre-encryption data exfiltration | 🔴 HIGH |
| Long-dwell low-and-slow recon: domain enumeration, AD queries | 2-month pre-detonation dwell pattern | 🟡 MEDIUM |
| MFA push notifications sent in rapid succession | MFA fatigue attack in progress | 🔴 HIGH |
| SIM swap indicators: carrier account access from new device | Scattered Spider entry method | 🔴 HIGH |
| Cobalt Strike beacon traffic | C2 post-exploitation | 🟡 MEDIUM |
| VSS deletion + bulk file change within same session | Full DragonForce attack chain final phase | 🔴 HIGH |

---

## Leaked UK Retail Attack Infrastructure (Scattered Spider / DragonForce — 2025)

> These indicators are from the M&S/Co-op/Harrods campaign (April–May 2025). Infrastructure rotates but patterns persist.

| Indicator | Type | Notes |
|---|---|---|
| Astrill VPN egress IPs | Network | Scattered Spider consistently uses Astrill VPN to mask geographic origin |
| Azure Virtual Desktop (AVD) login fingerprint from Russian ASNs | Session pattern | Shared with Jasper Sleet pattern (#032); both use US-proxy-via-AVD approach |
| Phone call to IT help desk from US-accented English speaker claiming IT emergency | Social engineering | Canonical Scattered Spider entry; Caller ID spoofing often used |
| New MFA device enrolled immediately after help desk call | Identity telemetry | Follow-on to successful help desk social engineering |
| After-hours or holiday weekend timing of payload detonation | Attack timing | M&S hit Easter weekend; deliberate reduced-IT-staff timing |

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Exploit Public-Facing Application | T1190 | Some campaigns |
| Initial Access | Valid Accounts | T1078 | Credential-based access via social engineering |
| Initial Access | Phishing — Spearphishing | T1566 | Lures for Scattered Spider affiliate delivery |
| Initial Access | Multi-Factor Authentication Request Generation | T1621 | MFA fatigue attacks |
| Execution | PowerShell | T1059.001 | LOLBin lateral movement |
| Persistence | Create/Modify System Process: Windows Service | T1543.003 | Malicious service creation |
| Defense Evasion | Impair Defenses: BYOVD | T1562.001 | Kernel-level EDR kill |
| Credential Access | OS Credential Dumping | T1003 | Mimikatz, LSASS |
| Discovery | System Information Discovery | T1082 | Post-access recon |
| Lateral Movement | Remote Desktop Protocol | T1021.001 | Domain-wide lateral movement |
| Collection | Data from Local System | T1005 | Staging for exfil |
| Exfiltration | Exfiltration to Cloud Storage | T1567.002 | Rclone to attacker-controlled cloud |
| Impact | Data Encrypted for Impact | T1486 | DragonForce ransomware payload |
| Impact | Inhibit System Recovery | T1490 | VSS deletion |

---

## Detection Queries

```kql
// Hunt: Help desk MFA change followed by new device enrollment from new IP
AuditLogs
| where OperationName in ("Update user", "Reset user password", "Delete registered MFA")
| join kind=inner (
    SigninLogs
    | where DeviceDetail !in (known_devices)
    | where TimeGenerated > ago(1h)
) on $left.InitiatedBy.user.userPrincipalName == $right.UserPrincipalName
| project TimeGenerated, OperationName, IPAddress, DeviceDetail, UserPrincipalName

// Hunt: BYOVD driver loading outside known vendor paths
DeviceDriverEvents
| where ActionType == "DriverLoad"
| where not(FolderPath has_any (@"C:\Windows\System32\drivers\",
                                  @"C:\Program Files\",
                                  @"C:\Program Files (x86)\"))
| where InitiatingProcessFileName !in (known_driver_loaders)
| project Timestamp, DeviceName, DriverName, FolderPath, InitiatingProcessFileName, SHA256

// Hunt: MFA fatigue — rapid successive auth requests
SigninLogs
| where AuthenticationRequirement == "multiFactorAuthentication"
| where ResultType == "500121"  // MFA denied
| summarize RequestCount = count(), FirstRequest = min(TimeGenerated), LastRequest = max(TimeGenerated)
  by UserPrincipalName, IPAddress, bin(TimeGenerated, 5m)
| where RequestCount > 5
| order by RequestCount desc

// Hunt: Rclone large transfers
DeviceNetworkEvents
| where InitiatingProcessFileName =~ "rclone.exe"
| where RemotePort in (443, 80, 22)
| summarize TotalBytes = sum(SentBytes), Connections = count()
  by DeviceName, RemoteUrl, InitiatingProcessCommandLine, bin(Timestamp, 1h)
| where TotalBytes > 1073741824  // >1GB
| order by TotalBytes desc

// Hunt: VSS deletion + bulk encryption in same session
let vss_events = DeviceProcessEvents
    | where ProcessCommandLine has_any ("vssadmin delete shadows", "shadowcopy delete")
    | project DeviceName, VSSTime = Timestamp, InitiatingProcessAccountName;
let encrypt_events = DeviceFileEvents
    | where ActionType == "FileModified"
    | summarize ModCount = count(), FirstMod = min(Timestamp) by DeviceName, bin(Timestamp, 5m)
    | where ModCount > 500;
vss_events
| join kind=inner encrypt_events on DeviceName
| where FirstMod between (VSSTime .. datetime_add('minute', 30, VSSTime))
| project DeviceName, VSSTime, FirstMod, ModCount, InitiatingProcessAccountName
```

---

*IOC file created: 2026-05-02 | Author: C3PO | TLP: WHITE*
