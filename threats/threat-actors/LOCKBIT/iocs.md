# LockBit — Indicators of Compromise (IOCs)

> **TLP: WHITE** | Last updated: 2026-04-11 | Admiralty Grade: A2
> Sources: CISA, H-ISAC, Arete IR, CyberPress, ThreatFox

---

## Network IOCs

### IP Addresses
| IP | Context | Source | Date |
|---|---|---|---|
| `205.185.116.233` | LockBit 5.0 C2 / DLS infrastructure (AS53667 PONYNET/FranTech) | Researcher Rakesh Krishnan | Dec 2025 |

### Domains
| Domain | Context | Source | Date |
|---|---|---|---|
| `karma0.xyz` | LockBit 5.0 data leak site / C2 domain (Cloudflare NS; Namecheap privacy) | Researcher Rakesh Krishnan | Dec 2025 |

### Open Ports (LockBit 5.0 C2 Server)
| Port | Protocol | Service |
|---|---|---|
| 21 | TCP | FTP |
| 80 | TCP | HTTP (Apache 2.4.58 / OpenSSL 3.1.3 / PHP 8.0.30) |
| 3389 | TCP | RDP |
| 5000 | TCP | HTTP |
| 5985 | TCP | WinRM |
| 49666 | TCP | File Server |

---

## File Indicators

### LockBit 5.0 (Windows) — SHA256
| Hash | Detection Name | Source |
|---|---|---|
| `7ea5afbc166c4e23498aa9747be81ceaf8dad90b8daa07a6e4644...` (partial) | `Ransom.Win64.LOCKBIT.YXFIOZ` | H-ISAC / Trend Micro |

> ⚠️ Full hashes not publicly released at time of profile creation. Ingest ThreatFox feed for live IOC data: https://threatfox.abuse.ch/browse/malware/win.lockbit/

### File Extension Patterns
| Pattern | Variant |
|---|---|
| `.[a-f0-9]{16}` (randomized 16-char hex) | LockBit 5.0 |
| `.lockbit` | LockBit 1.0 |
| `.abcd` | Predecessor (ABCD Ransomware) |

---

## Behavioral Indicators / Detection Signatures

### Process / Service Activity
- Sudden termination of 63+ security services (matched via hardcoded hash list) — alert on mass service stop events
- EvtClearLog API calls post-encryption (all Windows event logs wiped)
- ETW patching targeting `EtwEventWrite` API — look for ETW tampering before/during encryption

### Memory Indicators
- DLL reflection loading — PE binary decrypted and loaded in memory without touching disk (LockBit 5.0)
- In-memory loader patterns; avoid disk signatures for LockBit 5.0 specifically

### Command Line Patterns
Presence of these CLI flags in execution logs indicates LockBit 5.0:
```
-d [directory]    # Target directory
-b                # Background mode
-i                # Invisible mode
-m all|local|net  # Encryption scope
-n [note mode]    # Ransom note options
-t [timeout]      # Timeout setting
```

### Network Behavior
- RClone / MegaSync traffic to cloud storage prior to encryption (exfiltration phase)
- Large upload bursts to MEGA.nz or similar file sharing before ransomware detonation
- Tor onion address communication for victim negotiation portal

### Registry / Filesystem
- Shadow copy deletion via `vssadmin delete shadows /all`
- `wbadmin delete catalog` to destroy backup catalogs
- Random 16-character hexadecimal extension appended to all encrypted files

---

## Tools Used by LockBit Affiliates

| Tool | Purpose | ATT&CK |
|---|---|---|
| **Cobalt Strike** | C2, lateral movement | T1587.001 |
| **Metasploit** | Exploitation framework | T1587.001 |
| **Mimikatz** | Credential dumping | T1003 |
| **ProcDump** | LSASS credential dump | T1003 |
| **LostMyPassword** | Windows credential recovery | T1003 |
| **SoftPerfect Network Scanner** | Network discovery | T1046 |
| **SharpHound** | AD enumeration | T1087 |
| **RClone** | Cloud exfiltration | T1567.002 |
| **MegaSync** | Cloud exfiltration | T1567.002 |
| **StealBit** | LockBit-custom exfil tool | T1567 |
| **AnyDesk** | Persistent remote access | T1219 |
| **ScreenConnect** | Persistent remote access | T1219 |
| **Atera RMM** | Persistent remote access | T1219 |
| **TeamViewer** | Persistent remote access | T1219 |
| **Splashtop** | Persistent remote access (RDP) | T1219 |
| **Ligolo** | SOCKS5/TCP tunnel (reverse) | T1572 |
| **Plink** | SSH tunneling (Windows) | T1572 |
| **ThunderShell** | RAT via HTTP | T1071.001 |

---

## Exploited CVEs (Known)

| CVE | Product | Description |
|---|---|---|
| **CVE-2023-4966** | Citrix NetScaler (Citrix Bleed) | Session token hijacking — heavily exploited by LockBit affiliates |
| **CVE-2023-47246** | SysAid Server | Path traversal leading to RCE |
| **CVE-2021-44228** | Log4Shell (Apache Log4j) | Initial access vector used by affiliates |
| **CVE-2019-0708** | BlueKeep (Windows RDP) | Legacy RDP exploitation |
| **CVE-2020-1472** | Zerologon (Netlogon) | Privilege escalation to Domain Admin |
| **CVE-2021-34527** | PrintNightmare (Windows Print Spooler) | LPE / RCE |

> Note: LockBit affiliates rapidly operationalize newly disclosed CVEs. Assume any high-severity RCE/PE in Citrix, Fortinet, SonicWall, or VPN products will be weaponized quickly.

---

## Ransom Note Characteristics (LockBit 5.0)
- File: `[randomized_name]-README.txt` or similar
- Content explicitly states "LockBit 5.0"
- Contains unique personal identifier for victim's private Tor negotiation portal
- 30-day countdown before public data release
- "Chat with Support" section in Tor portal for negotiation

---

*Profile created: 2026-04-11 | TLP: WHITE | Ingest live IOCs via ThreatFox API*
