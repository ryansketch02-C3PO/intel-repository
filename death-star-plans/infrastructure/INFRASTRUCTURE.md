# 🏗️ Infrastructure Overview — C3PO Intel Agent
**Last Updated:** 2026-04-06

---

## Hardware

| Component | Details |
|---|---|
| **Device** | Raspberry Pi 5 Model B Rev 1.0 |
| **Hostname** | lobsterpi |
| **CPU** | BCM2712 (aarch64 / ARM64) |
| **RAM** | 7.9GB |
| **Storage** | 235GB (SD/NVMe) — 213GB free |
| **OS** | Debian GNU/Linux 12 (Bookworm) |
| **Kernel** | 6.12.62+rpt-rpi-2712 |

---

## Software Stack

| Component | Version | Purpose |
|---|---|---|
| **Node.js** | v22.22.1 | OpenClaw runtime |
| **OpenClaw** | 2026.3.13 (61d171a) | AI agent framework |
| **Install method** | npm global (`~/.npm-global`) | ⚠️ Do not mix with git installs |
| **Docker CE** | 29.3.1 | Container runtime (DefenseClaw/OpenShell) |
| **NVIDIA OpenShell** | v0.0.11 | Agent sandbox environment |
| **Cisco DefenseClaw** | v0.2.0 | AI agent security governance |
| **Python** | System + venv | DefenseClaw scanners |

---

## AI Model

| Setting | Value |
|---|---|
| **Provider** | Anthropic (API key — not Claude Pro subscription) |
| **Model** | `anthropic/claude-sonnet-4-6` |
| **Billing** | Pay-per-token via Anthropic API |
| **Estimated cost** | ~$18–36/month at current usage patterns |
| **Cache hit rate** | ~99–100% (very efficient) |

---

## Key File Paths

| Path | Purpose |
|---|---|
| `~/.openclaw/workspace/` | C3PO working directory (this repo is remote) |
| `~/.openclaw/openclaw.json` | OpenClaw configuration |
| `~/.openclaw/extensions/` | Installed plugins |
| `~/.defenseclaw/config.yaml` | DefenseClaw configuration |
| `~/.defenseclaw/audit.db` | DefenseClaw audit log (SQLite) |
| `~/.defenseclaw/.venv/bin/` | Scanner binaries (skill-scanner, mcp-scanner) |
| `~/.local/bin/` | User PATH binaries (symlinks to scanners + openshell) |
| `~/.msmtprc` | Email config (msmtp + Gmail App Password) |

---

## Integrations

### Discord
| Setting | Value |
|---|---|
| **Guild ID** | 1483619397435265096 |
| **Intel Channel** | 1483619397435265096 |
| **Scoundrels Channel** | 1488665479189893202 |
| **Bot account** | c3po#1418 |

### GitHub
| Setting | Value |
|---|---|
| **Account** | ryansketch02-C3PO |
| **Intel repo** | `c3po-intel-workspace` (private) |
| **Threat actor repo** | `threat-actor-repository` (private) |
| **Security docs repo** | `death-star-plans` (private) — this repo |
| **PAT expiry** | ~April 22, 2026 |

### Email
| Setting | Value |
|---|---|
| **Outbound** | msmtp + Gmail App Password |
| **Sending account** | ryansketch02@gmail.com |
| **Ryan's inbox** | ryansketch01@gmail.com |

---

## OpenClaw Skills (Active)

| Skill | Status | Purpose |
|---|---|---|
| coding-agent | ✅ Ready | Delegate coding tasks to Codex/Claude Code |
| discord | ✅ Ready | Discord channel operations |
| healthcheck | ✅ Ready | Security hardening and risk checks |
| node-connect | ✅ Ready | Diagnose OpenClaw node connection issues |
| skill-creator | ✅ Ready | Create/edit OpenClaw skills |
| video-frames | ✅ Ready | Extract frames from videos |
| weather | ✅ Ready | Weather lookups |
| codeguard | ✅ Ready | DefenseClaw CodeGuard (security scanner) |

---

## Network Ports

| Port | Service | Notes |
|---|---|---|
| 18789 | OpenClaw gateway | Local only |
| 18970 | DefenseClaw sidecar API | Local only |

---

*Maintained by C3PO | Death Star Plans repo | 2026-04-06*
