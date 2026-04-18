# 🔍 TeamPCP — Indicators of Compromise (IOCs)

**TLP:** WHITE  
**Last Updated:** 2026-03-31  

---

## Network IOCs

### IP Addresses
| IP | Role | Source |
|---|---|---|
| 67.217.57[.]240 | Primary C2 node (PCPcat campaign) — Sliver C2, appeared on 182 compromised hosts | Flare |
| 44.252.85[.]168 | Secondary C2 / infrastructure redundancy | Flare |

### Domains
| Domain | Notes |
|---|---|
| masscan[.]cloud | Linked to 67.217.57.240 during Nov 2025; associated with DeadCatx3 GitHub account |

---

## File / Package IOCs

### Compromised Software Versions
| Package | Malicious Version(s) | Platform | Notes |
|---|---|---|---|
| Trivy | v0.69.4 | GitHub Actions / Docker | Force-pushed to 75/76 version tags; credential stealer payload |
| Trivy Docker Image | v0.69.5, v0.69.6 | Docker Hub | Pushed directly without GitHub release |
| LiteLLM | 1.82.7, 1.82.8 | PyPI | .pth file auto-executes on any Python interpreter startup |
| Checkmarx KICS | (all tags) | GitHub Actions | Master branch clean; all tags pointed to malicious commits |
| CanisterWorm | — | npm | Self-propagating worm; infected 64+ packages / 135+ artifacts |

### Malicious Scripts
| File | Purpose |
|---|---|
| proxy.sh | Core persistence/tunneling/scanning backbone |
| pcpcat.py | Docker API / Ray dashboard scanner + auto-deployment |
| scanner.py | CIDR-based scanner; pulls from DeadCatx3 GitHub |
| kube.py | Kubernetes credential harvesting + DaemonSet persistence |
| react.py | CVE-2025-29927 RCE exploitation |
| mine.sh | XMRig Monero miner deployment |

---

## CVEs Exploited

| CVE | CVSS | Description |
|---|---|---|
| CVE-2025-29927 | 10.0 | React2Shell — Next.js/React RCE via misconfigured server components |
| CVE-2025-55182 | 10.0 | React2Shell (alternate tracking ID) |
| CVE-2026-33634 | 9.4 | Supply chain campaign (Trivy/KICS/LiteLLM) |

---

## Tooling Signatures (Detection Hints)

- Unexpected Docker containers / Kubernetes DaemonSets not in your deployment manifests
- New Kubernetes Jobs submitted from unknown IPs to Ray dashboards
- Redis `CONFIG`, `SLAVEOF`, or `EVAL` commands from unexpected sources
- Outbound connections to GitHub for unexpected downloads during CI/CD runs
- XMRig process activity / high sustained CPU on cloud workloads
- FRPS, GOST, or Sliver beacon traffic
- `.pth` files in Python environments (LiteLLM compromise indicator)
- npm packages with unexpected publish events or version bumps

---

## Underground Infrastructure

| Platform | Handle / Channel | Notes |
|---|---|---|
| Telegram | TeamPCP | ~700 members; publishes stolen data, breach claims |
| Telegram | ShellForce / Persy_PCP | Dedicated leak channel; redirect from Persy_PCP to main |
| GitHub | DeadCatx3 | 11 public repos; CIDR lists used by scanner.py |
| Tor forums | Various | Active/mentioned on 3+ .onion forums |
