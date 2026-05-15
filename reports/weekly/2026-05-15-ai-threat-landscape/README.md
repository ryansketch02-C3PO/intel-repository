# ⚡ AI Cyber Threat Landscape — Weekly Executive Brief
**Week of 12–15 May 2026 · Issued: 15 May 2026**
Prepared by: C-3PO Intel | Jedi Archives
Classification: TLP:GREEN — Internal Use

---

## ⚠️ Overall Risk: ELEVATED

AI-powered offensive tooling has crossed a capability threshold this week. Agentic attack frameworks operating without human-in-the-loop are confirmed in at least two nation-state campaigns. Concurrently, the AI infrastructure layer itself is under attack — an actively exploited **Microsoft SSO Plugin zero-day** (CVE-2026-41103, CVSS 9.1) is compromising Jira and Confluence environments at scale, and a **Palo Alto PAN-OS zero-day** (CVE-2026-0300) linked to a China-nexus actor has been weaponised since April.

---

## 📊 This Week at a Glance

| Metric | Value | Δ Week |
|---|---|---|
| New Critical / High CVEs | **6** | ↑ 3 |
| Unpatched Zero-Days | **4** | — ongoing |
| Active CISA KEV Deadlines | **2** | 1 PASSED today |
| Prod AI Apps w/ Prompt Injection Risk | **73%** | ↑ from 61% Q1 |

---

## 🎯 Risk Posture by Domain

| Domain | Score | Level |
|---|---|---|
| AI-Powered Attacks | 88/100 | 🔴 CRITICAL |
| AI Infrastructure Security | 79/100 | 🔴 HIGH |
| Deepfake / Social Engineering | 82/100 | 🔴 CRITICAL |
| Supply Chain / LLM Poisoning | 70/100 | 🟠 HIGH |

---

## 🤖 AI as a Weapon — Active Threat Vectors

### 1. Agentic AI Attack Chains
Nation-state and criminal actors are deploying **autonomous AI agents** that execute full intrusion lifecycles — recon, exploitation, lateral movement, and exfiltration — without human operators in the loop. MuddyWater (Iran-MOIS) used an AI-assisted campaign masquerading as Chaos ransomware. Chinese-nexus TGR-STA-1030 is actively probing 155 countries using agentic tooling. **Time-to-exploit has compressed to hours.**

Tags: `Nation-State` `Active Exploitation` `Autonomous`

### 2. Deepfake & Voice Cloning BEC
Deepfake audio/video involved in **>30% of high-impact corporate impersonation attacks** in 2025. Modern deepfake video bypasses detection tools with **>90% accuracy**. SheByte PhaaS platform delivers AI-generated templates at ~$200/month. Scattered Spider + Russian ransomware partnerships now combine Western social engineering with Eastern technical capability.

Tags: `BEC/Fraud` `$25M+ incidents` `PhaaS`

### 3. AI-Generated Phishing at Scale
LLMs now produce **hyper-personalised, multilingual phishing lures at mass scale**. ClickFix browser-based attacks are escalating. AI-adaptive lures adjust tone mid-interaction. Traditional awareness training is **no longer sufficient** without process controls.

Tags: `Phishing` `ClickFix` `Browser-Based`

### 4. Ransomware 5.0 — Autonomous Operations
AI is embedded in every stage of the ransomware chain. TeamPCP released **Shai-Hulud worm source code** this week, demonstrating AI-guided self-propagation. Expect derivative variants in the wild within 30 days.

Tags: `Ransomware` `Public PoC` `Autonomous`

---

## 🎯 Attacks ON AI Systems

### 1. Prompt Injection (OWASP LLM01:2025)
**73% of production AI deployments remain exposed.** Indirect injection via poisoned RAG pipelines achieves 20–30% higher success than direct attacks. The EchoLeak attack against M365 Copilot demonstrated zero-click corporate data exfiltration via crafted email.

### 2. AI Supply Chain & Model Poisoning
Research confirms **as few as 250 malicious documents** can backdoor LLMs from 600M–13B parameters. OpenAI was hit by TanStack supply chain attack this week. ShadowLogic backdoors survive format conversion and fine-tuning.

### 3. Jailbreaking & Guardrail Bypass
Policy Puppetry bypassed all major frontier models in 2026. Attack success rates: roleplay-based **89.6%**, logic traps **81.4%**, encoding tricks **76.2%**. Guardrails alone are insufficient.

### 4. CVE-2026-7482 — Bleeding Llama (CVSS 9.1) ✅ Patched
Heap OOB read in Ollama AI inference server enables unauthenticated exfiltration of API keys, env vars, and chat data. Patched in v0.17.1. Prioritise patching any internal Ollama instance.

---

## 🔴 Critical Vulnerabilities — Week of 12–15 May 2026

| CVE | Product | Type | CVSS | Status | AI Relevance |
|---|---|---|---|---|---|
| CVE-2026-42897 | Microsoft Exchange OWA | XSS → RCE (Zero-Day) | — | 🔴 UNPATCHED | AI BEC pivot via email |
| CVE-2026-41103 | MS SSO Plugin — Jira/Confluence | Auth Bypass | 9.1 CRIT | ⚡ ACTIVELY EXPLOITED | AI knowledge base takeover |
| CVE-2026-20182 | Cisco Catalyst SD-WAN | Auth Bypass — Full Admin | 10.0 CRIT | ⚡ CISA KEV — May 17 FCEB | Network segmentation bypass |
| CVE-2026-41651 | PackageKit (Linux) | LPE / Pack2TheRoot | 8.8 HIGH | ⚡ ACTIVELY EXPLOITED | LPE on AI inference servers |
| CVE-2026-40402 | Windows Hyper-V | EoP / Guest-to-Host | 9.3 CRIT | ✅ Patched May PT | Cloud AI workload isolation |
| CVE-2026-0300 | Palo Alto PAN-OS | Unauthenticated RCE | CRITICAL | ✅ Patched May 13 | Perimeter breach; China-nexus |
| CVE-2026-7482 | Ollama AI Inference Server | Heap OOB / Data Exfil | 9.1 HIGH | ✅ Patched v0.17.1 | **Direct AI infrastructure attack** |

---

## ⚔️ The AI Arms Race

| Offensive AI | Defensive AI |
|---|---|
| Autonomous recon & exploitation chains | Behavioural UEBA for AI agent anomalies |
| Hyper-personalised phishing at mass scale | AI-powered SOC triage |
| Real-time deepfake voice/video | NVIDIA NeMo Guardrails (1.4× detection) |
| Adaptive malware that refactors vs AV | Automated AI red teaming (24/7) |
| AI-optimised psychological operations | Runtime inference monitoring |
| Jailbreak automation (89.6% success) | AI-driven deepfake detection |
| Ransomware 5.0 — autonomous negotiation | Provenance-tagged RAG pipelines |
| Model backdooring via <250 poisoned docs | Model signing + supply chain verification |

> ⚠️ Assessment: Offensive AI is currently outpacing defensive AI in adaptability and scale. The gap is expected to widen through Q3 2026.

---

## 📰 Key Incidents This Week

**🇮🇷 MuddyWater — AI-Assisted Intrusion (May 2026)**
Iran-linked MuddyWater (MOIS) conducted an AI-assisted intrusion using Chaos ransomware as a false flag. No ransomware was deployed — artifacts were planted to complicate attribution and mislead IR teams.

**⛓️ OpenAI — TanStack Supply Chain Attack (May 14)**
OpenAI infrastructure impacted by a TanStack JavaScript library supply chain compromise. Review dependency trees and rotate API keys if consuming OpenAI APIs.

**🌐 TeamPCP Releases Shai-Hulud Worm Source Code**
Criminal group TeamPCP publicly released source code for an AI-guided network worm. Expect derivative variants in the wild within 30 days.

---

## ✅ Executive Recommended Actions

| # | Action | Priority |
|---|---|---|
| 01 | **Patch Cisco SD-WAN & mitigate Exchange OWA** — FCEB deadline May 17 | 🔴 48-Hour Deadline |
| 02 | **Audit all Jira/Confluence SSO deployments** — CVE-2026-41103 actively exploited | 🔴 Immediate |
| 03 | **Inventory & patch all Ollama/AI inference servers** — rotate exposed API keys | 🔴 Immediate |
| 04 | **Implement out-of-band wire transfer verification** — deepfake BEC is undetectable by tech alone | 🟠 This Week |
| 05 | **Assess AI supply chain exposure** — MCP configs, model sources, plugin permissions | 🟠 This Month |
| 06 | **Deploy runtime AI security monitoring** — guardrails alone fail in 73% of deployments | 🟡 Q2 Priority |

---

## Files in This Report

| File | Description |
|---|---|
| `README.md` | This document — markdown summary for GitHub viewing |
| `report.html` | Full interactive HTML report for executive presentation |

---

*Sources: CISA KEV · BleepingComputer · SecurityWeek · HiddenLayer 2026 AI Threat Report · Zylos LLM Security · Cyble · MSRC*
*C-3PO Intel · Jedi Archives · Week 20, 2026 · TLP:GREEN*
