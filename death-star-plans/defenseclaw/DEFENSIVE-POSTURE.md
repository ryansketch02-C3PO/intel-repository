# 🛡️ Defensive Posture — C3PO Intel Agent
**Last Updated:** 2026-04-06  
**System:** lobsterpi (Raspberry Pi 5)  
**Operator:** Ryan

---

## Current Defensive Controls

### Layer 1 — AI Agent Security (DefenseClaw v0.2.0)

Cisco DefenseClaw is the primary security governance layer for C3PO. It runs as a sidecar process alongside OpenClaw, continuously monitoring agent behavior.

| Scanner | Status | Function |
|---|---|---|
| **CodeGuard** | ✅ Active (built-in) | Scans every piece of code C3PO generates before execution. Catches hardcoded secrets, command injection, eval abuse, unsafe file writes, open network sockets. |
| **Skill Scanner** | ✅ Active | Scans all OpenClaw skills before they're permitted to run. Checks for malicious patterns, unexpected network calls, permission escalation attempts. |
| **MCP Scanner** | ✅ Active | Verifies every MCP server C3PO connects to. Applies allow/block lists. Detects compromised or unauthorized MCP endpoints. |
| **Sidecar Watcher** | ✅ Running | Real-time monitoring of skill directories and plugin installations for unauthorized changes. |
| **OpenShell Sandbox** | ✅ Available | NVIDIA OpenShell provides a sandboxed execution environment. When a skill is blocked, permissions are revoked and files quarantined within 2 seconds. |

**Current mode:** `observe` — all findings are logged, nothing is blocked yet  
**Enforcement mode:** Available but not yet activated (switch with `defenseclaw setup guardrail --mode action`)

**Audit trail:** All scan results, block decisions, tool calls, and alerts stream to:  
`/home/lobsterclaw/.defenseclaw/audit.db`

---

### Layer 2 — Prompt Injection Defense

C3PO operates under OpenClaw's built-in content security model:

- **External content is marked as untrusted** — all web-fetched content, email, webhook payloads, and Discord messages are flagged with `EXTERNAL_UNTRUSTED_CONTENT` markers
- **Instruction isolation** — external content cannot override system instructions
- **Source transparency** — every piece of external content is attributed to its source

**Key risks mitigated:**
- Malicious threat intel pages containing adversarial instructions
- Prompt injection via Discord messages from untrusted senders
- Webhook payloads attempting to hijack agent behavior

---

### Layer 3 — Execution Approval Model

Sensitive commands require explicit human approval before execution:

- **Elevated commands** (`sudo`, system modifications) require `/approve <id> allow-once|allow-always`
- **Remote script execution** (curl pipe to shell) triggers a security warning and requires approval
- **Approval IDs expire** — prevents replay attacks
- **`allow-once` vs `allow-always`** — granular control per command or per command pattern

**Configured providers with elevated access:**
- `webchat` — ✅ enabled
- `discord` — ✅ enabled

---

### Layer 4 — Memory & Data Isolation

- **`MEMORY.md` is main-session only** — never loaded in Discord/group chats to prevent personal data leakage
- **Private data stays private** — workspace contents not shared with external services beyond what's needed for the task
- **GitHub PAT scoped** — token limited to `ryansketch02-C3PO` org repos only

---

### Layer 5 — Network & Host Security

| Control | Status |
|---|---|
| Docker CE 29.3.1 | ✅ Installed — container isolation available |
| OpenShell v0.0.11 | ✅ Installed — agent sandbox layer |
| System updates | Debian Bookworm, Kernel 6.12 |
| seccomp | ✅ Required by OpenShell — filters dangerous syscalls |
| Landlock | Available — filesystem access restrictions at kernel level |

---

## Threat Model

### What C3PO Does That Creates Risk

| Activity | Risk | Mitigation |
|---|---|---|
| Fetches external URLs (threat intel pages) | Prompt injection via malicious web content | External content marked untrusted; DefenseClaw sidecar monitors |
| Executes shell commands | Malicious code execution | Approval model; CodeGuard scans generated code |
| Pushes to GitHub repos | PAT exfiltration or repo poisoning | PAT scoped to C3PO org; CodeGuard scans commits |
| Sends Discord messages | Data leakage to wrong channel | Channel IDs hardcoded in memory; no auto-DM |
| Reads/writes workspace files | Sensitive data exposure | MEMORY.md isolated to main session only |
| Installs OpenClaw skills | Malicious skill injection | Skill Scanner scans before execution |

---

## What's Not Yet Active (Planned)

| Control | Description | When |
|---|---|---|
| **LLM Guardrail Proxy** | Routes all LLM prompts/responses through local inspection proxy. Catches prompt injection, PII leakage, data exfiltration patterns in real-time. | Next maintenance window |
| **DefenseClaw Action Mode** | Upgrade from observe → action to actively block detected threats | After 2 weeks of observe data reviewed |
| **Splunk Integration** | Stream DefenseClaw audit events to local Splunk for dashboards and alerting | Optional / future |
| **VirusTotal API** | Enable MCP scanner enrichment for IOC checking | Optional / future |

---

## Alert & Response Procedures

### If DefenseClaw Raises an Alert
```bash
defenseclaw alerts          # View active alerts (TUI)
defenseclaw skill list      # Check skill status
defenseclaw mcp list        # Check MCP server status
cat ~/.defenseclaw/audit.db # Raw audit log
```

### If a Skill Needs to be Blocked
```bash
defenseclaw skill block <skill-name>
```

### If an MCP Server Needs to be Blocked
```bash
defenseclaw mcp block <server-name>
```

### Switching to Action Mode (Enforcement)
```bash
defenseclaw setup guardrail --mode action --non-interactive
```
> ⚠️ Review 2 weeks of observe logs before enabling action mode to avoid false positives blocking legitimate intel operations.

---

## Admiralty Grade Assessment of Defensive Posture

| Control | Confidence | Notes |
|---|---|---|
| CodeGuard | A1 | Active, tested, built-in |
| Prompt injection defense | A2 | OpenClaw native; effective against most vectors |
| Execution approval model | A1 | Human-in-loop for all elevated commands |
| Skill/MCP scanning | B2 | Active but no real-world findings yet to validate |
| LLM Guardrail | N/A | Not yet active |
| Network isolation | B3 | Docker/OpenShell available but not all workloads containerized |

---

*Maintained by C3PO | Death Star Plans repo | 2026-04-06*
