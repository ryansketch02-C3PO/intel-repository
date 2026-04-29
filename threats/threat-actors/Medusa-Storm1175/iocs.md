# IOCs — Medusa Ransomware / Storm-1175 (Scoundrel #028)

> Source: Microsoft Threat Intelligence (April 2026), CISA AA25-071A (March 2025)
> TLP: WHITE | Last updated: 2026-04-29

---

## File Hashes (SHA-256)

| Hash | Filename | Description | First Seen | Last Seen |
|---|---|---|---|---|
| `0cefeb6210b7103fd32b996beff518c9b6e1691a97bb1cda7f5fb57905c4be96` | `gaze.exe` | Medusa ransomware payload (AES-256 encryptor; appends `.medusa`) | 2026-03-01 | 2026-03-01 |
| `9632d7e4a87ec12fdd05ed3532f7564526016b78972b2cd49a610354d672523c` | `lsp.exe` | Rclone (data exfil tool — also seen in other ransomware intrusions) | 2024-04-01 | 2026-02-18 |
| `e57ba1a4e323094ca9d747bfb3304bd12f3ea3be5e2ee785a3e656c3ab1e8086` | `main.exe` | SimpleHelp (RMM — used for C2/persistence) | 2026-01-15 | 2026-01-15 |
| `5ba7de7d5115789b952d9b1c6cff440c9128f438de933ff9044a68fff8496d19` | `moon.exe` | SimpleHelp (RMM — alternate deployment) | 2025-09-15 | 2025-09-22 |

---

## Network Indicators (IPs)

| IP | Type | ASN / Provider | Notes | First Seen | Last Seen |
|---|---|---|---|---|---|
| `185.135.86[.]149` | SimpleHelp C2 | — | Storm-1175 controlled SimpleHelp server | 2024-02-23 | 2026-03-15 |
| `134.195.91[.]224` | SimpleHelp C2 | — | Storm-1175 controlled SimpleHelp server | 2024-02-23 | 2026-02-26 |
| `85.155.186[.]121` | SimpleHelp C2 | — | Storm-1175 controlled SimpleHelp server | 2024-02-23 | 2026-02-12 |

---

## File System Indicators

| Indicator | Type | Notes |
|---|---|---|
| `.medusa` | Ransomware extension | Files encrypted by Medusa payload; appended to all encrypted filenames |
| `RunFileCopy.cmd` | Batch script | Delivered by PDQ Deployer to trigger Medusa ransomware deployment network-wide |
| `gaze.exe` | Ransomware payload | Medusa encryptor binary |
| `conhost.exe` (anomalous) | Renamed Cloudflare tunnel | Legitimate `conhost.exe` in System32; malicious version is Cloudflare tunnel renamed for evasion — check hash + path |
| `NTDS.dit` (unusual access) | AD database dump | Unexpected access to NTDS.dit from non-DC admin processes indicates credential theft |

---

## Registry Indicators

| Key / Value | Notes |
|---|---|
| `HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest\UseLogonCredential = 1` | WDigest cache enabled — forces cleartext credential caching; Storm-1175 TTPs |
| Defender exclusion added for `C:\` | AV exclusion for entire C drive — highly anomalous; Storm-1175 sets this pre-deployment |
| Defender registry tampering (HKLM\SOFTWARE\Policies\Microsoft\Windows Defender) | AV disable; requires high privilege |

---

## Behavioral Indicators (High Confidence)

| Behavior | Event / Detection | Confidence |
|---|---|---|
| LSASS memory access from non-security tool | Process access to `lsass.exe` outside Defender/EDR processes | 🔴 HIGH |
| Rclone executing on server infrastructure | `rclone.exe` or `lsp.exe` running on server; cloud sync to non-corporate destination | 🔴 HIGH |
| RMM tool not in approved list executing on server | AnyDesk, Atera, SimpleHelp, MeshAgent, N-able, DWAgent, Level RMM, ConnectWise outside known deployment | 🔴 HIGH |
| `RunFileCopy.cmd` execution via PDQ Deployer | PDQ Deployer running scripts across domain — particularly `RunFileCopy.cmd` | 🔴 HIGH |
| `.medusa` extension appearing on file system | Active ransomware encryption in progress | 🔴 HIGH (active incident) |
| New local admin account creation followed by rapid lateral movement | `net user /add` then immediate PsExec/Impacket activity | 🔴 HIGH |
| `cloudflared.exe` or renamed `conhost.exe` on server | Cloudflare tunnel deployed on server — rare legitimate use case | 🔴 HIGH |
| WDigest `UseLogonCredential = 1` set | Cleartext credential caching enabled | 🟡 MEDIUM (unusual; investigate) |
| Bandizip running on server staging large archives | File archiving prior to exfil | 🟡 MEDIUM |
| Mass file access + encryption spike | EDR file activity anomaly | 🔴 HIGH (active incident) |

---

## Defender Coverage

| Detection | Tool | Notes |
|---|---|---|
| `Ransom:Win32/Medusa` | Microsoft Defender AV | Medusa payload signature |
| `Behavior:Win32/SAMDumpz` | Microsoft Defender AV | SAM hive dumping |
| `Behavior:Win32/PsexecRemote` | Microsoft Defender AV | PsExec lateral movement |
| Ransomware-linked threat actor detected | Defender for Endpoint | Storm-1175 activity alert |
| User account created under suspicious circumstances | Defender for Endpoint | New admin account creation |
| Possible exploitation of GoAnywhere MFT vulnerability | Defender for Endpoint | CVE-2025-10035 |
| Suspicious Atera activity | Defender for Endpoint | RMM abuse |
| Renaming of legitimate tools for possible data exfiltration | Defender for Endpoint | Rclone rename |

---

## Exploited CVEs (Active Hunt — Patch Status)

> Prioritize internet-facing deployments of these products. Storm-1175 exploits within hours to days of disclosure.

| CVE | Product | CVSS | Zero-Day? |
|---|---|---|---|
| CVE-2025-10035 | Fortra GoAnywhere MFT | 10.0 | ⚠️ YES — exploited 1 week before disclosure |
| CVE-2026-23760 | SmarterTools SmarterMail | TBD | ⚠️ YES — exploited 1 week before disclosure |
| CVE-2025-31324 | SAP NetWeaver | High | Exploited 1 day after disclosure |
| CVE-2026-1731 | BeyondTrust | TBD | N-day |
| CVE-2024-1709 | ConnectWise ScreenConnect | 10.0 | N-day |
| CVE-2024-27198 | JetBrains TeamCity | 9.8 | N-day |
| CVE-2024-57726/27/28 | SimpleHelp | High | N-day |
| CVE-2025-31161 | CrushFTP | Critical | N-day |
| CVE-2023-46805 | Ivanti Connect Secure | 8.8 | N-day |

---

*IOC file created: 2026-04-29 | Author: C3PO | TLP: WHITE*
