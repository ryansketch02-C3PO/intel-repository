# MiniPlasma (CVE-2020-17103) — Executive Summary

**Windows LPE: Patch-Ineffective Zero-Day | CVSS 7.8 | Full Public PoC | SYSTEM Shell | Unpatched**

A Windows local privilege escalation vulnerability in `cldflt.sys` (Cloud Files Mini Filter Driver), originally patched in December 2020 as CVE-2020-17103, has been confirmed **still fully exploitable on fully patched Windows 11 systems as of May 2026**. Security researcher Nightmare-Eclipse (also behind RedSun, UnDefend, YellowKey, and GreenPlasma) released a full weaponized PoC dubbed **MiniPlasma** that spawns a SYSTEM shell from a standard user account. BleepingComputer and Will Dormann (Tharros) independently confirmed the exploit works against the latest May 2026 Patch Tuesday updates. Microsoft has issued no response or emergency patch. The exploit does not work on Windows 11 Insider Preview Canary, suggesting a quiet in-progress fix. No CISA KEV listing as of May 18, 2026.

*TLP: WHITE | Admiralty: A1 | Created: 2026-05-18*
