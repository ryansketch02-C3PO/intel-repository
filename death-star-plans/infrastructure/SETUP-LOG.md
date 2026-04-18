# 🔧 Setup Log — DefenseClaw Installation
**Date:** 2026-04-06  
**Operator:** Ryan  
**System:** Raspberry Pi 5 (lobsterpi) — Debian 12 Bookworm, aarch64

---

## Overview

This document records the full installation process for Docker, NVIDIA OpenShell, and Cisco DefenseClaw on lobsterpi. Includes issues encountered and resolutions.

---

## Phase 1 — Docker CE Installation

### Prerequisites Check
- **OS:** Debian GNU/Linux 12 (Bookworm), aarch64
- **Kernel:** 6.12.62+rpt-rpi-2712
- **RAM:** 7.9GB (2.7GB available)
- **Disk:** 235GB total, 213GB free
- **Docker:** Not installed (confirmed)
- **Docker CDN:** Reachable

### Installation Command
```bash
sudo apt-get update && \
sudo apt-get install -y ca-certificates curl && \
sudo install -m 0755 -d /etc/apt/keyrings && \
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc && \
sudo chmod a+r /etc/apt/keyrings/docker.asc && \
echo "deb [arch=arm64 signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian bookworm stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && \
sudo apt-get update && \
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin && \
sudo usermod -aG docker lobsterclaw && \
sudo systemctl enable --now docker
```

### Result
✅ **Docker version 29.3.1** installed successfully  
✅ Docker service enabled and running  
✅ `lobsterclaw` added to docker group

---

## Phase 2 — NVIDIA OpenShell Installation

### Notes
- Initial attempt used incorrect URL (`install.nvidia.com`) — domain does not exist
- Correct installer is hosted on GitHub: `github.com/NVIDIA/OpenShell`
- Latest stable release: **v0.0.11** (2026-03-19)
- No NVIDIA GPU required — OpenShell uses Docker as the sandbox runtime

### Installation Command
```bash
curl -LsSf https://raw.githubusercontent.com/NVIDIA/OpenShell/main/install.sh \
  | OPENSHELL_VERSION=v0.0.11 sh
```

### Result
✅ **OpenShell v0.0.11** installed to `/home/lobsterclaw/.local/bin/openshell`  
✅ arm64 (aarch64) binary — fully compatible with Raspberry Pi 5

---

## Phase 3 — Cisco DefenseClaw Installation

### Notes
- DefenseClaw installer prompted to update OpenClaw during install
- OpenClaw is installed via npm (`~/.npm-global`) — **DO NOT update via git or mixed methods**
- The OpenClaw update prompt was **skipped/declined** — DefenseClaw installed successfully without it
- Safe OpenClaw update path when needed: `npm update -g openclaw`

### Installation Command
```bash
curl -LsSf https://raw.githubusercontent.com/cisco-ai-defense/defenseclaw/main/scripts/install.sh | bash
```

### Initialization
```bash
echo "n" | defenseclaw init
```

### Issues Encountered

#### Issue 1: Skill/MCP scanners not on PATH
**Symptom:** `defenseclaw status` showed `skill-scanner not found` / `mcp-scanner not found`  
**Root cause:** Scanner binaries installed into virtualenv at `~/.defenseclaw/.venv/bin/` but not symlinked to PATH  
**Resolution:**
```bash
ln -sf /home/lobsterclaw/.defenseclaw/.venv/bin/skill-scanner /home/lobsterclaw/.local/bin/skill-scanner
ln -sf /home/lobsterclaw/.defenseclaw/.venv/bin/mcp-scanner /home/lobsterclaw/.local/bin/mcp-scanner
```

#### Issue 2: Sidecar not running after init
**Symptom:** `Sidecar: not running` in status  
**Root cause:** Sidecar started during init but stopped when the long-running scan session timed out  
**Resolution:** Re-running `defenseclaw init` restarted the sidecar automatically

#### Issue 3: Incorrect `--agent` flag
**Symptom:** `Error: No such option: --agent` on `defenseclaw init --agent openclaw`  
**Root cause:** Flag doesn't exist in v0.2.0 — DefenseClaw auto-detects OpenClaw  
**Resolution:** Run `defenseclaw init` without the flag

#### Issue 4: Guardrail mode name
**Symptom:** `Error: Invalid value for '--mode': 'monitor'`  
**Root cause:** Mode is called `observe` not `monitor` in v0.2.0  
**Resolution:** Use `--mode observe`

### Final Status
```
defenseclaw doctor — 8 passed, 0 failed, 6 skipped

Sandbox:       available
skill-scanner: installed  (/home/lobsterclaw/.local/bin/skill-scanner)
mcp-scanner:   installed  (/home/lobsterclaw/.local/bin/mcp-scanner)
codeguard:     built-in
Sidecar:       running (PID active, gateway + watcher)
OpenClaw:      127.0.0.1:18789
Sidecar API:   127.0.0.1:18970
Guardrail:     disabled (not yet configured)
Splunk:        not configured
```

**Version:** DefenseClaw 0.2.0  
**Install path:** `/home/lobsterclaw/.local/bin/defenseclaw`  
**Config:** `/home/lobsterclaw/.defenseclaw/config.yaml`  
**Audit DB:** `/home/lobsterclaw/.defenseclaw/audit.db`

---

## Optional Steps Not Yet Completed

| Step | Description | Priority |
|---|---|---|
| LLM Guardrail | Route LLM traffic through inspection proxy (observe mode) | Medium |
| Splunk Integration | Stream audit events to local Splunk instance | Low |
| VirusTotal API | Enable for MCP scanner enrichment | Low |
| OpenClaw update | Update to latest via `npm update -g openclaw` when ready | Low |

---

*Documented by C3PO | 2026-04-06*
