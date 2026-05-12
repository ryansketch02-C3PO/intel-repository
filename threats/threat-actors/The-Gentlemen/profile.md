# The Gentlemen

## Identity

| Field | Value |
|---|---|
| **Scoundrel #** | 041 |
| **Primary Name** | The Gentlemen |
| **AKA** | TheGentlemen, Gentlemen Ransomware Group |
| **Founder** | "Hastalamuerte" — former Qilin affiliate |
| **Type** | Cybercriminal (RaaS — Ransomware-as-a-Service) |
| **Origin** | Unknown (experienced ransomware operators; Qilin lineage) |
| **Sponsor** | None — financially motivated criminal enterprise |
| **Active Since** | August 2025 |
| **Status** | 🔴 ACTIVE — #2 globally by victim count in Q1 2026 |
| **Threat Level** | 🔴 HIGH |
| **MITRE ATT&CK ID** | Not yet assigned |

---

## Mission

The Gentlemen is a **ransomware-as-a-service (RaaS)** operation that emerged in August 2025 and rapidly scaled to become the **second most active ransomware group globally** by Q1 2026. Founded by an experienced Qilin affiliate following a payment dispute, the group hit the ground running with pre-existing tradecraft, tooling, a stockpile of pre-compromised access, and a sophisticated understanding of the ransomware ecosystem — all inherited from their Qilin experience.

Their defining characteristic is **aggressive geographic diversification** — deliberately targeting non-US organizations to exploit the US-centric detection and enforcement focus of most ransomware response efforts. Their data audit service (analyzing stolen datasets >300 GB to identify the most valuable data for extortion leverage) represents genuine innovation in the extortion model.

---

## Origin and Formation

The group was founded by **Hastalamuerte**, an experienced Qilin RaaS affiliate who departed the Qilin program following a dispute over an unpaid commission of approximately **$48,000**. This origin explains both the group's rapid operational capability and its sophistication — they started with:

- Established tradecraft and tooling inherited from Qilin operations
- A stockpile of **approximately 14,700 pre-exploited FortiGate devices** (exploited primarily via CVE-2024-55591, a critical authentication bypass in FortiOS/FortiProxy)
- **969 validated brute-forced FortiGate VPN credentials** ready for attack
- Experienced operators with prior RaaS success

This pre-existing access inventory is what enabled The Gentlemen to scale so rapidly from zero to global top-3 in under 6 months.

---

## Activity Timeline

| Period | Activity | Victims |
|---|---|---|
| Aug 2025 | Emergence — first victims | ~0 public |
| Q4 2025 | Initial scaling | 35–40 victims |
| Q1 2026 | Breakout surge | 166–182 victims (varies by tracker) |
| May 2026 (YTD) | Ongoing — global top-2 position | 320+ cumulative (Check Point estimate) |

**Q1 2026 growth rate:** 315–420% increase over Q4 2025, making them the single fastest-growing ransomware operation in recent history.

---

## Targeting

### Primary Sectors
- Finance and professional services
- Manufacturing
- Real estate and property management
- Healthcare and education (opportunistic)
- Technology and IT services
- **Notably: non-US focus** — deliberately avoiding US-centric targeting model

### Geographic Focus
- Europe (primary)
- Asia-Pacific
- Latin America
- Middle East
- **Deliberately underweights US targets** compared to peer groups — exploits reduced law enforcement pressure outside US jurisdiction

---

## TTPs (MITRE ATT&CK)

### Initial Access
| ID | Technique |
|---|---|
| T1190 | Exploit Public-Facing Application — CVE-2024-55591 (FortiOS/FortiProxy auth bypass); pre-compromised FortiGate stockpile |
| T1078.002 | Valid Accounts: Domain Accounts — brute-forced FortiGate VPN credentials |
| T1133 | External Remote Services — VPN and remote management access |

### Execution & Persistence
| ID | Technique |
|---|---|
| T1059.001 | PowerShell |
| T1486 | Data Encrypted for Impact (ransomware payload) |
| T1491 | Defacement / Data Leak extortion (DLS posting) |

### Defense Evasion
| ID | Technique |
|---|---|
| T1562 | Impair Defenses — disable AV/EDR prior to ransomware deployment |
| T1036 | Masquerading |

### Lateral Movement & Exfiltration
| ID | Technique |
|---|---|
| T1021.001 | Remote Desktop Protocol |
| T1048 | Exfiltration Over Alternative Protocol — multi-platform data exfil |
| T1530 | Data from Cloud Storage Object |

### Impact
| ID | Technique |
|---|---|
| T1486 | Data Encrypted for Impact |
| T1657 | Financial Theft / Extortion |
| T1485 | Data Destruction (threatened if ransom unpaid) |

---

## Infrastructure and Technical Capabilities

- **Multi-platform ransomware support** — Windows and Linux/ESXi
- **Data audit service** — analyzes stolen datasets exceeding 300 GB to identify the most valuable information for extortion leverage; genuine innovation in the extortion model
- **Pre-compromised FortiGate stockpile** — ~14,700 devices (CVE-2024-55591); provides near-instant initial access at scale
- **Proxy malware use** — threat actors linked to The Gentlemen have been observed using proxy malware to expand their network reach (Cybersecurity Help, April 2026)
- **Lotus C2 signatures** reported in connection with Gentlemen infrastructure (BlackFog Q1 2026)

---

## Key Distinguishing Factors

1. **Pre-existing access inventory** — unlike most new groups that must acquire access, The Gentlemen launched with thousands of compromised devices ready to deploy
2. **Experienced operators** — founders come from top-tier RaaS program (Qilin); not rookies
3. **Geographic diversity play** — deliberately non-US targeting = reduced law enforcement heat
4. **Data auction innovation** — the 300 GB+ data audit service maximizes extortion ROI per victim
5. **Speed to scale** — 0 to global top-3 in ~6 months; only possible with pre-existing tradecraft and access

---

## Connection Web

- ⛓️ **Qilin (Scoundrel #031)** — Founding member Hastalamuerte is a former Qilin affiliate; shares tradecraft DNA; The Gentlemen is effectively a Qilin offshoot with a grudge
- ⛓️ **DragonForce (Scoundrel #033)** — Peer-tier RaaS competitor in current ransomware consolidation wave

---

## Geopolitical Context

The Gentlemen represent the **ransomware ecosystem's post-2025 consolidation dynamic** in microcosm: the top 10 groups now account for 71% of all victims (Check Point Q1 2026), reversing years of fragmentation. The Gentlemen achieved this position not through innovation in TTPs but through **operational execution with pre-existing capability** — a pattern that will likely be replicated by other experienced affiliates who exit or are expelled from incumbent RaaS programs.

The deliberate geographic diversification away from US targets is an emerging strategic pattern in the ecosystem, driven by increased FBI/DOJ enforcement success against US-focused actors and growing RaaS operator awareness that European and APAC victims offer comparable extortion value with lower enforcement risk.

---

## Defensive Recommendations

1. **Patch CVE-2024-55591 immediately** — this is The Gentlemen's primary stockpiled initial access vector; any unpatched FortiOS/FortiProxy device is potentially in their pre-compromised inventory
2. **Audit FortiGate admin accounts** — reset all credentials and review for unauthorized admin accounts
3. **Enforce FortiGate internet exposure** — minimize VPN portal exposure; restrict management interfaces to trusted IPs
4. **Deploy EDR with multi-platform coverage** — The Gentlemen target both Windows and Linux/ESXi
5. **Data exfiltration monitoring** — detect bulk staging and outbound transfers before ransomware payload deployment

---

## References

- [Check Point Research — State of Ransomware Q1 2026 (May 11, 2026)](https://research.checkpoint.com/2026/the-state-of-ransomware-q1-2026/)
- [BlackFog Q1 2026 Ransomware Report (May 6, 2026)](https://www.blackfog.com/blackfog-q1-2026-ransomware-report-press-release/)
- [GuidePoint Security — Ransomware Q1 2026](https://industrialcyber.co/reports/ransomware-reaches-elevated-new-normal-as-attack-volumes-hold-steady-into-2026-reshape-baseline-risk-expectations/)
- [Lyrie.ai — The Hidden Ransomware Crisis (May 8, 2026)](https://lyrie.ai/research/research/2026-05-08-hidden-ransomware-10-to-1-blind-spot)
- [Cybersecurity Help — Weekly Roundup April 24, 2026](https://www.cybersecurity-help.cz/blog/5374.html)

---

*Profile created: 2026-05-12 | Author: C3PO | Admiralty Grade: B2 | TLP: WHITE*
