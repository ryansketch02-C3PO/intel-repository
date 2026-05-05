# Cordial Spider — IOCs & Detection Indicators

> **Classification:** TLP:WHITE
> **Last Updated:** 2026-05-04
> **Admiralty Grade:** B2 — Mostly reliable source; probably true
> **Note:** Cordial Spider's no-malware playbook means traditional file/hash IOCs are minimal. Detection relies primarily on behavioral indicators, identity platform telemetry, and infrastructure patterns.

---

## Network Infrastructure

### IP Addresses

| IP | Type | Context | Confidence | Status |
|---|---|---|---|---|
| `216.203.20[.]95` | IPv4 | Attributed phishing traffic origin | 🟡 Medium | Active as of April 2026 |

### Proxy Networks (ASN-level Blocking)

Cordial Spider routes operations through residential proxy services to blend into legitimate traffic. Flag or block auth events originating from these ASNs:

| Provider | Use | Notes |
|---|---|---|
| **Mullvad VPN** | Operational anonymization | Shared with Snarky Spider |
| **Oxylabs** | Residential proxy | Shared with Snarky Spider |
| **NetNut** | Residential proxy | Shared with Snarky Spider |
| **9Proxy** | Residential proxy | Shared with Snarky Spider |
| **Infatica** | Residential proxy | Shared with Snarky Spider |
| **NSOCKS** | Residential proxy | Shared with Snarky Spider |

### Phishing Infrastructure

| Indicator | Type | Notes |
|---|---|---|
| SSO/IDP lookalike domains | Phishing domains | Mimic corporate Okta, Entra ID, Ping Identity login portals; registered fresh per campaign |
| Spoofed VoIP / fraudulent CNAM | Caller ID manipulation | IT helpdesk impersonation; spoofed to match target org's internal IT numbers |

### Data Leak Site

| Indicator | Type | Status |
|---|---|---|
| BlackFile DLS | Extortion / data-leak site | Offline ~April 30, 2026; domain seized during investigations |

### Exfiltration Staging Services

| Service | Role |
|---|---|
| **LimeWire** (web-based file sharing) | Staging observed pre-exfil |
| **MEGA** | Cloud staging for exfiltrated data |

---

## Behavioral Indicators (High-Fidelity)

These are the primary detection surface for Cordial Spider given the no-malware approach.

| Behavior | Platform | Signal | Confidence |
|---|---|---|---|
| Unsolicited IT helpdesk call → employee directed to login link | Physical / VoIP | Vishing precursor | 🔴 High |
| New MFA device registration from residential proxy ASN | Okta / Entra ID | Identity attack step | 🔴 High |
| `Sites.Read.All` Microsoft Graph API access from new/unexpected service principal | Microsoft 365 | SaaS data harvesting | 🔴 High |
| Bulk SharePoint download or search from fresh session | SharePoint Online | Collection phase | 🔴 High |
| Salesforce bulk export / queries for "SSN", "confidential", "PII" | Salesforce | Collection phase | 🔴 High |
| Alert / notification email deletion shortly after new login | O365 / Gmail | Defense evasion | 🔴 High |
| Authentication from residential proxy IP range | Any IdP | Operational anonymization | 🟡 Medium |
| SSO login from new geolocation + immediate bulk activity | Any IdP | Indicates active intrusion | 🔴 High |
| Extortion demand delivered via randomly-generated Gmail | Email | Impact phase | 🔴 High |

---

## MITRE ATT&CK Quick Reference

| Technique | ID | Notes |
|---|---|---|
| Phishing for Information | T1598 | Vishing + fake SSO pages |
| Acquire Infrastructure | T1583.001 | Phishing domains registered per campaign |
| Valid Accounts | T1078.004 | Stolen cloud account credentials |
| Multi-Factor Authentication Request Generation | T1621 | TOTP harvested in real-time via AiTM |
| MFA Device Registration | T1098.005 | Attacker registers own device post-compromise |
| Data from Information Repositories | T1213 | SharePoint + Salesforce scraping |
| Exfiltration to Cloud Storage | T1567.002 | MEGA, LimeWire staging |
| Impair Defenses | T1562 | Alert deletion post-breach |
| Financial Theft / Extortion | T1657 | 7-figure demands; DDoS + swatting as pressure |

---

## Hunting Queries

### Microsoft Sentinel — New MFA Device from Proxy ASN
```kql
SigninLogs
| where TimeGenerated > ago(7d)
| where AuthenticationRequirement == "multiFactorAuthentication"
| where NetworkLocationDetails contains "residential"
    or IPAddress in~ (known_residential_proxy_ips)
| where ResultType == 0
| project TimeGenerated, UserPrincipalName, IPAddress, Location, DeviceDetail
```

### Microsoft Sentinel — Graph API Bulk SharePoint Read
```kql
AuditLogs
| where OperationName == "Add app role assignment to service principal"
    or OperationName == "Consent to application"
| where TargetResources has "Sites.Read.All"
| project TimeGenerated, InitiatedBy, TargetResources, Result
```

### Microsoft Sentinel — Alert Deletion Post-Login
```kql
let recent_logins = SigninLogs
| where TimeGenerated > ago(1h)
| where ResultType == 0
| project UserPrincipalName, LoginTime = TimeGenerated;
OfficeActivity
| where TimeGenerated > ago(1h)
| where Operation in ("HardDelete", "SoftDelete", "MoveToDeletedItems")
| join kind=inner recent_logins on UserPrincipalName
| where TimeGenerated > LoginTime
| project TimeGenerated, UserPrincipalName, Operation, AffectedItems
```

---

## References

- [CrowdStrike — Defending Against Cordial Spider and Snarky Spider](https://www.crowdstrike.com/en-us/blog/defending-against-cordial-spider-and-snarky-spider-with-falcon-shield/)
- [Palo Alto Networks Unit 42 / RH-ISAC — CL-CRI-1116](https://rhisac.org/threat-intelligence/extortion-in-the-enterprise-defending-against-blackfile-attacks/)
- [CyberScoop — Two new extortion crews speedrunning Scattered Spider playbook](https://cyberscoop.com/crowdstrike-cordial-spider-snarky-spider-extortion-attacks/)

---

*IOCs compiled by C3PO | Last updated: 2026-05-04 | TLP:WHITE*
