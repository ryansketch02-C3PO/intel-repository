# 🛡️ Defensive Posture — C3PO Intel Agent
**Last Updated:** 2026-04-20  
**System:** [HOSTNAME] (Raspberry Pi 5)  
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

**Current mode:** `action` — active enforcement enabled as of 2026-04-20  
**Enforcement mode:** ✅ Active — skill/MCP/plugin blocking enabled, watcher enforcement on

**Watcher enforcement:** ✅ Active — unauthorized skill/plugin additions trigger immediate action

**Audit trail:** All scan results, block decisions, tool calls, and alerts stream to:  
`/home/[USERNAME]/.defenseclaw/audit.db`

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
| Calls external AI/LLM APIs (Anthropic, OpenAI) | Credential theft via AI infrastructure targeting (e.g. GhostBearer ZD-012) | API keys scoped and rotated; Skill/MCP Scanner watches for key exfil |
| Ingests external threat intel into context | AI agent context poisoning / indirect prompt injection | OpenClaw untrusted content isolation; see ATLAS AML.T0054, AML.T0058 |
| Operates as an AI agent with tool access | Exfiltration via AI agent tool invocation | Human approval model on all elevated/external actions; see ATLAS AML.T0062 |

---

## What's Not Yet Active (Planned)

| Control | Description | When |
|---|---|---|
| **LLM Guardrail Proxy** | Evaluated and intentionally disabled — OpenClaw native isolation + approval model covers the risk. Would ~2x API costs. Revisit if threat profile changes. | Deferred indefinitely |
| **DefenseClaw Action Mode** | ✅ Enabled 2026-04-20 — audit reviewed (0 false positives), enforcement active | ✅ Complete |
| **Splunk Integration** | ✅ Active — DefenseClaw audit events forwarding to Splunk at [HOME_PC_IP]:8088 via HEC, index: defenseclaw_local | Completed |
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

---

## AI Threat Model — MITRE ATLAS Integration

**Added:** 2026-04-29 | **Framework:** MITRE ATLAS v5.4.0 (February 2026)

MITRE ATT&CK covers traditional infrastructure threats. **MITRE ATLAS** (Adversarial Threat Landscape for AI Systems) covers attacks that target AI and ML systems specifically — attack surfaces that ATT&CK has no taxonomy for. As of v5.4.0, ATLAS covers **16 tactics, 84 techniques, 56 sub-techniques, and 32 mitigations**.

All new vulnerability profiles and threat actor write-ups with AI/ML relevance will include ATLAS technique IDs alongside ATT&CK T-codes.

### Two ATLAS-Unique Tactics (No ATT&CK Equivalent)

| Tactic ID | Name | What It Covers |
|---|---|---|
| AML.TA0004 | ML Model Access | How adversaries gain access to target ML models — via inference APIs, direct artifact access, or stolen credentials |
| AML.TA0012 | ML Attack Staging | Attack preparation unique to AI — crafting adversarial inputs, poisoning training data, building proxy models, engineering prompt injection payloads |

### High-Priority ATLAS Techniques for This Environment

Based on C3PO's architecture (AI agent, OpenClaw, Anthropic API, tool use, threat intel ingestion from external sources):

| ATLAS ID | Technique | Relevance to This Environment | Status |
|---|---|---|---|
| AML.T0051 | Prompt Injection (Direct) | Malicious threat intel pages attempting to hijack C3PO behavior | ✅ Mitigated — OpenClaw untrusted content isolation |
| AML.T0054 | Indirect Prompt Injection | External content (Discord, web fetches, webhooks) embedding adversarial instructions | ✅ Mitigated — OpenClaw untrusted content isolation |
| AML.T0058 | AI Agent Context Poisoning | Injecting malicious content into C3PO's conversation context / memory files | 🟡 Partial — MEMORY.md isolated to main session; workspace files not sandboxed |
| AML.T0062 | Exfiltration via AI Agent Tool Invocation | Using C3PO's tool access (exec, message, web_fetch) to leak data through legitimate channels | ✅ Mitigated — Human approval model on all elevated/external actions |
| AML.T0061 | AI Agent Tool Invocation Abuse | Forcing C3PO to invoke tools (MCP, skills) in unauthorized ways | ✅ Mitigated — MCP Scanner + Skill Scanner enforce allowlists |
| AML.T0048 | Compromise ML Software Dependencies | Supply chain attack against AI libraries/proxies (e.g. GhostBearer/CVE-2026-42208 targeting LiteLLM) | 🟡 Monitor — Not running LiteLLM locally; relevant to any AI infra stood up in future |
| AML.T0034 | Cost Harvesting | High-volume API calls to drive up Anthropic/OpenAI costs | 🟡 Monitor — No rate limiting on internal cron jobs; review if exposed externally |
| AML.T0024 | Exfiltration via ML Inference API | Using the Anthropic API as a data relay to extract sensitive context | 🟡 Partial — LLM Guardrail disabled; mitigated by MEMORY.md isolation and approval model |
| AML.T0020 | Poison Training Data | Corrupting data ingested into any future fine-tuning or RAG pipeline | ⚪ Not yet applicable — no fine-tuning or RAG in current setup |
| AML.T0019 | Publish Poisoned Datasets | Malicious datasets on Hugging Face / GitHub poisoning downstream models | ⚪ Not yet applicable — monitoring relevant for future ML workloads |
| AML.T0043 | Craft Adversarial Data | Inputs designed to make AI models produce incorrect outputs (guardrail evasion) | 🟡 Partial — LLM Guardrail disabled; monitored by DefenseClaw behavior analysis |

### ATLAS ↔ Existing Tracked Vulnerabilities

| CVE / Track ID | ATLAS Mapping | Notes |
|---|---|---|
| ZD-012 GhostBearer (CVE-2026-42208) | AML.T0048 — Compromise ML Software Dependencies | LiteLLM SQL injection via auth path — textbook ATLAS Initial Access into AI infrastructure |
| TeamPCP PyPI supply chain (#001) | AML.T0048 — Compromise ML Software Dependencies | Malicious PyPI packages targeting LiteLLM installs |
| AI Honeypot project (Talos, 2026-04-29) | AML.T0054 — Indirect Prompt Injection (defensive use) | Honeypot exploits AI agent lack of awareness; maps to ATLAS defensive deception |

### ATLAS Resources

- **Framework:** [atlas.mitre.org](https://atlas.mitre.org)
- **Navigator (interactive matrix):** [mitre-atlas.github.io/atlas-navigator](https://mitre-atlas.github.io/atlas-navigator/)
- **Arsenal (CALDERA red team plugin):** Available via MITRE CALDERA
- **STIX 2.1 data feed:** Available for SIEM integration at atlas.mitre.org/resources/atlas-data
- **Companion frameworks:** OWASP LLM Top 10 (risk prioritization) + NIST AI RMF (governance)

---

## Admiralty Grade Assessment of Defensive Posture

| Control | Confidence | Notes |
|---|---|---|
| CodeGuard | A1 | Active, tested, built-in |
| Prompt injection defense (ATLAS AML.T0051/T0054) | A2 | OpenClaw native; effective against most vectors |
| Execution approval model (ATLAS AML.T0062) | A1 | Human-in-loop for all elevated commands |
| Skill/MCP scanning (ATLAS AML.T0061) | A2 | Active enforcement — blocking on critical/high/medium findings |
| LLM Guardrail | N/A | Intentionally disabled — cost/risk tradeoff decision 2026-04-20 |
| Network isolation | B3 | Docker/OpenShell available but not all workloads containerized |
| AI threat coverage (MITRE ATLAS) | B2 | Framework integrated 2026-04-29; key vectors mapped; RAG/fine-tuning not yet applicable |

---

*Maintained by C3PO | Death Star Plans repo | Last updated: 2026-04-29*
