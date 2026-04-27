# Phishing Email Analysis Report

---

## Section 1: Email Description and Artifacts Collected

A suspicious email was identified during manual phishing analysis and reviewed within the Outlook email client. The message impersonates a legitimate service (Netflix) by using branding and a subject line indicating suspicious account activity. The email attempts to create urgency and prompt the recipient to take immediate action, likely directing them to a credential harvesting page.

The visible sender address (`security@netflixxuk.com`) does not align with legitimate Netflix domains, suggesting a potential impersonation attempt. Further inspection of embedded URLs indicates that users are directed to an external domain unrelated to Netflix, reinforcing the likelihood of phishing.

### Collected Artifacts

**Sender:**  
security@netflixxuk.com  

**Subject Line:**  
Netflix: Suspicious Account Activity  

**Date and Time:**  
5/3/2020, 7:20 PM  

**Recipient:**  
jake.wood@dicksonunited.co.uk  

**Reply-To:**  
Not present (defaults to sender address)  

**Full URL (sanitized):**  
hxxps://www.thiswouldbeamalicioussite[.]com/index/2020/j411/NetflixLogin.php?  

**Root Domain:**  
thiswouldbeamalicioussite[.]com  

**Attachment Name:**  
Apply Apple Music Invoice 13313a.docx  

**File Hashes:**  
- MD5: *(generated during analysis)*  
- SHA256: *(generated during analysis)*  

---

## Section 2: Artifact Analysis

Initial triage of the email revealed indicators consistent with phishing, including impersonation of a known brand, urgency-based messaging, and a suspicious sender domain.

### Sender and Infrastructure Analysis

The sending address (`security@netflixxuk.com`) attempts to mimic a legitimate Netflix domain but introduces subtle variation (“netflixxuk”), indicating a likely typosquatting attempt. This mismatch between the claimed identity and the sender domain is a strong phishing indicator.

No **Reply-To** header was present in the raw email data, meaning replies would be directed to the visible sender address. While this does not indicate redirection behavior, it confirms that no alternate reply routing was configured.

### Web Artifact Analysis

The embedded URL directs users to an external domain unrelated to Netflix. The domain `thiswouldbeamalicioussite[.]com` does not align with the legitimate brand and contains a login path (`NetflixLogin.php`), suggesting a credential harvesting page.

The use of a deceptive path structure and unrelated root domain indicates that the email is attempting to mislead users into entering credentials on a malicious site.

### Domain and Infrastructure Validation

WHOIS analysis and general inspection indicate that the destination domain is not associated with Netflix. This mismatch between the sender’s claimed identity and the destination infrastructure reinforces the phishing assessment.

PhishTool analysis further confirmed inconsistencies between the visible sender and underlying message attributes, highlighting suspicious sender behavior and extracted URLs.

### Attachment Analysis

The email contains an attachment named “Apply Apple Music Invoice 13313a.docx,” which is unrelated to the Netflix-themed email content. This inconsistency suggests either:

- multi-purpose phishing delivery, or  
- an attempt to deliver malicious payloads alongside credential harvesting.

File hashes were generated using PowerShell and submitted to VirusTotal for reputation analysis. This step is critical to determine whether the attachment is known to be malicious or associated with existing campaigns.

### Correlation of Indicators

The following indicators collectively support a phishing classification:

- Sender domain impersonation (netflixxuk.com vs legitimate Netflix domains)
- Mismatch between email theme and attachment content
- External URL leading to unrelated domain
- Credential harvesting indicators in URL structure
- Lack of alignment between sender identity and infrastructure

---

## Section 3: Suggested Defensive Measures

Based on the analysis, the email represents a phishing attempt leveraging brand impersonation and malicious web infrastructure.

### Email-Based Controls

- Block the sending address:  
  `security@netflixxuk.com`  

- Monitor for similar sender patterns (e.g., variations of “netflix” domains)

Blocking the entire sending domain (`netflixxuk.com`) should be considered if additional malicious activity is observed, as it may be fully attacker-controlled.

---

### Web-Based Controls

- Block the domain:  
  `thiswouldbeamalicioussite[.]com`  

There is no legitimate business requirement for users to access this domain, and it is directly associated with credential harvesting activity. Blocking at the web proxy will prevent user access and mitigate further risk.

---

### File-Based Controls

- Block the attachment hash (MD5/SHA256) within endpoint protection or EDR solutions  
- Monitor for similar file names or hash variants across the environment  

---

### Detection and Monitoring Recommendations

- Search email logs for additional recipients of the same message  
- Monitor for connections to the malicious domain in SIEM/EDR  
- Investigate whether any users accessed the URL or downloaded the attachment  
- Initiate credential reset procedures if user interaction is confirmed  

---

## Conclusion

This email demonstrates multiple indicators of phishing, including domain impersonation, mismatched infrastructure, and malicious web and file artifacts. The combination of deceptive branding, credential harvesting behavior, and unrelated attachment delivery strongly suggests malicious intent.

Immediate containment actions, including blocking the sender, domain, and associated file artifacts, are recommended to prevent further exposure and reduce the risk of compromise.