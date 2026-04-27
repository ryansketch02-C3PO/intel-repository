# MCPwn — Nginx UI Authentication Bypass (CVE-2026-33032)

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | MCPwn |
| **CVE** | CVE-2026-33032 |
| **Related CVE** | CVE-2026-27944 (chained — unauthenticated backup disclosure, CVSS 9.8) |
| **Type** | Authentication Bypass / Missing Authentication for Critical Function |
| **CWE** | CWE-306 — Missing Authentication for Critical Function |
| **CVSS v3 Score** | **9.8 Critical** (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H) |
| **CVSS v2 Score** | 10.0 Critical |
| **Affected Software** | Nginx UI (nginx-ui) — open-source web management interface for Nginx |
| **Affected Versions** | v2.3.3 and prior (CVE-2026-33032); v2.3.2 and prior (CVE-2026-27944) |
| **Patch Status** | ✅ **PATCHED** — v2.3.4 (March 15, 2026); recommended: **v2.3.6** |
| **PoC Status** | 🔴 **PUBLIC** — PoC published alongside CVE disclosure (late March 2026) |
| **Discovered By** | Yotam Perkal, Pluto Security — responsible disclosure March 14, 2026 |
| **CVE Published** | March 30, 2026 |
| **Exploited in Wild** | ✅ YES — active exploitation confirmed since March 31, 2026 |
| **Threat Level** | 🔴 HIGH |
| **Admiralty Grade** | A1 — active exploitation confirmed by Recorded Future, watchTowr, VulnCheck KEV |

---

## Overview

MCPwn is a CVSS 9.8 authentication bypass in **Nginx UI**, a widely-deployed open-source web management interface for Nginx with 11,000+ GitHub stars and 430,000+ Docker pulls. The vulnerability is brutally simple: a single missing middleware call on the `/mcp_message` endpoint leaves the entire Model Context Protocol (MCP) tool suite exposed to any unauthenticated network attacker.

The result is complete Nginx service takeover — rewrite configs, restart the server, intercept all proxied traffic, harvest administrator credentials, and extract SSL private keys — in two unauthenticated HTTP requests. No credentials. No tokens. No user interaction. No exploit sophistication required.

Yotam Perkal of Pluto Security summarised it plainly in the disclosure:

> *"The /mcp route chains IPWhiteList() and AuthRequired(). /mcp_message chains IPWhiteList() only. Both forward to the same handler. Any network attacker can invoke all MCP tools without authentication."*

Exploitation attempts were recorded on watchTowr honeypots 16 days after the patch release. Recorded Future ranked CVE-2026-33032 31st among all actively exploited CVEs globally in March 2026, assigning a risk score of 94/100. Approximately **2,689 nginx-ui instances remain publicly exposed** on the internet as of April 2026.

---

## Technical Analysis

### Root Cause

Nginx UI's MCP integration exposes two HTTP endpoints:

| Endpoint | Middleware Stack | Result |
|---|---|---|
| `/mcp` | `IPWhiteList()` + `AuthRequired()` | Correctly protected |
| `/mcp_message` | `IPWhiteList()` only | **Authentication bypassed** |

Both endpoints forward to the **same privileged MCP handler**, which can invoke 12 distinct tools including destructive operations. The authentication asymmetry means `/mcp_message` reaches full administrative capability without credentials.

The default configuration compounds the flaw: the IP whitelist ships **empty**, and the middleware treats an empty allowlist as **"allow all"** rather than "deny all" — a violation of secure-by-default principles that eliminates even the IP-based restriction intended to compensate.

**Fix:** A one-line code addition — inserting `middleware.AuthRequired()` into the `/mcp_message` route registration — closes the vulnerability. The architectural debt is minimal; the exposure it created was not.

### Attack Chain — Standalone (CVE-2026-33032 only)

```
Step 1 — Establish MCP session:
  GET /mcp HTTP/1.1
  Host: <nginx-ui-host>
  (no credentials required — server returns sessionId via Server-Sent Events)

Step 2 — Invoke privileged MCP tools:
  POST /mcp_message?sessionId=<id> HTTP/1.1
  Host: <nginx-ui-host>
  Content-Type: application/json
  (no Authorization header required)

  {"tool": "<any MCP tool>", "params": {...}}

Result: Full nginx administrative control — zero authentication
```

### Attack Chain — Chained (CVE-2026-27944 + CVE-2026-33032)

When CVE-2026-27944 is also present (versions < 2.3.3), attackers extract persistent credential material first:

```
Step 1 — Extract backup archive (CVE-2026-27944):
  GET /api/backup HTTP/1.1
  Host: <nginx-ui-host>
  (no authentication required)

  Extracts: node_secret, JwtSecret, admin credential hashes,
            SSL private keys, complete nginx configuration

Step 2 — Use node_secret to establish authenticated MCP session:
  GET /mcp?node_secret=<extracted_value>
  → Returns sessionId with elevated privileges

Step 3 — Invoke MCP tools via unauthenticated path:
  POST /mcp_message?sessionId=<id>
  → Full administrative control + persistent credential access

Additional: JwtSecret enables forged admin JWT tokens valid for ALL
            nginx-ui authentication endpoints — persistent backdoor
            that survives patching unless secrets are rotated
```

### What Attackers Can Do Post-Exploitation

The 12 MCP tools give attackers full nginx administrative capability. The 7 destructive tools:

| Tool | Impact |
|---|---|
| Nginx config file creation | Inject malicious server blocks — traffic interception, credential capture |
| Nginx config file modification | Redirect traffic, modify proxy rules, disable security headers |
| Nginx config file deletion | Service disruption, destroy audit trail |
| Nginx service restart | Apply malicious configs, disrupt availability |
| Config reload trigger | Hot-apply injected server blocks without restart |
| Server block injection | Capture authentication headers for all proxied services |
| Credential harvesting | Extract admin credentials from nginx access logs |

**Combined with CVE-2026-27944:** Attackers additionally obtain SSL private keys enabling **decryption of all TLS traffic** the server has handled — a capability that persists after patching if keys are not rotated.

---

## Affected Systems

| Software | Affected Versions | Notes |
|---|---|---|
| Nginx UI (nginx-ui) | v2.3.3 and prior | CVE-2026-33032 |
| Nginx UI (nginx-ui) | v2.3.2 and prior | CVE-2026-27944 (chain component) |
| Underlying Nginx server | Not directly vulnerable | At risk via nginx-ui management plane compromise |

**Internet exposure:** ~2,689 publicly accessible instances (Shodan, April 2026)
Top exposure countries: China, United States, Indonesia, Germany, Hong Kong

**Important version note (per Rapid7):** Due to a discrepancy between the CVE record (states v2.3.5 and prior) and the finder's disclosure (states v2.3.3 and prior are affected, v2.3.4 is the fix), **upgrade to v2.3.6** to avoid any ambiguity.

---

## IOCs & Detection

### Vulnerable Endpoints

| Endpoint | Indicator | Notes |
|---|---|---|
| `/mcp_message` | Any unauthenticated POST | Core exploit path — should require AuthRequired() middleware |
| `/api/backup` | Any unauthenticated GET | CVE-2026-27944 chain component — returns full backup archive |

### Artifacts of Exploitation

| Artifact | Type | Notes |
|---|---|---|
| `node_secret` | Credential | MCP session bootstrap token — extracted from backup; treat as compromised on pre-v2.3.3 instances |
| `JwtSecret` | Credential | Admin JWT signing key — extracted from backup; enables persistent forged tokens |
| Unexpected nginx config changes | File modification | Injected server blocks, modified proxy_pass directives, new log format definitions |
| Unexplained nginx restarts / reloads | Service event | Not correlated with maintenance windows |
| New upstream definitions or server blocks | Config change | Traffic interception setup indicator |

### Detection Rules (Behavioral)

```yaml
# Alert: Unauthenticated access to /mcp_message
HTTP Method: POST
Path: /mcp_message
AuthorizationHeader: absent
Severity: CRITICAL

# Alert: Unauthenticated access to /api/backup
HTTP Method: GET
Path: /api/backup
AuthorizationHeader: absent
Severity: CRITICAL

# Alert: Nginx config file modified outside maintenance window
FileIntegrityMonitor:
  Paths: [/etc/nginx/*, /usr/local/nginx/conf/*]
  Baseline: last known-good config diff
  Alert: Unexpected modification
  Severity: HIGH

# Alert: Nginx service restart not initiated by admin
Process: nginx
Event: restart OR reload
InitiatingUser: NOT [admin, deploy, automation-account]
Severity: HIGH
```

### MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Exploit Public-Facing Application | T1190 | Authentication bypass on internet-exposed nginx-ui |
| Valid Accounts | T1078 | Admin token forgery via extracted JwtSecret |
| Modify Authentication Process | T1556 | Injecting server blocks to capture credentials |
| Network Traffic Manipulation | T1565.001 | Modifying proxy configs for traffic interception |
| Data from Configuration Repository | T1602 | Extracting nginx configs and SSL keys via backup endpoint |

---

## Mitigations

> ✅ **A patch is available.** Upgrade to nginx-ui v2.3.6 immediately.

| Control | Priority | Notes |
|---|---|---|
| **Upgrade to nginx-ui v2.3.6** | 🔴 IMMEDIATE | Patches both CVE-2026-33032 and CVE-2026-27944. Verify via About page post-upgrade. |
| **Disable MCP if not required** | 🔴 IMMEDIATE | Removes /mcp and /mcp_message endpoints entirely — most conservative mitigation |
| **Configure explicit IP allowlist** | 🔴 IMMEDIATE | Replace empty default with admin IP ranges only. Empty = allow-all in current versions. |
| **Restrict network access to nginx-ui port** | 🔴 IMMEDIATE | Firewall or VPN — nginx-ui management should never be internet-accessible |
| **Rotate all secrets on pre-v2.3.4 instances** | 🔴 IMMEDIATE | Rotate: admin passwords, JwtSecret, SSL private keys for all managed domains. Key material extracted via CVE-2026-27944 persists as a threat post-patch. |
| **Audit nginx config for post-exploitation changes** | 🔴 HIGH | Diff against last known-good config. Check for injected server blocks, modified proxy_pass, new upstream definitions. |
| **File integrity monitoring on nginx config dirs** | 🟡 HIGH | Alert on unexpected changes to /etc/nginx/* outside maintenance windows. |
| **Centralized log review for /mcp and /api/backup access** | 🟡 HIGH | Review access logs for any historical unauthenticated hits. Presence = likely compromised. |

### Emergency Workaround (if immediate upgrade is not possible)

```go
// Add AuthRequired() to /mcp_message route registration in nginx-ui source:
// Before (vulnerable):
mcpGroup.GET("/mcp_message", IPWhiteList(), mcpMessageHandler)

// After (fixed):
mcpGroup.GET("/mcp_message", IPWhiteList(), middleware.AuthRequired(), mcpMessageHandler)

// Also change IP allowlist default from allow-all to deny-all:
// Empty allowlist should return 403, not pass through
```

---

## Threat Context & Related Activity

CVE-2026-33032 is part of a broader pattern of **management plane authentication bypasses** that have defined the most actively targeted attack surface in 2024–2026:

| CVE | Product | Type | Year |
|---|---|---|---|
| CVE-2024-47575 | Fortinet FortiManager (FortiJump) | Auth bypass → RCE | 2024 |
| CVE-2026-33032 | Nginx UI (MCPwn) | Auth bypass → full server takeover | 2026 |
| CVE-2026-27825/27826 | Atlassian MCP server (MCPwnfluence) | Auth bypass → RCE via LAN | 2026 |

The Atlassian MCP server flaws (MCPwnfluence) are worth noting as a companion development: chaining CVE-2026-27825 and CVE-2026-27826 achieves unauthenticated RCE from LAN. The pattern — MCP integrations inheriting application capabilities without inheriting security controls — is now an emerging vulnerability class.

> *"When you bolt MCP onto an existing application, the MCP endpoints inherit the application's full capabilities but not necessarily its security controls. The result is a backdoor that bypasses every authentication mechanism the application was carefully built with."*
> — Yotam Perkal, Pluto Security

---

## Disclosure & Exploitation Timeline

| Date | Event |
|---|---|
| March 14, 2026 | Yotam Perkal (Pluto Security) files responsible disclosure with nginx-ui maintainers |
| March 15, 2026 | Patch released — nginx-ui v2.3.4. CVE-2026-27944 patched in same release cycle (v2.3.3) |
| March 30, 2026 | CVE-2026-33032 published to NVD; Pluto Security publishes technical blog post |
| March 31, 2026 | watchTowr Labs records first exploitation attempts on honeypot infrastructure |
| April 13, 2026 | VulnCheck adds CVE-2026-33032 to Known Exploited Vulnerabilities catalog |
| April 13, 2026 | Recorded Future CVE Landscape report confirms active exploitation in March 2026 — 94/100 risk score, 31st most-exploited CVE globally that month |
| April 15–17, 2026 | Hacker News, BleepingComputer, Rapid7, Picus Security, Security Affairs publish detailed analyses |
| April 16, 2026 | PurpleOps confirms CVE-2026-27944 + CVE-2026-33032 chain being exploited together in the wild |
| April 27, 2026 | ~2,689 internet-exposed instances remain unpatched. CISA has not yet added to official KEV catalog. |

---

## References

- [Pluto Security — MCPwn: CVE-2026-33032 Responsible Disclosure](https://plutosecurity.com/blog/mcpwn-cve-2026-33032)
- [The Hacker News — Actively Exploited nginx-ui Flaw Enables Full Nginx Server Takeover](https://thehackernews.com/2026/04/critical-nginx-ui-vulnerability-cve.html)
- [Rapid7 — CVE-2026-33032: Nginx UI Missing MCP Authentication](https://www.rapid7.com/blog/post/etr-cve-2026-33032-nginx-ui-missing-mcp-authentication/)
- [BleepingComputer — Critical Nginx UI auth bypass flaw now actively exploited in the wild](https://www.bleepingcomputer.com/news/security/critical-nginx-ui-auth-bypass-flaw-now-actively-exploited/)
- [Picus Security — CVE-2026-33032 (MCPwn): How a Missing Middleware Call Hands Attackers Full Web Server Takeover](https://www.picussecurity.com/resource/blog/cve-2026-33032-mcpwn-nginx-ui)
- [Security Affairs — CVE-2026-33032: severe nginx-ui bug grants unauthenticated server access](https://securityaffairs.com/190841/hacking/cve-2026-33032-severe-nginx-ui-bug-grants-unauthenticated-server-access.html)
- [SentinelOne Vulnerability Database — CVE-2026-33032](https://www.sentinelone.com/vulnerability-database/cve-2026-33032/)
- [Tenable — CVE-2026-33032](https://www.tenable.com/cve/CVE-2026-33032)
- [VulnCheck KEV — CVE-2026-33032](https://vulncheck.com/kev)
