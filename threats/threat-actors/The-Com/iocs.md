# The Com (The Community) — IOCs & Detection Indicators

> **Classification:** TLP:WHITE
> **Last Updated:** 2026-05-04
> **Admiralty Grade:** B2
> **Note:** The Com is a **criminal ecosystem**, not a single actor. Traditional IOCs (specific IPs, hashes, domains) apply to individual subgroups, not the ecosystem itself. This file covers ecosystem-level infrastructure patterns, platform signals, and indicators relevant to identifying Com-affiliated activity. For subgroup-specific IOCs, see: `Scattered-Spider/iocs.md`, `Cordial-Spider/iocs.md`, `Snarky-Spider/iocs.md`.

---

## Ecosystem Infrastructure Patterns

### Common Proxy Infrastructure (Shared Across Subgroups)

All major Com cyber operations subgroups share a common residential proxy stack:

| Provider | Role |
|---|---|
| Mullvad VPN | Operational anonymization |
| Oxylabs | Residential proxy |
| NetNut | Residential proxy |
| 9Proxy | Residential proxy |
| Infatica | Residential proxy |
| NSOCKS | Residential proxy |
| Antidetect browsers (Linken Sphere, Dolphin, GoLogin) | Browser fingerprint spoofing |

Authentication events from these provider ASNs should be flagged regardless of which specific subgroup is responsible.

### Communication & Coordination Platforms

| Platform | Role | Notes |
|---|---|---|
| **Telegram** | Primary C2 / coordination | "The Comm Leaks" and derivative channels; real-time attack coordination |
| **Discord** | Recruitment, secondary comms | Primary recruitment channel for minors; monitored by FBI/NCA |
| **Roblox / Minecraft / Steam** | Youth pipeline recruitment | Where 11–17yo recruits are first contacted |
| **BreachForums** (post-v4) | Data marketplace | Offline August 2025; members migrating to Exploit.in, XSS |
| **Exploit.in / XSS** | Russian-language forums | English-speaking Com members increasingly present post-BreachForums |
| **Twitch / live streaming** | Notoriety / social proof | Attacks are livestreamed; successful breaches broadcast as status signals |

---

## Behavioral Indicators by Attack Phase

### Initial Recruitment / Reconnaissance
| Behavior | Signal | Confidence |
|---|---|---|
| Unsolicited contact of employee on gaming platform or Discord | Potential early recruitment or social engineering probe | 🟡 Medium |
| Employee reports receiving unusual contact claiming insider access or offering money | Potential Com recruitment attempt | 🟡 Medium |

### Initial Access (Hacker Com)
| Behavior | Signal | Confidence |
|---|---|---|
| Vishing call → employee redirected to SSO lookalike page | Com-affiliated initial access | 🔴 High |
| SIM swap → MFA bypass | Telecom-layer attack | 🔴 High |
| Insider recruited within target org | Most dangerous Com vector | 🔴 High |
| Help desk manipulation → password reset without proper verification | Social engineering | 🔴 High |

### Post-Compromise Indicators
| Behavior | Signal | Confidence |
|---|---|---|
| New MFA device from residential proxy ASN | Active identity attack | 🔴 High |
| Bulk SaaS data export (SharePoint, Salesforce, GitHub) shortly after new login | Collection phase | 🔴 High |
| Alert/notification deletion | Defense evasion | 🔴 High |
| Cloud storage staging (MEGA, LimeWire, Dropbox) | Pre-exfiltration | 🔴 High |
| Ransom demand via burner Gmail / compromised employee email | Extortion phase | 🔴 High |

### Escalation / Pressure Tactics
| Behavior | Signal | Actor |
|---|---|---|
| DDoS against public-facing infrastructure during active extortion | Cordial Spider / Snarky Spider | 🔴 High |
| Swatting — false emergency services contact at exec home/office | Snarky Spider primarily | 🔴 High |
| Data posted to dark web / leak site | Any Com subgroup with DLS | 🔴 High |
| Threatening calls to employees or family members | IRL Com crossover | 🟡 Medium |

---

## Subgroup Quick-Reference

| Subgroup | Specialization | IOC File |
|---|---|---|
| **Scattered Spider** | Social engineering, ransomware, identity attacks | `SCATTERED-SPIDER/iocs.md` |
| **Cordial Spider** (BlackFile/CL-CRI-1116) | Vishing, SaaS traversal, 7-figure extortion | `Cordial-Spider/iocs.md` |
| **Snarky Spider** (UNC6661) | Vishing, SaaS traversal, swatting escalation | `Snarky-Spider/iocs.md` |
| **LAPSUS$** | Insider recruitment, source code theft | See SCATTERED-SPIDER profile for overlap |
| **ShinyHunters / SLSH** | Mass DB theft, BreachForums | Disrupted June 2025; IOCs historical |
| **CryptoChameleon** | Crypto phishing, exchange impersonation | External tracking recommended |

---

## Key Arrested Individuals (For Background Investigation / HR Screening)

| Alias | Real Name | Role | Status |
|---|---|---|---|
| Pompompurin | Conor Brian Fitzpatrick | BreachForums v1 founder | 3 years federal prison (resentenced Sep 2025) |
| IntelBroker | Kai West | BreachForums v3 admin | Arrested Feb 2025; indicted June 2025 |
| tylerb | Tyler Buchanan | Scattered Spider core | Guilty plea 2025 |
| ShinyHunters (multiple) | French nationals | BreachForums v2/v4 admin | 4 arrested June 2025 |

---

## MITRE ATT&CK Coverage (Ecosystem-Level)

| Technique | ID | Com Usage |
|---|---|---|
| Phishing / Vishing / Smishing | T1566.001 / T1566.002 / T1566.004 | Primary initial access across all subgroups |
| SIM Swapping | T1111 | Telecom-layer MFA bypass |
| Acquire Infrastructure | T1583 | Fresh domains, AiTM pages per campaign |
| Valid Accounts | T1078.004 | Credential theft from phishing |
| MFA Request Generation | T1621 | AiTM real-time TOTP harvest |
| MFA Device Registration | T1098.005 | Post-compromise persistence |
| Insider Threat / Internal Spearphishing | T1534 | LAPSUS$/Scattered Spider insider access |
| Data from Information Repositories | T1213 | SharePoint, Salesforce, GitHub |
| Exfiltration to Cloud Storage | T1567.002 | MEGA, LimeWire, Dropbox |
| Impair Defenses | T1562 | Alert deletion |
| Financial Theft / Extortion | T1657 | Primary objective |
| DDoS | T1498 | Pressure tactic |

---

## Law Enforcement Resources

| Agency | Reference |
|---|---|
| FBI | PSA IC3-250723 — Public warning re: The Com ecosystem (July 2025) |
| UK NCA | Active investigation into IRL Com violence network |
| Europol / Interpol | Ongoing multinational investigations |
| CISA | Scattered Spider joint advisory (2023); ongoing monitoring |

---

## References

- [FBI PSA — The Com Criminal Ecosystem (July 2025)](https://www.ic3.gov/PSA/2025/PSA250723)
- [CrowdStrike — Defending Against Cordial Spider and Snarky Spider](https://www.crowdstrike.com/en-us/blog/defending-against-cordial-spider-and-snarky-spider-with-falcon-shield/)
- [Sophos — The Com: Inside a Criminal Ecosystem](https://news.sophos.com/en-us/2025/08/the-com-inside-a-criminal-ecosystem/)
- [Recorded Future / Insikt Group — The Com Ecosystem Report](https://www.recordedfuture.com/research/the-com-ecosystem)

---

*IOCs compiled by C3PO | Last updated: 2026-05-04 | TLP:WHITE*
