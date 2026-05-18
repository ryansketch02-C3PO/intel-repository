# UAT-8302 — Indicators of Compromise (IOCs)

> **Last Updated:** 2026-05-18
> **Admiralty Grade:** A1 | TLP:CLEAR
> ⚠️ **Tooling is shared across multiple China-nexus APT clusters. Detection of these tools is high-value but cannot alone attribute activity to UAT-8302 specifically. Treat any detection as a priority China-nexus escalation.**

---

## Malware Families

| Tool | Type | Notes |
|---|---|---|
| **NetDraft (NosyDoor)** | .NET/C# backdoor | Variant of FinalDraft/SquidDoor; unique identifier for this cluster's operations |
| **SNOWRUST** | Rust-based stager | New variant unique to UAT-8302; downloads VShell payload |
| **CloudSorcerer v3.0** | Cloud-service backdoor | Updated from 2024 Russia-targeting version; cloud C2 |
| **VShell** | Backdoor | Delivered by SNOWLIGHT/SNOWRUST |
| **SNOWLIGHT** | VShell stager | Shared with UNC5174, UNC6586, UAT-6382 |
| **DeedRAT (Snappybee)** | ShadowPad successor | Deployed alongside ZingDoor |
| **ZingDoor** | Backdoor | Deployed with DeedRAT |
| **Draculoader** | Shellcode loader | Loads Crowdoor and HemiGate |
| **Crowdoor** | Implant | Delivered by Draculoader |
| **HemiGate** | Implant | Delivered by Draculoader |

---

## Utility Tools (Dual-Use)

| Tool | Purpose | Notes |
|---|---|---|
| **gogo** | Network scanning | Open-source; used for post-compromise reconnaissance |
| **Impacket** | Credential extraction / lateral movement | Python suite; commonly abused by APTs |
| **Stowaway** | Proxy | Alternative backdoor access channel |
| **SoftEther VPN** | Traffic obfuscation | C2 tunneling and traffic blending |

---

## Behavioral Indicators

| Indicator | Stage | Notes |
|---|---|---|
| .NET process making outbound HTTPS to cloud storage APIs | C2 | NetDraft/CloudSorcerer C2 pattern |
| Rust binary (SNOWRUST) executing VShell payload in memory | Execution | SNOWRUST→VShell stager chain |
| gogo network scanner execution | Discovery | Post-compromise recon |
| SoftEther VPN service registration | Persistence | C2 obfuscation |
| Stowaway proxy process | C2 | Alternative access channel |
| Impacket execution outside authorized admin windows | Lateral Movement | Credential extraction / pass-the-hash |
| DeedRAT (Snappybee) DLL in non-standard path | Persistence | Unique to Earth Estries / UAT-8302 cluster |

---

## Detection Priorities

1. **NetDraft/NosyDoor signatures** — Cisco Talos technical report includes YARA/Snort rules; apply immediately
2. **SNOWRUST Rust binary** — Unusual for most environments; Rust-compiled malware detection is available in modern EDR
3. **CloudSorcerer C2 patterns** — Cloud API (OneDrive/Dropbox) connections from non-standard process trees
4. **DeedRAT (Snappybee)** — High-confidence China-nexus indicator; available signatures
5. **SoftEther VPN + Stowaway** — Both are legitimate tools weaponized; detect unexpected service registrations

---

## References

- Cisco Talos Technical Report: https://blog.talosintelligence.com/uat-8302/
- Snort/YARA rules: Available in Cisco Talos GitHub repository (see Talos blog for link)

---

*IOCs compiled: 2026-05-18 | Source: Cisco Talos | Admiralty Grade: A1 | TLP:CLEAR*
