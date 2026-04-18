# 🕵️ Insider Threat Weekly Intel Report
**Insider Threat Program**
Week of: March 17–24, 2026

---

## 💾 Data Exfiltration Incidents

- **[Mazda — Employee & Partner Data Exposure via Warehouse Management System](https://www.bleepingcomputer.com/news/security/mazda-discloses-security-breach-exposing-employee-and-partner-data/)**
  - Mazda disclosed unauthorized external access to a warehouse management system used for parts procurement from Thailand. Exposed 692 records containing user IDs, full names, email addresses, company names, and business partner IDs. Breach was detected in December 2025 but disclosed March 23, 2026 — a three-month gap. No customer data affected, but supply chain partner data is now in attacker hands. The Clop ransomware group had previously posted Mazda to its leak site in November 2025; Mazda denies any ransomware link to the current incident.
  - **Insider relevance**: Third-party vendor/partner data exposure; supply chain partner compromise creates pretexting opportunities for spear-phishing insiders at Mazda and its partners.

- **[HackerOne — Employee Data Stolen via Navia Benefits Administrator Hack](https://www.bleepingcomputer.com/news/security/hackerone-discloses-employee-data-breach-after-navia-hack/)**
  - Bug bounty platform HackerOne is notifying hundreds of employees that their data was stolen after attackers compromised Navia, a U.S. benefits administrator. Classic fourth-party risk — HackerOne didn't get hacked directly; a vendor they use did.
  - **Insider relevance**: Employee PII from a security firm is now exposed. High-value targeting opportunity; expect social engineering attempts against HackerOne staff. Pattern generalizes: benefits administrators hold rich employee data and are a soft underbelly for insider threat enablement.

- **[FAUX#ELEVATE — Corporate Credential Theft via Fake Resume Phishing](https://thehackernews.com/2026/03/hackers-use-fake-resumes-to-steal.html)**
  - Active campaign targeting French-speaking enterprise environments using highly obfuscated VBScript files disguised as resume/CV documents. Delivered via phishing email. Full infection chain completes in ~25 seconds: credential theft, browser credential exfiltration, desktop file exfiltration, and Monero miner deployment. Uses Dropbox for payload staging, mail[.]ru for C2 exfiltration. Selectively targets domain-joined machines — explicitly excludes home systems to maximize yield.
  - **Insider relevance**: HR teams and hiring managers are prime targets. VBScript disguised as a resume is a direct lure into corporate HR workflows. If an insider opens this during a hiring process, the entire enterprise credential set is exposed in seconds. Enforce strict attachment policies for recruiting workflows.

---

## 🔑 Credential Theft & Account Abuse

- **[APT28 / Fancy Bear — SquirrelMail XSS Credential Harvesting Toolkit Exposed](https://thehackernews.com/2026/03/weekly-recap-cicd-backdoor-fbi-buys.html)**
  - An exposed open directory on APT28 infrastructure revealed a sophisticated webmail credential harvesting toolkit targeting government and military organizations across Ukraine, Romania, Bulgaria, Greece, Serbia, and North Macedonia. The directory contained: 2,870 exfiltrated government/military emails, 244 credential sets, 143 Sieve forwarding rules silently redirecting incoming mail to attacker mailboxes, and 11,527 harvested contact addresses. The kit requires only that a victim open a malicious email — no further interaction needed — to steal credentials, bypass 2FA, and establish persistent forwarding.
  - **Insider relevance**: Silent email forwarding rules are a classic insider/account-compromise tradecraft. If APT28 is using this against government targets, assume similar TTPs are being adapted for corporate espionage. Audit Sieve/forwarding rules on all email accounts; flag unexpected rules immediately.

- **[Tycoon2FA Phishing-as-a-Service — Back After Europol Disruption](https://www.bleepingcomputer.com/news/security/tycoon2fa-phishing-platform-returns-after-recent-police-disruption/)**
  - The Tycoon2FA adversary-in-the-middle (AiTM) phishing platform — disrupted by Europol on March 4 — has already returned to prior activity levels. The platform is designed specifically to steal MFA-authenticated sessions, bypassing time-based OTPs entirely. Widely used to target M365 and Google Workspace accounts.
  - **Insider relevance**: If a privileged insider's session token is hijacked via Tycoon2FA, the attacker operates as the employee. Session monitoring and short token lifetimes are critical controls. Any org that relies on MFA as the last line of defense is exposed.

---

## 🌑 Dark Web Exposure

- **[TeamPCP — Credential Harvest from CI/CD Supply Chain (LiteLLM/Trivy)](https://thehackernews.com/2026/03/teampcp-backdoors-litellm-versions.html)**
  - TeamPCP's cascading supply chain attack (Trivy → KICS → LiteLLM) harvested SSH keys, cloud credentials, Kubernetes secrets, cryptocurrency wallets, and .env files from potentially hundreds of thousands of developer environments. The actor explicitly stated they are "stealing terabytes of trade secrets" and have partnered with additional threat groups. Exfiltrated data is encrypted and sent to C2 domain "models.litellm[.]cloud."
  - **What was found**: Cloud credentials, SSH keys, Kubernetes secrets, .env files (often containing API keys and database passwords)
  - **Estimated risk**: Critical. Any organization using Trivy, KICS, or litellm in CI/CD pipelines during the compromise windows should assume credential exposure.

- **[ShinyHunters — Claims Theft of 6.8M Infinite Campus Student Records](https://www.bleepingcomputer.com/news/security/infinite-campus-warns-of-breach-after-shinyhunters-claims-data-theft/)**
  - ShinyHunters — a persistent data broker threat actor — claims to have stolen records for 6.8 million individuals from K-12 student information platform Infinite Campus. Infinite Campus is investigating. ShinyHunters continues monetizing large-scale data theft on dark web forums.
  - **Insider relevance**: While K-12 focused, ShinyHunters regularly brokers stolen enterprise credentials. Monitor for aerospace/defense adjacent exposures on forums where ShinyHunters operates.

---

## 😈 Insider TTP Spotlight

- **[Silent Email Forwarding Rules — T1114.003 (Email Collection: Email Forwarding Rule)](https://thehackernews.com/2026/03/weekly-recap-cicd-backdoor-fbi-buys.html)**
  - APT28's exposed toolkit relied heavily on server-side Sieve rules to silently forward every incoming email to attacker-controlled mailboxes — persisting indefinitely even after credential rotation unless explicitly removed. This TTP is indistinguishable from legitimate business use and rarely monitored.
  - **Detection**: Audit all mailbox forwarding and transport rules. Alert on new Sieve rules created outside normal business hours or by non-admin accounts. Flag rules forwarding to external domains, especially free email providers.

- **[FAUX#ELEVATE — T1059.005 / T1204.002 / T1113 (VBScript Execution / Malicious File / Screen Capture)](https://www.securonix.com/blog/faux-elevate-threat-actors-crypto-miners-and-infostealers/)**
  - The FAUX#ELEVATE campaign demonstrates a recruitment workflow lure that bypasses enterprise security by exploiting the implicit trust users extend to incoming "resumes." 224,000 lines of junk comment padding in VBScript makes signature detection ineffective. Uses WMI domain-join gating to ensure execution only on enterprise assets — explicitly avoiding sandbox or home environments.
  - **Detection**: Monitor for VBScript execution spawning child processes that reach out to cloud storage (Dropbox) or SMTP services. Alert on Defender exclusion path modifications via registry. Flag UAC bypass attempts from documents opened via email.

---

## ⚠️ Sector Watch — Aerospace/Defense

- **[PTC Windchill CVE-2026-4681 — PLM System Under Imminent Threat](https://www.bleepingcomputer.com/news/security/ptc-warns-of-imminent-threat-from-critical-windchill-flexplm-rce-bug/)**
  - Windchill is the dominant PLM platform in aerospace and defense — used for weapons system design, engineering data management, and manufacturing process control. German federal police dispatched officers overnight to alert affected companies. PTC notes "credible evidence of imminent threat from a third-party group." An attacker with RCE on a Windchill server can access engineering designs, BOM data, supplier information, and export-controlled technical documentation.
  - **Insider threat angle**: If an adversary establishes a foothold via CVE-2026-4681, they could masquerade as a legitimate insider while exfiltrating controlled technical data. Combine this with the APT28 credential harvesting TTPs above and the attack surface is significant.

- **[Mazda Supply Chain Partner Data Exposure](https://www.bleepingcomputer.com/news/security/mazda-discloses-security-breach-exposing-employee-and-partner-data/)** — Mazda's supply chain incident exposed partner credentials that could be used for spear-phishing attacks against aerospace/automotive cross-sector suppliers. Mazda's supplier ecosystem includes aerospace-adjacent precision manufacturing firms.

---

## 📰 Other Noteworthy

- **[Russian Botnet Operator Sentenced to 2 Years for BitPaymer Ransomware Support](https://www.bleepingcomputer.com/news/security/russian-man-sentenced-for-operating-botnet-used-in-ransomware-attacks/)** — Russian national convicted for managing a phishing botnet used in BitPaymer ransomware attacks against 72 U.S. companies. Highlights ongoing DOJ enforcement against ransomware infrastructure operators; sentencing may have deterrent effect on access brokers.
- **[Yanluowang IAB Gets 81 Months in Prison](https://www.bleepingcomputer.com/news/security/yanluowang-ransomware-access-broker-gets-81-months-in-prison/)** — Russian initial access broker for Yanluowang ransomware sentenced to nearly 7 years. Two significant sentencings in one week signals increased prosecution pressure on ransomware ecosystem.
- **[FCC Bans All Non-US-Made Routers](https://www.bleepingcomputer.com/news/security/fcc-bans-new-routers-made-outside-the-usa-over-security-risks/)** — FCC updated its Covered List to ban all consumer routers manufactured outside the U.S., citing national security risks. Relevant for any org procuring network equipment; supply chain integrity now a regulatory compliance issue.

---

*Report generated by C3PO 🤖 | Week of March 17–24, 2026 | Sources linked inline*
