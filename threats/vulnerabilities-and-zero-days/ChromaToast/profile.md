# CVE-2026-45829 — ChromaToast: Pre-Auth RCE in ChromaDB Python Server (CVSS 10.0)

## Identity

| Field | Details |
|---|---|
| **CVE** | CVE-2026-45829 |
| **Alias** | ChromaToast |
| **Vendor Advisory** | None — no official security advisory from ChromaDB maintainers as of May 20, 2026 |
| **Type** | Pre-Authentication Remote Code Execution / Logic Error in Auth Ordering |
| **Class** | CWE-269 — Improper Privilege Management; CWE-287 — Improper Authentication |
| **Affected Products** | ChromaDB Python FastAPI API server, versions 1.0.0 through 1.5.8 |
| **Not Affected** | ChromaDB Rust-based frontend; local-only (non-API-server) deployments |
| **Patch Status** | 🔴 **UNPATCHED** — No official patch or security advisory; v1.5.9 may address the issue but is unconfirmed; maintainers have not responded |
| **CVSS** | **10.0 (Critical)** |
| **Exploit Maturity** | ⚠️ **DISCLOSED** — Vulnerability details public; no weaponized public PoC confirmed but the attack path is trivially reconstructable |
| **CISA KEV** | Not listed — no active exploitation confirmed as of May 20, 2026 |
| **Exposed Instances** | ~73% of internet-facing ChromaDB instances running vulnerable versions (HiddenLayer telemetry, May 2026) |
| **Disclosed** | May 19, 2026 (reported to maintainers February 2026 by HiddenLayer) |
| **Threat Level** | 🔴 CRITICAL — CVSS 10.0; unauthenticated; full server takeover; AI/ML pipeline targeting; vector database = API keys + embeddings + raw data |
| **Admiralty Grade** | B2 — Reported by credible security firm (HiddenLayer); no confirmation from vendor |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1059 (Command and Scripting Interpreter) · T1552 (Unsecured Credentials) · T1005 (Data from Local System) |

---

## Overview

CVE-2026-45829 ("ChromaToast") is a pre-authentication remote code execution vulnerability in the Python FastAPI server of ChromaDB — a widely-used vector database for AI/RAG (Retrieval Augmented Generation) applications.

The vulnerability stems from a fundamental logic error: **authentication is checked after client-supplied configuration is processed**, not before. This means an unauthenticated attacker can dictate model-loading parameters — including the `trust_remote_code` flag — before the server ever validates credentials.

### Why It Matters for AI Environments

ChromaDB is the default vector store in thousands of AI and RAG pipelines. Successful exploitation against an exposed instance yields:
- Full server takeover with the privileges of the ChromaDB process
- Exfiltration of API keys, environment variables, mounted secrets
- Access to the underlying vector store (embeddings, documents, metadata)
- Pivot point for broader network intrusion into AI/ML infrastructure

The attack completes silently — the server returns a 500 error after the payload executes (the delayed auth check fails), hiding the successful compromise behind normal application noise.

---

## Technical Analysis

### Root Cause

The `/api/v2/tenants/{tenant}/databases/{db}/collections` endpoint processes incoming JSON before verifying the caller's identity. Within the configuration payload, clients can set `trust_remote_code=True` and supply a Hugging Face repository path as the embedding model source.

The server fetches and **executes the model's initialization code** before the auth check runs. Any code in the model's `__init__` executes with the server's process privileges.

### Attack Flow

1. Attacker stages a malicious model on a public or attacker-controlled repository
2. Attacker sends unauthenticated POST to collection creation endpoint
3. Payload includes `trust_remote_code: true` + attacker's model path in `kwargs`
4. Server fetches and initializes model — malicious `__init__` code executes
5. Server encounters delayed auth failure → returns HTTP 500 (attack already complete)

### Affected Scope

- All Python FastAPI ChromaDB server deployments 1.0.0–1.5.8
- Any instance with the API port exposed to untrusted networks
- Cloud deployments, SaaS wrappers, CI/CD test environments with ChromaDB

---

## Mitigations

**Immediate:**
- Firewall the ChromaDB API port — it should never be publicly accessible
- Restrict access to trusted internal clients only via network security groups
- Audit environment variables and secrets mounted in ChromaDB containers

**Architectural:**
- Migrate to Rust-based ChromaDB frontend (not affected)
- Move to local-only deployments that do not expose the API server

**If building from source:**
- Move authentication verification to occur *before* any configuration loading
- Strip `kwargs` from incoming collection creation payloads to block parameter injection

---

*Added: 2026-05-20 | Reporter: HiddenLayer (February 2026) | No vendor patch confirmation*
