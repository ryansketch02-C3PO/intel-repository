# PTC Windchill / FlexPLM Zero-Day — Unauthenticated RCE via Java Deserialization (CVSS 10.0)

## Identity

| Field | Details |
|---|---|
| **CVE** | None assigned — no CVE as of May 20, 2026 |
| **Vendor Advisory** | PTC internal advisory + EAC partner communication to customers |
| **Type** | Unauthenticated Remote Code Execution / Java Deserialization |
| **Class** | CWE-502 — Deserialization of Untrusted Data |
| **Affected Products** | PTC Windchill (all versions) · PTC FlexPLM (all versions) — customer-managed deployments with internet-accessible Apache servlets |
| **Not Affected** | Cloud-hosted / PTC-managed instances with properly restricted servlet access |
| **Patch Status** | 🔴 **UNPATCHED** — No patch available; workaround published (Apache config block); apply immediately |
| **CVSS** | **10.0 (Maximum)** — vendor-assigned |
| **Exploit Maturity** | 🔴 **ATTACKED** — IoCs confirmed (web shell uploads post-exploitation) |
| **CISA KEV** | Not listed — expected given confirmed active exploitation |
| **Disclosed** | ~May 19–20, 2026 |
| **Threat Level** | 🔴 CRITICAL — CVSS 10.0; actively exploited; no patch; ubiquitous in aerospace & defense PLM/PDM; unauthenticated internet-facing RCE |
| **Admiralty Grade** | A2 — Vendor and partner confirmation; IoCs documented |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1505.003 (Web Shell) · T1059 (Command and Scripting Interpreter) · T1083 (File and Directory Discovery) |

---

## Overview

PTC Windchill and FlexPLM contain an unauthenticated remote code execution zero-day via Java deserialization in two Apache servlet endpoints. The vulnerability carries a vendor-assigned CVSS score of **10.0** — the maximum — and has already been exploited in the wild, with IoCs including post-exploitation web shell uploads documented by PTC's service partner EAC.

**Why this matters for aerospace & defense:** Windchill is the dominant Product Lifecycle Management (PLM) and Product Data Management (PDM) platform across the defense industrial base. It holds technical drawings, engineering models, BOM data, program schedules, and configuration management records for weapon systems, aircraft, ships, and other defense programs. Compromise of a Windchill instance = access to controlled technical data and program-sensitive information.

FlexPLM serves the same function for footwear and apparel supply chains but is co-deployed in multi-program contractor environments.

---

## Technical Analysis

### Vulnerable Endpoints

```
/servlet/WindchillGW/com.ptc.wvs.server.publish.Publish
/servlet/WindchillAuthGW/com.ptc.wvs.server.publish.Publish
```

Both endpoints accept and deserialize Java objects without authentication checks on the deserialization path. A crafted request can trigger arbitrary code execution with the privileges of the Windchill application server process.

### Observed Post-Exploitation

- Web shell uploads to the Windchill server filesystem (EAC IoCs)
- Likely follow-on: credential harvesting, lateral movement into connected engineering systems, data exfiltration of technical documents

---

## Workaround (Apply Immediately)

Block the vulnerable servlet paths in Apache:

1. Create `/etc/apache2/conf.d/90-app-Windchill-Auth.conf` (or highest-numbered file in conf.d):

```apache
<LocationMatch "^.*servlet/(WindchillGW|WindchillAuthGW)/com\.ptc\.wvs\.server\.publish\.Publish(?:;[^/]*)?/.*$">
    Require all denied
</LocationMatch>
```

2. Restart Apache

**If a file with prefix 90- or higher already exists, use a higher number to ensure this rule loads last.**

---

## A&D Relevance

Windchill is deployed at virtually every major U.S. and allied defense prime contractor (Lockheed Martin, Raytheon, Boeing, Northrop Grumman, General Dynamics, BAE Systems, and hundreds of tier-2/3 suppliers). A successful exploit chain against an internet-exposed Windchill instance provides access to:
- ITAR/EAR-controlled technical data
- Program schedules and SOW details
- Engineering designs and specifications
- Personnel and organizational structure via system metadata

This is a tier-1 espionage target for nation-state actors (China-nexus in particular given known PTC targeting history).

---

*Added: 2026-05-20 | No patch — apply Apache workaround immediately | Monitor EAC + PTC for CVE assignment and patch*
