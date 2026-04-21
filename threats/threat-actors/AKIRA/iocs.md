# Indicators of Compromise: Akira Ransomware

> **TLP: WHITE** | Source: CISA AA24-109A (Nov 2025), Qualys, Arctic Wolf, Blackpoint Cyber, HHS HC3
> Last Updated: 2026-04-21

---

## File Hashes (MD5 — Akira Encryptor Samples)

| Hash | Type |
|---|---|
| `e57340a208ac9d95a1f015a5d6d98b94` | Akira ransomware |
| `e8139b0bc60a930586cf3af6fa5ea573` | Akira ransomware |
| `a1f4931992bf05e9bff4b173c15cab15` | Akira ransomware |
| `08bd63480cd313d2e219448ac28f72cd` | Akira ransomware |
| `4aecef9ddc8d07b82a6902b27f051f34` | Akira ransomware |

## File Hashes (SHA256)

| Hash | Type |
|---|---|
| `2727c73f3069457e9ad2197b3cda25aec864a2ab8da3c2790264d06e13d45c3d` | Akira (2024-02 sample) |
| `5e1e3bf6999126ae4aa52146280fdb913912632e8bac4f54e98c58821a307d32` | Kerberos Ticket Dumper used by Akira |

---

## File Artifacts

| Indicator | Description |
|---|---|
| `.akira` extension | Files encrypted by Windows/Linux Akira variant |
| `.powerranges` extension | Files encrypted by Megazord (Rust-based) variant |
| `akiranote.txt` | Ransom note filename (typical) |
| `Log-DD-MM-YYYY-HH-MM-SS.txt` | Ransomware execution log format |
| `itadm` | Newly created domain admin account (persistence) |
| `encryptor.exe` | Akira encryptor executable |

---

## Exploited CVEs (Actively Weaponized)

| CVE | Product | Impact |
|---|---|---|
| CVE-2020-3259 | Cisco ASA/FTD | Sensitive info disclosure |
| CVE-2023-20269 | Cisco ASA — SSL VPN | Auth bypass (brute force) |
| CVE-2024-40766 | SonicWall SSLVPN | Improper access control |
| CVE-2024-37085 | VMware ESXi | Auth bypass |
| CVE-2023-27532 | Veeam Backup & Replication | Missing auth for critical function |
| CVE-2024-40711 | Veeam Backup & Replication | Deserialization RCE |
| CVE-2021-21972 | VMware vCenter | RCE |
| CVE-2022-40684 | Fortinet | Auth bypass |

---

## Tools / Utilities Observed In-Use

| Tool | Purpose | MITRE |
|---|---|---|
| SharpDomainSpray | Password spraying | T1110.003 |
| AdFind | AD enumeration | T1087.002 |
| BloodHound / SharpHound | AD attack path mapping | T1087.002 |
| SoftPerfect Network Scanner | Network recon | T1016 |
| Advanced IP Scanner | Network recon | T1016 |
| Mimikatz | Credential dumping | T1003.001 |
| LaZagne | Credential dumping | T1003 |
| Impacket (wmiexec.py) | Remote execution | T1047 |
| PowerTool | BYOVD — kill AV (Zemana driver) | T1562.001 |
| POORTRY | Signed vulnerable driver (BYOVD) | T1068 |
| STONETOP | POORTRY loader | T1105 |
| Cobalt Strike | C2 / lateral movement | T1219 |
| Ngrok | Encrypted tunnel for C2/exfil | T1572 |
| Cloudflare Tunnel | Covert C2 | T1572 |
| AnyDesk | Remote access / persistence | T1219 |
| LogMeIn | Remote access | T1219 |
| RustDesk | Remote access | T1219 |
| MobaXterm | Remote access | T1219 |
| SystemBC (Coroxy) | SOCKS5 proxy C2 | T1090 |
| RClone | Data exfil to cloud (Mega) | T1567.002 |
| WinSCP | Data exfil (SFTP) | T1048.002 |
| FileZilla | Data exfil (FTP) | T1048 |
| WinRAR / 7-zip | Data archival | T1560.001 |
| esentutl.exe | File collection via DB extraction | T1005 |
| Terminator | BYOVD — disable security tools | T1562.001 |

---

## PowerShell / Command Indicators

| Command / Pattern | Description |
|---|---|
| `powershell.exe -Command "Get-WmiObject Win32_Shadowcopy \| Remove-WmiObject"` | VSS deletion |
| `bcdedit /set safeboot` | Force safe-mode boot (ransomware pre-staging) |
| `net user itadm * /add` | Create persistence account |
| `net localgroup administrators itadm /add` | Escalate itadm to admin |
| `fsutil fsinfo drives` | Drive enumeration |
| `nltest /domain_trusts` | Domain trust discovery |
| `Get-ADComputer -Filter * -Properties *` | AD computer enumeration |
| `icacls.exe [target] /grant Everyone:F` | Permission modification pre-encryption |
| `Invoke-BloodHound` | BloodHound AD ingestor |
| `Set-ExecutionPolicy Unrestricted` | Execution policy bypass |

---

## Encryption Indicators

| Indicator | Detail |
|---|---|
| Algorithm (Windows) | ChaCha20 (file) + RSA-4096 (key) |
| Algorithm (Linux/ESXi) | AES, CAMELLIA, DES, IDEA; time-seed key gen |
| Key storage | RSA-4096 encrypted key appended to end of each encrypted file |
| ESXi encryptor args | `--encryption_path`, `--share_file`, `--encryption_percent`, `--fork` |
| Log file pattern | `Log-DD-MM-YYYY-HH-MM-SS.txt` |
| Unique victim code | Left in ransom note; used to authenticate on Tor chat |

---

## Network Indicators

| Indicator | Type | Notes |
|---|---|---|
| `.onion` Tor DLS | URL | Akira Dedicated Leak Site (changes over time) |
| Ngrok relay connections | Network | Encrypted tunnel; unusual for enterprise |
| api.telegram.org outbound | Network | Some variants route C2 through Telegram |
| Mega.nz outbound | Network | Primary cloud exfil destination (Rclone) |
| Cloudflare Tunnel services | Network | Legitimate-looking C2 |

---

## AV / EDR Signatures

| Product | Signature |
|---|---|
| Check Point NGFW | `Ransomware.Win32.Akira.TC.*` |
| Palo Alto | `Ransom/Win32.akira.b`, `ransomware/Linux.akira.d` |
| Fortigate AV | `W64/Generik.NFLQ!tr.ransom` |
| Snort | `1.63977.1 — MALWARE-OTHER MultiOS.Ransomware.Megazord download attempt` |

---

## STIX / Structured IOCs

- CISA AA24-109A STIX XML (Nov 2025): https://www.cisa.gov/sites/default/files/2025-11/AA24-109A.stix.xml
- CISA AA24-109A STIX JSON (Nov 2025): https://www.cisa.gov/sites/default/files/2025-11/AA24-109A.stix.json

---

*Intel Grade: A1 | TLP: WHITE | Sources: CISA AA24-109A, Qualys, Arctic Wolf, HHS HC3, Blackpoint Cyber*
