# 🎯 Threat Detection Weekly Intel Report
**Threat Detection Engineering**
Week of: March 17–24, 2026

---

## 🦠 New Malware & Tools

- **[CanisterWorm](https://thehackernews.com/2026/03/weekly-recap-cicd-backdoor-fbi-buys.html)** — TeamPCP
  - Type: Self-propagating supply chain worm
  - Capabilities: Credential harvesting (SSH keys, cloud creds, K8s secrets, .env files, crypto wallets), Kubernetes lateral movement (privileged pod deployment to every node), persistent systemd backdoor (`sysmon.service`) polling attacker C2 every 50 minutes for next-stage payloads
  - Delivery: Injected into Trivy vulnerability scanner releases and CI/CD GitHub Actions; cascaded into LiteLLM PyPI package (versions 1.82.7–1.82.8), Docker images, npm, Open VSX
  - Detection: Check for `sysmon.service` systemd user service; network egress to `checkmarx[.]zone` and `models.litellm[.]cloud`; rogue privileged pods in Kubernetes; `tpcp.tar.gz` in outbound traffic; `litellm_init.pth` in Python site-packages

- **[Perseus](https://thehackernews.com/2026/03/new-perseus-android-banking-malware.html)** — Unknown threat actor
  - Type: Android banking trojan / infostealer
  - Capabilities: Overlay attacks (fake login screens over legitimate apps), keylogging, personal note-taking app targeting (unusual focus on password/recovery phrase notes), real-time credential capture
  - Delivery: Disguised inside IPTV streaming apps distributed outside Google Play; targets Turkey and Italy primarily
  - Detection opportunity: Anomalous accessibility service usage; apps requesting overlay permissions from non-Play sources; suspicious network connections from IPTV-category apps

- **[DarkSword iOS Exploit Kit](https://thehackernews.com/2026/03/darksword-ios-exploit-kit-uses-6-flaws.html)** — UNC6353 (suspected Russia) + secondary actors
  - Type: Full-chain iOS exploit kit + infostealer (JavaScript)
  - Capabilities: Full iPhone compromise via watering hole (no user interaction beyond visiting a site); extracts credentials, crypto wallet data, all sensitive data; "hit-and-run" approach — exfiltrates and cleans up within minutes
  - Delivery: Watering hole via compromised websites; iFrame fingerprints visiting devices; routes iOS 18.4–18.6.2 devices to exploit chain
  - Exploits used: CVE-2025-31277 (JSCore memory corruption), CVE-2026-20700 (PAC bypass in dyld, zero-day), CVE-2025-43529 (JSCore memory corruption, zero-day), CVE-2025-14174 (ANGLE memory corruption, zero-day), CVE-2025-43510 (kernel memory management), CVE-2025-43520 (kernel memory corruption)
  - Detection: Network-level — suspicious JavaScript fingerprinting payloads; iFrame redirect chains from non-malicious-looking domains; MDM telemetry for unexpected data access patterns on iOS devices

- **[Beast Ransomware — Server Toolkit Analysis](https://www.team-cymru.com/post/beast-ransomware-server-toolkit-analysis)** — Beast RaaS (suspected Monster ransomware successor)
  - Type: Ransomware-as-a-Service
  - Capabilities: Advanced IP/Port Scanner for network mapping; credential harvesting via Mimikatz, LaZagne, Automim; AnyDesk for persistence; PsExec for lateral movement; MEGASync for bulk exfiltration; RDP/SMB targeting
  - Delivery: Standard ransomware playbook — VPN/firewall initial access, lateral movement, full domain compromise before encryption
  - Detection: Alert on MEGASync process execution in enterprise environments; Mimikatz/LaZagne process signatures; unusual AnyDesk installations; bulk file staging followed by external sync

---

## 🗺️ TTP Spotlight (MITRE ATT&CK)

- **[T1195.002 — Supply Chain Compromise: Compromise Software Supply Chain (TeamPCP/CanisterWorm)](https://thehackernews.com/2026/03/teampcp-backdoors-litellm-versions.html)**
  - How used: TeamPCP compromised Trivy vulnerability scanner → used harvested CI/CD secrets to inject malicious code into LiteLLM PyPI (versions 1.82.7–1.82.8), Docker images, and GitHub repos. Each compromise cascades to the next.
  - Detection angle: Verify checksums/hashes of CI/CD tooling on every pull; monitor for unexpected package version changes; alert on new `.pth` files in Python site-packages; restrict outbound connections from CI/CD runners to known-good endpoints

- **[T1114.003 — Email Collection: Email Forwarding Rule (APT28)](https://thehackernews.com/2026/03/weekly-recap-cicd-backdoor-fbi-buys.html)**
  - How used: APT28's SquirrelMail XSS toolkit deploys server-side Sieve forwarding rules that silently copy all incoming mail to attacker mailboxes. Requires only that the victim open a malicious email.
  - Detection: Query mailbox transport rules for rules forwarding to external domains; alert on new forwarding rules created by non-admin accounts; specifically monitor for rules using `.forward` files or Sieve scripts on webmail platforms

- **[T1059.005 — Command and Scripting Interpreter: Visual Basic (FAUX#ELEVATE)](https://thehackernews.com/2026/03/hackers-use-fake-resumes-to-steal.html)**
  - How used: 9.7MB VBScript (224K lines, 266 actual code lines) disguised as a resume document. WMI domain-join gate ensures execution only on enterprise machines. Completes full credential exfiltration in ~25 seconds.
  - Detection: Alert on VBScript spawning child processes that invoke curl/wget to cloud storage; monitor for Microsoft Defender exclusion path modifications via registry (`HKLM\SOFTWARE\Microsoft\Windows Defender\Exclusions`); alert on UAC registry changes from document-opened processes

- **[T1078 — Valid Accounts / T1550.004 — Use Alternate Authentication Material: Web Session Cookie (Tycoon2FA)](https://www.bleepingcomputer.com/news/security/tycoon2fa-phishing-platform-returns-after-recent-police-disruption/)**
  - How used: Tycoon2FA AiTM intercepts MFA tokens and session cookies in real time, allowing attackers to authenticate as victims with full session privileges — bypassing TOTP and push-based MFA entirely.
  - Detection: Impossible travel alerts; session anomalies (new device + new location on same session); IP reputation checks on session origins; phishing-resistant MFA (FIDO2/passkeys) eliminates this class of attack

---

## 🚨 IOCs This Week

- **[TeamPCP / CanisterWorm](https://thehackernews.com/2026/03/teampcp-backdoors-litellm-versions.html)**
  - C2 domains: `checkmarx[.]zone`, `models.litellm[.]cloud`
  - File artifacts: `sysmon.service` (systemd), `~/.config/sysmon/sysmon.py`, `litellm_init.pth`, `tpcp.tar.gz`
  - Kubernetes: Rogue privileged pods on every node; `chroot` into host filesystem
  - Kill switch: Any C2 URL containing `youtube[.]com` aborts execution

- **[APT28 Infrastructure](https://thehackernews.com/2026/03/weekly-recap-cicd-backdoor-fbi-buys.html)**
  - Previously attributed server: `203.161.50[.]145` (open directory, C2 source code exposed)
  - Exfil indicators: Sieve rules forwarding to external domains; unexpected `IMAP APPEND` operations; 2FA bypass attempts on Roundcube/SquirrelMail

- **[FAUX#ELEVATE Campaign](https://www.securonix.com/blog/faux-elevate-threat-actors-crypto-miners-and-infostealers/)**
  - Exfil email accounts: `olga.aitsaid@mail.ru`, `3pw5nd9neeyn@mail.ru` → `vladimirprolitovitch@duck.com`
  - Payload staging: Dropbox (`gmail2.7z`, `gmail_ma.7z`)
  - C2: Compromised Moroccan WordPress sites (mining config); mail[.]ru SMTP for exfil
  - Process artifacts: `mservice.exe` (XMRig miner), `RuntimeHost.exe` (persistent trojan), `WinRing0x64.sys` (kernel driver for mining)

- **[Interlock Ransomware](https://thehackernews.com/2026/03/interlock-ransomware-exploits-cisco-fmc.html)**
  - Initial access: CVE-2026-20131 (Cisco FMC deserialization) — craft HTTP request to specific servlet path
  - Persistence: ConnectWise ScreenConnect, custom JS/Java RATs, memory-resident webshell
  - Lateral movement: Certify (AD CS exploitation), PowerShell recon scripts, SOCKS5 proxy
  - Log evasion: Cron job every 5 minutes wipes `*.log` files; `HISTFILE` unset; HAProxy traffic laundering
  - UTC+3 operational timezone (possible Russian/Eastern European affiliation)

---

## 🔗 Supply Chain & CI/CD

- **[TeamPCP Supply Chain Cascade — Trivy → KICS → LiteLLM → Docker → npm → Open VSX](https://www.bleepingcomputer.com/news/security/trivy-supply-chain-attack-spreads-to-docker-github-repos/)**
  - The week's dominant story. TeamPCP compromised Aqua Security's Trivy scanner (32K GitHub stars, 100M+ Docker Hub downloads), then used harvested credentials to pivot across five ecosystems: GitHub Actions, Docker Hub, npm, Open VSX, and PyPI. Each compromise generates credentials for the next target. Actor explicitly stated intent to target more security tools in coming months.
  - Detection tips: Pin all tool versions with verified hashes; use isolated CI/CD environments with egress filtering; monitor for unexpected outbound connections from build runners; rotate all secrets that touched Trivy/KICS/LiteLLM during the compromise window (est. March 17–24)

- **[Cisco FMC Zero-Day Pre-Exploitation by Interlock](https://thehackernews.com/2026/03/interlock-ransomware-exploits-cisco-fmc.html)**
  - Interlock exploited CVE-2026-20131 as a zero-day for over a month before Cisco patched. AWS MadPot sensor network detected exploitation originating January 26, 2026. Demonstrates ransomware groups now have capability to develop and hold zero-days against enterprise security infrastructure — not just commodity software.

---

## ✈️ Aerospace/Defense Detections

- **[CVE-2026-4681 — PTC Windchill / FlexPLM RCE](https://www.bleepingcomputer.com/news/security/ptc-warns-of-imminent-threat-from-critical-windchill-flexplm-rce-bug/)**
  - Windchill is the backbone PLM system for aerospace and defense engineering. No patch available yet. IOCs published by PTC: presence of `GW.class` or `dpr_<8-hex-chars>.jsp` on the Windchill server indicates weaponization complete. Detection queries: monitor for HTTP requests containing `run?p=` or `.jsp?c=` patterns combined with unusual User-Agent strings; alert on unexpected `gateway exceptions` in Windchill logs; scan for webshell artifacts (`payload.bin`).
  - Detection rule tip: `http.uri contains "run?p=" AND http.uri contains ".jsp"` — flag for immediate investigation on Windchill servers

- **[Rockwell Automation — CVE-2021-22681 (CISA KEV March 5)](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)**
  - Studio 5000 Logix Designer key discovery allows unauthorized connection to Logix controllers. OT/ICS detection: monitor for unexpected authentication attempts to Logix controllers; baseline authorized connection sources and alert on deviations. EtherNet/IP traffic anomaly detection recommended.

- **[DoJ Disrupts 4 Mirai-Variant Botnets (AISURU, Kimwolf, JackSkid, Mossad)](https://thehackernews.com/2026/03/doj-disrupts-3-million-device-iot.html)**
  - 3+ million IoT devices (routers, IP cameras, DVRs) were commandeered across four Mirai-variant botnets targeting U.S. DoD and other high-value targets with DDoS. C&C servers taken down. Aerospace/defense orgs with internet-facing IoT infrastructure should audit for infection remnants and verify router/camera firmware patching.

---

## 🛠️ Detection Engineering Resources

- **[BoostSecurity — 20 Days Later: Trivy Compromise Act II](https://labs.boostsecurity.io/articles/20-days-later-trivy-compromise-act-ii/)** — Deep technical breakdown of the TeamPCP Trivy cascade and detection opportunities across the attack chain. Essential reading for anyone building CI/CD security detections.

- **[Amazon AWS MadPot — Interlock/Cisco FMC Research](https://aws.amazon.com/blogs/security/amazon-threat-intelligence-teams-identify-interlock-ransomware-campaign-targeting-enterprise-firewalls/)** — AWS published the full Interlock attack chain analysis including YARA-compatible artifact descriptions, custom RAT technical details, and infrastructure laundering techniques. Actionable for network and endpoint detections.

- **[Google GTIG + iVerify + Lookout — DarkSword iOS Exploit Kit Technical Report](https://cloud.google.com/blog/topics/threat-intelligence/darksword-ios-exploit-chain)** — Full technical breakdown of the 6-exploit DarkSword chain, CVE mapping, and detection guidance for MDM-managed iOS fleets.

- **[Securonix — FAUX#ELEVATE Campaign Analysis](https://www.securonix.com/blog/faux-elevate-threat-actors-crypto-miners-and-infostealers/)** — Full IOC set, detection logic, and behavioral signatures for the fake-resume phishing campaign. Includes SIEM use cases for VBScript abuse and Defender exclusion manipulation.

- **[Team Cymru — Beast Ransomware Server Toolkit Analysis](https://www.team-cymru.com/post/beast-ransomware-server-toolkit-analysis)** — Open directory analysis of Beast RaaS infrastructure; full toolset catalog with detection hooks for each stage.

---

*Report generated by C3PO 🤖 | Week of March 17–24, 2026 | Sources linked inline*
