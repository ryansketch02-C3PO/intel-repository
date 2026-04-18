# MuddyWater — Indicators of Compromise

> Admiralty Grade: A1-A2 (mix of government advisory + vendor research)
> TLP: WHITE
> Last Updated: 2026-04-18

---

## Network Infrastructure

### IP Addresses
| IP | Type | Context | Source |
|---|---|---|---|
| `157.20.182.49` | C2 / Ops Server | Primary MOIS sdrhi server — Germany-hosted; exposed Mar 2026 | ThreatHunter.ai / Cryptika |
| `162.0.230.185` | C2 | Secondary payload server (sdrhi cluster) | ThreatHunter.ai |
| `194.11.246.101` | C2 | FortiOS exploit source (sdrhi cluster) | ThreatHunter.ai |
| `18.223.24.218` | Exfil | AWS EC2 data exfiltration staging | ThreatHunter.ai |
| `82.25.35.25` | C2 | Handala C2 (historical — wiper operations) | ThreatHunter.ai |

### C2 Channels
| Indicator | Type | Notes |
|---|---|---|
| `api.telegram.org` | C2 channel | Small Sieve, GRAMDOOR, Dindoor — all route C2 via Telegram Bot API |
| Ethereum RPC endpoints (10 providers) | C2 channel | ChainShell resolves C2 address from Ethereum smart contract |
| Wasabi Cloud Storage (`s3.wasabisys.com`) | Exfil | Rclone exfiltration destination |
| Backblaze B2 (`backblaze.com`) | Exfil | Rclone exfiltration destination |
| `put.io` | Exfil | Rclone exfiltration destination |
| Firebase-hosted staging pages | Staging | Payload delivery via legitimate Google infrastructure |

---

## File Indicators

### Known Malicious Filenames / Paths
| Filename / Path | Tool | Notes |
|---|---|---|
| `Goopdate.dll` (non-Google path) | PowGoop | Loaded via `GoogleUpdate.exe` DLL side-loading |
| `config.txt` (alongside GoogleUpdate.exe) | PowGoop | Encrypted C2 configuration |
| `gram_app.exe` | Small Sieve | Python Telegram backdoor |
| `FML.dll` | Mori | DNS tunneling C2 backdoor |
| `reset.ps1` | ChainShell deployer | PowerShell installer — sets up Node.js + ChainShell |
| `sysuu2etiprun.js` | ChainShell | Blockchain C2 agent |
| `VfZUSQi6oerKau.js` | ChainShell dropper | Installer component |
| `%LOCALAPPDATA%\Nodejs\` | ChainShell | Unexpected Node.js installation path |
| `Virtual{Campaign}Guy{N}` | ChainShell | Scheduled task naming pattern |
| `OutlookMicrosift.exe` (deliberate typo) | Generic masquerade | Filename masquerading technique |

### Code-Signing Cert Subjects (Stolen/Abused — 2026)
| Subject | Context |
|---|---|
| `Amy Cherne` | Stolen cert used to sign Dindoor/Fakeset — Feb 2026 campaign |
| `Donald Gay` | Stolen cert used to sign Dindoor/Fakeset — Feb 2026 campaign |

---

## Registry Indicators
| Key | Value | Notes |
|---|---|---|
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | Malicious entry (varies) | Registry Run Key persistence |
| `HKCU\...\Run` → value name: `SystemTextEncoding` | POWERSTATS variant | Known malicious Run Key name |

---

## CVEs Actively Exploited by MuddyWater
| CVE | Product | CVSS | Notes |
|---|---|---|---|
| CVE-2020-1472 | Windows Netlogon | 10.0 Critical | ZeroLogon — still in active use for lateral movement |
| CVE-2020-0688 | Microsoft Exchange | 8.8 High | Exchange RCE — early toolkit |
| CVE-2017-0199 | Office/WordPad | 7.8 High | Early initial access vector |
| CVE-2024-55591 | FortiOS | Critical | Auth bypass — modified exploit creates super_admin accounts |
| CVE-2025-54068 | Laravel Livewire v3 | High | RCE — 2026 targeting |
| CVE-2026-1281 | Ivanti EPMM | Critical | 2026 targeting |
| CVE-2026-24061 | GNU Inetutils telnetd | High | Auth bypass — 2026 targeting |

---

## Behavioral / Process Indicators

### POWERSTATS Detection
```yaml
EventID: 4104  # PowerShell ScriptBlock Logging
Pattern: Base64 string + string reversal + GZip decompression
Alert: CRITICAL — MuddyWater obfuscation fingerprint
```

### PowGoop Detection
```yaml
Process: GoogleUpdate.exe
LoadedDLL: path NOT matching %PROGRAMFILES%\Google\Update\*
Alert: HIGH
```

### Small Sieve / Telegram C2
```yaml
NetworkConnection: api.telegram.org:443
InitiatingProcess: NOT IN [telegram.exe, known-telegram-clients]
Alert: HIGH
```

### ChainShell
```yaml
FileCreated: %LOCALAPPDATA%\Nodejs\*
ScheduledTask: name MATCHES "Virtual*Guy*"
Alert: HIGH
```

### Mori DNS Tunneling
```yaml
DNS: Query entropy > 4.0 bits/char
SubdomainLength: > 50 characters
Frequency: Burst (>10 queries/min) from same endpoint
Alert: MEDIUM
```

### Rclone Exfiltration
```yaml
Process: rclone.exe
CommandLine: CONTAINS ["wasabi", "backblaze", "put.io"]
Alert: HIGH — active exfiltration
```

### makecab.exe Pre-Exfil Staging
```yaml
Process: makecab.exe
OutputDirectory: CONTAINS [%TEMP%, %APPDATA%, C:\Users\*]
Alert: MEDIUM
```

### Unauthorized RMM
```yaml
ProcessCreated: [SimpleHelp.exe, AnyDesk.exe, Atera.exe, ScreenConnect.exe, NetBird.exe, Syncro.exe]
Condition: NOT IN approved software inventory
Alert: HIGH
```

---

## MITRE ATT&CK Mapping (Key Techniques)

| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Spearphishing Attachment | T1566.001 |
| Initial Access | Exploit Public-Facing Application | T1190 |
| Execution | PowerShell | T1059.001 |
| Execution | Windows Script File | T1059.005 |
| Persistence | Registry Run Keys | T1547.001 |
| Persistence | Scheduled Task | T1053.005 |
| Persistence | Remote Access Software | T1219 |
| Defense Evasion | Obfuscated Files or Information | T1027 |
| Defense Evasion | DLL Side-Loading | T1574.002 |
| Defense Evasion | Impair Defenses (AMSI bypass) | T1562.001 |
| Defense Evasion | Masquerading | T1036 |
| Credential Access | OS Credential Dumping (LSASS) | T1003.001 |
| Credential Access | Credentials from Password Stores | T1555 |
| Lateral Movement | Exploitation (ZeroLogon) | T1210 |
| Lateral Movement | Kerberoasting | T1558.003 |
| Collection | Screen Capture | T1113 |
| Exfiltration | Exfiltration to Cloud Storage | T1567.002 |
| C2 | Application Layer Protocol (Telegram) | T1071.001 |
| C2 | DNS Tunneling | T1071.004 |

---

## Source References
- CISA Advisory AA22-055A
- CISA MAR-10369127-1.v1 (PowGoop, Small Sieve, Canopy, Mori)
- ThreatHunter.ai Unified Brief + Detection Pack v3 (Stryker/Handala/MuddyWater)
- Trellix Iranian Cyber Capability 2026
- Cryptika / CyberSecurityNews ChainShell Campaign
- Ankura CTIX Dindoor/Fakeset Campaign (March 2026)
