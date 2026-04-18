# LockBit — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 2 | -0 | **2** | 3 | -2 | **2** | **4** | 🟢 LOW |
| **Destructive** | 1 | -0 | **1** | 2 | -2 | **1** | **2** | 🟢 LOW |
| **Disruptive** | 4 | -0 | **4** | 5 | -1 | **5** | **9** | 🔴 HIGH |
| **Cyber-Crime** | 4 | -0 | **4** | 5 | -1 | **5** | **9** | 🔴 HIGH |
| **Supply Chain** | 3 | -0 | **3** | 4 | -1 | **4** | **7** | 🟡 MEDIUM |

**Overall A&D Priority Score: 6.9 / 10** 🟡

---

## Scoring Rationale

**Espionage — LOW**
LockBit steals data for double-extortion leverage, not intelligence collection. Data exfiltration is a means to an end (ransom payment), not a primary intelligence mission. Capability exists but intent is financial, not espionage.

**Destructive — LOW**
LockBit encrypts files for leverage — they need the victim to be able to pay and receive a decryptor. Actual destruction works against their business model. No documented intentional data destruction.

**Disruptive — HIGH (9/10)**
Confirmed Boeing, Royal Mail, ICBC, Accenture hits. Manufacturing sector (heavily A&D-adjacent) is their most targeted vertical. LockBit 5.0 simultaneous Windows/Linux/ESXi encryption can take an entire datacenter offline within hours. High intent for large enterprises (4); maximum operational capability (5) with semi-custom tooling (-1 — LockBit platform is not commodity but is shared across affiliates).

**Cyber-Crime — HIGH (9/10)**
Pure financial motivation. A&D companies are large organizations with the capacity to pay multi-million dollar ransoms — exactly LockBit's hunting profile. The Boeing incident ($200M+ company, high-profile target) confirms they will hit A&D. Sector association (4), max capability (5).

**Supply Chain — MEDIUM**
LockBit affiliates use IABs (Initial Access Brokers) to acquire initial access — meaning they're indirectly connected to supply chain vectors. If a Tier-3 supplier sells access to an IAB who sells to a LockBit affiliate, the prime contractor is at risk. Less systematic than nation-state supply chain programs.

---

## A&D-Specific Risk Summary

LockBit is the premier ransomware disruption threat to A&D. They've already hit Boeing. They run a franchise model that means even if the core group is disrupted, affiliates keep attacking. LockBit 5.0's ESXi locker can shut down an entire virtualized datacenter in one shot — the kind of operational disruption that delays program deliverables, triggers contract penalties, and makes headlines. Primary concern: operational downtime and extortion, not espionage.
