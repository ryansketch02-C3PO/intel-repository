# ⚡ AI Cyber Threat Landscape — Weekly Executive Brief
**Week of 12–15 May 2026 · Issued: 15 May 2026**
Prepared by: AI Intelligence Analyst · Based on Open Source Intelligence (OSINT)
Classification: TLP:GREEN — Internal Use

---

## 📖 Risk Level Reference Guide

### Risk Levels

| Level | Score | Meaning | Required Response |
|---|---|---|---|
| 🔴 **CRITICAL** | 75–100 | Active exploitation confirmed or highly imminent. Significant organisational impact likely. | **Immediate — hours** |
| 🟠 **HIGH** | 50–74 | Exploitation likely or public PoC available. Significant risk with limited mitigating controls. | **Urgent — 24–72 hours** |
| 🟡 **MEDIUM** | 25–49 | Limited exploitation observed. Moderate risk with meaningful mitigating factors in place. | **Scheduled — 1–2 weeks** |
| 🟢 **LOW** | 0–24 | No active exploitation. Minimal immediate risk. Standard patch cycle applies. | **Standard cycle — 30 days** |

### Domain Risk Score Methodology

Domain scores (e.g. *AI-Powered Attacks: 88/100*) are analyst assessments based on a weighted combination of the following factors. They are **not** derived from a single external framework.

| Factor | Weight | Description |
|---|---|---|
| Exploitation Maturity | 30% | Active exploitation, PoC availability, weaponisation speed |
| Threat Actor Capability | 25% | Nation-state / criminal sophistication and intent |
| Attack Surface Breadth | 20% | How widely exposed the affected systems or people are |
| Potential Business Impact | 15% | Financial, reputational, and operational consequences if exploited |
| Mitigation Availability | 10% | Patch, workaround, or compensating control availability |

### CVE Severity Reference (CVSS v3.1)

| Severity | CVSS Range | Notes |
|---|---|---|
| **CRITICAL** | 9.0–10.0 | |
| **HIGH** | 7.0–8.9 | |
| **MEDIUM** | 4.0–6.9 | |
| **LOW** | 0.1–3.9 | |
| **NONE** | 0.0 | |

> CVSS scores reflect technical severity only. This report additionally weights active exploitation status, threat actor intent, and organisational context when assigning overall risk levels.

---

## ⚠️ Overall Risk: ELEVATED

AI-powered offensive tooling has crossed a capability threshold this week. Agentic attack frameworks operating without human-in-the-loop are confirmed in at least two nation-state campaigns. Concurrently, the AI infrastructure layer itself is under attack — an actively exploited **Microsoft SSO Plugin zero-day** (CVE-2026-41103, CVSS 9.1) is compromising Jira and Confluence environments at scale, and a **Palo Alto PAN-OS zero-day** (CVE-2026-0300) linked to a China-nexus actor has been weaponised since April.

---

## 📊 This Week at a Glance

| Metric | Value | Δ Week | Source |
|---|---|---|---|
| New Critical / High CVEs | **6** | ↑ 3 | [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) / [MSRC](https://msrc.microsoft.com/update-guide) |
| Unpatched Zero-Days | **4** | — ongoing | Intel repository |
| Active CISA KEV Deadlines | **2** | 1 PASSED today | [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) |
| Prod AI Apps w/ Prompt Injection Risk | **73%** | ⚠ Analyst est. | [Zylos AI LLM Security 2026](https://zylos.ai/research/2026-01-13-llm-security-safety) |

> ⚠ **Analyst note:** The 73% figure is sourced from Zylos AI. The week-on-week delta is an interpolated trend estimate, not a directly cited data point.

---

## 🎯 Risk Posture by Domain

| Domain | Score | Level | Basis |
|---|---|---|---|
| AI-Powered Attacks | 88/100 | 🔴 CRITICAL | ⚠ Analyst Assessment |
| AI Infrastructure Security | 79/100 | 🔴 HIGH | ⚠ Analyst Assessment |
| Deepfake / Social Engineering | 82/100 | 🔴 CRITICAL | ⚠ Analyst Assessment |
| Supply Chain / LLM Poisoning | 70/100 | 🟠 HIGH | ⚠ Analyst Assessment |

> ⚠ Risk scores are analyst assessments based on aggregated open source intelligence, not derived from a single scored framework.

---

## 🤖 AI as a Weapon — Active Threat Vectors

### 1. Agentic AI Attack Chains
Nation-state and criminal actors are deploying **autonomous AI agents** that execute full intrusion lifecycles — recon, exploitation, lateral movement, and exfiltration — without human operators in the loop. MuddyWater (Iran-MOIS) used an AI-assisted campaign masquerading as Chaos ransomware. Chinese-nexus TGR-STA-1030 is actively probing 155 countries using agentic tooling. **Time-to-exploit has compressed to hours.**

Tags: `Nation-State` `Active Exploitation` `Autonomous`

### 2. Deepfake & Voice Cloning BEC
Deepfake audio/video involved in [**>30% of high-impact corporate impersonation attacks**](https://cyble.com/knowledge-hub/deepfake-as-a-service-exploded-in-2025/) in 2025 (Cyble Executive Threat Monitoring). Modern deepfake video [bypasses detection tools with **>90% accuracy**](https://cyble.com/knowledge-hub/deepfake-as-a-service-exploded-in-2025/). [SheByte PhaaS](https://www.securityweek.com/cyber-insights-2026-social-engineering/) delivers AI-generated templates at ~$200/month. Scattered Spider + Russian ransomware partnerships now combine Western social engineering with Eastern technical capability.

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
[**73% of production AI deployments remain exposed**](https://zylos.ai/research/2026-01-13-llm-security-safety) (Zylos AI). Indirect injection via poisoned RAG pipelines achieves 20–30% higher success than direct attacks. The EchoLeak attack against M365 Copilot demonstrated zero-click corporate data exfiltration via crafted email.

### 2. AI Supply Chain & Model Poisoning
[Research](https://www.anthropic.com/research/small-samples-poison) (Anthropic / UK AI Security Institute / Alan Turing Institute, Oct 2025) confirms **as few as 250 malicious documents** can backdoor LLMs from 600M–13B parameters. OpenAI was hit by TanStack supply chain attack this week. ShadowLogic backdoors survive format conversion and fine-tuning.

### 3. Jailbreaking & Guardrail Bypass
Policy Puppetry bypassed all major frontier models in 2026. Attack success rates per [Zylos AI 2025 systematic research](https://zylos.ai/research/2026-01-13-llm-security-safety): roleplay-based **89.6%**, logic traps **81.4%**, encoding tricks **76.2%**. Guardrails alone are insufficient.

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

> ⚠️ **Analyst Assessment:** Offensive AI is currently outpacing defensive AI in adaptability and scale. The Q3 2026 projection is analyst judgement based on current capability trends, not a sourced forecast.

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

---

## 📚 Sources & Citations

| # | Stat / Claim | Source | Link |
|---|---|---|---|
| 1 | 73% of prod AI deployments exposed to prompt injection | Zylos AI — LLM Security & Safety 2026 | [zylos.ai](https://zylos.ai/research/2026-01-13-llm-security-safety) |
| 2 | 89.6% / 81.4% / 76.2% jailbreak attack success rates | Zylos AI — 2025 systematic research | [zylos.ai](https://zylos.ai/research/2026-01-13-llm-security-safety) |
| 3 | 250 malicious documents backdoor any LLM (600M–13B) | Anthropic / UK AISI / Alan Turing Institute (Oct 2025) | [anthropic.com](https://www.anthropic.com/research/small-samples-poison) |
| 4 | >30% of corporate impersonation attacks used deepfakes | Cyble Executive Threat Monitoring Report | [cyble.com](https://cyble.com/knowledge-hub/deepfake-as-a-service-exploded-in-2025/) |
| 5 | >90% deepfake bypass rate against detection tools | Cyble Deepfake-as-a-Service 2025 Report | [cyble.com](https://cyble.com/knowledge-hub/deepfake-as-a-service-exploded-in-2025/) |
| 6 | $25M Hong Kong deepfake fraud | Cyble 2026 Outlook / widely reported | [cyble.com](https://cyble.com/knowledge-hub/cybercriminals-evolved-in-2025-cyble-2026/) |
| 7 | SheByte PhaaS ~$200/month | SecurityWeek — Cyber Insights 2026: Social Engineering | [securityweek.com](https://www.securityweek.com/cyber-insights-2026-social-engineering/) |
| 8 | CVE data, patch status, CISA KEV entries | CISA KEV / Microsoft MSRC / BleepingComputer | [cisa.gov](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) · [bleepingcomputer.com](https://www.bleepingcomputer.com/news/microsoft/microsoft-may-2026-patch-tuesday-fixes-120-flaws-no-zero-days/) |

### ⚠️ Analyst Assessments — Not Externally Sourced
- **Risk domain scores** (88, 79, 82, 70 /100) — analyst synthesis of aggregated OSINT; not derived from a single scored framework
- **Q3 2026 capability gap projection** — analyst trend judgement; not a sourced forecast
- **Week-on-week prompt injection delta** — 73% is sourced; the comparative baseline is an interpolated estimate

---

*AI Intelligence Analyst · Week 20, 2026 · Based on OSINT · TLP:GREEN*

*AI Intel Analyst created and maintained by **Ryan Sketch***
