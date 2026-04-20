# ⚙️ DefenseClaw Configuration Reference
**Version:** 0.2.0  
**Last Updated:** 2026-04-06

---

## Current Configuration Summary

```
Mode:              observe (log only, no blocking)
Sandbox:           available (OpenShell v0.0.11)
Skill Scanner:     active — policy=permissive, lenient=True
MCP Scanner:       active — analyzers=yara
CodeGuard:         built-in, always active
Guardrail proxy:   disabled
Splunk:            not configured
VirusTotal:        not configured
Cisco AI Defense:  not configured (local scanning only)
```

---

## Key Paths

| Path | Description |
|---|---|
| `/home/[USERNAME]/.defenseclaw/config.yaml` | Main config file |
| `/home/[USERNAME]/.defenseclaw/audit.db` | SQLite audit database |
| `/home/[USERNAME]/.defenseclaw/policies/rego/` | OPA Rego policy files |
| `/home/[USERNAME]/.defenseclaw/splunk-bridge/` | Splunk connector (seeded, not active) |
| `/home/[USERNAME]/.defenseclaw/.venv/bin/skill-scanner` | Skill scanner binary |
| `/home/[USERNAME]/.defenseclaw/.venv/bin/mcp-scanner` | MCP scanner binary |
| `/home/[USERNAME]/.defenseclaw/device.key` | DefenseClaw device key |

---

## Scanner Details

### CodeGuard
- **Status:** Built-in, always active
- **Installed skill:** `~/.openclaw/workspace/skills/codeguard`
- **Function:** Scans every file C3PO generates or edits before it hits the filesystem
- **Catches:** Hardcoded secrets, command injection, unsafe deserialization, eval(input), open network sockets, privilege escalation patterns
- **Mode:** Integrated into the agent execution loop

### Skill Scanner
- **Binary:** `~/.local/bin/skill-scanner` → `~/.defenseclaw/.venv/bin/skill-scanner`
- **Policy:** `permissive` (lenient=True) — flags issues but doesn't block in observe mode
- **Function:** Scans OpenClaw skills for malicious patterns, unexpected network calls, permission mismatches
- **Watches:** `~/.openclaw/extensions/` — monitors for unauthorized skill additions

### MCP Scanner
- **Binary:** `~/.local/bin/mcp-scanner` → `~/.defenseclaw/.venv/bin/mcp-scanner`
- **Analyzers:** YARA rules
- **Function:** Verifies integrity of MCP servers C3PO connects to; enforces allow/block lists
- **Enforcement:** In action mode, blocked MCP servers are removed from sandbox network allow-list within 2 seconds

---

## Sidecar Services

| Service | Status | Port |
|---|---|---|
| gateway | ✅ Running | 18970 |
| watcher | ✅ Running | — |
| guardrail | ⏸️ Disabled | — |

**OpenClaw gateway connection:** `127.0.0.1:18789`  
**Sidecar API:** `127.0.0.1:18970`

---

## Useful Commands

```bash
# Check overall status
defenseclaw status

# Run full health check
defenseclaw doctor

# View audit log / alerts
defenseclaw alerts

# Scan all skills
defenseclaw skill list

# Scan all MCP servers
defenseclaw mcp list

# Generate AI Bill of Materials
defenseclaw aibom scan

# Block a skill
defenseclaw skill block <skill-name>

# Block an MCP server
defenseclaw mcp block <server-name>

# Enable LLM guardrail in observe mode
defenseclaw setup guardrail --mode observe --non-interactive

# Switch to action mode (blocking)
defenseclaw setup guardrail --mode action --non-interactive

# Restart sidecar
defenseclaw init
```

---

## Upgrade Path

### When to Enable Action Mode
Review `~/.defenseclaw/audit.db` after ~2 weeks of observe data. Confirm no legitimate C3PO operations are being flagged before switching to action mode. Then:

```bash
defenseclaw setup guardrail --mode action --non-interactive
```

### When to Enable LLM Guardrail
The LLM guardrail routes all Anthropic API traffic through a local inspection proxy. It scans every prompt and response for:
- Prompt injection attacks
- PII leakage
- Data exfiltration patterns
- Secrets in context

To enable:
```bash
defenseclaw setup guardrail --mode observe --non-interactive
```
> Note: Requires model configuration during setup. Run interactively first time.

### OpenClaw Update (when ready)
```bash
npm update -g openclaw
```
> ⚠️ Always use npm for updates — never mix with git installs. Previous issue: mixing install methods broke webchat access.

---

## Known Issues & Workarounds

| Issue | Workaround |
|---|---|
| Scanners not on PATH after install | `ln -sf ~/.defenseclaw/.venv/bin/skill-scanner ~/.local/bin/skill-scanner` (same for mcp-scanner) |
| Sidecar stops after long-running commands | Re-run `defenseclaw init` to restart |
| Guardrail setup requires interactive model selection | Run without `--non-interactive` first time |
| `defenseclaw init --agent openclaw` fails | Drop `--agent` flag — auto-detected in v0.2.0 |
| `--mode monitor` invalid | Use `--mode observe` |

---

*Maintained by C3PO | Death Star Plans repo | 2026-04-06*
