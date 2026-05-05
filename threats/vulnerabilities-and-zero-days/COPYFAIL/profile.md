# Copy Fail — CVE-2026-31431

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | Copy Fail |
| **CVE** | CVE-2026-31431 |
| **GHSA** | GHSA-2274-3hgr-wxv6 |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | CWE-787 — Out-of-bounds Write / Page Cache Poisoning via Crypto API |
| **Affected Product** | Linux Kernel — `algif_aead` module (`AF_ALG` userspace AEAD crypto interface) |
| **Affected Versions** | Kernel 4.14 through 7.0-rc — all kernels from July 2017 to April 2026 (9 years of distributions) |
| **Fixed Versions** | ✅ Linux 7.0 · 6.19.12 · 6.18.22 (and corresponding distro backports) |
| **Regression Introduced** | July 2017 — commit `72548b093ee3` (in-place AEAD optimization) |
| **Fix Commit** | `a664bf3d603d` — reverts in-place optimization; merged ~April 1, 2026 |
| **Attack Vector** | Local — requires any existing code execution on the target (no remote vector) |
| **CVSS v3.1** | **7.8 HIGH** (`CVSS:3.1/AV:L/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H`) |
| **Public PoC** | 🔴 **PUBLIC** — Theori `theori-io/copy-fail-CVE-2026-31431` (~732 bytes Python) |
| **Metasploit Module** | 🔴 **AVAILABLE** — `exploits/linux/local/cve_2026_31431_copy_fail` |
| **CISA KEV** | ✅ **LISTED** — Added to CISA KEV catalog ~May 1–2, 2026 |
| **Exploit Status** | 🔴 **ACTIVE** — CISA KEV listed; Microsoft Defender observing preliminary threat-actor testing activity; widespread exploitation expected imminently |
| **Disclosed By** | Theori Xint Code team (coordinated disclosure with Linux kernel security team) |
| **Disclosed** | April 29, 2026 |
| **Threat Level** | 🔴 HIGH — CISA KEV listed; preliminary exploitation activity observed; trivial post-foothold root on 9 years of Linux builds |
| **Admiralty Grade** | A1 — Theori technical disclosure; confirmed by Sysdig TRT, AlmaLinux, Amazon Linux security teams |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) · T1005 (Data from Local System) · T1543 (Create or Modify System Process) |

---

## Overview

Copy Fail is a local privilege escalation vulnerability in the Linux kernel's `algif_aead` module that lets any unprivileged local user overwrite a setuid binary's page cache and gain a root shell in seconds. A working public exploit exists as a 732-byte Python script. Metasploit module is available.

The vulnerability has been present in every Linux kernel since **July 2017** — nine years of enterprise distributions across Ubuntu, RHEL, Amazon Linux, SUSE, Debian, and their derivatives are all affected. Patching is complete upstream but **lagging significantly in enterprise distribution backports**.

**The comparison to Dirty Pipe (CVE-2022-0847) is instructive:**
- Dirty Pipe required precise pipe buffer timing and version-specific targeting
- Copy Fail is a **straight-line logic flaw** — no races, no timing windows, no crashes
- Dirty Pipe: kernel ≥ 5.8 only
- Copy Fail: **9 years of distributions** — if your Linux was built between 2017 and April 2026, it's vulnerable

**Important constraint:** This is not remotely exploitable on its own. An attacker needs an existing foothold — a shell via web app compromise, phishing, insider access, or container breakout. Once that foothold exists, Copy Fail escalates *any* unprivileged user to root in seconds on unpatched systems. It is best understood as a **foothold-to-root bridge** that dramatically lowers the post-exploitation effort for any attacker who lands on a Linux system.

---

## Root Cause — 9 Years of In-Place Crypto

`algif_aead` exposes the Linux kernel's AEAD (Authenticated Encryption with Associated Data) ciphers to userspace programs via `AF_ALG` sockets. In July 2017, commit `72548b093ee3` switched AEAD operations to **in-place processing** for performance, setting `req->src = req->dst` and chaining tag pages from the source scatterlist into the output scatterlist via `sg_chain()`.

**The flaw:** When userspace feeds the `AF_ALG` socket through `splice()`, those chained tag pages reference the **page cache of the spliced file** — the in-memory cached copy of that file's data. The `authencesn(hmac(sha256),cbc(aes))` algorithm writes 4 bytes of scratch data for Extended Sequence Number rearrangement at offset `assoclen + cryptlen`. Because of the chain, that 4-byte scratch write **lands inside the spliced file's cached data in memory**, bypassing the file's filesystem permissions entirely.

The HMAC verification fails (expected — the attacker controls this), but **the page cache corruption persists**. An attacker with knowledge of the internal layout can target specific offsets, repeat the primitive, and stage arbitrary data into the page cache of any file — including setuid binaries like `/usr/bin/su`.

The fix (merged ~April 1, 2026) simply reverts the 9-year-old optimization: `req->src` and `req->dst` are separated again, eliminating the chain.

---

## Exploit Chain — Four Syscalls to Root

```
Step 1 — Bind AF_ALG socket
  socket(AF_ALG, SOCK_SEQPACKET, 0)
  setsockopt(sock, SOL_ALG, ALG_SET_KEY, ...)
  bind(sock, {sa_family=AF_ALG, alg_type="aead",
              alg_name="authencesn(hmac(sha256),cbc(aes))"}, ...)
       ↓
Step 2 — Splice page cache of /usr/bin/su into crypto pipeline
  splice(fd_of_su, NULL, pipe_write_fd, NULL, page_size, 0)
  splice(pipe_read_fd, NULL, sock_fd, NULL, page_size, 0)
       ↓
Step 3 — Trigger scratch write via recvmsg()
  sendmsg(sock_fd, {AAD bytes 4-7 = target 4-byte payload}, ...)
  recvmsg(sock_fd, ...)
  → HMAC fails (expected) — but 4-byte write already landed in /usr/bin/su's page cache
       ↓
Step 4 — Repeat at successive offsets
  Repeat steps 2–3 targeting different offsets
  → Stage shellcode into /usr/bin/su's cached pages
       ↓
Step 5 — Execute
  Run `su` → kernel serves patched in-memory version → root shell
```

**Total script size: ~732 bytes of Python.** Theori's PoC is public on GitHub. Metasploit module available at `exploits/linux/local/cve_2026_31431_copy_fail`.

**Confirmed working on:** Ubuntu 24.04 · Amazon Linux 2023 · RHEL 10.1 · SUSE 16

---

## Affected Distributions & Patch Status

| Distribution | Status | Notes |
|---|---|---|
| **Linux upstream** | ✅ PATCHED | 7.0, 6.19.12, 6.18.22 |
| **AlmaLinux 9 / 10** | ✅ PATCHED | `kernel-7.0.0-553.42.1.el9` — `dnf update kernel` |
| **Rocky Linux 9 / 10** | ✅ PATCHED | `kernel-7.0.0-553.42.1.el9` — `dnf update kernel` |
| **Ubuntu 26.04 LTS Resolute** | ✅ NOT AFFECTED | Ships kernel not affected by this CVE |
| **Ubuntu 22.04 / 24.04 / 24.10** | ✅ PATCHED | `6.19.12` — `apt update && apt install linux-image-generic` + reboot |
| **Ubuntu 14.04–20.04 LTS** | ⚠️ VERIFY | `kmod` mitigation available; check USN advisory for kernel backport |
| **Debian 12 / 13** | ✅ PATCHED | `6.19.12` — `apt update && apt install linux-image-amd64` + reboot |
| **RHEL 9 / 10** | ✅ PATCHED | `kernel-7.0.0-553.42.1.el9` — `dnf update kernel` + reboot; note: `rmmod`/`modprobe.d` ineffective, SELinux enforcing is interim mitigation |
| **SUSE SLES 15 SP5 / SP6** | ✅ PATCHED | `6.18.22` — `zypper update kernel-default` + reboot |
| **Amazon Linux 2023** | ✅ PATCHED | `6.19.12` — `dnf update kernel` + reboot |
| **Amazon Linux 2 (all extras)** | ⚠️ VERIFY | Check ALAS advisory; AL2 update may lag behind AL2023 |
| **Oracle Linux (UEK)** | ✅ PATCHED | Followed within days of upstream |
| **Arch / Gentoo / rolling** | ✅ PATCHED | Upstream kernel 7.0 available |

> **Any kernel between 4.14 (July 2017) and the fixed versions above is vulnerable.** This covers the vast majority of enterprise Linux deployments that have not already received a distro backport.

---

## Workarounds (Apply Immediately on Unpatched Systems)

### Option 1 — Disable the algif_aead module (recommended)

```bash
# Block module loading permanently
echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf

# Remove if currently loaded
rmmod algif_aead 2>/dev/null || true

# Verify
lsmod | grep algif_aead  # Should return nothing
```

**Trade-off:** Breaks applications using userspace AEAD crypto via `AF_ALG`. In most enterprise environments this is limited to disk encryption tooling (`cryptsetup`, `kcapi`). If you're not using those tools, this is zero-impact.

### Option 2 — Seccomp policy (containers / Kubernetes)

Block `AF_ALG` socket creation via seccomp profile:

```json
{
  "syscalls": [
    {
      "names": ["socket"],
      "action": "SCMP_ACT_ALLOW",
      "args": [
        {"index": 0, "value": 38, "op": "SCMP_CMP_NE"}
      ]
    }
  ]
}
```

`AF_ALG = 38` — blocking `socket()` calls with `domain=38` prevents the mandatory first step of the exploit without affecting general networking.

### Option 3 — SELinux / AppArmor restriction

Restrict `AF_ALG` socket creation to known legitimate processes (cryptsetup, systemd-cryptsetup). Block all other processes from binding to `algif_aead`.

---

## Detection

### Falco Rule (Sysdig TRT — April 30, 2026)

```yaml
- list: known_af_alg_binaries
  items: [cryptsetup, "systemd-cryptse", "systemd-cryptsetup", veritysetup,
          integritysetup, "cryptsetup-resh", kcapi-enc, kcapi-dgst, kcapi-rng, kcapi-sym]

- macro: successful_af_alg_socket
  condition: >
    evt.type = socket and evt.rawres >= 0 and
    (evt.arg.domain contains AF_ALG or evt.rawarg.domain = 38)

- macro: successful_af_alg_seqpacket_socket
  condition: >
    successful_af_alg_socket and
    (evt.arg.type = 5 or evt.arg.type = 2053 or
     evt.arg.type = 524293 or evt.arg.type = 526341)

- rule: Unexpected Process Using Kernel AEAD Crypto Socket
  desc: >
    Detects AF_ALG SEQPACKET socket creation — mandatory first step of CVE-2026-31431.
    SOCK_SEQPACKET filters out legitimate AF_ALG users (hashing, symmetric crypto)
    which use SOCK_DGRAM.
  condition: >
    successful_af_alg_seqpacket_socket and
    not proc.name in (known_af_alg_binaries)
  output: >
    Unexpected process %proc.name opened AF_ALG AEAD kernel crypto socket
    (proc.pid=%proc.pid proc.exe=%proc.exe proc.cmdline=%proc.cmdline
     user.name=%user.name container.name=%container.name)
  priority: CRITICAL
  tags: [host, container, kernel, cve, CVE-2026-31431,
         MITRE_TA0004_privilege_escalation, MITRE_T1068]
```

### Additional Behavioral Indicators

| Indicator | Description | Confidence |
|---|---|---|
| `AF_ALG` socket type `SOCK_SEQPACKET` (type=5) from non-crypto process | First step of exploit chain | 🔴 HIGH |
| `splice()` from an `AF_ALG` socket to a setuid binary file descriptor | Core exploit primitive | 🔴 HIGH |
| Unexpected privilege transition: unprivileged UID → UID 0 following kernel crypto syscalls | Root escalation via Copy Fail | 🔴 HIGH |
| `su` or other setuid binary executed immediately after `AF_ALG` socket activity from same process | Exploit final step | 🔴 HIGH |
| `algif_aead` module loaded on system where it is not expected | Pre-exploit check | 🟡 MEDIUM |
| Unexpected `setuid` binary modification timestamp change | Page cache poisoning artifact | 🟡 MEDIUM |

### Kernel Audit Rule

```bash
# Audit AF_ALG socket creation (socket family 38)
auditctl -a always,exit -F arch=b64 -S socket -F a0=38 -k af_alg_audit
```

---

## Mitigations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Apply distro patch** — check if your distro has a backport available; upgrade kernel immediately if so |
| 🔴 IMMEDIATE | **Disable algif_aead module** (workaround above) on all systems where distro patch is unavailable and AEAD userspace crypto is not required |
| 🔴 HIGH | **Block AF_ALG socket creation** via seccomp on all container workloads and Kubernetes nodes — stops the exploit even on unpatched kernels |
| 🔴 HIGH | **Restrict interactive shell access** on multi-tenant systems, CI/CD runners, and shared build hosts — Copy Fail is only exploitable with local code execution |
| 🟡 HIGH | **Deploy Falco/Sysdig detection rule** — runtime detection catches exploit attempt before root is achieved |
| 🟡 HIGH | **Monitor for unexpected UID 0 transitions** via process audit — especially on container hosts and shared systems |
| 🟡 MEDIUM | **Audit SELinux/AppArmor policies** — ensure AF_ALG socket binding is restricted to known disk-encryption tooling |
| 🟡 MEDIUM | **Harden container runtimes** — Docker/containerd default seccomp profiles may not block AF_ALG; verify and update if needed |

---

## High-Risk Deployment Scenarios

| Scenario | Risk | Notes |
|---|---|---|
| Multi-tenant Linux hosts with shell access | 🔴 CRITICAL | Any user → root in seconds |
| Kubernetes nodes | 🔴 CRITICAL | Container escape → node root; combined with existing container breakout = full cluster compromise |
| CI/CD runners (shared build infrastructure) | 🔴 CRITICAL | Untrusted code runs as unprivileged user; Copy Fail yields runner host root; can compromise build artifacts |
| VMs with multiple non-root users | 🔴 HIGH | Standard post-foothold escalation |
| Containers with CAP_NET_BIND_SERVICE or host AF_ALG access | 🔴 HIGH | Depends on seccomp profile |
| Single-user workstations / isolated servers | 🟡 MEDIUM | Still exploitable; lower likelihood of attacker having initial foothold |
| Talos Linux / immutable OS distributions | ✅ LOW | No interactive users, no setuid binaries — not exploitable in practice |

---

## Disclosure Timeline

| Date | Event |
|---|---|
| July 2017 | Commit `72548b093ee3` introduces in-place AEAD optimization — vulnerability introduced |
| ~April 1, 2026 | Fix commit `a664bf3d603d` merged into mainline Linux kernel |
| April 22, 2026 | CVE-2026-31431 published (NVD/MITRE); GitHub advisory GHSA-2274-3hgr-wxv6 |
| April 29, 2026 | Theori Xint Code team public disclosure — technical write-up + PoC released (`theori-io/copy-fail-CVE-2026-31431`) |
| April 30, 2026 | Sysdig TRT analysis published; Falco detection rule released; Metasploit module available |
| May 1, 2026 | AlmaLinux patched kernel released; Amazon Linux advisory updated (fix pending); CERT-EU advisory |
| May 1, 2026 | Microsoft Defender analysis published; CISA adds CVE-2026-31431 to KEV catalog; preliminary threat-actor testing activity observed |
| May 1, 2026 | Ubuntu releases `kmod` mitigation package for all affected LTS releases (14.04–25.10); confirms Ubuntu 26.04 Resolute not affected |
| May 1, 2026 | C3PO profile created (ZD-014) |
| May 4, 2026 | Profile updated: CISA KEV confirmed, Ubuntu mitigation package status, exploitation escalation expected |
| May 5, 2026 | Kernel patches released for RHEL 9/10, AlmaLinux, Rocky Linux, Amazon Linux 2023, SUSE, Debian, Ubuntu — most major distros now patched; rootless container scope clarified; `rootsecdev/cve_2026_31431` second public PoC noted |

---

## References

- [Sysdig TRT — CVE-2026-31431: "Copy Fail" Linux kernel flaw lets local users gain root in seconds (April 30, 2026)](https://www.sysdig.com/blog/cve-2026-31431-copy-fail-linux-kernel-flaw-lets-local-users-gain-root-in-seconds)
- [Theori — Copy Fail technical disclosure + PoC](https://github.com/theori-io/copy-fail-CVE-2026-31431)
- [AlmaLinux — CVE-2026-31431 Copy Fail ready for testing (May 1, 2026)](https://almalinux.org/blog/2026-05-01-cve-2026-31431-copy-fail/)
- [Amazon Linux Security Center — CVE-2026-31431](https://explore.alas.aws.amazon.com/CVE-2026-31431.html)
- [Metasploit — exploits/linux/local/cve_2026_31431_copy_fail](https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/linux/local/cve_2026_31431_copy_fail.rb)
- [GHSA-2274-3hgr-wxv6 — GitHub Security Advisory](https://github.com/advisories/GHSA-2274-3hgr-wxv6)
- [Microsoft Defender — CVE-2026-31431 analysis + hunting guidance (May 1, 2026)](https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/)
- [Ubuntu Security — Copy Fail fixes available (April 30, 2026)](https://ubuntu.com/blog/copy-fail-vulnerability-fixes-available)
- [CERT-EU Security Advisory 2026-005](https://cert.europa.eu/publications/security-advisories/2026-005/)
- [Xint — Copy Fail Part 2: From Pod to Host (Kubernetes container escape)](https://xint.io/blog/copy-fail-linux-distributions) *(Part 2 pending publication)*

---

*Profile created: 2026-05-01 | Updated: 2026-05-05 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
