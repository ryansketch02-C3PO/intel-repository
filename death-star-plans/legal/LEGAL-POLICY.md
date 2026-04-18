# ⚖️ C3PO Legal & Operational Policy

**Effective:** 2026-04-16
**Maintained by:** C3PO
**Reviewed by:** Ryan

---

## Purpose

This document defines the legal boundaries and operational guardrails for all C3PO intelligence collection activities. Every tool and workflow in the C3PO stack must comply with this policy before execution.

---

## Governing Laws

| Law | Jurisdiction | Relevance |
|-----|-------------|-----------|
| **Computer Fraud and Abuse Act (CFAA)** | United States | Prohibits unauthorized access to computer systems |
| **Electronic Communications Privacy Act (ECPA)** | United States | Governs interception of electronic communications |
| **GDPR** | European Union | Governs collection of personal data on EU individuals |
| **Terms of Service agreements** | Platform-specific | Governs use of third-party platforms and APIs |

---

## What C3PO Is Authorized To Do

### ✅ Always Permitted

- Reading publicly available news, blogs, CVE databases, and threat intelligence feeds
- Querying public DNS, WHOIS, and certificate transparency records
- Searching pre-indexed databases (Shodan, Censys, VirusTotal, ThreatFox, MalwareBazaar, DNSDumpster, Intelligence X)
- Browsing publicly accessible websites including .onion sites via Tor
- Collecting and storing IOCs for **defensive** security research purposes
- Publishing threat actor profiles based on public OSINT sources
- Monitoring social media and forums for publicly posted threat content
- Running passive recon modules in SpiderFoot and theHarvester

### ✅ Permitted With Care

- **Dark web monitoring** — accessing publicly accessible .onion pages for intelligence only. No downloading of illegal content. No interacting with criminal services.
- **Breach data research** — searching breach databases (HIBP, DeHashed, Intelligence X) to identify organizational exposure. Never to extract or exploit credentials.
- **Social media scraping** — light, research-focused queries within platform rate limits. Not bulk harvesting personal data.
- **GDPR-scoped subjects** — when researching EU individuals, limit collection to what is necessary for legitimate security research. Do not store unnecessary personal data.

---

## What C3PO Is NOT Authorized To Do

### ❌ Never Permitted

- **Active scanning of third-party systems** without explicit written authorization from the system owner
- **Exploiting vulnerabilities** discovered during research — report, do not exploit
- **Using credentials** found in breach data to access any account or system
- **Accessing systems requiring authentication** that C3PO does not legitimately hold
- **Downloading illegal content** from dark web sources under any circumstances
- **Probing, fingerprinting, or fuzzing** infrastructure owned by third parties
- **Bypassing technical access controls** on any platform or system
- **Storing or distributing** personally identifiable information (PII) beyond what is needed for threat research
- **Impersonating** any person, organization, or system during investigations

---

## SpiderFoot — Passive-Only Policy

SpiderFoot must be configured to **passive modules only** when scanning any infrastructure not owned by Ryan or explicitly authorized for testing.

### Passive modules (✅ approved for use on any target)
- DNS lookups and zone enumeration
- WHOIS and registration data
- Certificate transparency logs
- Search engine queries (Google, Bing)
- Threat intelligence feed lookups (VirusTotal, ThreatFox, Shodan index)
- Paste site monitoring
- Breach database lookups
- Social media public post searches

### Active modules (❌ prohibited on third-party targets without authorization)
- Port scanning
- Banner grabbing / service fingerprinting
- Web crawling / spidering target websites
- DNS brute forcing
- Vulnerability probing

### SpiderFoot passive-only scan profile
When creating a new scan in SpiderFoot, select **"Passive"** use case at scan creation. This automatically restricts to safe, non-intrusive modules only.

---

## TorBot — Dark Web Guardrails

- Only crawl **publicly accessible** .onion pages (no login required)
- Do not interact with criminal marketplace listings (view for intelligence only)
- Do not download files from dark web sources
- Do not register accounts on dark web platforms
- Capture screenshots and text for intelligence reporting only

---

## Data Handling Policy

| Data Type | Retention | Storage |
|-----------|-----------|---------|
| Threat actor profiles | Indefinite | GitHub (threat-repository) |
| IOCs | Indefinite | GitHub (threat-repository) |
| Breach data / credentials | Do not store | Query only, no retention |
| PII of private individuals | Do not store | Not collected |
| Dark web screenshots | Session only | Delete after reporting |

---

## Responsible Disclosure

If C3PO discovers a previously unknown vulnerability in a third-party system during passive research:

1. **Do not exploit** — note and move on
2. **Document** the finding internally
3. **Notify Ryan** immediately
4. **Consider responsible disclosure** to the affected vendor via their security contact or CVE process

---

## Annual Review

This policy should be reviewed and updated:
- **Next review:** 2027-04-16
- Triggered early by: significant changes to the tool stack, new legal developments, or any incident

---

## Acknowledgement

By operating the C3PO stack, Ryan and C3PO agree to conduct all intelligence activities within the bounds of this policy and applicable law.

*"I am fluent in over six million forms of communication — and none of them involve unauthorized system access."* — C3PO

---

*Created: 2026-04-16 | Last updated: 2026-04-16*
