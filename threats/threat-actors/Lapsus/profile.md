# Lapsus$ — Strawberry Tempest / DEV-0537

## Identity

| Field | Details |
|---|---|
| **Name** | Lapsus$ (stylized LAPSUS$) |
| **Microsoft Tracking** | Strawberry Tempest · DEV-0537 |
| **Other Aliases** | None widely adopted — the Lapsus$ name is the universal identifier |
| **Type** | Cybercriminal — Extortion and Data Destruction (no ransomware) |
| **Origin** | UK (primary) and Brazil; international membership |
| **Active** | Late 2021 – September 2022 (primary campaign period) |
| **Current Status** | ⚠️ **DISPERSED** — Original collective inactive since late 2022; key members convicted; techniques live on in Scattered Spider, The Com, and ShinyHunters collective (2025 merger reported) |
| **Threat Level** | 🟡 MEDIUM (historical; techniques 🔴 HIGH relevance) |
| **Primary Motivation** | Notoriety + financial extortion + destruction |
| **Estimated Impact** | Source code from Microsoft, Nvidia, Samsung, EA, Ubisoft, Rockstar; identity platform access via Okta; T-Mobile network access; Brazilian government disruption |
| **Key Members** | **Arion Kurtaj** (Oxford, UK; age 16–17 during attacks; indefinite hospital order December 2023) · **Unnamed 17-year-old** (UK; Youth Rehabilitation Order) · **Brazilian member** (arrested Bahia, October 2022) |
| **Total Arrested** | 9 (7 UK arrests March 2022; 2 additional re-arrests / Brazilian arrest) |
| **Convictions** | Kurtaj — indefinite psychiatric hospital order; unnamed minor — 18-month Youth Rehabilitation Order |
| **Admiralty Grade** | A1 — UK court convictions; DHS CSRB report; Microsoft MSTIC; multiple victim confirmations |
| **ATT&CK** | T1078 (Valid Accounts) · T1621 (MFA Request Generation) · T1539 (Steal Web Session Cookie) · T1598 (Phishing for Information) · T1586 (Compromise Accounts — SIM Swap) · T1190 (Exploit Public-Facing App) |

---

## Overview

Lapsus$ is the most consequential proof-of-concept in the history of enterprise social engineering. Between late 2021 and September 2022, a loose collective of teenagers — primarily from the UK and Brazil — broke into EA, Nvidia, Samsung, Microsoft, Okta, T-Mobile, Uber, and Rockstar Games. They did it without a single custom zero-day exploit. No sophisticated malware. No nation-state backing.

They called help desks and asked to reset passwords. They texted employees offering cash for credentials. They spammed Microsoft Authenticator push notifications until someone clicked "Approve." They bought session tokens on Telegram. And when they got inside, they leaked everything publicly, in real time, to 50,000 Telegram followers — including the biggest gaming source code leak in history.

**The most famous moment:** On September 18, 2022, Arion Kurtaj — the group's leader, who was 17 years old and on bail from a prior arrest — logged into Rockstar Games' internal Slack from a budget Travelodge hotel room in Bicester, England. His setup: an Amazon Fire Stick plugged into the hotel TV, his phone as a hotspot. He leaked 90 development clips of *Grand Theft Auto VI* before police arrived. This was after his bail conditions explicitly prohibited him from using the internet. He was re-arrested the next morning.

The DHS Cyber Safety Review Board (CSRB) published a comprehensive post-incident review. Their conclusion: Lapsus$ succeeded because enterprise identity infrastructure was fundamentally broken, and most organizations had no answer to a motivated attacker who was willing to call your help desk and pretend to be your employee.

---

## Victims and Timeline

| Date | Victim | What Happened |
|---|---|---|
| Jun 2021 | EA | 780 GB source code for FIFA, Frostbite engine — attributed retrospectively |
| Dec 10, 2021 | Brazil Ministry of Health | 50 TB exfiltrated and deleted from AWS/vCenter servers; website defaced with contact info |
| Dec 23–24, 2021 | Correios, Embratel, NET (Brazil) | Brazilian postal service and telecoms; government disruption campaign |
| Jan 3, 2022 | Impresa (Portugal) | Largest Portuguese media company; posted from victim's Twitter; SMS phishing to customers |
| Jan 11, 2022 | Localiza (Brazil) | Car rental company; redirected website visitors to pornographic site |
| Feb 2022 | **Nvidia** | 1 TB data; 71,000+ employee credential hashes leaked; proprietary GPU source code |
| Mar 2022 | **Samsung** | 190 GB source code including Samsung Galaxy biometric unlock algorithms |
| Mar 2022 | Vodafone | Source code and internal documents |
| Mar 2022 | **Okta** (via Sitel contractor) | Access to Okta's customer support admin interface for ~5 days; ~366 organizations potentially affected |
| Mar 20–21, 2022 | **Microsoft** | 37 GB Azure DevOps source code (Bing, Bing Maps, Cortana); FBI appeal for information |
| Mar 17, 2022 | T-Mobile | T-Mobile employee account; T-Mobile source code stolen; attempted (failed) access to FBI/DoD accounts |
| Mar 22, 2022 | Mercado Libre | Argentine e-commerce giant; 300,000 user records |
| Mar 24, 2022 | **7 Members Arrested** (City of London Police) | Group posted "vacation notice" on Telegram; returned six days later |
| Mar 30, 2022 | Globant | IT consultancy; 70 GB data; first post-arrest breach |
| Apr–Aug 2022 | Ubisoft, LG, Revolut, BT/EE | Ongoing lower-profile intrusions while primary members on bail |
| Sep 15, 2022 | **Uber** | Employee MFA fatigue attack; full internal Slack, Google Workspace, AWS, HackerOne bug bounty program accessed |
| Sep 18, 2022 | **Rockstar Games** | GTA VI development footage leaked (90 clips); Kurtaj operating from hotel room on Amazon Fire Stick while on bail |
| Oct 2022 | Brazilian member arrested | "Operation Dark Cloud"; Feira de Santana, Bahia |
| Aug 2023 | Kurtaj trial verdict | Found guilty; ruled unfit to stand trial (autism, severe psychiatric presentation) |
| Dec 2023 | Kurtaj sentenced | Indefinite hospital order; judge cites stated intent to return to cybercrime as soon as possible |

---

## Technical Arsenal — The Identity Attack Playbook

Lapsus$ used **no custom malware in most intrusions**. Their entire toolkit was social engineering layered over legitimate enterprise identity infrastructure.

### Technique 1 — MFA Fatigue (Push Bombing)
Send repeated Microsoft Authenticator / Okta Verify push notifications to a target user. The attacker already has the username and password (from prior phishing, purchase, or breach). They just need the MFA approval. Keep sending until the user:
- Gets annoyed and clicks Approve to make it stop
- Accidentally approves while dismissing a real notification
- Is bombarded while asleep and approves reflexively upon waking

In the Uber incident (September 2022), the attacker pushed MFA prompts for over an hour, then sent a WhatsApp message claiming to be Uber IT support, saying the pushes were legitimate and the user needed to approve. The user approved. Uber's entire internal infrastructure was then accessible.

### Technique 2 — SIM Swapping
Contact a mobile carrier, impersonate the target employee, claim the phone was lost or damaged, request a number port to attacker-controlled SIM. The target's SMS-based MFA codes now route to the attacker. Even SMS-less phone-based MFA (voice calls) can be defeated once the number is ported.

Lapsus$ used this against Nvidia employees, T-Mobile staff, and others. The CSRB report noted that SIM swapping was particularly effective because carriers' identity verification processes were inconsistent and manipulable by a motivated social engineer.

### Technique 3 — Help Desk Social Engineering
Call the corporate IT help desk. Claim to be a locked-out employee. Provide enough personal detail (scraped from LinkedIn, social media, prior data breaches) to pass identity verification. Request a credential reset or MFA bypass. In several incidents, Lapsus$ successfully passed help desk verification and received direct credential resets from enterprise IT staff.

Lapsus$ actively studied their targets' organizational structures, HR systems, and employee data before calling. Microsoft's analysis noted they possessed "intimate knowledge about employees, team structures, help desks, crisis response workflows, and supply chain relationships."

### Technique 4 — Insider Recruitment (Advertising for Access)
Lapsus$ publicly advertised on their Telegram channel — 50,000+ followers — that they were willing to pay employees, contractors, or business partners at targeted companies for:
- Valid corporate credentials
- Approval of MFA prompts (single action, paid per approval)
- Installation of AnyDesk or other remote management tools on corporate workstations

This is outsourced insider threat at scale. The barrier to becoming a Lapsus$ "insider" was a single MFA push approval in exchange for cash. No loyalty, no recruitment, no sustained access required from the insider — just one click.

### Technique 5 — Supply Chain / Third-Party Contractor Targeting
Rather than attacking primary targets directly, Lapsus$ targeted their less-secured third-party vendors:

**Okta via Sitel:** Sitel provided customer support outsourcing to Okta. Lapsus$ compromised a Sitel support engineer's laptop. Sitel had access to a limited Okta admin console for customer support purposes. For approximately 5 days in January 2022, Lapsus$ could interact with Okta's customer identity management on behalf of any customer with an open support ticket.

**Uber via contractor:** A third-party contractor's stolen credentials were used as the initial access point for the September 2022 Uber breach.

### Technique 6 — Session Token Theft and Replay
Deployed Redline password stealer and purchased session tokens from criminal underground forums. Session tokens allow authentication without requiring the original password or MFA factor — they represent an already-authenticated session that can be replayed from a different device.

### Technique 7 — Internal Repository Mining
Once inside, searched JIRA, Confluence, GitLab, and source code repositories for hardcoded credentials, API keys, service account passwords, and internal documentation on security architecture. This enabled rapid lateral escalation from a single compromised account to admin-level access across multiple systems.

---

## The Operational Playbook (Full Sequence)

```
1. RECONNAISSANCE
   Scrape LinkedIn, GitHub, public breach databases for target employee PII
   Study org chart: who has admin access? Who works IT support? Who is in DevOps?
   Map supply chain: what contractors have access? Which MSPs support the target?
        ↓
2. INITIAL ACCESS (pick one or chain):
   a. Phishing → credential harvest
   b. Purchase credentials from dark web marketplace or criminal forum
   c. SIM swap → intercept MFA codes
   d. Pay insider → buy single MFA approval ($)
   e. Target contractor → less-hardened third party with privileged access
        ↓
3. MFA BYPASS (if applicable):
   a. Push bomb — spam until user approves
   b. Help desk call → credential/MFA reset via social engineering
   c. Session token replay — bypass MFA entirely with stolen token
        ↓
4. LATERAL MOVEMENT
   AD Explorer to enumerate all users and groups
   Search code repos for hardcoded secrets, service account credentials
   Exploit internal JIRA/Confluence/GitLab vulnerabilities
   Escalate to admin; access cloud environments (Azure, AWS, GCP)
        ↓
5. EXFILTRATION
   Download source code, internal documentation, employee databases
   Use NordVPN with geographically proximate exit nodes (bypass impossible travel alerts)
        ↓
6. EXTORTION / DESTRUCTION (choose one):
   a. Demand payment to prevent publication
   b. Publish everything publicly to Telegram (50,000+ live audience)
   c. Destroy or delete data rather than just exfiltrating
   d. All of the above
```

---

## The Telegram Theatre

What separated Lapsus$ from every other criminal group of 2022 was their decision to operate entirely in public.

Their Telegram channel had **50,000+ subscribers** who watched intrusions unfold in real time. The group:
- Announced breaches with screenshots as they happened
- Leaked sample data before extortion demands were even made
- Ran **polls asking followers which company to target next** — followers voted, Lapsus$ complied
- Posted the Nvidia extortion demand publicly, including their Bitcoin address
- Made jokes and taunts directed at victim companies' security teams
- Posted a "vacation notice" the day before seven members were arrested
- Returned six days later and immediately breached Globant

This wasn't just chaos. It served a strategic purpose: public leaks generate maximum pressure on victims, demonstrate capability to future targets, and attract both criminal collaborators and would-be insiders looking for a payout. The Telegram audience was simultaneously a PR operation, a recruitment tool, and a demand-amplification mechanism.

---

## A&D / Enterprise Relevance

**Relevance: HIGH — Supply chain and identity infrastructure risk.**

Lapsus$ is technically a historical actor, but its relevance to aerospace and defense is enduring because:

**Nvidia breach:** Nvidia is a critical defense and intelligence supplier — its GPU technology powers AI workloads across DoD, intelligence community, and defense contractors. The leaked source code included GPU driver internals and proprietary hardware documentation. The strategic value of that material to nation-state actors interested in understanding Nvidia's architecture is significant.

**Okta supply chain:** Okta is the identity backbone for thousands of enterprises including defense contractors and their suppliers. The January 2022 Sitel breach gave Lapsus$ admin console access to Okta customer accounts. Any organization that used Okta for identity management during that window was potentially downstream-affected. Defense supply chain organizations are frequent Okta customers.

**T-Mobile and DoD account attempt:** Lapsus$ member "White" explicitly attempted to access FBI and Department of Defense accounts through their T-Mobile breach. The attempt failed — but the intent was documented in court. The vector (telecom employee access to government accounts) is a persistent risk.

**The legacy that matters most:** Lapsus$'s techniques are now industry standard for every social engineering group that came after them. Scattered Spider (#013) runs an evolved version of the same playbook, with the same SIM-swap, push-bomb, and help-desk-call toolkit, now targeting defense and aerospace companies directly. Understanding Lapsus$ is prerequisite to understanding the current threat.

---

## Detection

### MFA Fatigue Detection
```kusto
// Detect abnormal MFA push volume suggesting push bombing
SigninLogs
| where AuthenticationRequirement == "multiFactorAuthentication"
| where ResultType != 0  // Failed attempts
| summarize MfaAttempts = count() by UserPrincipalName, IPAddress, bin(TimeGenerated, 10m)
| where MfaAttempts > 5
| order by MfaAttempts desc

// Alert: MFA approved after sustained failed attempts (possible fatigue attack)
SigninLogs
| where ResultType == 0  // Successful
| join kind=inner (
    SigninLogs
    | where ResultType != 0 and TimeGenerated > ago(1h)
    | summarize FailedAttempts = count() by UserPrincipalName
    | where FailedAttempts > 5
) on UserPrincipalName
| where TimeGenerated > ago(1h)
| project TimeGenerated, UserPrincipalName, IPAddress, Location, FailedAttempts
```

### Session Token Replay Detection
```kusto
// Detect impossible travel or device mismatch on session reuse
SigninLogs
| where ResultType == 0
| summarize 
    Locations = make_set(Location),
    IPs = make_set(IPAddress),
    Devices = make_set(DeviceDetail)
    by UserPrincipalName, bin(TimeGenerated, 1h)
| where array_length(Locations) > 1 or array_length(IPs) > 3
| project TimeGenerated, UserPrincipalName, Locations, IPs
```

### Help Desk Social Engineering Indicators
```kusto
// Alert on account changes following recent MFA failures
AuditLogs
| where OperationName in ("Update user", "Reset user password", "Delete MFA method", "Add MFA method")
| where InitiatedBy.app.displayName contains "Support" or InitiatedBy.user.userPrincipalName contains "helpdesk"
| join kind=leftouter (
    SigninLogs
    | where ResultType != 0
    | summarize RecentFailures = count() by UserPrincipalName, bin(TimeGenerated, 2h)
    | where RecentFailures > 3
) on $left.TargetResources[0].userPrincipalName == $right.UserPrincipalName
| where isnotnull(RecentFailures)
| project TimeGenerated, OperationName, TargetUser = TargetResources[0].userPrincipalName, RecentFailures
```

### Internal Repository Credential Mining
```kusto
// Alert on AD Explorer or bulk AD enumeration
DeviceProcessEvents
| where FileName =~ "AdExplorer.exe" or FileName =~ "AdExplorer64.exe"
| union (
    DeviceProcessEvents
    | where ProcessCommandLine has_any ("Get-ADUser", "Get-ADGroup", "Get-ADComputer")
    | where AccountName !in (known_admin_accounts)
)
| project Timestamp, DeviceName, AccountName, FileName, ProcessCommandLine
```

### Behavioral Indicators

| Indicator | Confidence |
|---|---|
| >5 MFA push failures followed by a single approval from same user | 🔴 HIGH — push bombing |
| Password reset by help desk immediately following failed MFA attempts | 🔴 HIGH — social engineering |
| Session login from new country/device with no prior impossible travel alert | 🔴 HIGH — session token replay |
| Mass export from source code repository or internal documentation system | 🔴 HIGH — exfiltration |
| New AnyDesk/TeamViewer install on corporate endpoint by non-IT user | 🔴 HIGH — insider recruitment completion |
| Employee account querying AD for all users and groups | 🟡 MEDIUM — reconnaissance |
| NordVPN exit node connecting to corporate VPN | 🟡 MEDIUM — operational security matching Lapsus$ pattern |

---

## Key Connections to Other Board Entries

- **#013 Scattered Spider** — Direct lineage. Scattered Spider runs an evolved version of the Lapsus$ social engineering playbook — same SIM swap, MFA fatigue, help desk call, insider recruitment toolkit, same focus on identity infrastructure. Many researchers track overlapping membership or mentorship between the groups. Scattered Spider is Lapsus$ with better operational security and wider targeting.
- **#033 DragonForce** — DragonForce's retail UK campaign (M&S, Co-op, Harrods) used the same Scattered Spider-derived playbook that traces back to Lapsus$. The identity social engineering lineage is continuous.
- **#013 / ShinyHunters collective** — Wikipedia notes a collective including Lapsus$, Scattered Spider, and ShinyHunters formed in 2025 on Telegram. The criminal ecosystem spawned by Lapsus$ remains operationally connected.

---

## What Lapsus$ Proved

The DHS CSRB's 2023 Lapsus$ review identified structural failures across the entire enterprise identity ecosystem. The core findings remain relevant in 2026:

1. **SMS and voice call MFA is effectively broken** against a motivated attacker with SIM-swap capability. Replace with FIDO2 hardware keys.
2. **MFA fatigue is a real attack surface.** Push-approval MFA without number matching or context is defeatable by persistence alone.
3. **Help desks are trust chokepoints.** Any process that allows a phone call to result in a credential reset or MFA bypass is an attack surface. Identity verification at the help desk must be equivalent to the security value of the access being restored.
4. **Supply chain identity is enterprise identity.** Okta's 366 potentially affected customers weren't breached through their own systems — they were breached through a contractor who processed their support tickets. Every third party with admin-adjacent access is a lateral access point.
5. **Teenagers with no budget beat billion-dollar security teams** because those teams were optimized for the wrong threat model. Perimeter security, malware detection, and network monitoring provided no protection against someone who simply called and asked.

---

## Disclosure / Prosecution Timeline

| Date | Event |
|---|---|
| Jun 2021 | EA breach attributed retrospectively to Lapsus$ (780 GB FIFA/Frostbite source code) |
| Dec 2021 | Brazil Ministry of Health attack — Lapsus$ public debut |
| Jan 2022 | Okta / Sitel breach (January 21) |
| Feb 2022 | Nvidia breach — 1 TB; 71,000+ credential hashes |
| Mar 2022 | Samsung, Vodafone, Microsoft (37 GB), T-Mobile, Mercado Libre — rapid-fire breaches |
| Mar 24, 2022 | 7 arrests by City of London Police |
| Mar 30, 2022 | Globant breach — group returns from "vacation" |
| Apr–Aug 2022 | Lower-profile intrusions; members on bail |
| Sep 15, 2022 | Uber breach — MFA fatigue attack via WhatsApp |
| Sep 18, 2022 | Rockstar Games breach — Kurtaj, hotel room, Amazon Fire Stick, bail violation |
| Sep 23, 2022 | Kurtaj re-arrested; operations effectively cease |
| Oct 2022 | Brazilian member arrested ("Operation Dark Cloud") |
| Aug 2023 | UK trial verdict — Kurtaj found guilty; other member found guilty |
| Dec 2023 | Kurtaj sentenced to indefinite hospital order; unnamed minor sentenced to 18-month Youth Rehabilitation Order |
| 2025 | Collective including Lapsus$, Scattered Spider, ShinyHunters reported on Telegram |

---

## References

- [Microsoft MSTIC — DEV-0537 Criminal Actor Targeting Organizations (March 22, 2022)](https://www.microsoft.com/en-us/security/blog/2022/03/22/dev-0537-criminal-actor-targeting-organizations-for-data-exfiltration-and-destruction/)
- [DHS CSRB — Review of the Attacks Associated with LAPSUS$ and Related Threat Groups (August 2023)](https://www.cisa.gov/sites/default/files/2023-08/CSRB_Lapsus%24_508c.pdf)
- [The Hacker News — British LAPSUS$ Teen Members Sentenced for High-Profile Attacks (December 2023)](https://thehackernews.com/2023/12/british-lapsus-teen-members-sentenced.html)
- [Wired — The Teenage Hacker Who Took Down Uber and Rockstar Games](https://www.wired.com/story/arion-kurtaj-trial-lapsus/)
- [Okta — January 2022 Breach Investigation Summary](https://www.okta.com/blog/2022/03/oktas-investigation-of-the-january-2022-compromise/)
- [Forescout — LAPSUS$: The Rise, Fall, and Return of a Hacking Group](https://www.forescout.com/resources/lapsu-the-rise-fall-and-return-of-a-hacking-group/)
- [ExpressVPN — What is Lapsus$? Group tactics and breach timeline (2025)](https://www.expressvpn.com/blog/lapsus/)
- [BleepingComputer — Lapsus$ gang compromised Microsoft employee account](https://www.bleepingcomputer.com/news/security/lapsus-gang-compromised-a-microsoft-employees-account/)

---

*Profile created: 2026-05-07 | Author: C3PO | Scoundrel #036 | Admiralty Grade: A1 | TLP: WHITE*
