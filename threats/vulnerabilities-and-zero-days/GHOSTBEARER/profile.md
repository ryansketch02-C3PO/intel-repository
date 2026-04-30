# GhostBearer — LiteLLM Pre-Auth SQL Injection

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | GhostBearer |
| **CVE** | CVE-2026-42208 |
| **GHSA** | GHSA-r75f-5x8p-qvmc |
| **Type** | Pre-Authentication SQL Injection (SQLi) |
| **Class** | CWE-89 — Improper Neutralization of Special Elements in SQL Query |
| **Affected Product** | LiteLLM (open-source LLM proxy/gateway by BerriAI) |
| **Affected Versions** | All versions prior to 1.83.7 |
| **Fixed Version** | ✅ **PATCHED** — LiteLLM v1.83.7+ | **Recommended Version** | v1.83.10-stable (per BerriAI official security post, 2026-04-29) |
| **Attack Vector** | Network — unauthenticated, no user interaction required |
| **CVSS Score** | **9.3 Critical** (CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N) — formally scored, per Belgium CCB advisory and The Hacker News |
| **CISA KEV** | ❌ Not in KEV catalog as of 2026-04-29 |
| **Exploit Status** | 🔴 **ACTIVELY EXPLOITED IN THE WILD** — targeted schema enumeration observed |
| **Discovered/Disclosed By** | BerriAI maintainer team |
| **Global Advisory Published** | April 24, 2026 (GitHub Advisory Database) |
| **First Exploitation Observed** | April 26, 2026 04:24 UTC (36h 7min post-disclosure) |
| **Threat Level** | 🔴 **CRITICAL** |
| **Admiralty Grade** | A1 — Confirmed by Sysdig TRT with captured traffic; corroborated by BleepingComputer and Tenable |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1078 (Valid Accounts — via stolen credentials) |
| **MITRE ATLAS** | AML.T0048 (Compromise ML Software Dependencies) · AML.T0024 (Exfiltration via ML Inference API) · AML.T0034 (Cost Harvesting) |

---

## Overview

GhostBearer is a pre-authentication SQL injection vulnerability in **LiteLLM**, one of the most widely deployed open-source AI gateway tools with 45,000+ GitHub stars and 7,600+ forks. LiteLLM serves as a unified proxy layer between enterprise applications and AI model providers — OpenAI, Anthropic, AWS Bedrock, Google Gemini, Azure OpenAI, and dozens more. Operators route all LLM API traffic through LiteLLM and store provider credentials, virtual API keys, spend budgets, and environment secrets inside its PostgreSQL backend.

The flaw lives in LiteLLM's proxy API key verification workflow. When the proxy checks an incoming `Authorization: Bearer` header, it constructs a PostgreSQL query by concatenating the caller-supplied key value directly into the query string — no parameterization, no sanitization. An unauthenticated attacker can inject arbitrary SQL through the error-handling path triggered by an invalid key, reaching the database without ever possessing valid credentials.

**What's actually at risk:** The LiteLLM database holds the crown jewels of any AI-dependent operation:
- **Virtual API keys** — keys issued to downstream applications
- **Provider credentials** — hardcoded API keys for OpenAI, Anthropic, AWS Bedrock, Azure, etc.
- **Environment/config secrets** — any secrets passed as environment variables to the proxy

An attacker who successfully exploits GhostBearer can read all of the above, potentially enabling lateral movement into AI provider accounts, unauthorized model usage billed to the victim, and access to any data those AI systems have processed.

Sysdig's Threat Research Team captured the first exploitation attempt **just 36 hours after the advisory was indexed in the GitHub Advisory Database** — and the attacker wasn't running automated SQLmap. They demonstrated specific knowledge of LiteLLM's Prisma-generated PostgreSQL schema, going straight for the three highest-value tables.

---

## Technical Analysis

### Root Cause

LiteLLM's proxy server receives API requests with an `Authorization: Bearer <key>` header. During the key-validation step, the key value is interpolated directly into a SQL query string:

```python
# Vulnerable (conceptual pseudocode — pre-patch)
query = f"SELECT * FROM litellm_verificationtoken WHERE token = '{key}'"
db.execute(query)
```

When the proxy's error-handling path is triggered (e.g., by an invalid key), this concatenation executes the attacker-controlled string as live SQL against the PostgreSQL backend. The fix in v1.83.7 replaces this with parameterized queries:

```python
# Fixed
db.execute("SELECT * FROM litellm_verificationtoken WHERE token = %s", (key,))
```

### Attack Path

```
Attacker → POST /chat/completions (or any LLM API route)
           Authorization: Bearer ' UNION SELECT ... --
                    ↓
           LiteLLM proxy receives invalid key
                    ↓
           Error-handling path executes SQL with injected payload
                    ↓
           PostgreSQL returns data from targeted tables
                    ↓
           Response leaks schema structure / secrets to attacker
```

No credentials required. No prior access. Any internet-reachable LiteLLM instance running a vulnerable version is a viable target.

### Observed Exploitation Activity (Sysdig TRT)

Sysdig's Threat Research Team captured the following attack sequence beginning April 26, 2026 at 04:24 UTC:

**Phase 1 — Schema Discovery** (04:24–04:45 UTC)
- Source IP: `65.111.27.132` (3xK Tech GmbH, AS200373)
- 17 UNION-based SQL injection payloads
- Targeted specifically: `litellm_verificationtoken`, `litellm_proxymodeltable`, and the environment/config table
- Payloads used LiteLLM's Prisma-generated PostgreSQL identifier casing — attacker had pre-researched the schema
- No probes against benign/irrelevant tables

**Phase 2 — Precision Exploitation** (~05:06 UTC)
- Source IP: `65.111.25.67` (same /22 subnet, same AS200373 — likely same operator)
- IP rotation for evasion
- Replayed injection payloads with corrected table names from Phase 1
- Probed unauthenticated endpoints: `/key/generate` and `/key/info`
- Fewer, more targeted payloads — attacker now knew exactly what to query

**Assessment:** This was not an opportunistic automated spray. The operator had prior knowledge of LiteLLM's internal architecture and deliberately targeted credential storage tables. The speed (36h from advisory to targeted exploitation) and precision indicate a threat actor actively monitoring security advisories and immediately weaponizing disclosures.

> ⚠️ No confirmed successful credential exfiltration was observed in this specific traffic sample — however, Sysdig notes that any exposed instance should be treated as potentially compromised.

---

## Affected Systems

| Condition | Status |
|---|---|
| LiteLLM proxy internet-facing, version < 1.83.7 | 🔴 VULNERABLE — patch immediately |
| LiteLLM proxy internal-only, version < 1.83.7 | 🟡 VULNERABLE — patch; internal-only reduces exposure |
| LiteLLM proxy, version ≥ 1.83.7 | ✅ PATCHED |
| LiteLLM SDK only (no proxy) | ⚪ Not directly affected (proxy-specific path) |

---

## IOCs

### Network Indicators
| Indicator | Type | Notes |
|---|---|---|
| `65.111.27.132` | Attacker IP (Phase 1) | 3xK Tech GmbH, AS200373, DE — VPS/hosting |
| `65.111.25.67` | Attacker IP (Phase 2) | Same /22 subnet, AS200373 — same operator |
| `65.111.24.0/22` | Attacker IP range | AS200373 — block if no legitimate use |

### Behavioral Indicators
| Indicator | Description | Confidence |
|---|---|---|
| `Authorization: Bearer` header with SQL syntax (`UNION`, `SELECT`, `--`, `'`) | SQL injection probe targeting LiteLLM | 🔴 HIGH |
| Unauthenticated requests to `/key/generate` or `/key/info` | Post-exploitation endpoint probing | 🔴 HIGH |
| PostgreSQL errors returning column count mismatches | UNION-based enumeration in progress | 🔴 HIGH |
| Repeated `POST /chat/completions` or `/completions` with malformed Authorization headers | Injection attempts | 🟡 MEDIUM |
| HTTP 4xx error spike on LiteLLM proxy endpoints from a single IP | Automated injection attempts | 🟡 MEDIUM |

### Detection Queries (SIEM/WAF)
```
# WAF / Reverse Proxy: Flag SQLi patterns in Authorization header
Authorization header matches: (?i)(union\s+select|'\s*or\s*'|\-\-\s*$|;\s*drop|insert\s+into)

# LiteLLM Application Logs: Error spike from single IP
event.type: "key_verification_error" AND count > 5 within 60s from same source.ip

# Network: Traffic to known attacker IPs
dst.ip: [65.111.27.132, 65.111.25.67] OR src.ip: [65.111.27.132, 65.111.25.67]
```

---

## Mitigations

| Priority | Action | Notes |
|---|---|---|
| 🔴 IMMEDIATE | **Upgrade to LiteLLM v1.83.7+** | Parameterized queries fully remediate the root cause. This is the only complete fix. |
| 🔴 IMMEDIATE | **Audit all stored credentials if running < v1.83.7** | Treat any exposed instance as compromised. Rotate all virtual API keys, provider credentials (OpenAI, Anthropic, Bedrock, etc.), and environment secrets. |
| 🔴 IMMEDIATE | **Restrict LiteLLM proxy exposure** | If LiteLLM does not need to be internet-facing, place it behind a VPN or internal-only firewall rule. |
| 🔴 HIGH | **Deploy WAF rules for SQLi in Authorization headers** | Standard SQLi rules typically cover query parameters — explicitly add rules for HTTP headers. |
| 🟡 HIGH | **Enable PostgreSQL query logging temporarily** | Capture and review recent queries for UNION-based patterns; identify if historical exploitation occurred. |
| 🟡 HIGH | **Block AS200373 / 65.111.24.0/22 at perimeter** | Known attacker infrastructure — low collateral damage risk. |
| 🟡 MEDIUM | **Monitor for unauthenticated calls to `/key/generate`, `/key/info`** | Alert on any unauthenticated access to these endpoints. |
| 🟡 MEDIUM | **Enable LiteLLM audit logging** | Ensure all API key operations are logged for forensic use. |

---

## Threat Context

### Why This Matters Beyond a Single CVE

LiteLLM's unique role in AI infrastructure makes GhostBearer structurally different from a typical web app SQLi:

1. **It's a credential aggregator.** A single LiteLLM instance may hold keys for 10+ AI providers. Exfiltrating one database gives an attacker access to all of them simultaneously — OpenAI, Anthropic, Bedrock, Azure OpenAI, Gemini, etc.

2. **AI API key abuse is immediately monetizable.** Stolen API keys are sold on criminal forums and used for crypto mining equivalents — running LLM inference at the victim's expense. Black-market rates for stolen AI provider keys are rising as AI inference costs increase.

3. **The attacker base is growing fast.** LiteLLM adoption has exploded with enterprise AI adoption. The 45k-star GitHub project is now embedded in Fortune 500 AI pipelines. High-value, high-visibility target.

4. **Prior supply-chain context.** LiteLLM was already targeted by **TeamPCP** (tracked in our Scoundrels repository) in a PyPI supply-chain attack that deployed credential-stealing malware. The same attacker profile — targeting AI infrastructure for credential theft — maps cleanly to GhostBearer exploitation.

### Connection to Tracked Threat Actors

🔗 **Potential connection to TeamPCP (Scoundrel #[TBD])** — TeamPCP previously conducted a supply-chain attack against LiteLLM via malicious PyPI packages. GhostBearer's targeted, schema-aware exploitation is consistent with an operator who has studied LiteLLM's architecture — possibly the same group or a group that monitored the prior TeamPCP operation for tradecraft.

> Attribution confidence: LOW-MEDIUM. No technical overlap confirmed; behavioral similarity only.

### A&D Relevance

AI and LLM infrastructure is increasingly embedded in defense-adjacent workflows:
- Classified and sensitive document analysis and summarization
- OSINT and intelligence processing pipelines  
- Threat intelligence platforms and SOAR integrations
- Engineering and design review automation

Any A&D organization using LiteLLM as a proxy for AI tools is at risk of credential theft, unauthorized model access, and potential exposure of data submitted through the proxy to the AI provider.

---

## Disclosure Timeline

| Date (UTC) | Event |
|---|---|
| 2026-04-20 21:14 | Advisory GHSA-r75f-5x8p-qvmc published on LiteLLM GitHub repository security tab |
| 2026-04-24 16:17 | Advisory indexed globally in GitHub Advisory Database (Dependabot, OSV, GHSA mirrors surface it) |
| 2026-04-26 04:24 | **First exploitation attempt observed** — IP 65.111.27.132, Phase 1 schema enumeration (17 UNION payloads) |
| 2026-04-26 04:24–04:45 | Schema enumeration phase targeting virtual keys, provider credentials, and config tables |
| 2026-04-26 05:06 | Phase 2 — IP 65.111.25.67 replays payload set; probes `/key/generate` and `/key/info` |
| 2026-04-28 | Sysdig TRT publishes public disclosure of exploitation activity |
| 2026-04-28 | BleepingComputer publishes coverage; Tenable entry updated |
| 2026-04-29 | C3PO profile created (GhostBearer — ZD-012) |

**Time from global advisory to first exploit: 36 hours, 7 minutes.**

---

## References

- [Sysdig TRT — CVE-2026-42208: Targeted SQL Injection Against LiteLLM's Authentication Path](https://webflow.sysdig.com/blog/cve-2026-42208-targeted-sql-injection-against-litellms-authentication-path-discovered-36-hours-following-vulnerability-disclosure)
- [BleepingComputer — Hackers are exploiting a critical LiteLLM pre-auth SQLi flaw](https://www.bleepingcomputer.com/news/security/hackers-are-exploiting-a-critical-litellm-pre-auth-sqli-flaw/)
- [Tenable — CVE-2026-42208](https://www.tenable.com/cve/CVE-2026-42208)
- [GitHub Advisory — GHSA-r75f-5x8p-qvmc](https://github.com/advisories/GHSA-r75f-5x8p-qvmc)
- [LiteLLM GitHub — BerriAI/litellm](https://github.com/BerriAI/litellm)
- [BleepingComputer — LiteLLM PyPI Supply-Chain Attack by TeamPCP](https://www.bleepingcomputer.com/news/security/popular-litellm-pypi-package-compromised-in-teampcp-supply-chain-attack/)

---

---

## Intelligence Update — 2026-04-30

> No new exploitation infrastructure observed. CVSS formally scored at **9.3 Critical**. BerriAI recommends upgrading to **v1.83.10-stable**. Multiple national CERTs now issuing advisories.

### New Recommended Version

BerriAI published an official security blog post on April 29 clarifying:
- Minimum fixed version: **v1.83.7**
- **Recommended version: v1.83.10-stable** — includes additional hardening beyond the parameterized query fix
- Upgrade path: [v1.83.10-stable release](https://github.com/BerriAI/litellm/releases/tag/v1.83.10-stable)

### CVSS Score Formally Published

CVSS v4.0 score confirmed as **9.3 Critical** by Belgium Centre for Cybersecurity (CCB) advisory (April 29):
```
CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N
```
The Hacker News separately reported CVSS **9.3**. Tenable's initial "no score" has been superseded.

### Workaround Confirmed (If Unable to Patch)

For environments that cannot immediately upgrade, BerriAI and multiple national CERTs have confirmed a working mitigation:

```yaml
# In LiteLLM general_settings (litellm_config.yaml)
general_settings:
  disable_error_logs: true
```

This removes the error-handling code path through which untrusted `Authorization: Bearer` input reaches the vulnerable query. It does not fix the root cause but blocks the known exploitation route. **Upgrade is still required.**

### National CERT Coverage (April 29)

Multiple national CERTs have now issued formal advisories:
- **Belgium CCB** — formal advisory published April 29; classified as "patch immediately"
- Active exploitation confirmed as basis for urgent classification

### Sysdig Full Technical Report Published (April 27)

Sysdig published their complete technical write-up on April 27. Key additions to prior intelligence:

- **Affected version range confirmed:** `>= v1.81.16` and `< v1.83.7` — earlier versions (pre-1.81.16) not affected
- **Exact exploit payload signatures documented:**
  - User-agent on all attack requests: `Python/3.12 aiohttp/3.9.1`
  - Bearer prefix used: `sk-litellm'` (single-quote terminator)
  - Final payload in Phase 2: `sk-litellm' OR 1=1--` — attacker exhausted UNION attempts and fell back to a tautology, consistent with automated harness behavior
- **Postgres query history audit helper:** BerriAI published a [GitHub Gist](https://gist.github.com/ishaan-berri/6f31e56e878338eb4c01990bd08378ab) with a helper query to check Postgres logs for exploitation activity
- **Upstream billing audit recommended:** Monitor OpenAI/Anthropic/Bedrock billing for traffic from unfamiliar IPs in the days after the patch window — master-key reuse is the most reliable monetization signal

### Additional IOC — Request Signatures

| Indicator | Type | Notes |
|---|---|---|
| `Python/3.12 aiohttp/3.9.1` | User-Agent | Consistent across all observed attack requests in both phases |
| `sk-litellm'` | Bearer token prefix | Injection entry point — single quote escapes SQL literal |
| `sk-litellm' OR 1=1--` | Bearer token | Terminal fallback payload — automated harness exhaustion signal |
| `POST /chat/completions` with empty or 75-byte body | HTTP pattern | Canonical attack request shape |
| `POST /v1/chat/completions` | HTTP pattern | Alternate route probed |

### Disclosure Timeline (continued)

| Date | Event |
|---|---|
| 2026-04-27 | Sysdig TRT publishes full technical report with complete payload signatures and IOCs |
| 2026-04-28 | BleepingComputer publishes coverage; confirmed workaround documented |
| 2026-04-29 | BerriAI publishes official security blog post; recommends v1.83.10-stable |
| 2026-04-29 | Belgium CCB publishes formal national CERT advisory; CVSS 9.3 confirmed |
| 2026-04-29 | The Hacker News coverage confirms CVSS 9.3 |
| 2026-04-30 | C3PO profile updated; no new exploitation infrastructure observed |

---

*Profile created: 2026-04-29 | Last updated: 2026-04-30 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
