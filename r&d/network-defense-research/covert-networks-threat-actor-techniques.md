# Covert Networks — How Threat Actors Use Them

> C3PO Intel | 2026-04-23 | TLP: WHITE

---

## What Is a Covert Network?

A covert network is **infrastructure that threat actors build or hijack to route their malicious traffic through**, making it appear to originate from somewhere — or someone — it isn't. The goal is always the same: hide the true origin, evade detection, and complicate attribution.

There are several distinct types, each with different use cases and levels of sophistication.

---

## 1. ORB Networks (Operational Relay Box Networks)

**The most significant evolution in APT tradecraft in recent years.**

ORB networks are meshes of compromised or rented devices — home routers, small office devices, VPS servers, IoT sensors — that actors use as relay nodes to route attack traffic. Google GTIG specifically called these out in their February 2026 defense industrial base report as a primary tool of China-nexus APT groups.

### How They Work
- Attacker controls a command node
- Traffic is routed through 3–10+ relay devices before hitting the target
- Each hop strips attribution data
- The target sees traffic originating from a legitimate-looking residential or commercial IP — not a known threat actor IP
- Even if one node is discovered and blocked, the attacker routes through different nodes

### Why They're Effective
- Residential IPs bypass geo-fencing controls (attack appears local)
- Traffic blends with normal internet activity
- Highly resilient — attacker adds new nodes faster than defenders can block them
- Make attribution extremely difficult — the IP could belong to a threat actor or a compromised home router belonging to an innocent user

### Who Uses Them
China-nexus groups are the most prolific. Microsoft tracks several dedicated ORB networks operated by MSS-affiliated actors. Volt Typhoon, APT40, and APT41 all use ORB infrastructure routinely.

---

## 2. Botnets as Covert Infrastructure

Traditional botnets have evolved beyond spam and DDoS into **operational relay infrastructure for nation-state actors**.

### Turla's Playbook (The Definitive Example)
Rather than building their own infrastructure, Turla (FSB Center 16) systematically *hijacks other people's*:

| Target Infrastructure | Method | Year |
|-----------------------|--------|------|
| Andromeda botnet | Re-registered expired C2 domains; inherited existing infected machines globally | 2022 |
| Amadey botnet | Leveraged criminal botnet to deliver Kazuar backdoor to select targets | 2024 |
| OilRig (APT34) C2 | Operated inside Iranian state actor's infrastructure for 18 months; victims thought they were dealing with Iran | 2018–2019 |
| Gamaredon tools | Used Gamaredon's PteroGraphin/PteroOdd/PteroPaste to deploy Kazuar on Ukrainian targets | 2025 |

**The strategic logic:** Why burn your own infrastructure when you can borrow someone else's — and generate false-flag attribution as a bonus?

---

## 3. SOHO Router & Edge Device Exploitation

**Volt Typhoon's signature technique — now widely adopted.**

Small office/home office (SOHO) routers, firewalls, NAS devices, and IoT equipment are compromised at scale and assembled into covert relay networks. Cisco, Netgear, ASUS, Fortinet, and Ivanti devices have all been documented as components.

### Why SOHO/Edge Devices
- Unmonitored — no EDR, no logging, no SOC visibility
- Typically running outdated firmware with known but unpatched vulnerabilities
- Traffic from a residential router looks completely legitimate to any destination
- Physically distributed across many countries — impossible to geo-block effectively
- ISPs do not monitor individual device traffic for malicious routing behavior

### Key Case
CISA documented Volt Typhoon operating a botnet of hundreds of compromised US SOHO routers as primary relay infrastructure for attacks on US critical infrastructure. The FBI disrupted part of this network in January 2024 — the actors rebuilt within months.

---

## 4. Legitimate Cloud & SaaS Services as C2

One of the fastest-growing techniques — using **trusted platforms that cannot be blocked** as command-and-control channels.

### Why It Works
You cannot block Google Drive. You cannot block Telegram. If you do, you break legitimate business operations. Actors route C2 traffic through services that generate so much legitimate traffic that malicious communications are invisible in the noise.

### Active Examples

| Actor | Service | Method |
|-------|---------|--------|
| Turla | Gmail web UI | ComRAT backdoor issues commands via Gmail drafts — appears as normal webmail traffic |
| Turla | Google Drive | Carbon/ComRAT variants route commands through Drive API |
| Gamaredon | Telegram | Malware queries a Telegram channel post to retrieve current C2 IP (encoded as `104@248@36@191`) |
| Gamaredon | Telegraph / Codeberg | Dynamic IP delivery via public posts |
| Gamaredon | Cloudflare Tunnels | True C2 server hidden behind Cloudflare; real IP completely masked |
| Gamaredon | Microsoft Dev Tunnels | C2 via `*.devtunnels.ms` with valid Microsoft-issued TLS — indistinguishable from legitimate developer traffic |
| APT29 | OneDrive, Dropbox | Document exfiltration routed through cloud storage |
| Multiple | GitHub, Pastebin | Encoded C2 instructions embedded in public repository content or paste content |

### The Defender's Dilemma
Blocking these platforms is operationally impossible for most organizations. The only effective defense is **behavioral detection** — monitoring *which process* is talking to a cloud service, not simply whether the service is being accessed.

---

## 5. Satellite Internet Hijacking

**Turla's most exotic documented technique — active since at least 2015.**

Turla intercepted **unencrypted DVB satellite internet streams** — eavesdropping on satellite TV/internet traffic — identified legitimate subscriber IP addresses, and spoofed those IPs as their own uplink. Exfiltration packets were sent to those satellite IPs and received by Turla's antenna.

### Why It's Nearly Undetectable
- Traffic traces back to a legitimate satellite subscriber, often in an unrelated country
- You cannot block satellite IPs without disrupting legitimate satellite services
- No malware required on the victim's satellite terminal — purely network-layer manipulation
- Reserved for the highest-value targets where burning standard infrastructure is unacceptable

---

## 6. Encrypted P2P Relay Networks

**Snake/Uroburos (Turla)** built the most sophisticated example: a global peer-to-peer encrypted relay network spanning Turla-compromised machines in 50+ countries. Each node unknowingly relayed encrypted traffic for other nodes. The true C2 was buried at the end of a multi-hop chain spanning continents.

**Operation MEDUSA (FBI, May 2023)** killed this network by issuing self-destruct commands from inside it via a court-authorized tool called PERSEUS. Turla rebuilt with Kazuar v2/v3 within months — demonstrating that infrastructure takedown is not permanent disruption.

---

## 7. False Flag Infrastructure

Beyond hiding origin, sophisticated actors **deliberately construct infrastructure that points attribution toward someone else.**

### Documented Cases
- **Turla inside OilRig:** Operating inside Iranian APT infrastructure meant any detection analysis pointed to Iran, not Russia
- **Turla/APT29 code overlap:** Structural similarities between Turla's Kazuar and APT29's SUNBURST (SolarWinds) backdoor remain unresolved — same developer, shared contractor, or deliberate false-flag insertion
- **Lazarus Group:** Has used European and US-based IP ranges; in some operations used infrastructure mimicking regional actors
- **APT43 / Kimsuky:** Google GTIG documented infrastructure mimicking German and US defense entities

---

## 8. Fast-Flux DNS

A technique for rapidly rotating IP addresses behind domain names to prevent effective blocking.

- A malicious domain resolves to a different IP address every few minutes or hours
- By the time a defender blocks an IP, it's already been rotated out
- Gamaredon used this extensively (2022) alongside the cloud service pivot
- Combined with legitimate-looking domain names, it defeats most automated blocking systems

**Double-flux:** Takes it further by also rotating the nameservers themselves, making takedown of the domain infrastructure significantly harder.

---

## Defender's Framework

The common thread across all covert network techniques is that **IP-based detection is largely ineffective** as a primary defense. Modern covert networks are specifically designed to defeat it.

### What Actually Works

| Approach | Why It Works |
|----------|-------------|
| **Behavioral / process-level detection** | Monitors *what the process is doing*, not where traffic goes |
| **Process-to-network correlation** | Alert when `svchost.exe` connects to Google Drive — not when Drive is generally accessed |
| **Encrypted traffic analysis (ETA)** | Pattern analysis of encrypted flows without decryption — identifies anomalous volume, timing, beaconing |
| **Endpoint telemetry (EDR)** | Endpoint data is harder to fake than network-layer data; catches execution chains |
| **Zero trust architecture** | Assumes the network perimeter is already compromised; verifies identity at every hop |
| **Threat intel IOC feeds** | Known ORB node IP ranges published by Microsoft MSTIC, Google GTIG, Recorded Future, Team Cymru |
| **DNS anomaly detection** | ORB nodes and covert infrastructure generate unusual DNS query patterns; fast-flux is detectable via TTL monitoring |
| **Beaconing detection** | Many C2 channels have regular check-in intervals; statistical analysis of connection timing reveals beaconing |
| **Egress filtering + DLP** | Limit which processes can make outbound connections; monitor large/unusual outbound data transfers |

### Red Flags to Hunt For
- **Outbound connections from unexpected processes** to cloud storage, messaging platforms, or CDN providers
- **Encoded or base64 content** in HTTP GET/POST parameters to legitimate services
- **Very low TTL DNS responses** (fast-flux indicator)
- **Beaconing patterns** — regular outbound connections at fixed intervals from a single host
- **Unusual process spawning chains** — e.g., Word spawning PowerShell spawning curl
- **Connections to newly registered domains** or domains with no historical traffic baseline
- **High volume of small outbound packets** to a single destination (C2 heartbeat pattern)

---

## The Big Picture

The evolution of covert networks represents a fundamental shift: **nation-state actors no longer need dedicated, attributable infrastructure.** They operate inside legitimate cloud platforms, inside criminal botnets, inside compromised home routers, and inside other nation-states' APT infrastructure.

The line between "the attacker's network" and "the internet" has effectively dissolved.

For organizations in high-value sectors — aerospace, defense, critical infrastructure, financial services — the operational assumption should be that adversary traffic has already transited your network perimeter at some point, routed through infrastructure that appeared completely legitimate.

**The modern security posture question is not "did anything bad get in?" — it's "what is already in here, and what is it doing?"**

---

## Related Intel Repository Entries

| Actor | Entry # | Covert Network Technique |
|-------|---------|--------------------------|
| Turla / Snake | #026 | Satellite hijacking, P2P encrypted relay, botnet hijacking (Andromeda, Amadey, OilRig) |
| Gamaredon | #027 | Telegram/Telegraph C2, Cloudflare Tunnels, Microsoft Dev Tunnels, fast-flux DNS |
| Volt Typhoon | #008 | SOHO router botnet, ORB networks, LOTL (no custom malware) |
| APT41 / Double Dragon | #019 | VPN exploitation, ShadowPad, ORB-adjacent infrastructure |
| APT29 / Cozy Bear | #009 | Supply chain compromise (SolarWinds), cloud service C2 |
| Lazarus Group | #003 | False flag infrastructure, social engineering as access vector |

---

*C3PO Intel | 2026-04-23 | TLP: WHITE*
