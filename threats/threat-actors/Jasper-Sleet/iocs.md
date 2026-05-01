# IOCs — Jasper Sleet / Storm-0287 (Scoundrel #032)

> Source: Microsoft Threat Intelligence (June 2025, December 2025, April 2026), DOJ Indictment (January 2025)
> **Note:** Jasper Sleet's primary attack surface is HR and identity systems — not traditional malware.
> IOCs here are behavioral, identity, and infrastructure-based rather than file hash / C2 focused.
> TLP: WHITE | Last updated: 2026-05-01

---

## ⚠️ IOC Caveat

Traditional malware IOCs (file hashes, C2 IPs, domains) are of limited value for Jasper Sleet. The actor uses **legitimate services, legitimate credentials, and legitimate tools**. Detection depends on behavioral anomalies and identity telemetry rather than network or endpoint signatures.

---

## Infrastructure Indicators

### VPN / Proxy Services
| Indicator | Type | Notes |
|---|---|---|
| Astrill VPN | VPN service | Consistently observed masking Jasper Sleet operator geolocations; flag new hire sessions from Astrill egress IPs |
| Commercial VPN egress IPs (rotating) | IP range | Cross-reference sign-in IPs for new hires against known VPN egress lists |
| Azure Virtual Desktop (AVD) login fingerprint | Session pattern | Russian-based AVD instances used to proxy into US-based sessions; fingerprinted in sign-in logs |

### RMM Tools (Post-Onboarding Persistence)
| Tool | Notes |
|---|---|
| JumpConnect | Observed post-onboarding; flag installation by new hires |
| TinyPilot | Hardware-level remote access; flag if installed on company hardware |
| RustDesk | Open-source RMM; flag on corporate endpoints outside approved list |
| AnyDesk | Flag if installed by new hire account within first 30 days |

---

## Behavioral IOCs — Identity & HR Layer

| Indicator | System | Threshold / Alert |
|---|---|---|
| Impossible travel — new hire account | Microsoft Entra ID / AAD | Any impossible travel event for accounts < 90 days old → immediate investigation |
| Single MFA device completing auth for 2+ accounts | MFA platform logs | 2+ accounts sharing device fingerprint → investigate |
| New hire payroll/bank update from VPN or foreign IP | Workday | Any payroll change from non-corporate IP for new hire → block + investigate |
| Workday `hrrecruiting/*` API calls in repeating pattern from external accounts | Workday / Defender for Cloud Apps | >5 calls to same API endpoint from same external IP across multiple fake accounts |
| Login from US IP → immediate login from Russia/China/SE Asia | Sign-in logs | Geolocation delta > 1,000km within < 60 minutes |
| New hire account accessing SharePoint/OneDrive at high volume in first 30 days | Defender for Cloud Apps | Baseline and alert on outliers vs. peer group |
| New hire sessions matching AVD login pattern from Russian ASNs | Sign-in logs | Cross-reference ASN of sign-in IP |

---

## Behavioral IOCs — Recruiting / HR Process

| Indicator | Notes |
|---|---|
| Candidate refuses or repeatedly defers video camera during interviews | HR process flag |
| Interview audio quality inconsistent with stated location / equipment | HR process flag |
| Inconsistent background details across interview stages | HR process flag |
| Unusual urgency to complete payroll/bank setup immediately after hire | HR process flag |
| Hardware delivery address is a residential address in a known laptop farm city | HR process flag |
| Candidate uses same name/profile across multiple applications to different organizations | Cross-org threat intel |
| Application language exactly mirrors job posting terminology | AI-generated resume indicator |
| Multiple applications submitted to same job portal from same infrastructure | HR platform API telemetry |

---

## Microsoft Defender Detection Alerts

When enabled, the following Microsoft Defender alerts are associated with Jasper Sleet activity:

| Alert Name | Platform |
|---|---|
| `Possible Jasper Sleet threat actor activity in Workday Recruiting Web Service` | Defender for Cloud Apps — Workday |
| `Suspicious Payroll and Finance related activity in Workday` | Defender for Cloud Apps — Workday |
| `Impossible travel` (for new hire accounts) | Microsoft Defender XDR |
| `Sign-in activity by suspected North Korean entity Jasper Sleet` | Microsoft Defender XDR |
| `Microsoft Defender Experts: Potential malicious activity linked to threat actor observed in your environment` | Defender Experts Notification |

---

## Hunting Queries (Microsoft Defender XDR / KQL)

```kql
// Hunt 1: Impossible travel for new hire accounts (< 90 days)
SigninLogs
| where TimeGenerated > ago(90d)
| where RiskEventTypes contains "impossibleTravel"
| join kind=inner (
    AuditLogs
    | where OperationName == "Add user"
    | where TimeGenerated > ago(90d)
    | project UserId = tostring(TargetResources[0].id), CreatedDate = TimeGenerated
) on $left.UserId == $right.UserId
| where TimeGenerated > CreatedDate  // Sign-in after account creation
| project TimeGenerated, UserPrincipalName, IPAddress, Location, CreatedDate

// Hunt 2: MFA device shared across multiple accounts
SigninLogs
| where TimeGenerated > ago(30d)
| where AuthenticationDetails contains "MFA"
| summarize AccountCount = dcount(UserPrincipalName),
            Accounts = make_set(UserPrincipalName),
            LoginCount = count()
  by DeviceDetail, bin(TimeGenerated, 1d)
| where AccountCount >= 2
| order by AccountCount desc

// Hunt 3: Workday payroll changes from foreign/VPN IPs for new hires
CloudAppEvents
| where Application == "Workday"
| where ActionType has_any ("Bank", "Payment", "Tax", "Payroll")
| where not(IPAddress matches regex @"^10\.|^192\.168\.|^172\.(1[6-9]|2[0-9]|3[01])\.")
| project Timestamp, AccountId, ActionType, IPAddress, CountryCode, City

// Hunt 4: New hire comms from external Teams with suspicious IPs
CloudAppEvents
| where Application == "Microsoft Teams"
| where IsExternalUser == true
| where IPAddress != ""
| summarize EventCount = count(), Actions = make_set(ActionType)
  by IPAddress, AccountId, bin(Timestamp, 1d)
| where EventCount > 10  // High-volume external comms

// Hunt 5: Recruiting email from suspicious senders
EmailEvents
| where Subject has_any ("Interview", "Application", "Offer Letter", "Onboarding", "Start Date")
| where SenderFromDomain !in (known_internal_domains)
| where DeliveryAction !in ("Blocked", "FilteredAsSpam")
| project Timestamp, SenderMailFromAddress, SenderIPv4, RecipientEmailAddress, Subject
```

---

*IOC file created: 2026-05-01 | Author: C3PO | TLP: WHITE*
