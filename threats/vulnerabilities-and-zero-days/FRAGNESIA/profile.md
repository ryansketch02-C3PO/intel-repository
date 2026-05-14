# Fragnesia — Linux Kernel LPE (Dirty Frag Class — Logic Bug Variant)

## Identity

| Field | Details |
|---|---|
| **Alias** | Fragnesia |
| **CVE** | **Not yet assigned** — additional CVEs pending; belongs to Dirty Frag vulnerability class |
| **Related CVEs** | CVE-2026-43284 (ESP / Dirty Frag parent), CVE-2026-43500 (RxRPC / Dirty Frag parent) — see ZD-017 |
| **Vendor Advisory** | Linux kernel upstream patch merged May 13, 2026 |
| **Type** | Local Privilege Escalation (LPE) |
| **Class** | Logic Bug — XFRM/ESP and TCP subsystem; improper page cache write primitive |
| **Affected Systems** | All Linux kernel versions **before May 13, 2026 upstream patch** (all major distributions) |
| **Patch Status** | ⚠️ **PARTIAL** — Upstream Linux kernel patched May 13, 2026; **distribution packages (Debian, Ubuntu, RHEL, etc.) still pending** |
| **CVSS** | Not yet scored; assessed **HIGH** (same class as Dirty Frag; more reliable exploitation) |
| **Exploit Maturity** | 🔴 **Public PoC** — Full exploit with page cache corruption published May 13, 2026 by William Bowling (Zelic) |
| **Disclosed** | 2026-05-13 |
| **Discovered By** | William Bowling (Zelic Security Research) |
| **Threat Level** | 🔴 HIGH — Public PoC achieving root shell; more reliable than Dirty Frag (no race condition); all major Linux distros still pending patches |
| **Admiralty Grade** | A2 — Researcher-confirmed full PoC; upstream kernel patched |
| **ATT&CK** | T1068 (Exploitation for Privilege Escalation) · T1611 (Escape to Host — container breakout) |

---

## Overview

**Fragnesia** is a newly disclosed Linux kernel local privilege escalation vulnerability in the **XFRM/ESP and TCP subsystem** (IPSec processing stack). It belongs to the **Dirty Frag vulnerability class** (see ZD-017) but represents a **more dangerous exploitation variant** than the original Dirty Frag.

Unlike Dirty Frag (CVE-2026-43284 + CVE-2026-43500), which required exploiting a **race condition** to achieve the page cache write primitive, Fragnesia abuses a **logic bug** — no race condition, no timing windows, no repeated attempts. Exploitation is **deterministic and reliable**.

Researcher William Bowling of **Zelic** disclosed the flaw alongside a full proof-of-concept exploit that achieves a **memory write primitive** in the kernel, used to corrupt the page cache memory of the `/usr/bin/su` binary to obtain a root shell. The same kernel subsystem vulnerability was described as affecting all Linux kernels released before May 13, 2026.

**Key difference from Dirty Frag:**

| Property | Dirty Frag (ZD-017) | Fragnesia (ZD-027) |
|---|---|---|
| Exploitation type | Race condition | Logic bug (deterministic) |
| Reliability | Moderate — requires timing | **High — no race condition** |
| Kernel subsystem | XFRM/ESP + RxRPC | XFRM/ESP + TCP |
| Patch status | Partial — ESP patched; RxRPC pending | Upstream patched May 13 |
| Public PoC | Yes | Yes — full root shell |

---

## Technical Analysis

### Root Cause

Fragnesia exploits a **logic bug** in the interaction between the Linux kernel's **XFRM (IPSec) ESP processing** and **TCP subsystem** path — specifically in how in-place decryption of ESP-encrypted TCP data allows controlled writes to kernel page cache memory.

The bug enables an attacker to obtain a **controlled page cache write primitive**: the ability to write to arbitrary memory pages backing filesystem objects, bypassing normal permissions. By targeting the `/usr/bin/su` binary's page cache, the attacker can overwrite the binary's code pages in memory with attacker-controlled instructions, then execute `su` to trigger the modified code and obtain a root shell.

### Exploitation Path

1. **Attacker achieves local access** (standard user shell, container escape vector, or any unprivileged process)
2. **Trigger logic bug** via crafted ESP-over-TCP packet processing in the kernel
3. **Obtain page cache write primitive** — controlled write to any kernel page cache region
4. **Corrupt `/usr/bin/su` page cache** — overwrite `su` binary's in-memory code pages
5. **Execute `su`** — triggers attacker-controlled code with SUID root privileges
6. **Root shell achieved**

### Why This Is Worse Than Dirty Frag

Dirty Frag (ZD-017) requires a race condition between two kernel code paths — timing-dependent, sometimes requires multiple attempts, and can be influenced by system load. Fragnesia's logic bug is **deterministic**:

- Works reliably on first attempt
- Not affected by system load or timing
- Can be executed from containerized environments (container escape path)
- Does not require any special prerequisites beyond local process execution

### Container Escape Vector

As with Dirty Frag, Fragnesia presents a **container escape path** on Linux hosts running Docker, Kubernetes, or similar container runtimes where the host kernel is shared. A compromised container process exploiting Fragnesia can escape container isolation and achieve root on the host system.

---

## Affected Systems

| System | Status |
|---|---|
| All major Linux distros (Debian, Ubuntu, RHEL, AlmaLinux, Rocky, Fedora) | ✅ **VULNERABLE** — Upstream fix exists; distribution packages still being built/published |
| Linux kernel ≥ May 13, 2026 upstream build | ✅ Patched upstream |
| Cloud VMs running unpatched Linux kernels | ✅ **VULNERABLE** — Treat as urgent |
| Docker/Kubernetes environments | ✅ **VULNERABLE** + container escape risk |

---

## Relationship to Dirty Frag (ZD-017)

Fragnesia and Dirty Frag are **distinct vulnerabilities** within the same kernel subsystem family:

- **Dirty Frag (ZD-017)**: CVE-2026-43284 (XFRM/ESP) + CVE-2026-43500 (RxRPC); race condition; ZD-017 still has RxRPC component **unpatched**
- **Fragnesia (ZD-027)**: New CVEs (unassigned); XFRM/ESP + TCP; **logic bug** — more reliable; upstream patched May 13 for this path

The Copy Fail (ZD-014) → Dirty Frag (ZD-017) → Fragnesia (ZD-027) sequence represents an **active vulnerability research campaign** targeting Linux kernel page cache primitives, each disclosure building on the previous.

**Defenders still patching Copy Fail (CISA KEV deadline May 15, 2026) and Dirty Frag should now prioritize Fragnesia as well, noting that distribution packages for all three may arrive simultaneously.**

---

## Patch / Mitigation

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | Apply **all available Linux kernel updates** when distribution packages are released — treats all three Dirty Frag class vulnerabilities simultaneously |
| 🔴 HIGH | **CISA KEV deadline May 15**: Copy Fail (ZD-014) patch deadline is *tomorrow* — that patch update may also address Fragnesia for some distributions |
| 🟠 MEDIUM | **Container isolation**: Review container security contexts; ensure containers do not run with CAP_NET_RAW or IPSec-related capabilities that could be leveraged for the XFRM path |
| 🟠 MEDIUM | **Monitor exploit activity**: Alert on `/usr/bin/su` execution from unexpected parent processes or container contexts |
| 🟡 MEDIUM | Apply **Copy Fail mitigations** (already noted in ZD-014/ZD-017): disable CONFIG_XFRM_INTERFACE or restrict IPSec where not required |

---

## A&D / Critical Infrastructure Relevance

Any Linux-based infrastructure — including:

- **Linux servers** in data centers, cloud deployments
- **Containerized workloads** (Docker, Kubernetes clusters)
- **Embedded Linux** in ICS/OT environments
- **Development build servers** where source code access provides exploitation paths

...is potentially vulnerable. The high reliability of Fragnesia (no race condition) makes it a more accessible exploitation target for mid-tier threat actors who lacked the timing expertise to weaponize Dirty Frag.

---

## References

- Cyber Threat Brief May 14, 2026 (YouTube transcript — Bleeping Computer / Zelic sourced)
- William Bowling (Zelic) — vulnerability researcher; PoC disclosed May 13, 2026
- Linux kernel upstream patch — merged May 13, 2026
- Related: ZD-017 (DIRTYFRAG) · ZD-014 (COPYFAIL)

---

*Profile created: 2026-05-14 | Author: C3PO | ZD-029 | Admiralty Grade: A2 | TLP: WHITE*
