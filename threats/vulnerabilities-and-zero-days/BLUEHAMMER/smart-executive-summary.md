# BlueHammer — Smart Executive Summary

**A publicly available exploit gives any local Windows user full system control. No patch exists.**

**Why it matters:** Every fully patched Windows 10 and Windows 11 machine in your organization is currently vulnerable. Working exploit code is on GitHub. Ransomware groups weaponize public exploits like this within days.

---

**What happened:** On April 3, 2026, a frustrated researcher dumped a working Windows zero-day directly to GitHub — no warning, no CVE, no patch. It's been independently confirmed by multiple security firms.

**What it does:** Any low-privileged user — a contractor, a compromised account, anyone with local access — can escalate to full SYSTEM control in under a minute.

**How it works:** It chains four legitimate Windows features (Defender updates, Volume Shadow Copy, Cloud Files API, and oplocks) in a sequence that briefly exposes credential files. No hacking of the kernel required.

**The catch:** Microsoft's antivirus detects the original file — but a basic recompile defeats it. The underlying technique is undetected by signature-based tools.

---

**What to do now:**

- **Enable Credential Guard** — blocks the credential theft step. Highest-impact action available today.
- **Deploy LAPS** — randomizes local admin passwords so a stolen hash can't spread laterally.
- **Behavioral EDR** — hunt for VSS enumeration and Cloud Files API calls from unexpected processes.
- **Alert on Event IDs 4723/4724** — rapid local admin password change + immediate revert is a BlueHammer fingerprint.
- **Patch immediately** when Microsoft releases a fix. Watch for Patch Tuesday.

---

**The bottom line:** No patch. Public exploit. Confirmed working. Treat this as an active risk until Microsoft acts.

*TLP: WHITE | Admiralty Grade: A2 | Last updated: 2026-04-14*
