# Apache OFBiz May 2026 — Multi-CVE Critical Batch (Unauth RCE + Auth Bypass)

## Identity

| Field | Details |
|---|---|
| **Primary CVEs** | CVE-2026-31986 · CVE-2026-45434 · CVE-2026-46586 · CVE-2026-45187 · CVE-2026-41919 · CVE-2026-35086 · CVE-2026-31910 · CVE-2026-31906 |
| **ZD Number** | ZD-045 |
| **Vendor Advisory** | Apache OFBiz Security Advisories (May 19, 2026 batch) |
| **Type** | Unauthenticated RCE · Authentication Bypass → RCE · Code Injection · Server-Side Template Injection · Privilege Escalation |
| **Affected Product** | Apache OFBiz (all versions before 24.09.06) |
| **Patch Status** | ✅ PATCHED — Apache OFBiz 24.09.06 fixes the full batch; update immediately |
| **Highest CVSS** | 🔴 **CRITICAL** — Multiple CVEs rated critical by Apache and Tenable |
| **Exploit Maturity** | ⚠️ PATCH-DAY RISK — All CVEs disclosed May 19, 2026; exploit development expected within days; Apache OFBiz has strong historical precedent for rapid weaponization |
| **CISA KEV** | ⚠️ Not yet listed; monitor closely — Apache OFBiz CVEs have a strong KEV listing pattern |
| **Disclosed** | 2026-05-19 |
| **Threat Level** | 🔴 HIGH — Multiple authentication bypass + unauth RCE paths in a widely-deployed enterprise ERP; Apache OFBiz has a documented history of mass exploitation within days of disclosure |
| **Admiralty Grade** | A1 — Official Apache security advisories |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1133 (External Remote Services) · T1059 (Command Injection) · T1134 (Token Manipulation) |

---

## Overview

On **May 19, 2026**, the Apache Software Foundation disclosed a significant batch of critical and high-severity vulnerabilities in **Apache OFBiz**, the open-source enterprise resource planning (ERP) and e-commerce platform used by organizations worldwide. The batch includes at least **two unauthenticated or pre-authentication code execution paths**, making this a high-priority patching target.

**Apache OFBiz has a well-established exploitation history** — every major CVE class (SSTI, SQLi, auth bypass, path traversal) has been weaponized and mass-exploited within days of public disclosure. CISA has historically added Apache OFBiz CVEs to the KEV catalog within weeks. The May 19, 2026 batch follows this pattern.

---

## CVE Breakdown

### 🔴 Critical: Unauthenticated RCE Paths

| CVE | Title | Severity | Notes |
|---|---|---|---|
| **CVE-2026-31986** | Apache OFBiz: Unauthenticated RCE via Default JWT Signing | 🔴 CRITICAL | Default JWT signing secret allows unsigned/forged tokens → arbitrary code execution without credentials |
| **CVE-2026-45434** | Apache OFBiz: Authentication Bypass via Password-Change Logic Flaw Leading to RCE | 🔴 CRITICAL | Password change logic flaw bypasses authentication; leads to authenticated execution path with arbitrary code capability |
| **CVE-2026-46586** | Apache OFBiz: Improper Validation in traverseContent Service | 🔴 CRITICAL | Improper code generation control; rated critical by Apache and Tenable |

### 🟠 High: Authenticated or Condition-Dependent RCE

| CVE | Title | Severity | Notes |
|---|---|---|---|
| **CVE-2026-45187** | Apache OFBiz: Improper Authorization in Scheduled Job Service | HIGH | Privilege escalation via scheduled job manipulation |
| **CVE-2026-41919** | Apache OFBiz: Authentication Bypass due to Improper Neutralization | HIGH | Auth bypass under specific conditions |
| **CVE-2026-35086** | Apache OFBiz: Authenticated Remote Code Execution | HIGH | Authenticated RCE — relevant post-auth bypass exploitation chain |
| **CVE-2026-31986** (secondary path) | Apache OFBiz: FreeMarker SSTI via Duplicate Parameter | HIGH | Server-side template injection via duplicate parameter handling |
| **CVE-2026-31906** | Apache OFBiz: Reflected XSS via HTML Attribute Injection | MEDIUM | Client-side attack vector |

---

## Technical Analysis

### CVE-2026-31986 — Unauth RCE via Default JWT Signing

This is the highest-priority CVE in the batch. Apache OFBiz uses JWT tokens for session management. When deployed with the **default JWT signing secret** (a well-known, publicly documented value), an unauthenticated attacker can forge valid JWT tokens and use them to authenticate as any user — including the administrator account — without valid credentials. This provides a direct path to code execution via OFBiz's built-in Groovy scripting capabilities.

**Exploitation chain:**
1. Forge JWT token using default/known signing key
2. Authenticate as administrator (or any privileged role)
3. Execute arbitrary Groovy/Java code via OFBiz's script execution endpoints

**Why this matters:** Many OFBiz deployments retain the default JWT secret because changing it requires configuration awareness. This is a low-skill, near-zero-prerequisite path to full server compromise.

### CVE-2026-45434 — Auth Bypass via Password-Change Logic Flaw

A logic flaw in the password change workflow allows an attacker to bypass authentication requirements. Combined with OFBiz's Groovy/Java execution capabilities, this creates an authenticated-equivalent RCE path from an unauthenticated starting position.

### Historical Apache OFBiz Exploitation Pattern

Apache OFBiz has been a persistent exploitation target:
- **2023**: CVE-2023-51467 (auth bypass) + CVE-2023-49070 (pre-auth RCE via Groovy) — mass exploitation by multiple threat actors within 72 hours
- **2024**: CVE-2024-25065 (path traversal auth bypass) — CISA KEV within 2 weeks
- **2025**: Multiple CVEs weaponized for ransomware deployment (DragonForce, RansomHub affiliates)
- **May 2026**: This batch continues the pattern; weaponization expected within days

---

## Affected Versions

| Version | Status |
|---|---|
| Apache OFBiz **before 24.09.06** | 🔴 VULNERABLE — all critical CVEs apply |
| Apache OFBiz **24.09.06** | ✅ PATCHED |

---

## Detection

- Anomalous JWT tokens in OFBiz authentication logs
- Groovy/Java script execution events by unexpected user accounts
- Requests to `/accounting`, `/webtools/control/main`, or Groovy execution endpoints from external IPs
- New admin account creation or privilege escalation events
- Unexpected scheduled job creation or modification

## MITRE ATT&CK Mapping

| Tactic | Technique | Notes |
|---|---|---|
| Initial Access | T1190 — Exploit Public-Facing Application | Unauth access via JWT forgery |
| Authentication Bypass | T1134 — Access Token Manipulation | JWT forgery / manipulation |
| Execution | T1059 — Command and Scripting Interpreter | Groovy/Java execution in OFBiz context |
| Persistence | T1505.003 — Web Shell | Deploy web shell via OFBiz file upload |
| Privilege Escalation | T1078 — Valid Accounts | Forge admin-level JWT token |

---

## Defensive Recommendations

1. **Update to Apache OFBiz 24.09.06 immediately** — this is a patch-day critical update
2. **Change the JWT signing secret** from any default or well-known value to a cryptographically random 256-bit secret
3. **Restrict OFBiz admin interfaces** — do not expose `/webtools/control/` to the internet
4. **Enable WAF rules** targeting OFBiz-specific exploitation patterns (JWT forgery, path traversal, Groovy execution endpoints)
5. **Monitor for** CISA KEV listing of this batch — expected within days based on historical pattern
6. **Audit deployed OFBiz instances** — identify all internet-exposed deployments and prioritize patching

---

## Disclosure Timeline

| Date | Event |
|---|---|
| 2026-05-19 | Apache discloses full batch of critical CVEs in OFBiz; fixed in 24.09.06 |
| 2026-05-19 | Profile created (C3PO); KEV watch initiated |
| Days 1–7 | HISTORICAL: Apache OFBiz CVEs typically weaponized within this window |

---

## References

- [Apache OFBiz Security Advisories](https://ofbiz.apache.org/security.html)
- [Vulnerability Lookup (CIRCL) — Apache OFBiz batch May 19, 2026](https://vulnerability.circl.lu/recent)
- [Tenable CVE Database — CVE-2026-45434, CVE-2026-46586](https://www.tenable.com/cve)
- [CISA KEV Catalog — Monitor for Apache OFBiz entries](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)

---

*Profile created: 2026-05-19 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
