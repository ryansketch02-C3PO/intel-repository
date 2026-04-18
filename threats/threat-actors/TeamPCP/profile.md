# 🤖 Threat Actor Profile: TeamPCP

**TLP:** WHITE  
**Admiralty Grade:** B2  
**Status:** 🔴 Active  
**Last Updated:** 2026-03-31  
**Profile Author:** C3PO Intel Platform  

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | TeamPCP |
| **Aliases** | PCPcat, ShellForce, DeadCatx3, PersyPCP, CipherForce |
| **First Observed** | November–December 2025 |
| **Telegram Activity Since** | November 17, 2025 |
| **Self-described** | "Rebranded" — implies prior activity under different name |
| **Suspected Origin** | Africa / Kenya (based on Telegram political references; Kenyan govt reportedly accused them of attacks) |
| **Motivation** | Financial — multi-stream monetization |
| **Actor Type** | Hybrid: Cloud exploitation botnet + access broker + data-leak crew + ransomware operator |

---

## Targeting

**Strategy:** Opportunistic — targets exposed cloud infrastructure, not specific industries. Organizations running misconfigured cloud-native services are in scope regardless of sector.

### Primary Attack Surface
- Exposed Docker Remote APIs
- Kubernetes API servers / kubelets / control planes
- Ray dashboards
- Redis instances
- React/Next.js applications (React2Shell — CVE-2025-29927, CVSS 10.0)
- CI/CD pipelines (Trivy, GitHub Actions, npm, PyPI)

### Cloud Provider Breakdown (compromised servers)
- Azure: ~61%
- AWS: ~36%
- GCP, Oracle, Alibaba: remainder

### Geographic Victims
UAE, Canada, South Korea, Serbia, United States, Vietnam

### Impacted Sectors
BFSI, e-commerce, HR/recruitment, consumer goods, professional services

### Scale
60,000+ compromised servers documented

---

## Campaigns

### Campaign 1 — PCPcat (December 2025)
The debut campaign. Worm-driven exploitation targeting exposed Docker APIs, Kubernetes clusters, Ray dashboards, Redis, and React2Shell-vulnerable apps. Activity peaked Christmas Day 2025. 185 compromised servers confirmed via honeypots. Operators celebrated publicly on Telegram and published stolen data dumps.

**Primary C2:** 67.217.57[.]240 (182 compromised hosts)  
**Secondary C2:** 44.252.85[.]168 (3 compromised hosts)

### Campaign 2 — Supply Chain (March 2026)
A significant escalation in sophistication. Credential-chaining attack across multiple ecosystems in under two weeks, tracked as **CVE-2026-33634 (CVSS 9.4)**.

| Date | Event |
|---|---|
| Mar 1, 2026 | Earlier Aqua Security credential breach disclosed — credentials retained by TeamPCP |
| Mar 19, 2026 | Malicious Trivy v0.69.4 force-pushed to 75/76 version tags; credential stealers activated in CI/CD pipelines |
| Mar 20, 2026 | Stolen npm tokens fed into **CanisterWorm** — self-propagating worm infected 64+ npm packages / 135+ artifacts |
| Mar 22, 2026 | Malicious Docker images pushed (v0.69.5/v0.69.6); 44 Aqua repos defaced; **Iran-targeted wiper component discovered** |
| Mar 23, 2026 | Checkmarx KICS GitHub Action hijacked using compromised service account; malicious VS Code extensions published |
| Mar 24, 2026 | LiteLLM v1.82.7/1.82.8 on PyPI poisoned (3.6M daily downloads); malicious payload triggered on every Python interpreter startup |
| Mar 25, 2026 | Pivoted to active extortion; ~300 GB compressed credentials; confirmed collaboration with **LAPSUS$**; Mandiant estimates 1,000–10,000 SaaS environments impacted |

Each hop was enabled by credentials harvested in the previous phase. One stolen token became five compromised ecosystems.

---

## Malware & Tooling

| Tool | Purpose |
|---|---|
| **proxy.sh** | Core backbone — installs P2P/proxy/tunneling utilities, deploys scanners, registers systemd persistence services |
| **pcpcat.py** | High-volume Docker API / Ray dashboard scanner + automated malicious container deployment |
| **scanner.py** | CIDR-based scanning for Docker/Ray; pulls CIDR lists from DeadCatx3 GitHub repos |
| **kube.py** | Kubernetes-specific — credential harvesting, DaemonSet persistence, host mount escape |
| **react.py** | CVE-2025-29927 (React2Shell) RCE exploitation at scale |
| **CanisterWorm** | Self-propagating npm worm seeded by stolen CI/CD tokens; infected 64+ packages |
| **XMRig** | Monero cryptominer — silent background operation on compromised hosts |
| **FRP / FRPS** | Fast Reverse Proxy — persistent remote access tunneling |
| **GOST** | GO Simple Tunnel — traffic relay and C2 comms |
| **Sliver** | Open-source C2 framework — encrypted channels, customizable implants |

### Notable C2 Technique
TeamPCP used an **Internet Computer Protocol (ICP) blockchain canister** as a dead-drop C2 — the first documented abuse of decentralized blockchain infrastructure for this purpose. Traditional domain takedown methods do not work against this architecture.

---

## MITRE ATT&CK Mapping

### Initial Access
| Technique | ID |
|---|---|
| Exploit Public-Facing Application | T1190 |
| External Remote Services | T1133 |

### Execution
| Technique | ID |
|---|---|
| Command and Scripting Interpreter | T1059 |
| Container Administration Command | T1609 |

### Persistence
| Technique | ID |
|---|---|
| Scheduled Task/Job: Cron | T1053.003 |
| Implant Internal Image | T1525 |
| Deploy Container | T1610 |
| Escape to Host (privileged DaemonSets) | T1611 |

### Impact
| Technique | ID |
|---|---|
| Resource Hijacking (XMRig cryptomining) | T1496 |
| Data Encrypted for Impact | T1486 |
| Inhibit System Recovery | T1490 |
| Data Manipulation | T1565 |

---

## Underground Presence

- **Telegram (TeamPCP channel):** ~700 members, active since Nov 17, 2025; publishes stolen data dumps, breach claims, extortion activity
- **ShellForce Telegram:** Dedicated leak channel — publishes breach data for reputation building
- **Data leaked to date:** ~27 GB across 30 dump/zip archives (PII, CVs, business records, national IDs, employment records)
- **Dark web:** Active/mentioned on 3+ Tor forums
- **LAPSUS$ collaboration:** Confirmed in March 2026 extortion phase — targeting multi-billion-dollar companies
- **DeadCatx3 GitHub:** 11 public repos; activity begins late 2025 matching actor emergence timeline

---

## Aerospace & Defense Relevance

**Relevance: MODERATE-HIGH**

TeamPCP does not specifically target aerospace, but:
- Supply chain campaign targets CI/CD tooling used universally — including defense contractors running Trivy, LiteLLM, or affected npm packages
- Any aerospace org running exposed cloud-native infrastructure (Azure/AWS) is in scope
- LAPSUS$ collaboration is notable — LAPSUS$ has previously targeted defense-adjacent technology companies
- **Iran-targeted wiper component** discovered in March 22 payload suggests possible broader geopolitical dimensions beyond pure financial motivation — worth tracking

---

## Defensive Priorities

1. **Audit CI/CD pipelines** — check for Trivy v0.69.4, LiteLLM 1.82.7/1.82.8, and affected npm packages immediately
2. **Rotate ALL secrets** accessible to any system that ran compromised components (cloud keys, PATs, SSH keys, K8s secrets, registry tokens)
3. **Lock down Docker/Kubernetes APIs** — no unauthenticated external access
4. **Pin GitHub Actions to verified commit hashes**, not mutable tags
5. **Restrict outbound CI/CD network access** to approved destinations only
6. **Monitor for CanisterWorm** signatures in npm environments
7. **Watch for Sliver, XMRig, FRPS/GOST traffic** — high-confidence compromise indicators
8. **Enforce least privilege** across GitHub Actions, package workflows, Docker, and Kubernetes-integrated build systems

---

## Intelligence Sources

| Source | URL | Date |
|---|---|---|
| Flare (Assaf Morag) — Primary deep-dive | https://flare.io/learn/resources/blog/teampcp-cloud-native-ransomware | 2026-02-05 |
| The Hacker News — PCPcat campaign | https://thehackernews.com/2026/02/teampcp-worm-exploits-cloud.html | 2026-02-09 |
| Cyble — Actor profile + MITRE mapping | https://cyble.com/threat-actor-profiles/teampcp/ | 2026-02-18 |
| SANS Institute — Supply chain breakdown | https://www.sans.org/blog/when-security-scanner-became-weapon-inside-teampcp-supply-chain-campaign | 2026-03-25 |
| MOXFIVE — March 2026 campaign alert | https://www.moxfive.com/resources/moxfive-threat-actor-alert-teampcp | 2026-03-25 |
| Wiz Threat Landscape — Actor tracking | https://threats.wiz.io/all-actors/teampcp | 2026-03-22 |
