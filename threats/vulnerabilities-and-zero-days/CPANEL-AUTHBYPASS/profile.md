# cPanel Auth Bypass — CVE-2026-41940

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | cPanel Auth Bypass |
| **CVE** | CVE-2026-41940 |
| **Type** | Authentication Bypass — Pre-Authentication, Remote |
| **Class** | CWE-306 — Missing Authentication for Critical Function (watchTowr) · CWE-287 — Improper Authentication (NVD) |
| **Root Cause Chain** | CRLF Injection + Encryption-Skip + Two-File Session Promotion |
| **Affected Products** | cPanel & WHM (all versions after 11.40) · WP Squared (< 136.1.7) |
| **Estimated Exposure** | ~1.5 million internet-exposed cPanel instances (Rapid7/Shodan) · ~70 million domains |
| **Patch Status** | ✅ **PATCHED** — Emergency update April 28–29, 2026 |
| **CVSS v3.1** | **9.8 CRITICAL** (`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`) |
| **CVSS v4.0** | **9.3 CRITICAL** (`CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N`) |
| **PoC Status** | 🔴 **PUBLIC** — watchTowr PoC (April 29) · cPanelSniper full framework (GitHub, May 4) |
| **CISA KEV** | ✅ **LISTED** — Added April 30, 2026 · Federal Remediation Deadline: **May 3, 2026** (expired) |
| **Zero-Day Window** | **~64 days** — active exploitation from ~February 23, 2026; patch released April 28 |
| **Exploitation Status** | 🔴 **ACTIVE** — Multi-actor; 40,000+ hosts compromised; Sorry Ransomware · Mirai botnet · APT (Southeast Asia) |
| **Discovered By** | Sina Kheirkhah, watchTowr Labs (April 28 coordinated disclosure) |
| **In-The-Wild Exploitation** | ~February 23, 2026 — predates researcher discovery by months |
| **Threat Level** | 🔴 **CRITICAL** |
| **Admiralty Grade** | A1 — Vendor advisory, CISA KEV, Rapid7, watchTowr, Shadowserver all confirm |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1078 (Valid Accounts) · T1059 (Command Execution) · T1543 (Persistent Service) · T1486 (Data Encrypted for Impact) |

---

## Overview

CVE-2026-41940 is the most operationally significant web hosting infrastructure vulnerability in years. An unauthenticated attacker can send a single crafted HTTP request to any cPanel or WHM login endpoint and obtain a full WHM root session — no credentials, no 2FA, no user interaction required.

The vulnerability exists in `cpsrvd`, the Perl daemon that handles all cPanel/WHM authentication, and chains three independent security failures:

1. **A sanitizer that wasn't called** — the Basic-auth handler in `cpsrvd` failed to invoke the CRLF-stripping function that all other code paths correctly used
2. **Encryption that could be silently disabled** — a missing cookie segment causes the session password field to be written to disk in plaintext
3. **A two-file session model that launders the injection** — injected CRLF lines look harmless in the JSON cache but are promoted to top-level authenticated session keys when the token-denied handler re-parses the raw session file

The result: an attacker can write `user=root`, `hasroot=1`, `tfa_verified=1`, and `successful_internal_auth_with_timestamp=<any>` into a session file before authentication, then trigger a session reload that promotes those values into a fully authenticated WHM root session. **Two-factor authentication is explicitly bypassed** — the injected `tfa_verified=1` flag short-circuits the 2FA check entirely.

**Scale of exposure:** ~1.5 million internet-exposed instances serving approximately 70 million domains. Active exploitation has been confirmed since at least February 23, 2026 — 64 days before the patch. In the post-disclosure window, 40,000+ hosts were identified as likely compromised. Three distinct campaigns have been confirmed.

**What an attacker gets post-bypass:**
- Full root-equivalent control over all hosted websites, databases, email, and DNS
- SSH key management — add backdoor keys for persistent access
- Cron job creation for persistence or recurring C2 callbacks
- Database dumps (MySQL/MariaDB) across all hosted accounts
- WHM (admin tier): control over all cPanel accounts on the server, server configuration, software installation

---

## Technical Analysis

### The Three-Layer Failure Chain

**Layer 1 — Missing Sanitizer in the Basic-Auth Path**

`Cpanel/Session.pm` contains `filter_sessiondata()` — a function that strips `\r`, `\n`, `=`, and `,` from session values before writing them to disk. It exists precisely to prevent CRLF injection. The problem: it was opt-in, and every caller was expected to invoke it explicitly. Every code path did — except the Basic Authorization HTTP header handler inside `cpsrvd`, which read credentials directly off the `Authorization:` header and wrote them to the session file raw.

The patch moves the sanitizer inside the save function itself, making it impossible to bypass by omission.

**Layer 2 — Encryption Disabled by Attacker-Controlled Input**

The session writer encrypts the password field using a per-session encryption key embedded in the `whostmgrsession` cookie: `whostmgrsession=<name>,<32-hex-key>`. When the key component is absent (attacker sends `whostmgrsession=:<name>` with no `,<hex>` tail), the code path fell into an `else` branch that wrote the password field to disk in plaintext. An attacker who deliberately truncates the cookie causes their injection payload to land on disk unencrypted and unsanitized.

**Layer 3 — Session Cache Promotion via Token-Denied Handler**

cPanel sessions exist in two on-disk representations:
- A **raw line-oriented file** — each `\r\n` creates a new `key=value` record
- A **JSON cache** — embedded newlines appear as harmless `\n` escape sequences inside a string

The session loader normally reads the JSON cache first, where injected newlines are invisible. The bypass exploits the **token-denied handler** — a code path triggered when a session token is flagged as suspect. This handler re-parses the *raw* file directly and overwrites the JSON cache, **promoting injected lines to top-level session keys**. A subsequent auth check reads a JSON cache containing `user=root`, `hasroot=1`, and `successful_internal_auth_with_timestamp` — a fully authenticated WHM root session.

### Complete Exploit Chain (~5 HTTP Requests)

```
Step 1: POST /login/?login_only=1
        Authorization: Basic base64("<user>:<payload>\r\nuser=root\r\nhasroot=1\r\n
                                     tfa_verified=1\r\nsuccessful_internal_auth_with_timestamp=1")
        Cookie: whostmgrsession=:<session-name>    ← truncated: no ,<hex> segment

        Server writes unencrypted, unsanitized session file to disk
        ↓
Step 2: Trigger token-denied handler via any failing session request
        Server re-parses raw session file → overwrites JSON cache
        Injected lines promoted to top-level session keys
        ↓
Step 3: Present session token in any normal WHM request
        docheckpass_whostmgrd sees successful_internal_auth_with_timestamp → AUTH OK
        → FULL WHM ROOT SESSION — no password, no 2FA
        ↓
Step 4 (post-exploitation): any WHM API action yields root RCE
        (account creation, SSH key injection, cron, package install, PHP-FPM config, hooks)
```

---

## Affected Versions and Patches

| Branch | Vulnerable Through | Fixed Version |
|---|---|---|
| 11.86.0.x | 11.86.0.40 | **11.86.0.41** |
| 11.110.0.x | 11.110.0.96 | **11.110.0.97** |
| 11.118.0.x | 11.118.0.62 | **11.118.0.63** |
| 11.124.0.x | 11.124.0.34 | **11.124.0.35** |
| 11.126.0.x | 11.126.0.53 | **11.126.0.54** |
| 11.130.0.x | 11.130.0.18 | **11.130.0.19** |
| 11.132.0.x | 11.132.0.28 | **11.132.0.29** |
| 11.134.0.x | 11.134.0.19 | **11.134.0.20** |
| 11.136.0.x | 11.136.0.4 | **11.136.0.5** |
| WP Squared | < 136.1.7 | **136.1.7** |

> **Critical:** Servers with **auto-update disabled or version pinning will not self-patch.** Force upgrade via SSH: `/scripts/upcp --force`. After upgrading, restart cpsrvd: `/scripts/restartsrv_cpsrvd`.

---

## Exploitation Campaigns

### Campaign 1 — Sorry Ransomware
- **Type:** Opportunistic mass exploitation post-disclosure
- **Encryptor:** Go-based Linux encryptor; ChaCha20 stream cipher + RSA-2048 key protection
- **Indicator:** `.sorry` file extension on encrypted files; qTox ransom note
- **Scale:** 7,135 cPanel/WHM hosts confirmed with `.sorry` files (Censys, May 3); WordPress sites specifically targeted
- **Backup Destruction:** Attackers wiped server backups before encryption to prevent recovery

### Campaign 2 — Mirai Botnet Variant ("nuclear.x86")
- **Type:** Botnet recruitment, launched within hours of watchTowr PoC publication
- **Method:** WHM access to download, execute, and delete ELF binary from `87.121.84.78`
- **Indicator:** Binary named `nuclear.x86`; shell history shows `wget/curl → execute → rm` pattern

### Campaign 3 — APT (Southeast Asia Government and Military)
- **Type:** Targeted state-level intrusion; likely operationalized during the zero-day window
- **Targets:** Philippine Coast Guard, Philippine Air Force 15th Strike Wing, Philippine Government Arsenal (Dept. of National Defence), Lao Ministry of National Defence, Lao Ministry of Natural Resources and Environment; MSPs in Philippines, Laos, Canada, South Africa, US
- **Framework:** AdapdixC2 + OpenVPN + Ligolo (network pivoting)
- **C2:** `95.111.250[.]175` / `delicate-dew.serveftp[.]com:4455`
- **Persistence:** Masqueraded systemd service: `systemd-update.service`; payload at `/usr/local/bin/.netmon/systemd-helper`
- **Prior Activity:** Same actor exfiltrated ~4.37 GB from China Railway Society Electrification Committee in March 2026 (pre-CVE-41940 disclosure)
- **Attribution:** Vietnamese comments throughout Python tooling; possible false flag — no confirmed country attribution (Ctrl-Alt-Intel)

### Exploitation Framework: cPanelSniper (Public, May 4)
Pure Python, no dependencies, full four-stage chain: hostname discovery → pre-auth session creation → CRLF injection → session verification. Post-exploitation: interactive WHM shell, account listing, RCE, password modification, backdoor account creation. Supports threaded bulk scanning. **Any unpatched instance is now a trivial target.**

---

## Post-Compromise Persistence Mechanisms

Forensic analysis (I Am Tzar, May 4) documents four persistence mechanisms deployed post-WHM access:

| Mechanism | Location | Notes |
|---|---|---|
| SSH backdoor key | `/root/.ssh/authorized_keys` | ed25519 key; survives password resets |
| SUID ELF binary | `/usr/bin/.system_cache` | 964 KB; SUID bit; hidden by leading dot |
| Daily cron callback | `/etc/cron.daily/cpanel_sync` | Fetches updated payloads from C2 |
| Bashrc dropper | `/root/.bashrc` | Re-executes on every interactive admin login |

> **Implication:** Manual cleanup is unreliable. Any server internet-exposed and unpatched during February 23–April 28 should be **rebuilt from pre-breach backups**, not cleaned in place.

---

## Detection

### Session File Forensics

```bash
# Find session files with injected CRLF in pass= field
grep -lP 'pass=.*\r' /var/cpanel/sessions/raw/* 2>/dev/null

# Find sessions with privileged keys from a badpass-origin session
grep -lE '^(hasroot|tfa_verified|successful_internal_auth_with_timestamp)=1' \
    /var/cpanel/sessions/raw/* 2>/dev/null

# Check for backdoor SSH keys across all accounts
grep -r "ssh-rsa\|ecdsa-sha2\|ssh-ed25519" /home/*/.ssh/authorized_keys \
    --include="authorized_keys" -l

# Look for unauthorized cron entries
find /var/spool/cron/ -newer /tmp -type f -ls

# New PHP files in web roots (last 24h)
find /home/*/public_html/ -name "*.php" -newer /tmp -mtime -1 -ls
```

### Access Log Analysis

```bash
# Successful logins from unusual IPs on cPanel/WHM ports
grep " 200 " /usr/local/cpanel/logs/access_log | \
  grep -E ":(2082|2083|2086|2087)/" | \
  awk '{print $1, $7}' | sort | uniq -c | sort -rn | head -50
```

### Network Detection Rules

```
# Successful unauthenticated auth to cPanel/WHM ports from unknown IPs
dst.port IN [2082, 2083, 2086, 2087]
src.ip NOT IN [known_admin_ips, known_customer_ips]
response.status: 200

# Cookies with truncated whostmgrsession format (no ,<hex> segment)
http.cookie MATCHES "whostmgrsession=:[^,]+"

# Authorization header with embedded CRLF characters (URL-encoded)
http.header["Authorization"] CONTAINS "%0d%0a" OR "%0a"
```

### Network IOCs

| Indicator | Campaign |
|---|---|
| `87.121.84.78` | Mirai — nuclear.x86 delivery |
| `95.111.250[.]175` | APT C2 |
| `delicate-dew.serveftp[.]com:4455` | APT C2 domain |
| `.sorry` file extensions on web server files | Sorry Ransomware |
| `/usr/bin/.system_cache` | SUID backdoor binary |
| `/etc/cron.daily/cpanel_sync` | Persistence cron |
| `systemd-update.service` | APT masqueraded service |
| `/usr/local/bin/.netmon/systemd-helper` | APT Ligolo payload |

### Behavioral Indicators

| Indicator | Confidence |
|---|---|
| Successful session on WHM port without preceding valid POST /login/ | 🔴 HIGH |
| New SSH public key added to `~/.ssh/authorized_keys` | 🔴 HIGH |
| New `.php` files with obfuscated content in web roots | 🔴 HIGH |
| Large database exports following unusual session | 🔴 HIGH |
| DNS record modifications across hosted domains | 🟡 MEDIUM |
| cPanel session from IP with no prior login history | 🟡 MEDIUM |

---

## Mitigations and Remediation

### All Systems — Immediate

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Patch:** `/scripts/upcp --force` → verify `/usr/local/cpanel/cpanel -V` → restart cpsrvd |
| 🔴 IMMEDIATE | **Manually patch pinned/auto-update-disabled servers** — they will not self-update |
| 🔴 IMMEDIATE | **Block ports 2083, 2087, 2095, 2096** at the perimeter if patch cannot be applied immediately |
| 🔴 IMMEDIATE | **Restrict WHM access to trusted IP ranges** — these ports should never be internet-accessible without IP allowlist or VPN |
| 🔴 HIGH | **Audit recent cPanel access logs** for successful logins from unknown IPs |

### Systems Exposed February 23–April 28 (Treat as Compromised)

| Priority | Action |
|---|---|
| 🔴 CRITICAL | **Rebuild from pre-breach backups** — manual cleanup is unreliable given documented persistence mechanisms |
| 🔴 CRITICAL | **Rotate all credentials** — root, WHM resellers, API tokens, SSH keys |
| 🔴 HIGH | **Purge all sessions** — `/var/cpanel/sessions/raw/` and `/var/cpanel/sessions/cache/` |
| 🔴 HIGH | **Hunt for persistence** — audit `authorized_keys`, `/etc/cron.daily/`, `~/.bashrc`, SUID binaries, systemd services |
| 🔴 HIGH | **Audit all users created since February 23** — backdoor accounts are standard post-exploitation |
| 🟡 HIGH | **Scan website files for webshells** — especially WordPress `wp-content/uploads` |
| 🟡 HIGH | **Notify affected customers** if compromise is confirmed — GDPR Art. 33 / CCPA breach notification may apply |

---

## A&D / Enterprise Relevance

**Relevance: MODERATE — supply chain and contractor infrastructure risk.**

cPanel is ubiquitous in commercial web hosting. While prime A&D contractors typically run dedicated infrastructure, this vulnerability is relevant to:

- **Tier 2/3 defense supply chain vendors** hosting their websites, portals, or partner extranets on shared cPanel hosting
- **SMB subcontractors** whose cPanel-hosted contact forms, proposal portals, or document repositories could be compromised as an initial access vector for spearphishing campaigns targeting prime contractor relationships
- **Hosting providers used by defense-adjacent organizations** — a single cPanel server hosts dozens to hundreds of domains; one compromised server exposes all tenants
- **Managed service providers (MSPs) running WHM reseller hosting** — MSP compromise via WHM auth bypass is a classic supply chain pivot vector

Any organization with a web presence on shared hosting should verify their hosting provider has patched. Any organization running their own cPanel/WHM instance must have patched immediately.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| ~February 23, 2026 | Earliest confirmed in-the-wild exploitation (KnownHost CEO) — exact start date may be earlier |
| ~April 14, 2026 | Researcher reports to cPanel; cPanel initially responds "nothing wrong" (per webhosting.today) |
| April 28, 2026 | cPanel issues security advisory; patches released across all supported branches |
| April 29, 2026 | CVE-2026-41940 assigned (CVSS 9.8); watchTowr Labs publishes full technical analysis and PoC |
| April 30, 2026 | CISA adds CVE-2026-41940 to KEV catalog; Shadowserver reports 44,000+ compromised IPs scanning honeypots; C3PO profile created (ZD-013) |
| May 2, 2026 | KnownHost, InMotion, HostPapa, Namecheap confirm emergency port blocks (TCP/2083, 2087) |
| May 3, 2026 | CISA federal remediation deadline passed — FCEB agencies required to have patched; Shadowserver count drops to ~3,540 |
| May 4, 2026 | cPanelSniper public exploitation framework published on GitHub; Sorry Ransomware scale confirmed (7,135 hosts); APT Southeast Asia campaign detailed; Cloudflare emergency WAF rule deployed |
| May 5, 2026 | Multi-actor exploitation confirmed (HelpNetSecurity) |
| May 6, 2026 | Profile updated and merged; ~2,000 compromised IPs still active; patch cycle largely complete at major providers |

---

## References

- [watchTowr Labs — CVE-2026-41940 Technical Analysis and PoC (Sina Kheirkhah, April 29, 2026)](https://labs.watchtowr.com)
- [Rapid7 ETR — CVE-2026-41940 cPanel WHM Authentication Bypass](https://www.rapid7.com/blog/post/etr-cve-2026-41940-cpanel-whm-authentication-bypass/)
- [HelpNetSecurity — cPanel zero-day exploited for months before patch (April 30, 2026)](https://www.helpnetsecurity.com/2026/04/30/cpanel-zero-day-vulnerability-cve-2026-41940-exploited/)
- [SecurityWeek — Over 40,000 Servers Compromised in Ongoing cPanel Exploitation (May 4, 2026)](https://www.securityweek.com/over-40000-servers-compromised-in-ongoing-cpanel-exploitation/)
- [Picus Security — CVE-2026-41940 Explained (May 1, 2026)](https://www.picussecurity.com/resource/blog/cve-2026-41940-explained-cpanel-whm-authentication-bypass-hit-1-5m-servers)
- [webhosting.today — CVE-2026-41940 Live: Active Exploitation (May 4, 2026)](https://webhosting.today/2026/05/04/cve-2026-41940-live-cpanel-authentication-bypass-active-exploitation-and-what-comes-next/)
- [Ctrl-Alt-Intel — Southeast Asia APT Campaign Full Report (May 4, 2026)](https://ctrl-alt-intel.com)
- [Imperva — Imperva Customers Protected Against CVE-2026-41940](https://www.imperva.com/blog/imperva-customers-protected-against-cve-2026-41940-in-cpanel-whm/)
- [TheCyberThrone — CISA adds cPanel and Linux Kernel to KEV (May 4, 2026)](https://thecyberthrone.in/2026/05/04/cisa-adds-cpanel-and-linux-kernel-to-kev/)
- [Tenable — CVE-2026-41940](https://www.tenable.com/cve/CVE-2026-41940)
- [cPanel Security Advisories](https://docs.cpanel.net/security-advisories/)

---

---

## Intelligence Update — 2026-05-11

### Patched | Sorry Ransomware Campaign Ongoing | Southeast Asia APT Active

**Patch status:** Emergency patch shipped April 28–29, 2026. All cPanel & WHM installations should be on the patched version by now. CISA KEV federal deadline (May 3) passed — FCEB agencies required to have remediated.

**Current exploitation landscape:**
- **Sorry Ransomware** campaign confirmed at 7,135+ compromised hosts as of May 4; operators continue scanning for unpatched stragglers
- **Southeast Asia APT** campaign — government and military targets in the Philippines, Laos, and regionally; MSPs and hosting providers used as pivots
- **Mirai botnet** operators incorporated CVE-2026-41940 into automated scanning infrastructure
- Approximately **2,000 compromised IPs** were still active as of May 6; this number declining but not zero

**Remaining risk:** Organizations that patched are protected from new intrusions, but any system that was compromised during the 64-day zero-day window (Feb 23 – Apr 28) may still have active webshells or backdoors installed. Patching stops new exploitation; it does not evict existing implants. Incident response sweep recommended for any cPanel host that was internet-accessible during that window.

**Hosting provider supply chain angle:** cPanel's massive deployment footprint (~1.5M instances, ~70M domains) means a single compromised hosting provider can yield access to thousands of customer sites. MSPs and shared hosting environments that were compromised during the zero-day window remain a latent threat to all downstream customers.

*Profile created: 2026-04-30 | Updated: 2026-05-11 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
