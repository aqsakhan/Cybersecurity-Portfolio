# Snapped Phish-ing Line - Phishing Campaign Investigation

## Objective

Investigate a phishing campaign targeting employees at SwiftSpend Financial, identify the phishing infrastructure used by the attacker, analyze the phishing kit, and uncover indicators of compromise.

---

## Lab Information

- **Platform:** TryHackMe
- **Room:** Snapped Phish-ing Line
- **Category:** Phishing Analysis
- **Room Link:** https://tryhackme.com/room/snappedphishingline

---

## Scenario

Multiple employees at SwiftSpend Financial reported receiving suspicious emails. Several users had already submitted their credentials and were subsequently locked out of their accounts. The incident was escalated to determine how the attack worked, identify affected users, and uncover additional attacker infrastructure.

---

## Tools Used

- VirusTotal
- CyberChef
- Firefox Browser
- Linux Terminal
- sha256sum
- unzip
- cat
- find

---

## Investigation Process

### 1. Email Analysis

The phishing emails were reviewed to identify recipients and sender information.

| Artifact | Value |
|-----------|---------|
| Recipient Identified | William McClean |
| Malicious Sender | Accounts.Payable@groupmarketingonline.icu |

### Findings

- The sender impersonated a legitimate accounts payable department.
- The email contained a malicious attachment designed to redirect users to a phishing page.

---

### 2. Phishing URL Investigation

The attachment was opened and inspected to determine where victims were being redirected.

| Artifact | Value |
|-----------|---------|
| Root Domain | kennaroads.buzz |
| Impersonated Brand | Microsoft |

### Findings

- Victims were redirected to a fake Microsoft login page.
- The phishing page was hosted on attacker-controlled infrastructure.

---

### 3. Exposed Directory Discovery

Directory enumeration of the phishing infrastructure revealed publicly accessible files.

Discovered URL:

```text
http://kennaroads.buzz/data/
```

An exposed directory listing contained the phishing kit archive.

| Artifact | Value |
|-----------|---------|
| Archive Name | Update365.zip |

### Screenshot

<img width="1078" height="541" alt="image" src="https://github.com/user-attachments/assets/91940dde-ad3b-4180-a982-061f359b6102" />

---

### 4. Phishing Kit Analysis

The phishing kit archive was downloaded and analyzed.

#### SHA256 Hash

```text
ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686
```

Hash generated using:

```bash
sha256sum Update365.zip
```

### VirusTotal Results

| Artifact | Value |
|-----------|---------|
| Additional Threat Category | Trojan |
| Files Contained in Archive | 49 |

### Findings

- VirusTotal identified the archive as phishing-related.
- The archive also carried a Trojan classification.
- The phishing kit contained multiple files used to support credential harvesting.

---

### 5. Credential Theft Investigation

The exposed phishing kit directory contained logs of harvested credentials.

Location:

```text
/data/Update365/
```

A review of the log files revealed captured user activity.

| Artifact | Value |
|-----------|---------|
| Repeated Victim Email | michael.ascot@swiftspend.finance |

### Findings

- The victim submitted credentials more than once.
- This indicates successful interaction with the phishing page.

---

### 6. Attacker Infrastructure Analysis

The phishing kit was extracted for further analysis.

Command used:

```bash
unzip Update365.zip
```

The credential collection script was identified.

Command used:

```bash
find . -name "submit.php"
```

Analysis of the PHP file revealed the attacker-controlled mailbox receiving stolen credentials.

| Artifact | Value |
|-----------|---------|
| Credential Collection Email | m3npat@yandex.com |

### Findings

- Stolen credentials were being exfiltrated directly to an external Yandex mailbox.
- This confirmed the attacker's collection mechanism.

---

### 7. Hidden Resource Discovery

Further analysis of the phishing infrastructure revealed a hidden file.

Location:

```text
kennaroads.buzz/data/Update365/office365/flag.txt
```

The contents were Base64 encoded.

Encoded Value:

```text
fUxSVV8zSHRfaFQxd195NExwe01IVAo=
```

CyberChef was used to:

1. Decode from Base64
2. Reverse the resulting string

Recovered Value:

```text
THM{pL4y_w1Th_tH3_URL}
```

---

## Indicators of Compromise (IOCs)

### Email Addresses

```text
Accounts.Payable@groupmarketingonline.icu
m3npat@yandex.com
```

### Domains

```text
kennaroads.buzz
```

### File Names

```text
Update365.zip
```

### Hashes

```text
ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686
```

---

## Conclusion

The investigation revealed a phishing campaign impersonating Microsoft to steal employee credentials. The attacker hosted a publicly accessible phishing kit on the domain kennaroads.buzz, exposing both operational files and harvested credentials. Analysis of the phishing kit identified the attacker's credential collection email address and confirmed the use of infrastructure designed to capture and exfiltrate victim credentials.

---

## Skills Demonstrated

- Phishing Investigation
- Email Analysis
- URL Analysis
- Directory Enumeration
- Threat Intelligence Research
- VirusTotal Analysis
- Hash Analysis
- Credential Theft Investigation
- IOC Identification
- Open Source Intelligence (OSINT)
- Incident Documentation
