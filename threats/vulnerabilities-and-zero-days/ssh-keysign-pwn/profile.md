# ssh-keysign-pwn — Linux LPE via ssh-keysign (SSH Server Private Key Read)

## Identity

| Field | Details |
|---|---|
| **Alias** | ssh-keysign-pwn |
| **CVE** | **CVE TBD** — not yet assigned as of May 15, 2026 |
| **Type** | Local Privilege Escalation (LPE) → Root-Owned File Read |
| **Class** | Privilege Logic Bug — ssh-keysign SUID binary abused to read files accessible only to root |
| **Affected Systems** | All Linux kernels before upstream patch merged ~May 14, 2026 (all distributions still pending) |
| **Patch Status** | ⚠️ **PARTIAL** — Upstream kernel patched May 14, 2026 by Linus Torvalds; **distribution packages pending** |
| **CVSS** | Not yet scored; assessed **HIGH** — unprivileged user → root SSH key read; PoC public |
| **Exploit Maturity** | 🔴 **Public PoC** — Working PoC published by `_SiCK` within hours of patch commit analysis on May 14, 2026 |
| **Disclosed** | May 14–15, 2026 (patch landed May 14; PoC published same day) |
| **Discovered / PoC by** | **_SiCK** (same researcher responsible for Copy Fail 2 and related kernel LPE research) |
| **Prior Fix Proposed** | Yann Horn (Google Project Zero) proposed a fix for the underlying issue six years ago |
| **Threat Level** | 🟠 HIGH — Public PoC; all distros still unpatched; enables SSH host key theft; low barrier to exploitation on unpatched systems |
| **Admiralty Grade** | B2 — Public PoC; researcher-confirmed; upstream patched; vendor confirmation pending |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) · T1552.004 (Unsecured Credentials: Private Keys) |

---

## Overview

**ssh-keysign-pwn** is a newly disclosed Linux local privilege escalation vulnerability involving the **`ssh-keysign` SUID binary**, a component of OpenSSH used to generate cryptographic signatures during host-based authentication. An unprivileged user can abuse `ssh-keysign`'s privileged execution to **read root-owned files**, with the most impactful application being the extraction of the **SSH server's private host key** — enabling impersonation of the server.

The upstream patch landed in Linus Torvalds' kernel tree on approximately May 14, 2026. Within hours, the researcher `_SiCK` — who had previously discovered Copy Fail 2 using the same technique of reverse-engineering patch commits — analyzed the fix and published a working PoC, collapsing the typical researcher-to-exploit timeline.

**A fix was proposed by Yann Horn (Google Project Zero) six years ago** but was not merged into the kernel mainline at the time. The vulnerability existed in all kernels up through May 14, 2026.

---

## Technical Analysis

### How ssh-keysign Works

`ssh-keysign` is installed as a **SUID binary** — it runs with root privileges when executed by any user, enabling it to access the SSH host private key files (typically `/etc/ssh/ssh_host_*_key`) on behalf of the SSH daemon. Its intended purpose is limited: sign challenges during SSH host-based authentication without requiring the daemon to hold the key directly.

### The Exploitation Path

The vulnerability enables an unprivileged user to invoke `ssh-keysign` in a manner that causes it to **read arbitrary root-owned files** beyond its intended scope. This can be triggered to read:
- **SSH private host keys** (`/etc/ssh/ssh_host_rsa_key`, `ssh_host_ed25519_key`, etc.)
- Potentially other sensitive root-owned files depending on the exploitation approach

### Impact of SSH Host Key Theft

Possession of a server's SSH private host key allows an attacker to:
1. **Impersonate the server** — perform man-in-the-middle attacks against SSH clients connecting to that host
2. **Defeat known_hosts verification** — clients that previously trusted the server will accept connections from the attacker
3. **Decrypt cached sessions** — in some configurations, historic SSH sessions may be decryptable

### Researcher Context

`_SiCK` has established a pattern of discovering Linux kernel LPEs by analyzing kernel patch commits and reverse-engineering the vulnerability from the fix:
- **Copy Fail 2** — discovered via the same commit-analysis methodology
- **ssh-keysign-pwn** — PoC published within ~1 hour of analyzing the upstream fix commit

This methodology means future kernel patches may similarly produce PoCs within hours of upstream merges.

---

## Affected Scope

- **All Linux kernels** up to (and including) the last kernel before the May 14, 2026 upstream patch
- **All major Linux distributions** — patches pending as of May 15, 2026
- Debian, Ubuntu, RHEL, CentOS, AlmaLinux, Rocky Linux, Fedora, Arch, and others — all have distribution kernel packages that predate the fix
- Any system with `ssh-keysign` installed (default in most OpenSSH deployments on Linux)

---

## Mitigations

Until distribution kernel patches are available:

1. **Remove the SUID bit from ssh-keysign** — this disables host-based SSH authentication but eliminates the attack surface
   ```bash
   chmod 0755 /usr/lib/openssh/ssh-keysign  # Debian/Ubuntu
   chmod 0755 /usr/libexec/openssh/ssh-keysign  # RHEL/CentOS/Fedora
   ```
   ⚠️ This disables `HostbasedAuthentication` — only apply if host-based auth is not in use
2. **Rotate SSH host keys after patching** — if exploitation cannot be ruled out, regenerate host keys to invalidate any previously extracted material
3. **Monitor for unexpected ssh-keysign invocations** — audit SUID binary execution; ssh-keysign should only be called by the ssh daemon, not user processes
4. **Apply upstream kernel patch** — distributions expected to ship fixes within days

---

## Detection

### Process-Level Indicators
- `ssh-keysign` invoked by non-root, non-sshd processes
- Unexpected access to `/etc/ssh/ssh_host_*_key` files from non-root UIDs
- Audit rules: `-a always,exit -F arch=b64 -S execve -F path=/usr/lib/openssh/ssh-keysign -k ssh_keysign_exec`

### File Access Monitoring
- Inotify or auditd watch on `/etc/ssh/ssh_host_*_key` — any read by a non-root UID is anomalous

---

## MITRE ATT&CK Mapping

| Tactic | Technique | Notes |
|---|---|---|
| Privilege Escalation | T1068 — Exploitation for Privilege Escalation | SUID binary abuse to escalate read access |
| Credential Access | T1552.004 — Unsecured Credentials: Private Keys | SSH host private key extraction is primary goal |
| Lateral Movement | T1021.004 — Remote Services: SSH | Stolen host key enables server impersonation |
| Defense Evasion | T1550.003 — Use Alternate Authentication Material | Impersonating server defeats SSH client host verification |

---

## Disclosure Timeline

| Date | Event |
|---|---|
| ~6 years prior | Yann Horn (Google Project Zero) proposes fix for underlying issue — not merged |
| ~May 14, 2026 | Upstream kernel patch merged by Linus Torvalds |
| May 14, 2026 | `_SiCK` analyzes patch commit; publishes working PoC ~1 hour later |
| May 15, 2026 | Profile created; distribution packages still pending |
| **TBD** | Distribution kernel updates expected (Debian, Ubuntu, RHEL, etc.) |

---

## References

- [AllSec.sh — ssh-keysign-pwn: Linux LPE allows unprivileged users to read root-owned files (May 14–15, 2026)](https://allsec.sh/tag/Zero-Day)
- Linux Kernel upstream commit (May 14, 2026) — ssh-keysign privilege logic fix

---

*Profile created: 2026-05-15 | Author: C3PO | Admiralty Grade: B2 | TLP: WHITE*
