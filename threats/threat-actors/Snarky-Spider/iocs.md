# Snarky Spider — IOCs & Detection Indicators

> **Classification:** TLP:WHITE
> **Last Updated:** 2026-05-04
> **Admiralty Grade:** B2 — Mostly reliable source; probably true
> **Note:** Like Cordial Spider, Snarky Spider's no-malware playbook means traditional file/hash IOCs are minimal. Detection relies on behavioral signals, identity telemetry, and physical security awareness. Key differentiator: Snarky Spider is more aggressive — **swatting of executives is an active TTP**.

---

## Network Infrastructure

### Proxy Networks (ASN-level Blocking)

Snarky Spider uses the same residential proxy stack as Cordial Spider but via separate accounts/routing to maintain operational separation:

| Provider | Use | Notes |
|---|---|---|
| **Mullvad VPN** | Operational anonymization | Shared with Cordial Spider |
| **Oxylabs** | Residential proxy | Shared with Cordial Spider |
| **NetNut** | Residential proxy | Shared with Cordial Spider |
| **9Proxy** | Residential proxy | Shared with Cordial Spider |
| **Infatica** | Residential proxy | Shared with Cordial Spider |
| **NSOCKS** | Residential proxy | Shared with Cordial Spider |

### Phishing Infrastructure

| Indicator | Type | Notes |
|---|---|---|
| SSO/IDP lookalike domains | Phishing domains | Registered via **different providers** than Cordial Spider — distinct domain registrars/hosting |
| Spoofed VoIP | Caller ID spoofing | IT helpdesk impersonation; smishing also observed in addition to vishing |
| AiTM (Adversary-in-the-Middle) phishing pages | Phishing | Real-time TOTP harvesting infrastructure |

### Data Leak Site

| Indicator | Type | Status |
|---|---|---|
| Snarky Spider DLS | Extortion / data-leak site | Details limited in public reporting; separate from BlackFile |

---

## Behavioral Indicators (High-Fidelity)

### Identity / Authentication Layer

| Behavior | Platform | Signal | Confidence |
|---|---|---|---|
| Unsolicited IT helpdesk call or SMS → employee directed to login link | VoIP / SMS | Vishing/smishing precursor | 🔴 High |
| New MFA device registration from residential proxy or unusual ASN | Okta / Entra ID / Ping | Identity takeover step | 🔴 High |
| MFA device type distinct from Cordial Spider patterns | Okta / Entra ID | Differentiator for attribution | 🟡 Medium |
| SSO login from residential proxy → immediate bulk SaaS activity | Any IdP | Active intrusion indicator | 🔴 High |
| Alert / inbox deletion shortly after new login event | O365 / Gmail | Defense evasion | 🔴 High |

### SaaS Collection Layer

| Behavior | Platform | Signal | Confidence |
|---|---|---|---|
| `Sites.Read.All` or `Files.ReadWrite.All` from unexpected principal | Microsoft Graph | Collection phase | 🔴 High |
| Bulk SharePoint download from new session/device | SharePoint | Data harvesting | 🔴 High |
| Salesforce internal search for PII terms ("SSN", "confidential") | Salesforce | Data harvesting | 🔴 High |
| Cloud storage staging to file-sharing services | Cloud | Pre-exfiltration staging | 🟡 Medium |

### Physical / Escalation Layer ⚠️

| Behavior | Signal | Action Required |
|---|---|---|
| Unexpected emergency services contact (police, fire, SWAT) at exec/employee home or office | **Swatting** — active Snarky Spider escalation TTP | Immediate: notify facilities security + local LE proactively; treat as active extortion indicator |
| Extortion demand received by target company | Impact phase confirmed | Correlate with any recent auth anomalies |
| DDoS activity against public-facing infrastructure | Pressure tactic | Correlate with extortion demand timeline |

---

## Distinguishing Snarky Spider vs. Cordial Spider

| Signal | Implication |
|---|---|
| MFA device type is different from BlackFile/CL-CRI-1116 pattern | Snarky Spider vs. Cordial Spider attribution |
| Domain registrar / hosting differs from Cordial Spider infrastructure | Separate operational cell |
| Swatting occurs — not just DDoS | Snarky Spider (more aggressive) |
| Activity hours differ from Cordial Spider baseline | Separate operators, different time zones |

---

## MITRE ATT&CK Quick Reference

| Technique | ID | Notes |
|---|---|---|
| Phishing | T1566.001 / T1566.002 | Email + smishing used alongside vishing |
| Vishing | T1566.004 | Primary initial access vector |
| Acquire Infrastructure | T1583.001 | AiTM phishing portals, different registrars than Cordial Spider |
| Valid Accounts | T1078.004 | Stolen cloud credentials |
| MFA Request Generation | T1621 | Real-time TOTP harvest via AiTM |
| MFA Device Registration | T1098.005 | Different device type than Cordial Spider |
| Data from Information Repositories | T1213 | SharePoint, Salesforce, SaaS repositories |
| Exfiltration to Cloud Storage | T1567.002 | File-sharing staging services |
| Impair Defenses | T1562 | Alert/notification deletion |
| Extortion | T1657 | Aggressive demands; no negotiation |
| DDoS | T1498 | Pressure against non-payers |
| Physical Coercion (Swatting) | — | No ATT&CK code; real-world escalation via false LE calls |
| Cryptocurrency Theft | T1657 | CrowdStrike notes crypto theft in addition to extortion |

---

## Hunting Queries

### Microsoft Sentinel — New MFA Device from Proxy (same as Cordial Spider)
```kql
SigninLogs
| where TimeGenerated > ago(7d)
| where AuthenticationRequirement == "multiFactorAuthentication"
| where NetworkLocationDetails contains "residential"
| where ResultType == 0
| project TimeGenerated, UserPrincipalName, IPAddress, Location, DeviceDetail
| order by TimeGenerated desc
```

### Okta System Log — New Authenticator Enrollment from Proxy
```
eventType eq "user.mfa.factor.activate" 
AND client.ipAddress in (<residential_proxy_ranges>)
```

### Generic — Swatting Awareness Correlation
```
If LE contact event occurs at exec location:
1. Pull all auth events for that exec's accounts in past 72h
2. Check for new MFA device registrations
3. Check for bulk data access from new session
4. Cross-reference with any recent extortion email receipts
→ Treat swatting as a lagging IR indicator, not just a physical incident
```

---

## Physical Security Recommendations

Given Snarky Spider's confirmed swatting TTP:
1. **Proactive LE notification:** When an org identifies active Snarky Spider intrusion, notify local law enforcement for exec residences *before* a swatting call is made
2. **Exec briefing:** All executives under active extortion situations should be briefed on swatting risk and provided guidance on how to respond to emergency services contact
3. **Security detail consideration:** For high-value targets with confirmed active extortion, consider temporary physical security measures

---

## References

- [CrowdStrike — Snarky Spider Adversary Profile](https://www.crowdstrike.com/en-us/adversaries/snarky-spider/)
- [CrowdStrike — Defending Against Cordial Spider and Snarky Spider](https://www.crowdstrike.com/en-us/blog/defending-against-cordial-spider-and-snarky-spider-with-falcon-shield/)
- [CyberScoop — Two new extortion crews speedrunning Scattered Spider playbook](https://cyberscoop.com/crowdstrike-cordial-spider-snarky-spider-extortion-attacks/)

---

*IOCs compiled by C3PO | Last updated: 2026-05-04 | TLP:WHITE*
