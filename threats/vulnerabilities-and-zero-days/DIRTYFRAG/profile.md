# Dirty Frag — CVE-2026-43284 + CVE-2026-43500

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | Dirty Frag |
| **Aliases** | "Copy Fail 2: Electric Boogaloo" (second public exploit targeting same CVEs) |
| **CVEs** | **CVE-2026-43284** (xfrm-ESP / IPSec) + **CVE-2026-43500** (RxRPC) |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | Page Cache Write via In-Place Decryption Fast Paths — same class as Dirty Pipe (CVE-2022-0847) and Copy Fail (CVE-2026-31431) |
| **Affected Components** | Linux kernel `esp4`, `esp6` (IPSec ESP) modules + `rxrpc` (RxRPC/AFS) module |
| **Affected Kernel Versions** | Linux kernel **4.10 through 7.0** |
| **Affected Distributions** | Ubuntu (all releases) · RHEL 8/9/10 · AlmaLinux 8/9/10 · Fedora 44 · openSUSE Tumbleweed · CentOS Stream 10 · Debian · Rocky Linux · CloudLinux · virtually all enterprise distributions |
| **Not Affected** | Kernels < 4.10; systems with esp4/esp6/rxrpc modules blacklisted |
| **CVE-2026-43284 (ESP) CVSS v3.1** | **8.8 HIGH** (kernel.org CNA assessment) |
| **CVE-2026-43500 (RxRPC) CVSS v3.1** | **7.8 HIGH** (Canonical assessment; NVD pending) |
| **Regression Introduced** | CVE-2026-43284: January 2017 — commit `cac2661c53f3` (ESP in-place decryption fast path) · CVE-2026-43500: June 2023 (same pattern applied to RxRPC) |
| **Public Disclosure** | May 7, 2026 — embargo broken before coordinated patches; working PoC published same day |
| **Discovered By** | Hyunwoo Kim (@v4bel) — same researcher who discovered Copy Fail (CVE-2026-31431) |
| **CVE-2026-43284 Patch** | ✅ Upstream patched — mainline commit `f4c50a4034e6`; distro patches rolling out |
| **CVE-2026-43500 Patch** | ⚠️ **NO PATCH** — Reserved; NVD entry pending as of May 11, 2026 |
| **Overall Patch Status** | ⚠️ PARTIAL — ESP half patched upstream + major distros; RxRPC half unpatched |
| **PoC Status** | 🔴 **PUBLIC** — Working single-command root PoC published May 7, 2026; second exploit "Copy Fail 2: Electric Boogaloo" also public |
| **Active Exploitation** | 🔴 **CONFIRMED** — Microsoft observing limited in-the-wild exploitation (SSH → ELF binary → `su` LPE chain); campaign targeting GLPI instances observed |
| **CISA KEV** | ❌ Not listed as of May 11, 2026 |
| **Container Escape** | 🔴 **CONFIRMED PATH** — Any container with access to `AF_KEY`, `XFRM netlink`, or `AF_RXRPC` sockets escapes to host root |
| **Threat Level** | 🔴 HIGH — Public PoC; active exploitation; 9-year kernel exposure; independent of Copy Fail mitigations |
| **Admiralty Grade** | A1 — Multiple major distro advisories, Sysdig TRT, Wiz, Microsoft, Ubuntu/Canonical, AlmaLinux, CloudLinux, researcher write-up |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) · T1611 (Escape to Host) · T1055 (Process Injection — via page cache poisoning) · T1574 (Hijack Execution Flow) |

---

## Overview

Dirty Frag is the **second universal Linux local privilege escalation vulnerability in eight days**, disclosed on May 7, 2026 — one week after Copy Fail (CVE-2026-31431). Both vulnerabilities belong to the same **page cache write** class pioneered by Dirty Pipe (CVE-2022-0847), where an attacker manipulates Linux's shared in-memory file cache to silently overwrite the contents of privileged binaries without touching the on-disk file.

Where Copy Fail exploited the kernel's AEAD crypto API (`algif_aead`), Dirty Frag exploits **two separate in-place decryption fast paths in networking subsystems** — IPSec ESP and RxRPC — and chains them together to route around distribution-specific hardening. The result: **an unprivileged local user can write arbitrary shellcode into the shared page cache of `/usr/bin/su` and gain root in a single command**, on virtually any Linux system, regardless of whether the Copy Fail mitigation is applied.

The embargo was broken by an unrelated third party before distributions could coordinate patches, forcing researcher Hyunwoo Kim to publish ahead of schedule with a working exploit already in the wild. **Defenders had zero mitigation window.**

---

## The Bug — Root Cause

### Linux Page Cache — Background

Linux maintains a performance cache of recently-used file contents in RAM, called the **page cache**. When any process reads `/usr/bin/su`, the kernel doesn't go to disk — it serves bytes directly from shared in-memory pages. This cache is shared across all processes. File permissions normally prevent unprivileged processes from writing to those pages.

### The Flaw

Two Linux networking features use an **in-place decryption fast path** optimization:
- **IPSec ESP** (`esp4`, `esp6`): Decrypts received packets directly into the buffer they arrived in — instead of copying to a fresh allocation first
- **RxRPC** (`rxrpc`): Same pattern, applied to the RxRPC transport (used for AFS)

**The kernel doesn't verify who owns the memory before decrypting into it.**

This means an attacker can borrow a page from the page cache of `/usr/bin/su` — using standard `vmsplice()` + `splice()` syscalls — and then configure the kernel's decryption using their own chosen key, so the "decrypted output" is attacker-controlled bytes. The kernel dutifully writes those bytes into the borrowed page, which is the shared in-memory copy of `su` that every process on the system uses.

**Result:** `/usr/bin/su` is silently rewritten in RAM with attacker shellcode. The next invocation of `su` runs the attacker's code as root.

### Why Two CVEs

| CVE | Subsystem | Introduced | Bypass Context |
|-----|-----------|-----------|---------------|
| CVE-2026-43284 | `esp4` / `esp6` (IPSec xfrm) | January 2017 | Requires unprivileged user namespaces (`CAP_NET_ADMIN` in namespace); blocked on Ubuntu by AppArmor |
| CVE-2026-43500 | `rxrpc` (RxRPC/AFS) | June 2023 | Does **not** require namespace creation; but `rxrpc.ko` not shipped in all distros |

The exploit chains them to cover each other's blind spots:
- **Systems allowing unprivileged user namespaces** (most defaults): ESP variant (CVE-2026-43284)
- **Ubuntu** (restricted namespaces via AppArmor, but `rxrpc.ko` ships by default): RxRPC variant (CVE-2026-43500)

Together they cover virtually all major Linux configurations.

### Relation to Prior Vulnerabilities

| Vulnerability | CVE | Class | Differences |
|--------------|-----|-------|-------------|
| **Dirty Pipe** | CVE-2022-0847 | Page Cache Write | Race condition in pipe buffer flags; patched 2022 |
| **Copy Fail** | CVE-2026-31431 | Page Cache Write | Via `algif_aead` AEAD crypto API; blacklisting `algif_aead` mitigates |
| **Dirty Frag** | CVE-2026-43284 + CVE-2026-43500 | Page Cache Write | Via IPSec ESP + RxRPC; **independent of Copy Fail**; `algif_aead` blacklist provides **no protection** |

Note: The January 2017 commit that introduced CVE-2026-43284 was also the root cause of CVE-2022-27666 (CVSS 7.8, buffer overflow). The same dangerous code has been patched before — and the in-place decryption pattern survived.

---

## Exploit Mechanics — ESP Variant (CVE-2026-43284)

```
1. Open a pipe; vmsplice() to attach /usr/bin/su page cache as a paged fragment

2. Configure an XFRM Security Association (SA) via AF_KEY or XFRM netlink:
   - Attacker-chosen cipher (e.g., AES-CBC)
   - Attacker-chosen key
   - Attacker-chosen SPI

3. splice() pipe into a UDP socket configured for ESP encapsulation

4. Send a crafted ESP datagram to loopback (127.0.0.1)

5. Kernel decrypts ESP payload in-place across the spliced page
   → Deposits 192-byte x86_64 stub into shared /usr/bin/su page cache:
      setuid(0); setgid(0); execve("/bin/sh")

6. Run /usr/bin/su → attacker shellcode executes as root
```

**Syscalls required:** `socket`, `setsockopt`, `bind`, `vmsplice`, `splice`, `sendmsg` — all standard, widely available, no special permissions beyond local shell access.

**Success rate:** Deterministic logic bug. No race condition. No timing window. Kim reports very high success rates with minimal kernel panic risk — unlike many LPE exploits that cause system instability on failure.

---

## Active Exploitation

Microsoft's security blog (published alongside disclosure) confirmed **limited in-the-wild exploitation**:

**Observed attack chain:**
```
External connection → SSH access
    ↓
Spawn interactive shell
    ↓
Stage + execute ELF binary (./update)
    ↓
Privilege escalation via 'su' (Dirty Frag or Copy Fail — Microsoft notes overlap)
    ↓
Modify GLPI LDAP authentication file
    ↓
Reconnaissance of GLPI directory + system config
    ↓
Access sensitive data + PHP session files
    ↓
Forcibly wipe session files (disrupt active sessions + access session contents)
```

**Target profile:** GLPI (IT asset management / helpdesk platform) instances accessible via SSH. Post-LPE, attackers modified authentication configuration and harvested/destroyed session data.

**Attribution:** Unknown threat actor as of May 11, 2026.

---

## Container Escape

Dirty Frag is **confirmed to create a container-to-host escape path** in container deployments:

- Any container with access to `AF_KEY`, `XFRM netlink`, or `AF_RXRPC` sockets can exploit Dirty Frag
- **Default Docker** seccomp profile: blocks `AF_RXRPC` but does **not** block `AF_KEY` or `XFRM netlink`
- **Default Kubernetes pods** (unconstrained): inherit host kernel; ESP variant exploitable
- Result: compromise of any container with default security posture → host root → full cluster compromise

**No public container escape PoC yet** (Ubuntu advisory notes PoC not published for container path), but the vector is technically confirmed.

---

## Patch Status by Distribution (as of 2026-05-11)

### CVE-2026-43284 (ESP — xfrm)

| Distribution | Status | Action |
|---|---|---|
| Linux mainline | ✅ Patched | Commit `f4c50a4034e6` |
| AlmaLinux 8/9/10 | ✅ Patched | Production repos as of May 8 |
| CloudLinux 7h/8/9/10 | ✅ Patched | Stable rollout; KernelCare livepatch available |
| Debian | ✅ Patched | Advisory issued |
| RHEL 8/9/10 | ⚠️ In progress | AlmaLinux built patch independently ahead of Red Hat |
| Ubuntu (all LTS) | ⚠️ Patches pending | Kernel packages in progress; mitigation guidance available |
| Fedora 44 | ⚠️ In progress | — |
| openSUSE Tumbleweed | ⚠️ Advisory issued | — |
| Rocky Linux | ⚠️ Advisory issued | — |

### CVE-2026-43500 (RxRPC)

| Distribution | Status | Notes |
|---|---|---|
| All distributions | ❌ **UNPATCHED** | No upstream patch as of May 11; CVE assigned, NVD pending |
| AlmaLinux 9/10 | ⚠️ Partially mitigated | Only affected if `kernel-modules-partner` from Devel repo installed |

---

## Mitigation

### Module Blacklist (Immediate — Apply Now)

> ⚠️ **COMPATIBILITY WARNING:** `esp4`/`esp6` are required for IPSec kernel data path (strongSwan, Libreswan, IKEv2 VPN). `rxrpc` is required for AFS clients. **Do not apply on hosts terminating or transiting IPSec tunnels.** Audit before applying.

**One-line mitigation (most Linux distributions):**
```bash
sudo sh -c "printf 'install esp4 /bin/false\ninstall esp6 /bin/false\ninstall rxrpc /bin/false\n' > /etc/modprobe.d/dirtyfrag.conf; rmmod esp4 esp6 rxrpc 2>/dev/null; true"
```

**Ubuntu — also regenerate initramfs:**
```bash
echo "install esp4 /bin/false" | sudo tee /etc/modprobe.d/dirty-frag.conf
echo "install esp6 /bin/false" | sudo tee -a /etc/modprobe.d/dirty-frag.conf
echo "install rxrpc /bin/false" | sudo tee -a /etc/modprobe.d/dirty-frag.conf
sudo update-initramfs -u -k all
sudo rmmod esp4 esp6 rxrpc 2>/dev/null
```

**Verify modules are unloaded:**
```bash
grep -qE '^(esp4|esp6|rxrpc) ' /proc/modules && echo "VULNERABLE: modules loaded" || echo "MITIGATED: modules not loaded"
```

**Remove mitigation after patching:**
```bash
sudo rm /etc/modprobe.d/dirtyfrag.conf
# Ubuntu also: sudo update-initramfs -u -k all
```

### Drop Page Cache (Post-Exploit Cleanup)

If the system may have been targeted before mitigation was applied, drop the page cache to flush any poisoned binaries:
```bash
sudo sh -c "echo 3 > /proc/sys/vm/drop_caches"
```

**Safe on live systems** — only flushes clean cache and dentry/inode entries.

### Extended Module Blacklist (AWS Recommendation)

AWS's 2026-027 bulletin extends the blacklist beyond the three primary modules:
```bash
sudo sh -c "printf 'install esp4 /bin/false\ninstall esp6 /bin/false\ninstall rxrpc /bin/false\ninstall xfrm_user /bin/false\ninstall ipcomp4 /bin/false\ninstall ipcomp6 /bin/false\n' > /etc/modprobe.d/dirtyfrag.conf"
```
> Note: `xfrm_user`, `ipcomp4`, `ipcomp6` may be compiled into the kernel rather than as modules — in that case module blacklisting is not effective.

### Container Hardening

```bash
# Add to seccomp profile — block exploit socket families:
# AF_KEY (socket family 15) — used by ESP variant
# AF_RXRPC (socket family 34) — used by RxRPC variant

# Docker run with restricted seccomp:
docker run --security-opt seccomp=/path/to/custom-profile.json <image>

# Kubernetes: Apply Pod Security Standards + custom seccomp profile
# Default Docker seccomp already blocks AF_RXRPC; add AF_KEY and XFRM netlink explicitly
```

---

## MITRE ATT&CK Mapping

| Tactic | Technique ID | Technique | Notes |
|--------|-------------|-----------|-------|
| Privilege Escalation | T1068 | Exploitation for Privilege Escalation | Core technique — page cache write via ESP/RxRPC |
| Privilege Escalation | T1548.001 | Abuse Elevation Control Mechanism: Setuid | Exploit targets `/usr/bin/su` — setuid binary is the execution vehicle |
| Defense Evasion | T1055 | Process Injection | Shellcode injected into shared page cache of legitimate binary |
| Defense Evasion | T1574 | Hijack Execution Flow | Redirects what code executes when `su` runs without touching on-disk binary |
| Privilege Escalation / Lateral | T1611 | Escape to Host | Confirmed container-to-host escape path on default Docker/K8s |

---

## Detection

### Falco / Sysdig Rules

Sysdig TRT released specific Falco rules:

```yaml
# Dirty Frag — xfrm-ESP Page Cache Poisoning LPE
- rule: Dirty Frag xfrm-ESP Page Cache Poisoning LPE
  desc: Detects unprivileged AF_KEY or XFRM netlink socket use typical of Dirty Frag ESP exploit
  condition: >
    syscall.type = socket and
    fd.net_proto = 15 and  # AF_KEY
    not proc.name in (known_ipsec_daemons) and
    user.uid != 0
  output: >
    Potential Dirty Frag ESP exploit: unprivileged AF_KEY socket (user=%user.name uid=%user.uid
    proc=%proc.name pid=%proc.pid)
  priority: CRITICAL
  tags: [LPE, CVE-2026-43284, dirtyfrag]

# Dirty Frag — RxRPC Page Cache Poisoning LPE
- rule: Dirty Frag RxRPC Page Cache Poisoning LPE
  desc: Detects unprivileged AF_RXRPC socket use — no legitimate non-AFS userspace consumers
  condition: >
    syscall.type = socket and
    fd.net_proto = 34 and  # AF_RXRPC
    not proc.name in (afsd, kafs) and
    user.uid != 0
  output: >
    Potential Dirty Frag RxRPC exploit: unprivileged AF_RXRPC socket (user=%user.name uid=%user.uid
    proc=%proc.name pid=%proc.pid)
  priority: CRITICAL
  tags: [LPE, CVE-2026-43500, dirtyfrag]
```

### Splunk — Privilege Escalation via `su` Following Network Activity

```spl
index=linux_auditd
| eval su_exec=if(exe="/usr/bin/su" AND auid!=0, 1, 0)
| eval suspicious_net=if(syscall IN ("socket","setsockopt","sendmsg") AND uid!=0, 1, 0)
| stats count by host, user, exe, syscall, _time
| where su_exec=1
| join host [
    search index=linux_auditd syscall IN ("socket","setsockopt","bind","vmsplice","splice","sendmsg") NOT uid=0
    | stats count as net_syscalls by host, user
    | where net_syscalls > 3
]
| where count > 0
| eval alert="Potential Dirty Frag: suspicious su after network syscalls"
```

### Splunk — Unauthorized Setuid Binary Modification (Page Cache Indicator)

```spl
index=linux_auditd syscall=openat
| eval setuid_target=if(match(name, "(?:/usr/bin/su|/usr/bin/sudo|/usr/bin/passwd|/bin/su)"), 1, 0)
| where setuid_target=1 AND uid!=0
| stats count by host, user, name, uid
| where count > 0
| eval alert="Possible page cache poisoning of setuid binary"
```

### Behavioral Indicators
- Unprivileged process creating `AF_KEY` socket (family 15) — unusual; only expected from IPSec daemons (strongSwan, Libreswan)
- Unprivileged process creating `AF_RXRPC` socket (family 34) — almost never legitimate outside AFS daemons (`afsd`, `kafs`)
- `vmsplice()` + `splice()` syscall sequence on network socket from non-root process
- `su` execution immediately following network socket operations from same unprivileged process
- ELF binary named `./update` executed from non-standard path (observed in in-the-wild campaign)
- Modification of GLPI authentication files following privilege escalation

---

## Copy Fail Mitigation Does NOT Protect Against Dirty Frag

> ⚠️ **Critical note for organizations that applied the Copy Fail (`algif_aead`) blacklist:**
>
> The Copy Fail mitigation — blacklisting the `algif_aead` module — provides **zero protection** against Dirty Frag. The two vulnerabilities share the same bug class but exploit entirely separate kernel subsystems (AEAD crypto vs. IPSec/RxRPC).
>
> If you patched/mitigated Copy Fail and assumed you were covered: **you are not**. Apply the Dirty Frag mitigation separately.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| January 2017 | CVE-2026-43284 introduced — commit `cac2661c53f3` adds ESP in-place decryption fast path |
| June 2023 | CVE-2026-43500 introduced — same in-place pattern added to RxRPC |
| April 30, 2026 | Hyunwoo Kim reports vulnerabilities to Linux kernel maintainers |
| May 7, 2026 | Third party breaks embargo before patches coordinated; Kim publishes on oss-security with working PoC |
| May 7, 2026 | AlmaLinux, CloudLinux, Debian, SUSE advisories issued; distro patches begin |
| May 8, 2026 | CVE-2026-43284 (ESP) assigned; CVE-2026-43500 (RxRPC) assigned; Sysdig TRT, Wiz, Microsoft advisories |
| May 8, 2026 | AlmaLinux patches reach production repos; CloudLinux KernelCare livepatches available |
| May 8, 2026 | Microsoft confirms limited in-the-wild exploitation |
| May 8, 2026 | "Copy Fail 2: Electric Boogaloo" second public exploit published |
| May 11, 2026 | Profile created; RxRPC patch still unavailable; RHEL/Ubuntu kernel packages pending |

---

## References

- [The Hacker News — Linux Kernel Dirty Frag LPE Exploit Enables Root Access (May 8, 2026)](https://thehackernews.com/2026/05/linux-kernel-dirty-frag-lpe-exploit.html)
- [Researcher write-up — Hyunwoo Kim (@v4bel)](https://dirtyfrag.io)
- [Sysdig TRT — Dirty Frag CVE-2026-43284 and CVE-2026-43500 detection analysis](https://www.sysdig.com/blog/dirty-frag-cve-2026-43284-and-cve-2026-43500-detecting-unpatched-local-privilege-escalation-via-linux-kernel-esp-and-rxrpc)
- [Ubuntu / Canonical Advisory — Dirty Frag fixes available (May 8, 2026)](https://ubuntu.com/blog/dirty-frag-linux-vulnerability-fixes-available)
- [AlmaLinux Advisory — Dirty Frag (May 7, 2026)](https://almalinux.org/blog/2026-05-07-dirty-frag/)
- [CloudLinux Advisory — Dirty Frag mitigation and kernel update](https://blog.cloudlinux.com/dirty-frag-mitigation-and-kernel-update)
- [oss-security disclosure — Hyunwoo Kim (May 7, 2026)](https://www.openwall.com/lists/oss-security/2026/05/07/8)
- [Upstream ESP fix — commit f4c50a4034e6](https://git.kernel.org/pub/scm/linux/kernel/git/netdev/net.git/commit/?id=f4c50a4034e62ab75f1d5cdd191dd5f9c77fdff4)
- [NVD — CVE-2026-43284](https://nvd.nist.gov/vuln/detail/CVE-2026-43284)
- [NVD — CVE-2026-43500](https://nvd.nist.gov/vuln/detail/CVE-2026-43500) *(entry pending)*

---

*Profile created: 2026-05-11 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
