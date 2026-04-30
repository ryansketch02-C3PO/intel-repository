# IOCs — RansomHub (Scoundrel #030)

> Sources: CISA AA24-242A (August 2024), Arete IR (2024), Symantec, GuidePoint GRIT
> Status: Platform DARK as of April 1, 2025 — IOCs remain relevant; affiliate tradecraft persists on Qilin/DragonForce
> TLP: WHITE | Last updated: 2026-04-30

---

## File Hashes (SHA-256)

| Hash | Description | Notes |
|---|---|---|
| `de7913504efe4584bdd9dd1ec13c4de4152a84df5e1cbc31d0dd8fe70c88b5e0` | RansomHub ransomware encryptor (Windows) | Requires `-pass [SHA-256]` to execute |
| `aae552a3f15e8e0e750a9c4a6c4c2e9b3a89765042f19491f0f9d39d42476855` | RansomHub Linux/ESXi variant | ELF encryptor; targets VMware ESXi datastores |

---

## File System Indicators

| Indicator | Type | Notes |
|---|---|---|
| `README_[6-char-extension].txt` | Ransom note | In every encrypted directory; 6-char extension = first 6 chars of Curve25519 public key |
| `[6-char-extension]` appended to files | Encrypted file extension | Unique per engagement |
| `%TEMP%\[random].bmp` | Desktop wallpaper | Dropped and set as wallpaper during execution |

---

## Registry Indicators

| Key / Value | Notes |
|---|---|
| `HKCU\Control Panel\Desktop\Wallpaper` modified | RansomHub sets ransom note wallpaper |
| `HKLM\SYSTEM\CurrentControlSet\Control\SafeBoot\Minimal\[service]` modified | Safeboot persistence (-safeboot flag) |
| Defender tamper (various HKLM\SOFTWARE\Policies paths) | AV disabling pre-encryption (affiliate-dependent) |

---

## Process / Command Indicators

| Indicator | Type | Notes |
|---|---|---|
| `RansomHub.exe -pass [64-char SHA-256]` | Execution pattern | Will not run without valid -pass argument |
| `powershell.exe -Command Get-VM \| Stop-VM -Force` | VM shutdown | Forces shutdown of all VMs before encryption |
| `vssadmin delete shadows /all /quiet` | VSS deletion | Inhibit recovery |
| `wmic shadowcopy delete` | VSS deletion | Alternate method |
| `fsutil behavior set SymlinkEvaluation R2L:1` | Symlink policy | Enables remote-to-local symbolic links for share encryption |
| `wevtutil cl [log]` | Event log clear | Post-execution cleanup |
| `net stop [service]` (bulk) | Service termination | Kills AV, backup, database services before encryption |

---

## Tools Used by Affiliates

| Tool | Purpose | Detection Notes |
|---|---|---|
| AnyDesk | Remote access / persistence | Legitimate tool — flag on server systems outside approved deployment |
| Atera | RMM / C2 | Same — flag unapproved instances |
| Splashtop | Remote access | Same |
| ConnectWise ScreenConnect | Remote access | Same |
| Cobalt Strike / Metasploit | Post-exploitation | Anomalous beacon traffic; named pipe patterns |
| WinSCP | Exfiltration | Flag staging large data transfers to external destinations |
| Rclone | Exfiltration | Cloud sync to attacker-controlled storage; flag on servers |
| AWS S3 CLI (`aws s3 cp`) | Exfiltration | Legitimate tool — flag bulk uploads to non-corporate buckets |
| PsExec | Lateral movement | Flag execution from non-admin workstations; look for service creation events |
| ADRecon / SharpHound | Recon | LDAP queries; BloodHound collection artifacts |

---

## Exploited CVEs (Active Hunt)

> These CVEs remain actively exploited by RansomHub successor platforms (Qilin, DragonForce, LockBit 5.0).

| CVE | Product | CVSS | Notes |
|---|---|---|---|
| CVE-2024-1709 | ConnectWise ScreenConnect | 10.0 | Auth bypass; patch immediately |
| CVE-2023-3519 | Citrix NetScaler ADC/Gateway | 9.8 | Unauthenticated RCE |
| CVE-2023-27997 | Fortinet FortiOS SSL-VPN | 9.8 | Heap overflow; pre-auth |
| CVE-2023-46805 | Ivanti Connect Secure | 8.2 | Auth bypass |
| CVE-2024-21887 | Ivanti Connect Secure | 9.1 | Command injection |
| CVE-2023-4966 (Citrix Bleed) | Citrix NetScaler | 9.4 | Session token leak |
| CVE-2023-48788 | Fortinet FortiClientEMS | 9.8 | SQL injection |
| CVE-2023-34362 | Progress MOVEit Transfer | 9.8 | SQL injection; mass exploitation |
| CVE-2021-26084 | Atlassian Confluence | 9.8 | OGNL injection |
| CVE-2023-22515 | Atlassian Confluence | 9.8 | Privilege escalation |

---

## Behavioral Detection Rules

```yaml
# Hunt: Unauthorized RMM tool on server
Process: [anydesk.exe, atera_agent.exe, splashtop_streamer.exe, ScreenConnect.Client.exe]
Host: Server role (not approved deployment)
Action: Alert P1

# Hunt: Rclone or WinSCP mass transfer
Process: [rclone.exe, WinSCP.exe]
Network: External destination (non-corporate)
Data volume: > 1GB in 60s
Action: Alert P1

# Hunt: VSS deletion
EventID: 8193 OR 8194 OR 8196 (VSS)
OR CommandLine contains: "delete shadows" OR "shadowcopy delete"
Action: Alert P1 — potential active ransomware

# Hunt: Bulk service termination
EventID: 7036 (service stopped)
Count: > 10 services in 60s from same process
Action: Alert P1

# Hunt: Safeboot persistence
Registry: HKLM\SYSTEM\CurrentControlSet\Control\SafeBoot\Minimal\*
Write by: non-system process
Action: Alert P1

# Hunt: Mass file extension change
FileExtension changes: > 100 files in 30s with same new extension
Action: Alert P1 — active encryption in progress
```

---

*IOC file created: 2026-04-30 | Author: C3PO | TLP: WHITE*
