# 🐱 Miyako — Indicators of Compromise

> **Admiralty Grade:** C2 | **TLP:** TLP:WHITE (publicly sourced)
> Last updated: 2026-04-08

---

## Forum / Identity Indicators

| Alias | Platform | Notes |
|---|---|---|
| miyako | BreachForums | Primary handle; joined Aug 2024; GOD status; rep score 1,529 |
| mommy | BreachForums | Alternate handle tracked by Intel 471 |
| Miya | BreachForums / Underground | Shortened alias |
| nastya_Miyako | BreachForums | Used for Feb 2025 DoD contractor listing |
| Minari | BreachForums | Earlier alias confirmed by S2W profiling |
| miyak0 | BreachForums | Variant spelling used in Saudi/other listings |
| miyak000 | BreachForums | Used in European travel agency listing |

---

## CVEs in Active Use

| CVE | Product | CVSS | Action |
|---|---|---|---|
| CVE-2024-3400 | Palo Alto Networks PAN-OS GlobalProtect | **10.0** | Patch immediately; check for indicators of exploitation |
| CVE-2024-1709 | ConnectWise ScreenConnect | **10.0** | Authentication bypass; patch and audit |
| CVE-2024-1708 | ConnectWise ScreenConnect | 8.4 | Path traversal; companion to above |
| CVE-2024-3094 | XZ Utils (liblzma) | **10.0** | SSH daemon backdoor; check Linux systems |
| F5 BIG-IP AFM | F5 BIG-IP Advanced Firewall Manager | — | Specific CVE undisclosed; patch all BIG-IP AFM |
| Webmin | Webmin web admin | — | Audit all Webmin-exposed instances |
| OpenBSD | OpenBSD (zero-day) | — | Undisclosed; monitor vendor advisories |

---

## Behavioral / TTPs Indicators

### Network
| Indicator | Notes |
|---|---|
| Unusual outbound DNS volume / long DNS queries | DNS tunneling for C2 |
| Outbound TLS connections to non-standard ports | Sliver C2 framework |
| SSH sessions from unexpected external IPs | Persistent SSH access post-compromise |
| SSH tunneling outbound | Covert C2 channel over SSH |
| PowerShell → curl/wget → execute pattern | Payload download-and-execute chain |

### Host
| Indicator | Notes |
|---|---|
| Cleared Windows Event Logs (Event ID 1102, 104) | Forensic cleanup |
| Cleared bash history / auth.log / wtmp / lastlog | Linux forensic cleanup |
| Sliver C2 artifacts on disk | Open-source C2 framework |
| Unauthorized Cron jobs | Linux persistence |
| Compiled HTML files (.chm) executing scripts | System Binary Proxy Execution |
| NTDS.dit access or VSS shadow copy creation | AD credential dumping |
| Unexpected BloodHound/SharpHound execution | AD reconnaissance |
| New or modified NTFS alternate data streams | File hiding technique |
| Unexpected SSH authorized_keys modifications | Persistent SSH backdoor |

---

## Access Types Sold (Reference for Detection)

| Access Type | Price Range | Detection Focus |
|---|---|---|
| Firewall root access (Linux) | $300–$800 | Unauthorized root sessions, unexpected firewall config changes |
| VPN credentials (OpenVPN/IPSec/WireGuard) | $200–$400 | Impossible travel, off-hours VPN logins, new device enrollment |
| SSH + DNS full access | $400 | Unauthorized SSH keys, DNS zone changes |
| Network admin panel access | $300 | Admin console logins from unknown IPs |

---

## Hunting Queries (Behavioral)

### PowerShell Download-Execute Chain (Intel 471 case study)
Hunt for PowerShell processes spawning curl/wget followed by execution of a scripting interpreter:
- Parent: `powershell.exe` or `pwsh.exe`
- Child: `cmd.exe`, `bash.exe`, `python.exe`, `perl.exe`
- Network connection to external domain in same process tree

### Sliver C2 Detection
- Process names mimicking legitimate binaries
- Outbound TLS to unusual IP ranges on ports 443, 8443, 80, 8080
- Check for Sliver-specific certificate fingerprints

### DNS Tunneling
- Unusually high DNS query frequency from single host
- DNS queries with long, randomized subdomains
- DNS query volume exceeding baseline by >10x

---

## Sources

- Intel 471 mommy Emerging Threat Report (Jun 2025): https://www.intel471.com/blog/mommy-access-broker
- Intel 471 PDF: https://go.intel471.com/hubfs/Emerging%20Threats/2025%20Emerging%20Threats/Emerging%20Threat%20-%20Mommy%20-%2027%20June%202025.pdf
- Intel 471 Threat Hunting Case Study (Dec 2025): https://www.intel471.com/blog/threat-hunting-case-study-detecting-iab-activity
- S2W Dark Web Weekly Feb W2 (Feb 2025): https://s2w.inc/en/resource/detail/755
- AhnLab ASEC February 2026 Report (Mar 2026): https://asec.ahnlab.com/en/92903/

---

*Last updated: 2026-04-08*
