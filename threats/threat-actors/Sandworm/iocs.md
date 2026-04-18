# Sandworm (APT44) — Indicators of Compromise

> **Admiralty Grade: A1 | TLP:WHITE**
> Note: Sandworm operates with high OPSEC and rotates infrastructure frequently. IOCs have short shelf life. Prioritize behavioral/TTP-based detection over static indicators.

---

## Malware Hashes (Selected)

### NotPetya / ETERNALPETYA (2017)
| Hash | Type | File |
|---|---|---|
| `027cc450ef5f8c5f653329641ec1fed91f694e0d229928963b30f6b0d7d3a745` | SHA-256 | NotPetya main binary |
| `64b0b58a2c030c77fdb2b537b2fcc4af432bc55ffb36599a31d418c7c69e94b1` | SHA-256 | NotPetya variant |

### Industroyer2 (April 2022)
| Hash | Type | File |
|---|---|---|
| `d69665f56ddef7ad4e71971f06432e59f1510a7194386e5f0e8926aea7b88e00` | SHA-256 | industroyer2.exe |
| `7b678d0a0f7a74acd1e15c30b6bbd6ee67f4879c6e24d16a7f53e9f5f39da39f` | SHA-256 | CaddyWiper companion |

### CaddyWiper (multiple variants)
| Hash | Type | Notes |
|---|---|---|
| `a294620543334a721a2ae8eaaf9680a0786f4b9a216d75b55cfd28f39e9430ea` | SHA-256 | March 2022 variant |
| `b01e0c6ac0b8bcde145ab7b68cf246deea9402fa7ea3aede7105f7051fe240c1` | SHA-256 | October 2022 variant |

### ZEROLOT (2025)
| Hash | Type | Notes |
|---|---|---|
| IOCs under ESET embargo — monitor ESET threat feeds | — | Distributed via Active Directory GPO |

### Kapeka Backdoor (2024)
| Hash | Type | Notes |
|---|---|---|
| `3b2e708eaa4744c76a633391cf2c7a8186166323d6e81c6d0f36ed99e4e48eba` | SHA-256 | Kapeka dropper (WithSecure) |

---

## Network Indicators

### C2 / Infrastructure Patterns
Sandworm rotates infrastructure aggressively. Patterns are more actionable than static IPs:

- **Cloud-hosted C2** — Azure, AWS, legitimate cloud providers used to blend traffic
- **GOGETTER tunneler** — Yamux multiplexer over TLS; watch for unusual TLS connections on non-standard ports
- **MicroSCADA exploitation** — ISO image mounted as virtual CD-ROM on SCADA hypervisors; watch for autorun events
- **Tor routing** — used for initial access/credential theft in some campaigns

### Historic Sandworm C2 (Public Record)
| Indicator | Type | Campaign |
|---|---|---|
| `188.40.134[.]9` | IP | BlackEnergy C2 (2015) |
| `5.149.254[.]114` | IP | BlackEnergy C2 (2015) |
| `toknowall[.]com` | Domain | VPNFilter campaign (linked cluster) |

---

## File System Indicators

### Industroyer2 / CaddyWiper Attack Chain
| Indicator | Type | Notes |
|---|---|---|
| `a.iso` | Filename | MicroSCADA OT attack ISO image |
| `lun.vbs` | Filename | VBS script inside OT attack ISO |
| `n.bat` | Filename | Batch file, runs native scilc.exe MicroSCADA binary |
| `s1.txt` | Filename | Contains unauthorized MicroSCADA control commands |
| `TANKTRAP` PowerShell scripts | Technique | Used to spread wipers via GPO |

### GOGETTER Persistence
| Indicator | Type | Notes |
|---|---|---|
| Systemd service with `WantedBy=multi-user.target` pointing to tunneler binary | Config | Linux persistence mechanism |
| Yamux multiplexer library in Golang binary | Binary indicator | GOGETTER network signature |

---

## Behavioral Indicators (Prioritize These)

1. **GPO mass wiper deployment** — group policy objects creating scheduled tasks to copy + execute wiper binaries across domain
2. **ISO image mounted on SCADA/OT hypervisor** — especially with autorun enabled; `a.iso` or similarly generic names
3. **Native SCADA binary (scilc.exe for MicroSCADA)** executing from unusual paths or with unusual arguments
4. **KillDisk / wiper execution immediately after ICS protocol tool execution** — coordinated IT/OT dual-layer attack
5. **REGEORG.NEO / Neo-REGEORG webshells** on internet-facing servers as initial persistence
6. **Trojanized software installers from torrent sources** (Ukrainian/Russian forums) deploying DCRAT
7. **CyberArmyofRussia_Reborn Telegram posts** coinciding with network anomalies in your environment (info op coordination)

---

## MITRE ATT&CK Mapping

| Technique | ID | Notes |
|---|---|---|
| Spearphishing Attachment | T1566.001 | Ukraine defense orgs, journalists |
| Exploit Public-Facing Application | T1190 | Zyxel, Exim, VPNs |
| External Remote Services | T1133 | VPN/router footholds |
| Supply Chain Compromise | T1195 | M.E.Doc (NotPetya), software developer pivots |
| Living Off the Land Binaries | T1218 | MicroSCADA scilc.exe, SDelete, WinRAR |
| Group Policy Modification | T1484.001 | TANKTRAP / ZEROLOT GPO deployment |
| Disk Wipe | T1561 | CaddyWiper, NotPetya, ZEROLOT, STING |
| Service Stop | T1489 | Disabling energy sector ICS services |
| Inhibit System Recovery | T1490 | BCDEdit modification, shadow copy deletion |
| Modify ICS Configuration | T0821 | Industroyer, Industroyer2 — circuit breaker commands |
| Data Destruction | T1485 | Wipers across IT and OT |
| Indicator Removal | T1070 | CaddyWiper post-ICS-attack cleanup |

---

## Detection Notes

- Sandworm is **highly OPSEC-aware** and aware of incident responders. Aggressive remediation can cause dormant implants to activate (documented behavior).
- Static IOCs (IPs, hashes) should be treated as **ephemeral** — this actor rebuilds infrastructure frequently.
- **Behavioral detections** (GPO abuse, LotL in OT, Yamux signatures, ISO autorun on hypervisors) have longer operational shelf life.
- Monitor **CyberArmyofRussia_Reborn** and **Solntsepek** Telegram channels — their posts have historically preceded or coincided with confirmed APT44 attacks.

---

## Sources

- Mandiant APT44 Technical Annex (April 2024): https://cloud.google.com/blog/topics/threat-intelligence/apt44-unearthing-sandworm
- ESET Industroyer2 Analysis: https://www.welivesecurity.com/2022/04/12/industroyer2-industroyer-reloaded/
- WithSecure Kapeka Report: https://labs.withsecure.com/publications/kapeka
- CISA Seashell Blizzard Advisory: https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-249a
- MITRE ATT&CK G0034: https://attack.mitre.org/groups/G0034/
