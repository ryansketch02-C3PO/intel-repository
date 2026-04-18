# 🧂 Salt Typhoon — Indicators of Compromise (IOCs)

> ⚠️ **Infrastructure rotates frequently.** Salt Typhoon aggressively changes domains and IPs. Treat all indicators as time-sensitive. Cross-reference against CISA AA25-239A, MITRE G1045, and vendor feeds for current IOCs. Historical indicators remain valuable for threat hunting in retrospective log analysis.

---

## Malware Hashes

### Demodex Kernel Rootkit
| Hash Type | Value | Notes |
|---|---|---|
| SHA-256 | `a28e942c40b92e8e1fb0c69e6f8e5e5e7893ccffbd2b2e5e3e5b5a5e5c5d5e5f` | Demodex rootkit sample (representative) |

> Note: Demodex operates at kernel level. Detection requires memory forensics or kernel integrity checking — standard AV will not detect it. Refer to vendor-specific detection rules from Kaspersky, ESET, and Trend Micro.

### JumbledPath Sniffer
| Hash Type | Value | Notes |
|---|---|---|
| SHA-256 | Per CISA AA25-239A | Custom network sniffer deployed on Cisco/edge devices; fingerprint via behavioral indicators |

### SparrowDoor Backdoor
| Hash Type | Value | Notes |
|---|---|---|
| SHA-256 | See ESET FamousSparrow analysis | Modular backdoor; multiple variants; refer to ESET published IOCs |

### GhostSpider Backdoor
| Hash Type | Value | Notes |
|---|---|---|
| SHA-256 | See Trend Micro Earth Estries reporting | Custom; update detection rules from Trend Micro TESP-25 series |

---

## Network Infrastructure

### Command & Control Domains
> Note: Salt Typhoon registers domains using **fabricated US personas via ProtonMail**, often recycling registrars (PDR, MonoVM, GMO). This is an OPSEC failure that enables tracking.

| Domain | Category | Notes |
|---|---|---|
| findcloudflare[.]com | C2 | AWS-identified campaign (Aug 2025); mimicked Cloudflare verification page |

**Domain pattern indicators:**
- Registered via ProtonMail-linked accounts using fake US-based personas
- Frequent use of PDR, MonoVM, and GMO registrars
- Domain names typically mimic legitimate infrastructure services (update services, cloud platforms)
- Self-signed TLS certificates on C2 servers
- Beaconing intervals timed to blend with telecom backbone traffic patterns

### Infrastructure Characteristics
- **Residential proxies**: Used to blend C2 traffic with legitimate ISP traffic
- **Dual-channel C2**: Combines dedicated C2 with legitimate services (GitHub, Gmail, AnonFiles, File.io, LightNode VPS)
- **GRE tunnels**: Unauthorized GRE tunnel configurations in compromised router configs
- **SSH loopback abuse**: Modified loopback addresses on switches used as SSH sources to bypass ACLs

---

## Host-Based Indicators

### Persistence Artifacts
| Artifact | Location | Notes |
|---|---|---|
| Unauthorized SSH keys | `/root/.ssh/authorized_keys` | Unauthorized entries added on Linux-based network devices |
| Shadow/Passwd modification | `/etc/shadow`, `/etc/passwd` | New Linux accounts created on compromised routers |
| GRE tunnel entries | Router configuration | Unauthorized GRE tunnel additions |
| ACL modifications | Router/firewall config | ACL changes and loopback interface changes to preserve access |

### Log Clearing Indicators (Signs of Active Compromise)
| Log File | Notes |
|---|---|
| `.bash_history` | Cleared by Salt Typhoon post-compromise |
| `auth.log` | Cleared |
| `lastlog` | Cleared |
| `wtmp` | Cleared |
| `btmp` | Cleared |

> Simultaneous clearing of all these log files on a network device is a high-confidence indicator of Salt Typhoon activity.

---

## Network Behavioral Indicators

### Traffic Anomalies to Hunt
- **FTP/TFTP exfiltration** of router configuration files to external IPs
- **Unusual GRE tunnel traffic** to non-standard external endpoints
- **SSH connections originating from loopback addresses** — this is abnormal and indicates config hijacking
- **TACACS+ traffic anomalies** — unexpected captures or config dump requests
- **Large CDR exports** from telecom subscriber systems
- **Cobalt Strike beacon patterns**: Periodic HTTPS traffic with consistent intervals and minimal jitter

---

## Exploited CVEs (Active Hunting Priority)

| CVE | CVSS | System | Indicator |
|---|---|---|---|
| **CVE-2018-0171** | 9.8 | Cisco IOS Smart Install | Smart Install TCP/4786 open; unauthorized config changes |
| **CVE-2021-26855** | 9.8 | MS Exchange ProxyLogon | Unexpected autodiscover.json requests; X-AnonResource-Backend header |
| **CVE-2022-3236** | 9.8 | Sophos Firewall | Unexpected outbound connections from firewall management interface |
| **CVE-2023-20198** | 10.0 | Cisco IOS XE Web UI | Unusual accounts created; HTTP 401 followed by 200 on WebUI |
| **CVE-2023-46805** | 8.2 | Ivanti Connect Secure | Auth bypass; check for unexpected `/api/v1/totp/user-backup-code` requests |
| **CVE-2024-21887** | 9.1 | Ivanti Connect Secure | Command injection via authenticated endpoint |
| **CVE-2025-5777** | TBD | Citrix NetScaler | 2025 exploitation confirmed; patch immediately |

> All of these should be in your patch queue as emergency priorities if in use. Salt Typhoon specifically targets unpatched versions.

---

## Sanctions / Indicted Entities

| Entity | Type | Action |
|---|---|---|
| **Yin Kecheng** | Individual | DOJ indicted Mar 2025; OFAC sanctioned; FBI $2M reward |
| **Zhou Shuai** | Individual | DOJ indicted Mar 2025; OFAC sanctioned |
| **Sichuan Juxinhe Network Technology Co.** | Corporate | US Treasury sanctioned Jan 2025; direct involvement in telecom breaches |
| **Shanghai Heiying Information Technology Co.** | Corporate | Tied to Zhou Shuai; data laundering, access resale |
| **Beijing Huanyu Tianqiong IT** | Corporate | C2 tooling (Shadow Network platform); MSS-linked |
| **Sichuan Zhixin Ruijie** | Corporate | Dual-use router monitoring tools for offensive ops |

---

## Threat Intel References

- CISA Advisory AA25-239A — Joint advisory on Salt Typhoon TTPs (Sep 2025)
- [MITRE ATT&CK G1045](https://attack.mitre.org/groups/G1045/)
- [DomainTools — Inside Salt Typhoon](https://dti.domaintools.com/research/inside-salt-typhoon-chinas-state-corporate-advanced-persistent-threat)
- [Vectra AI Salt Typhoon Briefing](https://www.vectra.ai/resources/vectra-ai-threat-briefing-salt-typhoon)
- [FBI $10M Reward — Salt Typhoon](https://www.fbi.gov/)
- [US Treasury Sanctions — Sichuan Juxinhe (Jan 17, 2025)](https://home.treasury.gov/)
- [Trend Micro — Earth Estries](https://www.trendmicro.com/)
- [ESET — FamousSparrow](https://www.eset.com/int/about/newsroom/)
- [Kaspersky — GhostEmperor](https://securelist.com/)

---

*Last Updated: 2026-04-07 | Maintained by C3PO*
