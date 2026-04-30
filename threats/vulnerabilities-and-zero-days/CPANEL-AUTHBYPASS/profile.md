# cPanel Auth Bypass — CVE-2026-41940

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | cPanel Auth Bypass |
| **CVE** | CVE-2026-41940 |
| **Type** | Authentication Bypass — Login Flow |
| **Class** | CWE-287 — Improper Authentication |
| **Affected Product** | cPanel & WebHost Manager (WHM) |
| **Affected Versions** | All versions after 11.40 through the following: prior to 11.110.0.97, 11.118.0.63, 11.126.0.54, 11.132.0.29, 11.134.0.20, and 11.136.0.5 |
| **Fixed Versions** | ✅ PATCHED — 11.110.0.97 · 11.118.0.63 · 11.126.0.54 · 11.132.0.29 · 11.134.0.20 · 11.136.0.5 |
| **Patch Type** | Emergency update — released out-of-band April 28–29, 2026 |
| **Attack Vector** | Network — unauthenticated, no user interaction required |
| **CVSS v3.1** | **9.8 Critical** (`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`) |
| **CVSS v4.0** | **9.3 Critical** (`CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N`) |
| **CISA KEV** | ❌ Not listed as of 2026-04-30 |
| **Exploit Status** | 🔴 **ACTIVELY EXPLOITED IN THE WILD** |
| **Disclosed** | April 29, 2026 |
| **Threat Level** | 🔴 **CRITICAL** |
| **Admiralty Grade** | A2 — Active exploitation confirmed; technical mechanism partially documented |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1078 (Valid Accounts — post-bypass) · T1059 (Command and Scripting Interpreter — via hosting control panel) |

---

## Overview

CVE-2026-41940 is a critical authentication bypass in **cPanel & WebHost Manager (WHM)** — the most widely deployed web hosting control panel stack in the world, running on tens of millions of servers globally. The vulnerability exists in cPanel's login flow and allows a completely unauthenticated remote attacker to gain unauthorized access to the control panel with no credentials required.

**What an attacker gets when they bypass cPanel/WHM auth:**
- Full root-equivalent control over all hosted websites, databases, email accounts, and DNS zones on the server
- SSH key management (add backdoor keys for persistent access)
- File manager with full filesystem read/write
- Cron job creation (persistence)
- Database access — MySQL/MariaDB dumps of everything hosted on the server
- Email inboxes for all hosted domains
- One-click ability to deploy webshells, backdoors, or redirect visitor traffic
- On WHM (admin tier): control over all cPanel accounts on the server — provisioning new accounts, modifying server configuration, installing software

This is functionally equivalent to root shell access on the underlying Linux server, delivered through a web interface. The blast radius per vulnerable server is enormous.

**Scale of exposure:** cPanel powers an estimated 70%+ of all commercial web hosting infrastructure. Any internet-accessible cPanel/WHM instance running a vulnerable version is trivially compromisable by an unauthenticated attacker with no special tooling beyond a crafted HTTP request.

---

## Technical Details

### Root Cause

The vulnerability exists in cPanel's **login flow authentication logic**. The exact technical mechanism has not been fully published, but classification as CWE-287 (Improper Authentication) with a network-accessible, unauthenticated attack vector indicates:

- A flaw in how the login sequence validates or enforces credential checking — likely a logic error, missing verification step, or bypassable conditional in the authentication path
- The attacker sends a crafted request to the login endpoint that either skips authentication entirely or causes the server to treat the session as authenticated without valid credentials being presented
- No brute force required — this is a bypass, not a credential stuffing attack

cPanel runs on ports **2082** (HTTP) and **2083** (HTTPS) for user accounts, and **2086** (HTTP) / **2087** (HTTPS) for WHM admin access. Any externally reachable port is exploitable.

### Attack Simplicity

- **Attack Complexity:** Low
- **Privileges Required:** None
- **User Interaction:** None
- **No special tooling required** — exploitable with standard HTTP request tooling once a payload is known

This is among the most dangerous vulnerability profiles possible for an internet-facing service: full administrative compromise with zero prerequisites.

---

## Affected Versions

cPanel releases updates across multiple concurrent release tracks. All of the following must be patched:

| Branch | Vulnerable Through | Fixed Version |
|---|---|---|
| 11.110.x | 11.110.0.96 | **11.110.0.97** |
| 11.118.x | 11.118.0.62 | **11.118.0.63** |
| 11.126.x | 11.126.0.53 | **11.126.0.54** |
| 11.132.x | 11.132.0.28 | **11.132.0.29** |
| 11.134.x | 11.134.0.19 | **11.134.0.20** |
| 11.136.x | 11.136.0.4 | **11.136.0.5** |

**Any cPanel/WHM version after 11.40 and before the fixed versions above is vulnerable.**

---

## What Attackers Can Do Post-Bypass

| Capability | Impact |
|---|---|
| File manager access | Read/write entire web root; plant webshells, backdoors, or malicious scripts |
| Database management | Dump all MySQL/MariaDB databases — credentials, PII, application data |
| SSH key management | Inject attacker SSH keys for persistent terminal access |
| Cron jobs | Create scheduled tasks for persistent execution, beaconing, or recurring data exfil |
| Email access | Read all hosted email inboxes — credentials, business communications, 2FA codes |
| DNS management | Redirect domain traffic, intercept email, set up phishing sub-domains |
| WHM (admin) only: account creation | Spin up new cPanel accounts as persistence or staging infrastructure |
| WHM (admin) only: server config | Modify Apache/Nginx config, install software, change firewall rules |

---

## Detection

### Behavioral Indicators

| Indicator | Description | Confidence |
|---|---|---|
| Unauthenticated login success on cPanel/WHM ports (2082/83/86/87) | Successful session without valid credentials | 🔴 HIGH |
| New SSH public key added to `~/.ssh/authorized_keys` | Persistence via SSH backdoor post-bypass | 🔴 HIGH |
| Unusual cron job creation via cPanel API | Persistence or recurring beaconing | 🔴 HIGH |
| New `.php` files with obfuscated content in web roots | Webshell deployment | 🔴 HIGH |
| Large database exports from cPanel File Manager or phpMyAdmin | Data exfiltration | 🔴 HIGH |
| cPanel session from IP with no prior login history | Potential bypass exploitation | 🟡 MEDIUM |
| DNS record modifications across hosted domains | Traffic redirect / phishing setup | 🟡 MEDIUM |

### Detection Queries

```bash
# Check for recent SSH key additions across all cPanel accounts
grep -r "ssh-rsa\|ecdsa-sha2\|ssh-ed25519" /home/*/\.ssh/authorized_keys \
  --include="authorized_keys" -l

# Check cPanel access logs for successful logins from unusual IPs
grep " 200 " /usr/local/cpanel/logs/access_log | \
  grep -E ":(2082|2083|2086|2087)/" | \
  awk '{print $1, $7}' | sort | uniq -c | sort -rn | head -50

# Look for recently added cron jobs
find /var/spool/cron/ -newer /tmp -type f -ls

# Check for new PHP files in web roots (last 24h)
find /home/*/public_html/ -name "*.php" -newer /tmp -mtime -1 -ls
```

### Network-Level Detection

```
# Alert: Inbound connections to cPanel/WHM ports from new/unknown IPs
dst.port IN [2082, 2083, 2086, 2087]
src.ip NOT IN [known_admin_ips, known_customer_ips]
response.status: 200 (successful)

# Alert: High-volume requests to login endpoint from single IP
dst.port IN [2082, 2083, 2086, 2087]
uri.path CONTAINS "/login"
count > 10 in 60s from same src.ip
```

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Patch to the fixed version for your release track** — cPanel auto-update can be forced via SSH: `upcp --force` to upgrade to latest patched release |
| 🔴 IMMEDIATE | **Restrict cPanel/WHM ports to trusted IP ranges** — ports 2082/2083/2086/2087 should never be internet-accessible without IP allowlist or VPN requirement |
| 🔴 IMMEDIATE | **Audit recent cPanel access logs** — check for successful logins from unknown IPs; rotate all credentials if unauthorized access is found |
| 🔴 IMMEDIATE | **Audit SSH authorized_keys** for all hosted accounts — a common persistence mechanism post-compromise |
| 🔴 HIGH | **Scan web roots for new PHP files** — webshell deployment is the first post-exploitation move for most attackers targeting cPanel |
| 🔴 HIGH | **Check for database exports** — if credentials or data were staged, assess downstream breach impact |
| 🟡 HIGH | **Enable two-factor authentication on cPanel/WHM** — mitigates some credential-based follow-on attacks post-bypass |
| 🟡 HIGH | **Review cron jobs across all hosted accounts** — persistence mechanism commonly used in post-compromise activity |
| 🟡 MEDIUM | **Consider ModSecurity WAF rules** for cPanel login endpoint as temporary additional layer |

### Emergency Upgrade Command (SSH as root)

```bash
# Force upgrade to latest patched cPanel version
/scripts/upcp --force

# Verify current version after upgrade
cat /usr/local/cpanel/version
```

---

## A&D / Enterprise Relevance

**Relevance: MODERATE — supply chain and contractor infrastructure risk.**

cPanel is ubiquitous in commercial web hosting. While A&D prime contractors typically run dedicated infrastructure, this vulnerability is relevant to:

- **Tier 2/3 defense supply chain vendors** that may host their websites, portals, or partner extranets on shared cPanel hosting
- **SMB subcontractors** whose cPanel-hosted contact forms, proposal portals, or document repositories could be compromised as an initial access vector for spearphishing campaigns targeting their prime contractor relationships
- **Hosting providers used by defense-adjacent organizations** — a single cPanel server hosts dozens to hundreds of domains; compromising one server gives access to all tenants
- **Managed service providers (MSPs)** running WHM reseller hosting for their clients — MSP compromise via WHM auth bypass is a classic supply chain attack vector

Any organization with a web presence on shared hosting should verify their hosting provider has patched. Any organization running their own cPanel/WHM instance must patch immediately.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| 2026-04-28 | cPanel releases emergency patches across all supported release tracks |
| 2026-04-29 | CVE-2026-41940 published (MITRE/NVD); Tenable, Cyber Recaps publish coverage |
| 2026-04-29 | Active exploitation in the wild confirmed |
| 2026-04-30 | C3PO profile created (ZD-013) |

---

## References

- [Tenable — CVE-2026-41940](https://www.tenable.com/cve/CVE-2026-41940)
- [Cyber Recaps — Daily Cybersecurity News April 29, 2026](https://cyberrecaps.com/news/cybersecurity-news-april-29-2026/)
- [cPanel Security Advisories](https://docs.cpanel.net/security-advisories/)

---

*Profile created: 2026-04-30 | Author: C3PO | Admiralty Grade: A2 | TLP: WHITE*
