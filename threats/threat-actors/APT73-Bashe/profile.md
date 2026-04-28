# APT73 / Bashe — Scoundrel #023

## Overview

| Field | Details |
|---|---|
| **Also Known As** | Bashe, Eraleign |
| **Scoundrel #** | 023 |
| **Type** | Cybercriminal — Ransomware-as-a-Service (RaaS) |
| **Origin** | Assessed: Czech Republic |
| **Sponsorship** | Financially motivated — independent criminal operation |
| **Active Since** | April 2024 |
| **Status** | ACTIVE — major activity spike April 27, 2026 |
| **Confirmed Victims** | 115+ (as of April 27, 2026) |
| **Threat Level** | HIGH |
| **Admiralty Grade** | A2 — confirmed activity, some victim authenticity disputes |
| **Primary Motivation** | Financial gain; credibility building; affiliate recruitment |

---

## Background

APT73 emerged in April 2024 as one of the most brazen opportunists to arise from the wreckage of Operation Cronos — the February 2024 FBI/NCA takedown of LockBit. The group is believed to have been founded by a former LockBit affiliate who took the RaaS playbook, mimicked LockBit's data leak site structure almost exactly, and self-styled the operation as an "Advanced Persistent Threat" — a designation normally reserved for state-sponsored actors.

The "APT" label is deliberate theatre. It is a marketing strategy designed to signal credibility to potential affiliates and intimidate victims. Researchers at CloudSEK documented instances of APT73 reposting old breach data and claiming credit for attacks it did not carry out — reflecting an operation more focused on reputation management than technical sophistication.

That said, **the group is operationally active and real.** As of April 27, 2026, APT73 posted approximately 30 new victims in a single day — including targets across UK financial services, UAE government, Philippines public works, Bangladesh aviation, and Kenyan government systems. With 115+ confirmed victims across two years of activity, it has established itself as a credible mid-tier RaaS operation.

---

## Modus Operandi

**Model:** Double extortion RaaS — encrypt victim files + exfiltrate data, then demand ransom under threat of public leak. Operates a data leak site (DLS) that mirrors LockBit's structure to attract post-Cronos affiliates.

### Targeting Profile

| Sector | Victims |
|---|---|
| Business Services | 25 |
| Technology | 18 |
| Financial | 13 |
| Healthcare | 7 |
| Manufacturing | 6 |
| Government/Energy | Multiple |

**Primary geographies:** United Kingdom, United States, India, France, Brazil — with reach across 40+ countries

**Revenue sweet spot:** Mid-sized organizations ($10M–$500M annual revenue)

### Initial Access (Assessed — affiliate-dependent)
- Phishing / spearphishing
- Exposed RDP exploitation
- VPN credential abuse (purchased from IABs)
- Unpatched public-facing vulnerabilities

### Tooling (Inherited LockBit Affiliate Tradecraft)
- Cobalt Strike / Brute Ratel for C2
- Mimikatz / LaZagne for credential dumping
- Advanced Port Scanner / SoftPerfect for discovery
- Rclone for data exfiltration
- PsExec / WMI for lateral movement and ransomware distribution
- `wbadmin DELETE SYSTEMSTATEBACKUP` — shadow copy deletion
- `wevtutil` — event log clearing
- ESXi targeting for virtual machine fleet encryption

---

## Intelligence Caveats — Authenticity Concerns

CloudSEK (January 2025) documented specific fabricated claims:

| Claimed Victim | Reality |
|---|---|
| Malindo Air | Repost of 2019 breach data |
| Betclic | Data curated from existing combolists |
| Federal Bank India | Identical to BreachForums post from May 2023 |
| Bank Rakyat Indonesia | Publicly available document — no breach confirmed |

Organizations listed on the APT73 DLS should independently validate data before assuming a genuine breach. The reputational risk of appearing on a ransomware DLS is real regardless of data authenticity.

---

## Notable Victims

| Victim | Country | Sector | Date |
|---|---|---|---|
| Hargreaves Lansdown | UK | Financial Services | Apr 2026 |
| UAE Ministry of Climate Change | UAE | Government | Mar 2026 |
| Kenya IFMIS | Kenya | Government | Apr 2026 |
| Philippine DPWH | Philippines | Government | Apr 2026 |
| Maerco Telecom (iam.ma) | Morocco | Telecom | Apr 2026 |
| Novoair | Bangladesh | Aviation | Apr 2026 |
| University of Belgrade | Serbia | Education | Apr 2026 |
| Sharjah Electricity, Water and Gas Authority | UAE | Energy/Utilities | Apr 2026 |
| Indonesian National Army database | Indonesia | Military | Feb 2025 |
| Robinhood Broker (7.7M email records) | USA | Finance/Tech | Oct 2024 |
| Alior Bank | Poland | Finance | Dec 2024 |
| Bristol-Myers Squibb | USA | Pharmaceutical | Dec 2024 |
| Hewlett Packard Enterprise (CDS) | USA | Technology | Oct 2024 |

---

## MITRE ATT&CK TTPs

| Technique | ID |
|---|---|
| Phishing | T1566 |
| Valid Accounts (RDP/VPN) | T1078 |
| Exploit Public-Facing Application | T1190 |
| OS Credential Dumping | T1003 |
| Network Service Discovery | T1046 |
| Lateral Movement via RDP | T1021.001 |
| Exfiltration via Cloud (Rclone) | T1567 |
| Inhibit System Recovery (VSS) | T1490 |
| Data Encrypted for Impact | T1486 |
| Double Extortion via DLS | T1491 |

---

## Infrastructure

| Asset | Type | Status |
|---|---|---|
| `apt73grpjgjwykrenq7vnjejue76vosdzptdvmonv7vyqnsyokrw57ad.onion` | Tor DLS (APT73) | Offline mid-2025 |
| `fleqwmg7xnanypt5km2m75l72q7nlcvlp2m4sdmgjxorsn6tb3zyp3qd.onion` | Tor DLS (APT73) | Offline mid-2025 |
| `basheqtvzqwz4vp6ks5lm2ocq7i6tozqgf6vjcasj4ezmsy4bkpshhyd.onion` | Tor DLS (BASHE) | Active mid-2025 |
| `basherq53eniermxovo3bkduw5qqq5bkqcml3qictfmamgvmzovykyqd.onion` | Tor DLS (BASHE) | Active mid-2025 |
| `eraleignews.com` | Clearnet | Eraleign alias |

**Rebrand note:** APT73 partially rebranded to **BASHE** in 2025 — multiple Tor mirrors under the BASHE name. Both brands are the same operation.

---

## Connections

| Actor | Relationship |
|---|---|
| LockBit | Founding members assessed as former LockBit affiliates; DLS structure cloned post-Cronos |
| BLACKCAT/ALPHV | Potential post-disruption affiliate migration overlap |
| REvil | Historical LockBit affiliate pool included ex-REvil operators |

---

## Timeline

| Date | Event |
|---|---|
| Feb 2024 | Operation Cronos takes down LockBit |
| Apr 22, 2024 | APT73 launches DLS; first victims posted |
| Late 2024 | Volume escalates — banking, finance, technology targets |
| Jan 2025 | CloudSEK publishes fabricated breach claim analysis |
| Mid-2025 | Partial rebrand to BASHE; new .onion mirrors |
| Mar 2026 | UAE Ministry of Climate Change targeted |
| Apr 27, 2026 | ~30 new victims posted in single day — major activity spike |
| Apr 28, 2026 | Added to C3PO Threat Actor Repository as Scoundrel #023 |

---

## References

- [ransomware.live — APT73/Bashe](https://www.ransomware.live/group/apt73)
- [CloudSEK — Unmasking Media-Hungry Ransomware Groups: Bashe (APT73)](https://www.cloudsek.com/blog/unmasking-media-hungry-ransomware-groups-bashe-apt73)
- [Solace Cyber — APT73 / Bashe Ransomware](https://solacecyber.co.uk/apt73-ransomware/)
- [DeXpose — APT73/Bashe Targets UAE Ministry of Climate Change](https://www.dexpose.io/apt73-bashe-targets-uaes-ministry-of-climate-change-and-environment/)
