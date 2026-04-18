# 🔍 APT41 — Indicators of Compromise

**Last Updated:** 2026-04-15  
**Sources:** ESET Winnti IOC repo, MITRE ATT&CK G0096, Mandiant, Google GTIG, Zscaler ThreatLabz, Proofpoint

> ⚠️ IOCs decay over time. Cross-reference against current threat intel feeds before actioning. Treat IPs/domains with caution — infrastructure rotates frequently.

---

## 🧬 Malware Hashes (SHA-1)

### Winnti RAT
| SHA-1 | Detection Name |
|---|---|
| `B08D72576B93687DFC61ABFA740DD39490D6A262` | Win64/Agent.HE |
| `DE197A5DC5B38E4B72BC37C14CF38E577DDEB8B5` | Win64/Winnti.BE |
| `4EA2ED895111A70B9A59DF37343440E4A3A97A47` | Win64/Winnti.BE |
| `C452BDF6FF99243A12789FF4B99AC71A5DA5F696` | Win64/Agent.HE |
| `723B27ABA08CBB3A9CA42F7E8350451D00829E5A` | Win64/Winnti.BK |
| `55155C3A7B993584A07ACDBF92F2200804C00E02` | Win64/Winnti.BM |

### PortReuse Backdoor
| SHA-1 | Detection Name |
|---|---|
| `395e87c5bd00f78bf4c63880c6982a7941a2ecd0` | MSIL/Injector.UNL |
| `E14A6A8447CE1D45494E613D6327430D9025A2E5` | Win64/Winnti.CG |
| `A1AED6FD6990A74590864F9D2A6E714A715FCE3E` | Win64/Winnti.CF |
| `52A8C38890360D0B32993A44C9E94E660F3FA8F4` | Win64/Winnti.BU |

### AceHash (Credential Dumper)
| SHA-1 | Detection Name |
|---|---|
| `47A262BAE22BB77850A1E3E38F8E529189D291F6` | Win64/Winnti.BY |
| `35C026F8C35BFCEECD23EACE19F09D3DF2FD72DA` | Win32/Spy.Agent.ORQ |
| `43FF18CEB3814F1DAE940AD977C59A96BB016E76` | Win32/Spy.Agent.ORQ |

### Winnti Droppers
| SHA-1 | Detection Name |
|---|---|
| `95A41FDDDC8CAF097902B484F8440BDDAD0C5B32` | Win32/Winnti.AH |
| `D9A54F79CA15C7E363DBE62B4D1C5C8D103103A2` | Win32/Winnti.AH |
| `3DF753F56BB53F72D3DF735A898D7221C3B5272E` | Win64/TrojanDropper.Agent.CJ |
| `1EC1B5A902869ED5D51012826A34FFA9225853CB` | Win32/Winnti.AK |

### PipeMon
| Detection Names |
|---|
| Win64/PipeMon.A |
| Win64/PipeMon.B |
| Win64/PipeMon.C |
| Win64/PipeMon.D |
| Win64/PipeMon.E |

### ShadowPad
| Detection Names |
|---|
| Win32/Shadowpad.C |

### TOUGHPROGRESS (2024-2025 Campaign)
- Multi-stage chain: **PLUSDROP** → **PLUSINJECT** → **TOUGHPROGRESS**
- Delivered via spear-phishing → ZIP on compromised gov website → LNK masquerading as PDF
- C2 via Google Calendar API event read/write

---

## 🌐 Network Indicators

### Known C2 IP Addresses (Winnti/PortReuse campaigns)
| IP Address | Notes |
|---|---|
| `154.223.131.237` | Historical C2 |
| `117.16.142.9` | Historical C2 |
| `103.19.3.109` | Historical C2 |
| `110.45.146.253` | Historical C2 |
| `117.16.142.69` | Historical C2 |
| `122.10.117.206` | Historical C2 |
| `207.148.125.56` | Historical C2 |
| `118.193.236.206` | Historical C2 |
| `167.88.176.205` | Historical C2 |
| `103.224.83.95` | Historical C2 |
| `103.19.3.21` | Historical C2 |
| `103.224.80.44` | DUST campaign (C0017) payload server |

### Known C2 Domains
| Domain | Notes |
|---|---|
| `xp101.dyn-dns.com` | Winnti C2 |
| `svn-dns.ahnlabinc.com` | Winnti C2 (masquerades as AhnLab) |
| `dns1-1.7release.com` | Winnti C2 |
| `ssl.dyn-dns.com` | Winnti C2 |
| `checkin.travelsanignacio.com` | 2018 Winnti C2 |

### Infrastructure Patterns
- 106+ unique Cobalt Strike servers attributed to APT41 (2020–2021)
- Frequent abuse of free web hosting services for malware staging
- URL shorteners in phishing redirect chains
- Compromised university and government websites for payload hosting
- Cloudflare CDN used to proxy C2 traffic (C0017 campaign)
- **Google AMP Cache URLs** used in phishing redirects (2024 aerospace campaign)

---

## 🪟 Windows Artifacts

### Malicious Service Names / Display Names
- `clr_optimization_v4.0.30229_32` — Winnti Hong Kong university campaign
- `Windows Defend` — DUSTPAN masquerading as Windows Defender (DUST campaign)

### Scheduled Tasks (DEADEYE persistence, C0017)
- `\Microsoft\Windows\PLA\Server Manager Performance Monitor`
- `\Microsoft\Windows\Ras\ManagerMobility`
- `\Microsoft\Windows\WDI\SrvSetupResults`
- `\Microsoft\Windows\WDI\USOShared`

### Web Shells
- **ANTSWORD** — Apache Tomcat targeting
- **BLUEBEAM** — Apache Tomcat targeting
- **China Chopper** — Microsoft Exchange targeting

### Named Pipes
- Various PipeMon-associated named pipes (see ESET Winnti IOC repo for full list)

### Registry Keys
- GOODLUCK malware modifies registry for credential theft (specific keys vary by target)

---

## 🔑 Code-Signing Certificates (Abused)

APT41 maintains a **library of stolen and purchased code-signing certificates**. Notable certificate SHA-1 thumbprints linked to Winnti campaigns are documented in the [ESET Winnti IOC repository](https://github.com/eset/malware-ioc/blob/master/winnti_group/README.adoc).

Key behavioral indicator: **valid-looking code signatures on unusual binaries** — especially DLLs loading via search-order hijacking or side-loading.

---

## 🧭 MITRE ATT&CK Techniques

| Tactic | ID | Technique |
|---|---|---|
| Initial Access | T1566.001 | Phishing: Spearphishing Attachment |
| Initial Access | T1566.002 | Phishing: Spearphishing Link |
| Initial Access | T1190 | Exploit Public-Facing Application |
| Initial Access | T1195.002 | Supply Chain Compromise: Software Supply Chain |
| Execution | T1204.002 | User Execution: Malicious File |
| Execution | T1059.003 | Command and Scripting: Windows Command Shell |
| Persistence | T1505.003 | Server Software Component: Web Shell |
| Persistence | T1053.005 | Scheduled Task/Job |
| Persistence | T1543.003 | Create/Modify System Process: Windows Service |
| Persistence | T1542.003 | Pre-OS Boot: Bootkit |
| Defense Evasion | T1553.002 | Subvert Trust Controls: Code Signing |
| Defense Evasion | T1574.001 | Hijack Execution Flow: DLL |
| Defense Evasion | T1036.005 | Masquerading: Match Legitimate Name/Location |
| Defense Evasion | T1027 | Obfuscated Files or Information |
| Defense Evasion | T1027.002 | Software Packing (VMProtect, Themida) |
| Defense Evasion | T1055 | Process Injection |
| Defense Evasion | T1480.001 | Execution Guardrails: Environmental Keying |
| Defense Evasion | T1070.004 | Indicator Removal: File Deletion |
| Credential Access | T1003.001 | OS Credential Dumping: SAM |
| Credential Access | T1003.003 | OS Credential Dumping: NTDS |
| Discovery | T1082 | System Information Discovery |
| Discovery | T1046 | Network Service Discovery |
| Discovery | T1135 | Network Share Discovery |
| Discovery | T1087.001 | Account Discovery: Local |
| Discovery | T1087.002 | Account Discovery: Domain |
| Lateral Movement | T1021.002 | Remote Services: SMB/Admin Shares |
| Lateral Movement | T1078 | Valid Accounts |
| Collection | T1560.001 | Archive Collected Data: Archive via Utility |
| Collection | T1213.006 | Data from Information Repositories: Databases |
| C2 | T1102.001 | Web Service: Dead Drop Resolver |
| C2 | T1071.001 | Application Layer Protocol: Web Protocols |
| C2 | T1071.004 | Application Layer Protocol: DNS |
| C2 | T1090 | Proxy |
| Exfiltration | T1567.002 | Exfiltration to Cloud Storage (OneDrive) |
| Exfiltration | T1048.003 | Exfiltration Over DNS |
| Exfiltration | T1041 | Exfiltration Over C2 Channel |
| Impact | T1486 | Data Encrypted for Impact (ransomware) |
| Impact | T1496 | Resource Hijacking (cryptomining) |

---

## 🔍 Detection Opportunities

- **Anomalous Google Calendar / Google Sheets API calls** from endpoints (TOUGHPROGRESS, VOLDEMORT)
- **OneDrive traffic** from servers or non-standard processes
- **CHM, LNK files** arriving via email or web download
- **DLL side-loading** from legitimate but unusual parent executables (AhnLab uninstaller, svchost variants)
- **Certutil.exe** used to download payloads
- **SAM/NTDS registry hive access** via `reg save`
- **Ntdsutil execution** on domain controllers
- **makecab.exe** used for staging archives
- **MiPing / WIDETONE** port scan artifacts
- Unusual **named pipes** or **service names** matching Winnti patterns
- Processes executing from `%TEMP%` or `%APPDATA%` with valid code signatures

---

## 📋 Threat Hunting Queries (Splunk SPL Concepts)

```spl
| search process_name="certutil.exe" AND (cmdline="*download*" OR cmdline="*urlcache*")
| search process_name="makecab.exe" AND parent_process!="msiexec.exe"
| search registry_path="*SAM*" AND process_name="reg.exe" AND cmdline="*save*"
| search process_name="ntdsutil.exe"
| search network_destination_domain IN ("*.googleapis.com", "*.onedrive.com") AND process_name NOT IN ("chrome.exe","msedge.exe","firefox.exe","outlook.exe")
| search file_extension IN (".chm", ".lnk") AND file_path="*Temp*"
```

---

## 📚 IOC Sources

- [ESET Malware IOC: Winnti Group](https://github.com/eset/malware-ioc/blob/master/winnti_group/README.adoc)
- [MITRE ATT&CK G0096](https://attack.mitre.org/groups/G0096/)
- [Mandiant APT41 DUST Campaign](https://cloud.google.com/blog/topics/threat-intelligence/apt41-us-state-governments)
- [Google GTIG: TOUGHPROGRESS](https://cloud.google.com/blog/topics/threat-intelligence)
- [Zscaler: DodgeBox/MoonWalk](https://www.zscaler.com/blogs/security-research/dodgebox-deep-dive-updated-arsenal-apt41-part-1)
