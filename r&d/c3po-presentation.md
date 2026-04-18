# Project C3PO — AI-Powered Cyber Threat Intelligence
## A Personal Assistant for Aerospace & Defense OSINT
### 5-Minute Presentation

---

### 1️⃣ What We Built (1 min)

Set up **OpenClaw** — an open-source personal AI assistant framework — running on a Raspberry Pi. The assistant (named C3PO) is powered by **Claude Sonnet** (Anthropic's AI model) and acts as a dedicated cybersecurity threat intelligence analyst.

- Runs 24/7 on a Raspberry Pi at home
- Fully autonomous — no manual intervention needed
- Focused mission: aerospace & defense threat intel

---

### 2️⃣ The Intel Pipeline (1 min)

Connected **Perplexity AI** as a real-time web search engine so C3PO can actively hunt for threats across the internet — not just rely on training data.

**Sources monitored:**
- CVE/NVD, CISA, MITRE
- Threat intel blogs (Mandiant, CrowdStrike, Recorded Future)
- X/Twitter (key security researchers)
- Iran Monitor for nation-state activity
- Aerospace-specific sources (Aviation ISAC, DoD advisories)

---

### 3️⃣ Automated Delivery to Discord (1 min)

C3PO posts **3 intel briefs per day** to a private Discord server — automatically, no prompting needed:

| Time     | Brief Name                        |
|----------|-----------------------------------|
| 8:00 AM  | 🛸 The Rebel Alliance Rundown     |
| 12:00 PM | 🌌 The Galactic Republic Update   |
| 4:00 PM  | 🌎 The Outer Rim Report           |

Each brief covers: active threats, new CVEs/zero-days, aerospace/defense news, threat actor activity, and Iranian cyber operations.

---

### 4️⃣ Interactive Q&A Channel (1 min)

Created a dedicated **#c3po channel** on Discord where I can ask C3PO questions directly — like having an on-call analyst. The intel delivery channel is locked down so only the bot and server owner can post — keeping the feed clean.

---

### 5️⃣ Security Hardening (1 min)

Before going live, we audited and hardened the Pi:

- **Found:** No firewall, RDP and SSH exposed to the public internet
- **Fixed:** Installed `ufw`, blocked all inbound by default, closed RDP
- **Confirmed:** OpenClaw dashboard was already loopback-only (not internet-exposed)
- **Result:** Pi Connect remote access still works, C3PO's outbound connections unaffected

---

### 💡 Key Takeaway

For minimal cost (a Raspberry Pi + API keys), you get a **dedicated, always-on threat intelligence analyst** that monitors your focus area, delivers structured briefings, and answers ad-hoc questions — all through Discord.
