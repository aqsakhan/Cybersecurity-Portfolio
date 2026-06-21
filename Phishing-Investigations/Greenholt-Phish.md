# Greenholt Phish - Phishing Email Investigation

## Objective

Analyze a suspicious email reported by an employee and determine whether it is a legitimate communication or a phishing attempt.

---

## Lab Information

- **Platform:** TryHackMe
- **Room:** The Greenholt Phish
- **Category:** Phishing Analysis
- **Room Link:** https://tryhackme.com/room/phishingemails5fgjlzxc

---

## Tools Used

- VirusTotal
- EasyDMARC
- sha256sum
- Email Header Analysis

---

## Investigation Summary

A sales executive reported a suspicious email that appeared to originate from a known customer. The message contained an unexpected money transfer request, a generic greeting, and an attachment, prompting further investigation.

---

## Email Analysis

### Sender Information

| Artifact | Value |
|-----------|---------|
| Display Name | Mr. James Jackson |
| Sender Address | info@mutawamarine.com |
| Reply-To Address | info.mutawamarine@mail.com |

### Observation

The Reply-To address differed from the sender address, which is a common phishing indicator.

---

## Header Analysis

| Artifact | Value |
|-----------|---------|
| Originating IP | 192.119.71.157 |
| IP Owner | HostPapa |

The originating IP address was extracted from the email headers and investigated using VirusTotal.

---

## Email Authentication

### SPF Record

```text
v=spf1 include:spf.protection.outlook.com -all
```

### DMARC Record

```text
v=DMARC1; p=quarantine; fo=1
```

Authentication records were validated using EasyDMARC.

---

## Attachment Analysis

| Artifact | Value |
|-----------|---------|
| File Name | SWT_#09674321____PDF__.CAB |
| SHA256 Hash | 2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f |
| File Size | 400.26 KB |
| Actual File Type | RAR Archive |

### Hash Generation

```bash
sha256sum SWT_#09674321____PDF__.CAB
```

The hash was investigated using VirusTotal.

---

## Findings

- Suspicious financial request
- Mismatched sender and Reply-To addresses
- Compressed archive attachment
- Attachment disguised through file naming
- Multiple phishing indicators identified

---

## Conclusion

Based on sender analysis, email header investigation, email authentication checks, and attachment analysis, the email was identified as a phishing attempt. The attachment represented the primary threat and should not be executed by end users.

---

## Skills Demonstrated

- Email Header Analysis
- Phishing Investigation
- SPF Validation
- DMARC Validation
- Attachment Analysis
- SHA256 Hashing
- VirusTotal Investigation
- IOC Identification
- Security Incident Documentation
