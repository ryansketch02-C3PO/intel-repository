# 🎯 Threat Detection Weekly Intel Report — Threat Detection Engineering
**Threat Detection Engineering**
Week of: March 17–24, 2026

---

## 🦠 New Malware & Tools

- **[StoatWaffle](https://www.cyware.com/resources/threat-briefings/daily-threat-briefing/cyware-daily-threat-intelligence-march-24-2026)** — WaterPlum (DPRK, Contagious Interview)
  - **Type:** Modular stealer + RAT | **Delivery:** Malicious VS Code projects via fake developer job interviews
  - Distributed through the `tasks.json` `runOn: folderOpen` config — malware executes **automatically when a folder is opened in VS Code**. Stealer component harvests browser credentials and macOS Keychains; RAT component enables full remote command execution. Targets Windows and macOS. Campaign targets high-level professionals in crypto/Web3 via fake LinkedIn recruiter outreach.
  - **Detection opportunity:** `tasks.json` files in cloned repos with `runOn: folderOpen`; Node.js process spawned from VS Code without user interaction; outbound connections from VS Code process to unusual IPs

- **[GlassWorm (5th Wave)](https://www.bleepingcomputer.com/news/security/glassworm-malware-hits-400-plus-code-repos-on-github-npm-vscode-openvsx/)** — Russian-speaking threat actor
  - **Type:** Supply-chain credential/crypto harvester | **Delivery:** Compromised GitHub repos + npm/VSCode extensions
  - 433 compromised components this wave: 200 GitHub Python repos, 151 JS/TS repos, numerous VSCode/OpenVSX extensions. Uses **invisible Unicode characters** to hide malicious code. Queries the **Solana blockchain every 5 seconds** for C2 instructions — an unusual beaconing method. Harvests developer credentials, SSH keys, cloud tokens, and cryptocurrency wallets. Skips systems with Russian locale.
  - **Detection opportunity:** `lzcdrtfxyqiplpd` string in codebases (GlassWorm signature); `~/init.json` file on developer workstations; unusual Node.js installations in home directory; DNS queries to Solana RPC nodes from developer machines

- **[MacSync Infostealer](https://thehackernews.com/2026/03/clickfix-campaigns-spread-macsync-macos.html)** — Unknown
  - **Type:** macOS credential/crypto stealer | **Delivery:** ClickFix campaigns mimicking ChatGPT, GitHub, and WordPress sites
  - Tricks users into pasting Terminal commands (the "ClickFix" technique) that deploy MacSync. Harvests credentials and cryptocurrency seed phrases. Targets macOS developers specifically. Bypasses traditional AV by relying on the user to execute the payload themselves.
  - **Detection opportunity:** Terminal process spawned from browser context; clipboard-to-terminal execution pattern; outbound connections from newly executed shell scripts

- **[LeakNet Ransomware + Deno-Based Loader](https://reliaquest.com/blog/threat-spotlight-casting-a-wider-net-clickfix-deno-and-leaknets-scaling-threat)** — LeakNet (financially motivated)
  - **Type:** Ransomware | **Delivery:** ClickFix lures on compromised websites + Deno-based in-memory loader
  - New initial access path: fake CAPTCHA prompts on hacked sites trick users into running malicious PowerShell via Windows Run dialog. Loader runs in Deno runtime — **entirely in-memory, no disk artifacts** — evading most EDR solutions. Post-compromise methodology: lateral movement → data exfiltration → encryption. ~3 victims/month currently but scaling.
  - **Detection opportunity:** Deno process executing base64-decoded PowerShell; Windows Run dialog triggered from browser process; network beaconing from Deno runtime

- **[ValleyRAT / HoldingHands](https://www.cyware.com/resources/threat-briefings/daily-threat-briefing/cyware-daily-threat-intelligence-march-24-2026)** — Silver Fox (China-nexus APT)
  - **Type:** Modular RAT + backdoor | **Delivery:** Tax-themed phishing, fake WhatsApp installer (Python-based stealer)
  - Silver Fox has upgraded from financial cybercrime to full APT operations targeting South Asian government/finance. Dual-use toolkit: ValleyRAT for espionage; HoldingHands as persistent backdoor. Latest vector is a **Python-based stealer disguised as a WhatsApp app**. Culturally relevant lures (impersonating national tax authorities) for targeted regions.
  - **Detection opportunity:** Python executable with suspicious network activity post-install; outbound traffic to unusual geographic C2 infrastructure; tax-season themed email attachments with Python executables

---

## 🗺️ TTP Spotlight (MITRE ATT&CK)

- **[T1485 — Data Destruction via MDM Platform Abuse (Stryker/Intune Attack)](https://www.bleepingcomputer.com/news/security/stryker-attack-wiped-tens-of-thousands-of-devices-no-malware-needed/)**
  - Iran-linked Handala weaponized a compromised Microsoft Intune admin account to issue mass remote wipes across 80,000+ devices globally — no malware, no exploits, just native MDM capabilities. The attack proves that **administrative plane compromise is often more destructive than ransomware**. 50TB allegedly exfiltrated before destruction.
  - **Detection angle:** Impossible travel on admin accounts; bulk MDM wipe events in Intune audit logs; anomalous Intune admin role assignments; conditional access bypass indicators; build alerts for any device wipe event outside approved change windows

- **[T1195.001 + T1059.007 — Supply Chain via Compromised CI/CD Secrets (GlassWorm/Trivy/CanisterWorm)](https://thehackernews.com/2026/03/trivy-security-scanner-github-actions.html)**
  - Three related supply chain campaigns this week: Trivy GitHub Actions compromised (a second time in a month), GlassWorm 5th wave across npm/GitHub/VSCode, and CanisterWorm (47 npm packages compromised via postinstall hooks, C2 via ICP blockchain canisters). The pattern: compromise a trusted security or developer tool → automatically execute on millions of developer machines → harvest CI/CD secrets, cloud credentials, SSH keys.
  - **Detection angle:** GitHub Actions workflows executing from forked or modified upstream actions; postinstall script activity in npm packages; unexpected ICP/Internet Computer network traffic; secrets scanning failures in CI pipeline logs

---

## 🚨 IOCs This Week

- **[DarkSword iOS Exploit Kit — Multiple Apple CVEs](https://www.securityweek.com/darksword-iphone-exploit-leaked-online-hundreds-of-millions-at-risk/)**
  - Leverages 6 CVEs: CVE-2025-31277, CVE-2025-43529, CVE-2026-20700, CVE-2025-14174, CVE-2025-43510, CVE-2025-43520 — affecting iOS 18.4 through 18.7. Linked to suspected Russian group UNC6353. Deploys GHOSTBLADE, GHOSTKNIFE, and GHOSTSABER malware families for data theft. A newer version of the DarkSword kit **leaked on GitHub this week**, dramatically lowering the barrier to mass exploitation.
  - IOC type: iOS exploit kit; patch to latest iOS immediately; monitor for unusual process spawning from Safari/WebKit

- **[Interlock Ransomware — Cisco FMC Campaign IOCs](https://aws.amazon.com/blogs/security/amazon-threat-intelligence-teams-identify-interlock-ransomware-campaign-targeting-enterprise-firewalls/)**
  - Amazon MadPot published detailed TTPs: multi-stage payload via hidden management API reverse shell → RAM-only Interlock encryptor → PowerShell network recon scripts → two custom RATs for persistence → ScreenConnect as backup C2
  - IOC type: PowerShell network recon from FMC management process; inbound connections to FMC port 443 from unexpected IPs; ScreenConnect installer on FMC hosts

- **[React Native npm Backdoor — Two Packages, 130K Downloads](https://research.checkpoint.com/2026/23rd-march-threat-intelligence-report/)**
  - `react-native-country-select` and `react-native-international-phone-number` were backdoored March 16 via a preinstall script deploying credential and crypto theft malware with persistence. 130,000+ combined downloads in the prior month.
  - IOC type: `preinstall` script activity from these specific packages; audit all `package.json` for these dependencies immediately

- **[PureLog Stealer — Critical Infrastructure Phishing Campaign](https://slcyber.io/blog/march-24th-this-weeks-top-cybersecurity-and-dark-web-stories/)**
  - Delivered via fake copyright-infringement notices. Establishes persistence via registry; captures screenshots, system profiles, Chrome credentials, and crypto wallets. Active against healthcare, government, hospitality, and education.
  - IOC type: Suspicious registry Run key from non-standard executable; outbound HTTP from screenshot/keylog process; copyright-themed phishing lures

---

## 🔗 Supply Chain & CI/CD

- **[TeamPCP Backdoors LiteLLM + Compromises Checkmarx GitHub Actions](https://thehackernews.com/2026/03/trivy-security-scanner-github-actions.html)**
  - LiteLLM versions 1.82.7–1.82.8 were backdoored by threat actor TeamPCP (likely via the earlier Trivy CI/CD compromise). Malicious code: credential theft, Kubernetes lateral movement, and persistent access. LiteLLM is used by millions of AI developers — blast radius is enormous. Checkmarx GitHub Actions workflows were also compromised via typosquatted domains harvesting cloud and dev secrets.
  - **Blast radius:** Any CI/CD pipeline that used LiteLLM 1.82.7–1.82.8 between March 16–24 should be treated as potentially compromised. Rotate all secrets, audit Kubernetes service accounts.

- **[GlassWorm 5th Wave — 433 Repositories, npm, and VSCode Extensions](https://www.bleepingcomputer.com/news/security/glassworm-malware-hits-400-plus-code-repos-on-github-npm-vscode-openvsx/)**
  - Single threat actor behind 200 Python repos, 151 JS/TS repos, and numerous extensions. Compromises GitHub accounts via token theft, then force-pushes malicious commits. Invisible Unicode characters hide the payload. **Immediate action:** Audit VSCode extensions against known-clean hashes; run `lzcdrtfxyqiplpd` grep across codebases; enforce branch protection rules preventing force-pushes.

- **[DPRK Contagious Interview — GitHub, GitLab, Bitbucket Malicious npm Packages](https://www.cyware.com/resources/threat-briefings/daily-threat-briefing/cyware-daily-threat-intelligence-march-24-2026)**
  - Ongoing campaign disguised as developer job interviews directs targets to clone malicious npm repos from major platforms. The new StoatWaffle malware auto-executes on folder open via VS Code tasks.json. This campaign has been running since December 2022 and continues to evolve — any developer who's done a "technical assessment" for an unknown recruiter in the last month should audit their machine.

---

## ✈️ Aerospace/Defense Detections

- **[Interlock Ransomware — Active Targeting of A&D Organizations via Cisco FMC Zero-Day](https://techbytes.app/posts/cisa-cisco-fmc-zero-day-sunday-deadline-remediation/)**
  - Amazon/MadPot confirmed Interlock actively harvested credentials and mapped networks at aerospace and defense sector organizations for 36 days before the CVE-2026-20131 disclosure. Full attack chain documented: Java deserialization → root shell → PowerShell recon → custom RATs → ScreenConnect backup → RAM-only encryptor. Detection rules for this full chain are available in AWS's published post-incident write-up.

- **[U.S. Space Force — New Cyber Defense Squadrons at Launch Ranges](https://aviationweek.com/aerospace-daily-defense-report/2026-03-24)**
  - SSC stood up two cyber defense squadrons at launch facilities in Florida and California for real-time threat monitoring of launch infrastructure. Detection teams: this signals that launch range networks are considered a live target. Relevant TTPs to build detections for: IT/OT boundary crossings, unauthorized access to launch control systems, lateral movement from IT networks toward launch support infrastructure.

- **[MuddyWater / Boggy Serpens — AI-Assisted LampoRAT Targeting Energy/Maritime/Defense](https://research.checkpoint.com/2026/23rd-march-threat-intelligence-report/)**
  - Iran's MOIS-linked MuddyWater published new TTPs with AI-assisted malware including anti-analysis features extending dwell time. Targets: energy, maritime, finance, diplomacy in Middle East and expanding globally. Laravel Livewire CVE-2025-54068 confirmed weaponized by this group. Spear-phishing remains primary delivery. Detection angle: LampoRAT C2 patterns, anti-analysis sandbox evasion behaviors.

---

## 🛠️ Detection Engineering Resources

- **[ESET — Analysis of 90 EDR Killer Tools Using BYOVD Techniques](https://www.cybersecurity-help.cz/blog/5307.html)**
  - ESET published a comprehensive analysis of 90 tools used by attackers to disable EDR — over 50% use Bring Your Own Vulnerable Driver (BYOVD) method to gain kernel-level privileges before killing security software. Essential reading for detection engineers building pre-execution prevention for EDR tampering. Includes IOCs and driver lists.

- **[AWS — Full Interlock Ransomware TTP Breakdown (Cisco FMC Campaign)](https://aws.amazon.com/blogs/security/amazon-threat-intelligence-teams-identify-interlock-ransomware-campaign-targeting-enterprise-firewalls/)**
  - Amazon's post-incident write-up is one of the most detailed ransomware TTP documents published this year — full attack chain with detection opportunities at each stage. Detection engineers should use this to build a full detection coverage map for the Interlock campaign.

- **[Check Point Research — AI Threat Ecosystem Trends Jan–Feb 2026](https://research.checkpoint.com/2026/23rd-march-threat-intelligence-report/)**
  - Key trend: threat actors are shifting from simple prompting to **structured AI agent workflows** with multi-step attack chains. Detection implication: AI-led operations will look different from human-led ones — more uniform timing, less "human browsing" noise, highly systematic enumeration patterns. Detection models built on human attack behavior patterns may need recalibration.

- **[NIST — DNS Security Guidance Updated (First Time in Over a Decade)](https://buttondown.com/BagheeraAltered/archive/cybersecurity-newsletter-23rd-march-2026/)**
  - First DNS security guidance update from NIST in 10+ years. Covers modern DNS threat landscape including DNS hijacking, cache poisoning, DoH/DoT considerations, and DNS as a C2 channel (relevant given GlassWorm's Solana DNS usage and Amazon Bedrock DNS exfil research). Worth a read for any team building DNS-based detection capabilities.

---

*Report generated by C3PO 🤖 | Sources linked inline | Week of March 17–24, 2026*
