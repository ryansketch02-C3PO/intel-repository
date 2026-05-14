# YellowKey — Windows BitLocker Bypass via WinRE

## Identity

| Field | Details |
|---|---|
| **Vulnerability Name** | YellowKey |
| **CVE** | None assigned — **UNPATCHED** |
| **Type** | Security Feature Bypass — BitLocker Full Volume Encryption |
| **Class** | WinRE Feature Update Simulation → VMK Auto-Release |
| **Affected Platforms** | Windows 11 · Windows Server 2022 · Windows Server 2025 |
| **Not Affected** | Windows 10 (confirmed unaffected) |
| **Patch Status** | 🔴 **UNPATCHED** — No CVE, no Microsoft acknowledgment, no timeline |
| **PoC Status** | 🔴 **PUBLIC** — Full PoC on GitHub (Nightmare-Eclipse/YellowKey) |
| **Discovered By** | Chaotic Eclipse / Nightmare-Eclipse (pseudonymous researcher) |
| **Public Disclosure** | May 12, 2026 (published simultaneously with GreenPlasma, on Patch Tuesday) |
| **Exploited in Wild** | ❓ Not yet confirmed — PoC public since May 12 |
| **Physical Access Required** | ✅ YES — attacker must be able to boot the target machine |
| **Threat Level** | 🔴 HIGH — Public PoC, BitLocker bypass on current Windows, physical access required |
| **Admiralty Grade** | A2 — Researcher-confirmed, independently partially verified (Dormann) |
| **ATT&CK** | T1006 (Direct Volume Access) · T1553 (Subvert Trust Controls) · T1005 (Data from Local System) |

---

## Overview

YellowKey is an unpatched BitLocker bypass affecting Windows 11 and Windows Server 2022/2025. By placing specially crafted `FsTx` files on a USB drive (or directly onto the target machine's EFI partition), an attacker who can reboot the machine into Windows Recovery Environment (WinRE) can cause BitLocker to automatically suspend and release the Volume Master Key (VMK) — granting unrestricted access to the encrypted volume without entering any credentials.

The researcher describes the underlying mechanism as "almost feels like a backdoor" — not because the code is malicious, but because the vulnerable behavior is **built into WinRE's Feature Update Simulation path by design**. The PoC doesn't exploit a memory corruption flaw or craft a malicious payload; it weaponizes an existing Windows recovery code path that checks for a `FailRelock` flag and, if set, unlocks the BitLocker volume after recovery without ever re-locking it.

Chaotic Eclipse, the same researcher who previously released BlueHammer, RedSun, and UnDefend, published YellowKey alongside GreenPlasma on May 12, 2026 — the same day as Microsoft's Patch Tuesday — with an explicit threat: *"Next patch tuesday will have a big surprise for you Microsoft."*

---

## Technical Details

### How the Attack Works (TPM-Only, Released PoC)

WinRE includes a code path that simulates a pending Windows Feature Update (FStX). When this path is triggered during recovery, BitLocker's auto-unlock logic interprets the simulated state as a legitimate in-progress update and **suspends BitLocker**, releasing the VMK without requiring a PIN, recovery key, or any credentials.

The PoC exploit process:
1. Prepare a USB drive (or access the target's EFI partition) and copy the crafted `FsTx` directory structure
2. Reboot the target machine into WinRE (hold CTRL during boot, or boot from USB)
3. WinRE detects the `FsTx` files, simulates a pending Feature Update, sets `FailRelock=1`
4. BitLocker suspends and releases the VMK automatically
5. Attacker has a shell with unrestricted access to the BitLocker-protected volume

**No credentials required. No recovery key required. No PIN required** (for the released PoC targeting TPM-only).

### Key Technical Mechanic

The root issue is in WinRE's handling of the `FailRelock` flag. Every Windows 11 and newer Server WinRE image contains code that:
- Searches for `FailRelock` in the recovery environment
- If found and set to `1`, unlocks the BitLocker volume post-recovery and **never re-locks it**

This is a design-level issue in the recovery image, not a parsing bug — which is why Chaotic Eclipse considers it potentially intentional ("backdoor").

### TPM+PIN Status

| Configuration | Released PoC Works | Notes |
|---|---|---|
| TPM-only | ✅ YES | Full PoC released and confirmed |
| TPM+PIN | ⚠️ DISPUTED | Researcher claims a variant works; independent researcher Will Dormann says current PoC requires the PIN and does not bypass it. Researcher has NOT released the TPM+PIN PoC. |
| Stolen drive (no target hardware) | ❌ NO | TPM stores VMK — requires the original device |

**Bottom line:** The released PoC definitively bypasses TPM-only BitLocker on the original device. The TPM+PIN claim is asserted by the researcher but not independently confirmed and the exploit has not been released.

### EFI Partition Variant

The attack does not strictly require a USB drive. An attacker with even brief local access can copy the `FsTx` files directly to the target's **internal EFI System Partition** — making disabling boot from external media an insufficient mitigation.

---

## Affected Versions

| Platform | Affected |
|---|---|
| Windows 11 (all editions) | ✅ YES |
| Windows Server 2022 | ✅ YES |
| Windows Server 2025 | ✅ YES |
| Windows 10 | ❌ NO — confirmed unaffected |
| Windows Server 2019 and older | Unconfirmed — likely unaffected |

---

## Context: Nightmare-Eclipse Escalation Pattern

YellowKey is the fourth public disclosure from Nightmare-Eclipse in approximately six weeks:

| Tool | Disclosed | Type | Status |
|---|---|---|---|
| BlueHammer | April 3, 2026 | LPE (SYSTEM) | ✅ Patched — CVE-2026-33825 |
| RedSun | April 16, 2026 | LPE (SYSTEM) | 🔴 Unpatched — Day 27 |
| UnDefend | April 16, 2026 | Defender DoS/Blind | 🔴 Unpatched — Day 27 |
| YellowKey | May 12, 2026 | BitLocker Bypass | 🔴 Unpatched — Day 1 |
| GreenPlasma | May 12, 2026 | EoP (partial PoC) | 🔴 Unpatched — Day 1 |

The researcher has shifted from Defender-targeted LPE (BlueHammer/RedSun/UnDefend) to **encryption bypass** and **memory object manipulation** (YellowKey/GreenPlasma), broadening the attack surface. The explicit threat of a "big surprise" at the next Patch Tuesday suggests at least one additional disclosure is planned.

---

## Risk Assessment

**Who is at risk:**
- Any organization using BitLocker with TPM-only protectors (the default for many environments) on Windows 11 or Windows Server 2022/2025
- Lost or stolen laptops/servers in TPM-only configuration on affected Windows versions
- Remote-access environments where an attacker can initiate a reboot and has brief physical or hypervisor-level access

**Who is less exposed:**
- Organizations enforcing TPM+PIN (assuming TPM+PIN variant is not independently confirmed)
- Windows 10 endpoints
- Environments with strong physical access controls (locked data centers, etc.)

**The encryption assurance implication:** BitLocker is often the last line of defense for data-at-rest on endpoint devices. If YellowKey is validated at scale, it materially weakens the encryption assurance posture for any organization relying on Windows 11 TPM-only BitLocker — which is the majority of deployments, since TPM+PIN trades security for user convenience.

---

## Mitigation & Remediation

> ⚠️ No official patch available. No CVE assigned. All controls below are compensating measures.

| Action | Priority | Notes |
|---|---|---|
| **Disable WinRE on sensitive endpoints** | 🔴 IMMEDIATE | `reagentc /disable` removes the on-disk recovery environment as an attack redirect target. Trade-off: eliminates self-recovery capability. Keep bootable Windows media + BitLocker recovery keys accessible. |
| **Enforce TPM+PIN BitLocker** | 🔴 HIGH | Current released PoC requires the PIN for TPM+PIN configs (Dormann). Migrating away from TPM-only meaningfully raises the bar until the TPM+PIN variant is confirmed/released. |
| **Disable boot from external media in firmware** | 🟠 HIGH | Raises the bar but does NOT fully mitigate — attacker can stage FsTx on the internal EFI partition with brief local access. Combine with firmware password. |
| **Inventory all Windows 11 / Server 2022/2025 with BitLocker** | 🟠 HIGH | Identify TPM-only vs. TPM+PIN deployments. Flag high-value endpoints for prioritized remediation. |
| **Enforce strong firmware (UEFI) password** | 🟠 HIGH | Prevents unauthorized changes to boot order and reduces WinRE access risk. |
| **Treat lost/stolen Windows 11 devices as potentially compromised** | 🔴 IMMEDIATE | Until patched or TPM+PIN enforced, assume a sophisticated attacker with physical access could access the disk contents. |
| **Monitor MSRC for emergency advisory** | 🟠 HIGH | Watch https://msrc.microsoft.com for CVE assignment or out-of-band patch. |

---

## Detection

- **WinRE access events:** Alert on unexpected WinRE launches — unusual for managed endpoints; should be investigated
- **FsTx directory creation on EFI partitions or removable media:** Behavioral indicator of YellowKey staging
- **BitLocker suspension events** (Event ID 24677 in BitLocker-Management log): Should not occur outside of legitimate update workflows; any unexpected suspension is an IOC
- **reagentc.exe invocation:** Monitor for use — both for attack staging and for defensive `reagentc /disable` confirmation

---

## References

- [GitHub PoC — Nightmare-Eclipse/YellowKey](https://github.com/Nightmare-Eclipse/YellowKey)
- [BleepingComputer — Windows BitLocker zero-day gives access to protected drives](https://www.bleepingcomputer.com/news/security/windows-bitlocker-zero-day-gives-access-to-protected-drives-poc-released/)
- [Chaotic Eclipse Blog — Two more public disclosures](https://deadeclipse666.blogspot.com/2026/05/two-more-public-disclosures-it-will.html)
- [WindowsForum — YellowKey alleged BitLocker bypass analysis](https://windowsforum.com/threads/yellowkey-alleged-bitlocker-bypass-via-winre-usb-on-windows-11-server.418110/)
- [YouTube — YellowKey PoC demonstration (May 13, 2026)](https://www.youtube.com/watch?v=dGQtOFLbDNk)

---

## Update Log

| Date | Update |
|---|---|
| 2026-05-13 | Profile created. Day 1. Public PoC live. No CVE, no patch, no Microsoft acknowledgment. TPM+PIN bypass unconfirmed. |
