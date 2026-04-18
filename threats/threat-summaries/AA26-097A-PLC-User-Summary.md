# ⚠️ PLC Security Alert — Iranian State Actor Targeting Your Controllers
### Based on FBI/CISA/NSA Joint Advisory AA26-097A | April 7, 2026 | TLP: CLEAR

---

## What's Happening

The FBI, CISA, and NSA have issued a joint warning: **Iranian government hackers (IRGC Cyber Electronic Command) are actively attacking Rockwell Automation / Allen-Bradley PLCs inside US critical infrastructure right now.** The campaign has been running since at least March 2026 and is assessed to be an escalation tied to ongoing Iran-US-Israel hostilities.

This is not a theoretical risk. In a prior campaign (November 2023), the same group — known as **CyberAv3ngers** — compromised **at least 75 PLC devices** across US water utilities. They are back, and they have expanded their target list.

**Affected devices confirmed in this campaign:**
- Rockwell Automation / Allen-Bradley **CompactLogix** PLCs
- Rockwell Automation / Allen-Bradley **Micro850** PLCs
- Potentially Siemens S7 PLCs (based on port targeting observed)

---

## How They're Getting In — In Plain English

These attackers are not exploiting exotic zero-days. They are walking through **unlocked front doors.**

Here's the attack in three steps:

**Step 1 — They find your PLC on the internet.**
If your PLC has any port exposed to the public internet (even for convenience — remote monitoring, vendor access, etc.), automated scanning tools find it within hours of it being connected. The attackers are specifically hunting for devices listening on:
- Port **44818** or **2222** (EtherNet/IP — the Rockwell/Allen-Bradley communication protocol)
- Port **502** (Modbus)
- Port **102** (Siemens S7)
- Port **22** (SSH)

**Step 2 — They connect using your own software.**
They use **Rockwell's own Studio 5000 Logix Designer** software to establish a legitimate-looking connection to your PLC. No special exploit needed. If the device answers on 44818 and there's no strong authentication required, they're in.

**Step 3 — They do damage.**
Once connected, they:
- **Download your project files** (.ACD files — your ladder logic, your process configuration, your engineering IP)
- **Manipulate what your operators see** on HMI/SCADA displays (operators may think the process is running normally when it isn't)
- **Install Dropbear SSH** (a backdoor) on your devices so they can return anytime through port 22
- **Modify or destroy your logic**, causing process disruption

Several US victims have already experienced **operational disruption and financial losses.**

---

## What to Look For

### On your network logs, look for:
- Any inbound connection to your PLC network from these IPs (observed Jan 2025 – Mar 2026):
  - `135.136.1.133`
  - `185.82.73.162`
  - `185.82.73.164`
  - `185.82.73.165`
  - `185.82.73.167`
  - `185.82.73.168`
  - `185.82.73.170`
  - `185.82.73.171`
- **Any inbound traffic on ports 44818, 2222, 502, 102, or 22** originating from outside your network or from unexpected locations
- Connections from **overseas hosting providers** or unfamiliar ASNs to your OT environment

### On your PLC devices, look for:
- The **physical mode switch moved out of RUN position** (if it's in PROG or REMOTE without you doing it — that's a red flag)
- Unexpected changes to your **project files** or ladder logic — compare against your last known-good backup
- **Unfamiliar SSH services running** (Dropbear) — these shouldn't exist on your PLCs
- Changes to **HMI display values** that don't match physical sensor readings

### On your HMI/SCADA, look for:
- Display values that **don't match physical readings** or seem implausible
- Operator alarms or setpoint changes you didn't initiate
- Login activity at **unusual hours** or from unexpected workstations

---

## What To Do About It

### 🚨 Do These Right Now

**1. Get your PLC off the public internet.**
This is the single most important action. If your PLC can be reached from outside your network without going through a firewall, VPN, or jump host — disconnect it. There is no good reason for a PLC to be directly internet-accessible. If a vendor says they need direct internet access to your controller, push back hard.

**2. Check your physical mode switch (Rockwell controllers).**
Set it to **RUN**. In RUN mode, remote program downloads are blocked. Only move it to PROG/REMOTE when you are actively downloading logic, and switch it back immediately when done. This is free protection that takes 10 seconds.

**3. Enable programming protection (Siemens S7).**
In TIA Portal, configure your PLC's access protection settings to require a password for program modifications. Default is wide open.

**4. Back up your project files right now.**
Download a copy of all your .ACD (Rockwell) or project files (Siemens), verify them, and store them **offline** (USB drive, separate secure server — not connected to the same network as the PLC). You want to be able to recover quickly if logic gets overwritten.

---

### 🔒 Do These Soon (Within Days/Weeks)

**5. Implement a VPN or jump host for any remote access.**
If remote access to your PLC network is genuinely required (vendor support, remote monitoring), it must go through a VPN or secure gateway with **multi-factor authentication**. The PLC itself should never be the endpoint that external users connect to directly.

**6. Firewall OT ports at the perimeter.**
Your firewall should be blocking ports 44818, 2222, 502, 102, and 22 from the internet. These protocols are for internal/OT network use only. If you're unsure whether these ports are exposed, ask your IT team or use CISA's free Cyber Hygiene scanning service (contact@cisa.dhs.gov).

**7. Disable what you don't use.**
On each PLC, disable unused services: Telnet, FTP, RDP, VNC, web interfaces. Every open service is an additional attack surface. If you don't use it, turn it off.

**8. Check for and apply outstanding patches.**
Review Rockwell's SD1771 bulletin and CVE-2021-22681 (authentication bypass in Logix controllers). Contact your Rockwell rep or PSIRT@rockwellautomation.com for patch status on your specific firmware version.

**9. Set up network monitoring for OT traffic.**
Even basic logging of what talks to your PLCs and when is valuable. You want to know if Studio 5000 connects to your controller at 2am from an IP you don't recognize.

**10. Build a device control list.**
Know which engineering workstations are authorized to connect to each PLC. Unexpected workstations connecting to a controller should trigger an alert.

---

## Quick Reference — Am I At Risk?

Ask yourself these questions:

| Question | If Yes → Action |
|---|---|
| Can I reach my PLC from the internet without a VPN? | **Disconnect it immediately** |
| Is my PLC's mode switch in PROG or REMOTE right now? | **Switch to RUN** |
| Do I have a current backup of my project files? | **Back up now** |
| Do I know what IP addresses normally connect to my PLC? | **Build a device control list** |
| Is port 44818 or 2222 open on my external firewall? | **Block it immediately** |
| Do I have MFA on VPN/remote access to my OT network? | **Implement MFA** |

---

## Who's Behind This (Context)

**CyberAv3ngers** is an arm of Iran's Revolutionary Guard (IRGC) Cyber Electronic Command. They deliberately target **critical infrastructure controllers** — water, energy, industrial processes — with the intent to cause **real-world physical disruption**, not just data theft. They've done it before (2023 water utilities campaign), and they're doing it again now in the context of broader Iranian-US-Israeli conflict escalation in 2026.

They are not looking for random targets. They are looking for **accessible PLCs in sectors that hurt when disrupted** — utilities, water, energy. If your PLC is internet-exposed, you are a valid target regardless of your organization's size or perceived strategic importance.

---

## Report an Incident

If you find evidence of compromise or suspicious activity on your OT systems:

- **CISA:** contact@cisa.dhs.gov | 1-844-Say-CISA (1-844-729-2472)
- **FBI:** Your local FBI field office
- **Rockwell PSIRT:** PSIRT@rockwellautomation.com

**When reporting, include:** date/time of observed activity, affected system types, what you observed, and your contact info.

---

## Sources

- FBI/CISA/NSA/EPA/DOE Joint Cybersecurity Advisory AA26-097A (April 7, 2026) — https://www.ic3.gov/CSA/2026/260407.pdf
- CISA: https://cisa.gov/topics/industrial-control-systems
- Rockwell Automation SD1771 & CVE-2021-22681

*Prepared by C3PO Intel | TLP: CLEAR — Suitable for broad distribution to operators and asset owners*
