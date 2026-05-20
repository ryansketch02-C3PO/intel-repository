# 🔬 Chaotic Eclipse — Threat Actor Profile

**Scoundrel #042**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | Chaotic Eclipse |
| **Aliases** | Nightmare-Eclipse (GitHub) · deadeclipse666 (Blogspot) · @ChaoticEclipse0 (X/Twitter) |
| **MITRE ATT&CK ID** | None assigned |
| **Type** | Independent Security Researcher — Protest Disclosure / Adversarial Whistleblower |
| **Origin** | Unknown — identity not publicly unmasked |
| **Sponsor** | None — assessed as independent individual; no state attribution |
| **Active Since** | March 2026 (current persona) — prior research identity unknown |
| **Status** | 🔴 Active — explicit threat of continued disclosure |
| **Motivation** | Personal grievance against Microsoft MSRC · Protest against vulnerability disclosure process |
| **Threat Level** | 🟠 HIGH (indirect) — not a traditional attacker; tooling actively weaponized by third-party threat actors |
| **Admiralty Grade** | A3 — Researcher identity unconfirmed; tools verified by independent researchers |
| **TLP** | TLP:WHITE |

---

## ⚠️ Classification Note

Chaotic Eclipse is **not a traditional threat actor**. They do not conduct intrusions, operate ransomware, or target victims. They are a pseudonymous security researcher who has published unpatched Windows exploit code as a form of protest against Microsoft's Security Response Center (MSRC).

The threat this profile tracks is **indirect but material**: every tool they publish is immediately weaponized by third-party threat actors. Huntress confirmed real-world exploitation of their tools within 24–48 hours of release in all confirmed cases. The researcher is the source; the downstream risk is significant.

---

## Overview

Chaotic Eclipse emerged in late March 2026 when the `Nightmare-Eclipse` GitHub account was created — six days before the first exploit drop. Their blog post opening stated *"I didn't plan to restart this blog and create a new GitHub account"* — indicating a prior research identity that was deliberately shed for anonymity before going public.

The disclosure campaign began on **April 3, 2026** with BlueHammer and a pointed message to Microsoft: *"I was not bluffing Microsoft, and I'm doing it again."* The phrase "doing it again" and the reference to "previous times" both imply a history of prior interactions and warnings to Microsoft that preceded the public disclosures.

By May 13, 2026, the researcher has published **five distinct Windows exploit tools** across three disclosure events, all targeting Microsoft products, with explicit threats of further disclosures:

| # | Tool | Disclosed | Type | Status |
|---|---|---|---|---|
| 1 | BlueHammer | Apr 3, 2026 | LPE → SYSTEM (Defender TOCTOU + SAM read) | ✅ Patched — CVE-2026-33825 |
| 2 | RedSun | Apr 16, 2026 | LPE → SYSTEM (Defender cloud-file write) | 🟡 POTENTIALLY SILENTLY PATCHED (researcher claim; unconfirmed; Day 33) |
| 3 | UnDefend | Apr 16, 2026 | Defender DoS / definition block + telemetry falsification | 🔴 Unpatched — Day 33 |
| 4 | YellowKey | May 12, 2026 | BitLocker bypass via WinRE FStX simulation | 🔴 Unpatched — Day 7 |
| 5 | GreenPlasma | May 12, 2026 | EoP / CTFMON arbitrary section creation (partial PoC) | 🔴 Unpatched — Day 7 |

---

## The MSRC Dispute

The researcher submitted what became BlueHammer to Microsoft's Security Response Center privately. By their account, MSRC mishandled, dismissed, or actively mistreated them. Key quotes from the researcher's blog:

> *"they told personally by them that they will ruin my life and they did"*
> *"they mopped the floor with me and pulled every childish game they could"*
> *"left me homeless with nothing"*

These claims go beyond procedural frustration — they allege personal retaliation and financial harm. Microsoft has not responded to these specific allegations, issuing only a generic statement: *"We support coordinated vulnerability disclosure, a widely adopted industry practice."*

**The video requirement friction point:** Will Dormann (principal vulnerability analyst, Tharros) noted: *"MSRC used to be quite excellent to work with. But to save money Microsoft fired skilled people, leaving flowchart followers. I wouldn't be surprised if Microsoft closed the case after the reporter refused to submit a video of the exploit, since that's apparently an MSRC requirement now."* This is assessed as a likely contributing factor.

**The credit omission:** When Microsoft patched BlueHammer as **CVE-2026-33825** on April 14, 2026, they credited **Zen Dodd and Yuanpei Xu** — not Chaotic Eclipse. Two days later, RedSun and UnDefend were dropped. The timing is not coincidental. Whether the credit omission was deliberate or reflected a parallel independent discovery is unknown.

**The named callout:** The researcher's BlueHammer post included: *"special thanks to Tom Gallagher!"* — Tom Gallagher is the VP of Engineering for MSRC. This was a direct, personal attribution of blame to senior MSRC leadership.

---

## Technical Profile

Chaotic Eclipse demonstrates **exceptional depth in Windows internals**, specifically around security subsystem interactions. Across five tools, they have shown mastery of:

| Domain | Tools |
|---|---|
| Windows Defender update/remediation workflow internals | BlueHammer, RedSun, UnDefend |
| Volume Shadow Copy Service (VSS) timing and race conditions | BlueHammer |
| Cloud Files API (`cldapi.dll`) — used as primary attack primitive | BlueHammer, RedSun, GreenPlasma |
| NTFS junctions and opportunistic locks (oplocks) | BlueHammer, RedSun |
| CTFMON.exe named object initialization and `BaseNamedObjects` | GreenPlasma |
| Registry symbolic links (`REG_OPTION_CREATE_LINK`) | GreenPlasma |
| WinRE Feature Update Simulation code paths + BitLocker VMK handling | YellowKey |
| DACL manipulation via `SetEntriesInAcl` + `TreeSetNamedSecurityInfo` | GreenPlasma |

The recurring exploitation of `cldapi.dll` across three separate, distinct tools indicates a deep and systematic audit of that library specifically — not opportunistic discovery. The researcher has likely identified it as a durable attack surface and continues to mine it.

**Tool quality:** BlueHammer was released with known bugs ("buggy but functional") — likely intentional, leaving room for improvement by operators while ensuring plausible deniability about enabling mass exploitation. RedSun and UnDefend were more polished. GreenPlasma was deliberately made partial, withholding the SYSTEM shell component as a calculated tease.

---

## Behavioral Pattern & Escalation Timeline

| Date | Action | Microsoft Response |
|---|---|---|
| ~Mar 2026 | MSRC dispute occurs; researcher claims retaliation | N/A |
| Mar 27, 2026 | `Nightmare-Eclipse` GitHub account created | N/A |
| Apr 3, 2026 | **BlueHammer** dropped — *"I was not bluffing Microsoft, and I'm doing it again"* | Patched Apr 14 as CVE-2026-33825; Zen Dodd + Yuanpei Xu credited |
| Apr 16, 2026 | **RedSun + UnDefend** dropped — 2 days after credit omission | No response; both remain unpatched |
| May 12, 2026 | **YellowKey + GreenPlasma** dropped — on Patch Tuesday; *"Next patch tuesday will have a big surprise for you Microsoft"* | No response |
| May 13, 2026 | RedSun + UnDefend at Day 27 unpatched; YellowKey + GreenPlasma at Day 1 | No response |
| **~Jun 9, 2026** | **Next Patch Tuesday — researcher has threatened a "big surprise"** | — |

**Pattern:** Every Microsoft action or inaction triggers an escalation. The researcher is directly pacing their disclosures against Microsoft's patch cycle, maximizing public exposure and operational pressure.

---

## Motivation Assessment

| Factor | Assessment |
|---|---|
| Financial gain from disclosures | ❌ No — tools are public, free, no broker activity observed |
| State sponsorship | ❌ No — no state attribution; tooling is weaponized by Russia-geolocated actors but researcher is assessed as independent |
| Ideological/political | Partial — protest against MSRC process; some broader disclosure ethics stance implied |
| Personal grievance | ✅ Primary — specific named targets, emotional language, claims of personal financial harm |
| Ego/notoriety | Partial — researcher is clearly technically proud; "almost feels like a backdoor but what do you know, maybe I'm just insane" (YellowKey) |

**Assessment:** The campaign is primarily driven by personal grievance and a desire to force Microsoft to respond. This is not a financially motivated actor, not a state proxy, and not an ideological hacktivist. It is an individual who believes they were wronged, who has the technical capability to cause significant damage, and who is deliberately leveraging that capability as pressure. The emotional escalation pattern (getting worse over time, named personal targets) is consistent with this assessment.

---

## Downstream Threat Actors Using Nightmare-Eclipse Tooling

The researcher's tools have been confirmed used in real-world intrusions by separate third-party threat actors. Huntress confirmed exploitation of BlueHammer as early as April 10, 2026 — seven days after release. Threat actor infrastructure in confirmed incidents has been geolocated to Russia by Lyrie.ai.

**Kill chain observed in the wild:**
1. Initial access via compromised SSLVPN/FortiGate credentials
2. UnDefend deployed — blocks Defender signature updates, falsifies health telemetry
3. RedSun or BlueHammer executed — escalates to SYSTEM
4. Post-exploitation: credential dumping, tunneling, lateral movement

Nightmare-Eclipse tooling is now a commodity component of post-exploitation playbooks for multiple threat actors.

---

## Predictive Intelligence

**Near-term (Jun 9, 2026 — Next Patch Tuesday):**
- High probability of new disclosure on or around June 9, per explicit researcher threat
- GreenPlasma SYSTEM shell completion is a candidate for the "big surprise"
- RedSun and UnDefend still unpatched — researcher may use their continued unpatched status as leverage for additional escalation
- `cldapi.dll` surface has likely not been fully exhausted — additional primitives probable

**Medium-term:**
- If Microsoft patches RedSun + UnDefend without crediting the researcher, another escalation event is likely
- If CISA KEV listing for RedSun/UnDefend forces Microsoft's hand, researcher may view it as a win and potentially de-escalate
- Researcher shifting from Defender-class tools (BlueHammer/RedSun/UnDefend) to broader Windows primitives (WinRE/CTFMON) suggests expanding attack surface discovery

---

## References

- [BleepingComputer — Disgruntled researcher leaks BlueHammer](https://www.bleepingcomputer.com/news/security/disgruntled-researcher-leaks-bluehammer-windows-zero-day-exploit/)
- [BleepingComputer — YellowKey + GreenPlasma](https://www.bleepingcomputer.com/news/security/windows-bitlocker-zero-day-gives-access-to-protected-drives-poc-released/)
- [BleepingComputer — Recently leaked Windows zero-days exploited in attacks](https://www.bleepingcomputer.com/news/security/recently-leaked-windows-zero-days-now-exploited-in-attacks/)
- [Huntress — Nightmare-Eclipse Tooling Seen in Real-World Intrusion](https://www.huntress.com/blog/nightmare-eclipse-intrusion)
- [Lyrie.ai — BlueHammer/RedSun/UnDefend Defender Trilogy](https://lyrie.ai/research/research/defender-bluehammer-redsun-undefend-trilogy)
- [Windows Central — BlueHammer / MSRC dispute analysis](https://www.windowscentral.com/microsoft/microsoft-security-response-center-bluehammer-exploit)
- [Picus Security — BlueHammer & RedSun CVE-2026-33825 Explained](https://www.picussecurity.com/resource/blog/bluehammer-redsun-windows-defender-cve-2026-33825-zero-day-vulnerability-explained)
- [Chaotic Eclipse Blog](https://deadeclipse666.blogspot.com)
- [GitHub — Nightmare-Eclipse](https://github.com/Nightmare-Eclipse)

---

## Update Log

| Date | Update |
|---|---|
| 2026-05-13 | Profile created. Five tools public. RedSun/UnDefend Day 27 unpatched. YellowKey/GreenPlasma Day 1. Threat of "big surprise" at June PT. |
| 2026-05-18 | **MiniPlasma (6th tool)** published May 17. LPE via `cldflt.sys`/`HsmOsBlockPlaceholderAccess`; CVE-2020-17103 incomplete fix/regression. SYSTEM confirmed on fully patched Win11 (May PT). Does NOT work on Insider Canary. Tracked as ZD-040. RedSun/UnDefend now Day 32; YellowKey/GreenPlasma Day 6. |
| 2026-05-19 | **No new tool today** (GitHub confirmed at 6 repos). **CRITICAL:** Researcher claims Microsoft quietly patched RedSun without CVE assignment — unconfirmed; do not stand down detections. Day counters: RedSun/UnDefend Day 33; YellowKey/GreenPlasma Day 7; MiniPlasma Day 2. |

---

## 📡 Intelligence Update — 2026-05-18

### MiniPlasma — 6th Tool Published (May 17, 2026)

Chaotic Eclipse published **MiniPlasma** on May 17, 2026 — a Windows LPE targeting `cldflt.sys` (Cloud Files Mini Filter Driver) that achieves SYSTEM via a race condition in `HsmOsBlockPlaceholderAccess`. The researcher characterizes it as proof that **CVE-2020-17103** (James Forshaw/GPZ, September 2020, reportedly patched December 2020) was never properly fixed:

> *"The exact same issue that was reported to Microsoft by Google Project Zero is actually still present, unpatched. The original PoC by Google worked without any changes."*

BleepingComputer confirmed SYSTEM shell on fully patched Windows 11 Pro (KB5089549). Will Dormann (Tharros) confirmed independently. Does NOT work on Windows 11 Insider Preview Canary. Microsoft has issued no CVE or advisory.

**Updated tool count: 6 tools public. 5 confirmed unpatched; 1 potentially silently patched (unconfirmed).**

| # | Tool | Status as of 2026-05-19 |
|---|---|---|
| 1 | BlueHammer | ✅ Patched (CVE-2026-33825) |
| 2 | RedSun | 🟡 POTENTIALLY SILENTLY PATCHED (researcher claim; unconfirmed; Day 33) |
| 3 | UnDefend | 🔴 Unpatched — Day 33 |
| 4 | YellowKey | 🔴 Unpatched — Day 7 |
| 5 | GreenPlasma | 🔴 Unpatched — Day 7 |
| 6 | MiniPlasma | 🔴 Unpatched — Day 2 |

**Next milestone:** ~June 9, 2026 (Patch Tuesday) — researcher threatened a "big surprise." Tracked as ZD-040.

*Profile updated: 2026-05-19 | Author: C3PO*

---

## 📡 Intelligence Update — 2026-05-19

### No New Tool Released | RedSun Potentially Quietly Patched (Unconfirmed)

GitHub account `Nightmare-Eclipse` confirmed at **6 repositories** as of 13:00 EDT, May 19, 2026. No new tool has been published today.

**RedSun Quiet Patch Claim:** Multiple sources reporting May 17–19 indicate the researcher themselves has stated Microsoft **silently patched RedSun** without assigning a CVE or publishing a public advisory. Sources: SecurityLab.ru (Russian-language, May 18), TechRadar MiniPlasma coverage. Creedtek.org analysis (May 14) adds appropriate caution: *"that claim should be treated with caution until confirmed by Microsoft’s advisory language or release notes."*

**Assessment:**
- Silent patch with no CVE = no CISA KEV eligibility, no federal mandate, no ecosystem notification. Defenders not actively tracking this researcher would have zero signal.
- MiniPlasma (same `cldflt.sys`/`cldapi.dll` surface, Day 2) confirmed working on fully patched May 2026 — no evidence of broad Cloud Files hardening occurred alongside any RedSun fix.
- **Do not stand down RedSun detections** pending independent PoC verification or Microsoft public statement.

**Day counters as of 2026-05-19:**
| Tool | Days Since Public Disclosure | Status |
|---|---|---|
| RedSun | Day 33 | 🟡 Potentially silently patched (unconfirmed) |
| UnDefend | Day 33 | 🔴 Unpatched |
| YellowKey | Day 7 | 🔴 Unpatched |
| GreenPlasma | Day 7 | 🔴 Unpatched |
| MiniPlasma | Day 2 | 🔴 Unpatched |
