# REvil (Sodinokibi) — Indicators of Compromise

> **Status:** Disrupted — IOCs primarily historical (2019–2022 peak activity)
> **Grade:** A2 | **TLP:** WHITE
> **Note:** REvil core infrastructure is offline. These IOCs are for threat hunting historical infections, incident response, and detecting any resurgence.

---

## File Hashes (SHA256)

### REvil Core Samples
| Hash | Type | Context |
|---|---|---|
| `25ac4873ae4f955032f8f0e8ed4ec78df2e2ce814454b7b5abd9489feb4e30c3` | REvil executable | Secureworks-analyzed sample (2019) |
| `f450ef75377d132cd469ad569e97ae64dc0abc225a3755da32495c625141f3ab` | Sodinokibi | VirusTotal reference sample |

### Kaseya VSA Attack (Jul 2021) — CVE-2021-30116
| Hash | File | Context |
|---|---|---|
| `d55f983c994caa160ec63a59f6b4250fe67fb3e8c43a388aec60a4a6978e9f1e` | agent.exe | Kaseya VSA malicious update dropper |
| `8dd620d9aeb35960bb766458c8890ede987c33d239cf730f93fe49d90ae759dd` | REvil executable | Kaseya-connected REvil payload |
| `e2a24ab94f865caeacdf2c3ad015f31f23008ac6db8312c2cbfb32e4a5466ea2` | REvil executable | Kaseya-connected REvil payload |

### REvil Executable (Original)
| Hash | Type |
|---|---|
| `512b538ce2c40112009383ae70331dcf` | MD5 — REvil executable |
| `d3a0c325121ab4775ab48bbb7b2ef21c0f123109` | SHA1 — REvil executable |

---

## Network Indicators

### Kaseya Attack Infrastructure (2021)
| IP / Domain | Type |
|---|---|
| `35.226.94[.]113` | Kaseya attack C2 (Jul 2021) |
| `161.35.239[.]148` | Kaseya attack C2 (Jul 2021) |
| `162.253.124[.]162` | Kaseya attack C2 (Jul 2021) |

### General REvil C2 Pattern
- **Protocol:** HTTPS over Tor (onion domains)
- **C2 domains** stored in ransomware `dmn` configuration key (encrypted; varies per campaign)
- **Leak site:** "Happy Blog" — historical dark web leak/extortion site (offline)
- **Victim portal:** Tor-hosted; victims receive unique URL from ransom note

---

## Registry Artifacts

| Key | Value | Description |
|---|---|---|
| `HKCU\Software\recfg` | `pk_key` | Session public key |
| `HKCU\Software\recfg` | `sk_key` | Session private key (encrypted with attacker public key) |
| `HKCU\Software\recfg` | `0_key` | Session private key (encrypted with binary-embedded key) |
| `HKCU\Software\recfg` | `rnd_ext` | Random extension appended to encrypted files |

> ⚠️ **High-confidence IOC:** Presence of `HKCU\Software\recfg` registry key is a strong indicator of active or completed REvil infection.

---

## File System Artifacts

| Artifact | Description |
|---|---|
| `[random 5-10 char extension]` | Appended to all encrypted files (e.g., `.9781xsd4`) |
| `[ext]-readme.txt` | Ransom note dropped in every encrypted directory |
| `C:\kworking\agent.exe` | Kaseya attack dropper location |
| `C:\kworking\agent.crt` | Base64-encoded payload pre-decode |
| `mpsvc.dll` | Signed malicious DLL used in Kaseya attack (DLL sideloading) |
| `msmpeng.exe` (masquerading) | REvil process masquerades as Windows Defender |

---

## Behavioral Indicators (EDR / SIEM)

| Indicator | MITRE ID | Detection Guidance |
|---|---|---|
| Deletion of Volume Shadow Copies via WMIC | T1490 | Alert: `wmic shadowcopy delete` or `vssadmin delete shadows` |
| Mass file rename in short duration | T1486 | >100 file extensions changed within 60 seconds |
| PowerShell decoding and executing Base64 payload | T1059.001 | Monitor: encoded commands spawned from scheduled tasks |
| PuTTY Link (Plink) used to tunnel RDP | T1021.001 | Alert on plink.exe network connections |
| Cobalt Strike beacon activity | S0154 | Memory-resident; detect via process hollowing / named pipes |
| Disabling Windows Defender via PowerShell | T1562.001 | Alert: Set-MpPreference -DisableRealtimeMonitoring $true |
| MySQL process termination before encryption | T1489 | Alert: mysql.exe terminated by non-admin process |
| Russian keyboard layout check in process memory | T1497 | Behavioral sandbox flag |
| Writes to `HKCU\Software\recfg` | T1112 | High-confidence IOC — alert immediately |

---

## CVEs Exploited by REvil

| CVE | Component | Notes |
|---|---|---|
| **CVE-2021-30116** | Kaseya VSA | Zero-day authentication bypass; Kaseya supply chain attack (Jul 2021) |
| CVE-2019-2725 | Oracle WebLogic | Initial REvil deployment vector (Apr 2019) |
| CVE-2018-8453 | Windows win32k.sys | Local privilege escalation exploit |
| CVE-2020-1472 | Windows Netlogon | ZeroLogon — post-initial-access privilege escalation |
| CVE-2019-19781 | Citrix ADC/Gateway | Initial access |
| CVE-2020-0609 / 0610 | Windows RDP | BlueGate RDP vulnerability |
| CVE-2020-5902 | F5 BIG-IP | Remote code execution for initial access |
| CVE-2020-2021 | Palo Alto Firewall | Authentication bypass |

---

## Key Persons / Attribution

| Name | Alias | Role | Status |
|---|---|---|---|
| Yaroslav Vasinskyi | Rabotnik | Affiliate; Kaseya attacker | Sentenced 13y7m (May 2024); $16M restitution |
| Yevgeniy Polyanin | — | Affiliate; Texas attacks | Indicted; $6.1M seized; at large |
| Ivan Kondratyev | Bassterlord | Affiliate (also LockBit) | Indicted 2022 |
| "Unknown" / UNKN | — | Core developer/administrator | Identity unconfirmed; still at large |

---

## Cryptocurrency / Financial

- Shifted from **Bitcoin** to **Monero** in early 2020 for transaction privacy
- Used cryptocurrency mixing services to launder proceeds
- $6.1M seized from Polyanin (2021)
- $200M+ confirmed ransom payments at time of disruption
- State Dept offered $10M for information on leadership; $5M for arrest leads

---

## Threat Feeds

- [ThreatFox: Sodinokibi/REvil](https://threatfox.abuse.ch/browse/malware/win.revil/)
- [CISA Advisory AA21-131A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-131a)
- [Secureworks REvil/Sodinokibi Research](https://www.secureworks.com/research/revil-sodinokibi-ransomware)
- [DOJ Vasinskyi Indictment (Nov 2021)](https://www.justice.gov/archives/opa/pr/ukrainian-arrested-and-charged-ransomware-attack-kaseya)
- [DOJ Vasinskyi Sentencing (May 2024)](https://www.justice.gov/archives/opa/pr/sodinokibirevil-affiliate-sentenced-role-700m-ransomware-scheme)

---

*IOCs compiled by C3PO 🤖 | Grade: A2 | TLP: WHITE | 2026-04-12*
*Note: REvil is currently disrupted. Monitor for rebranded successors — former members are active in LockBit and other RaaS ecosystems.*
