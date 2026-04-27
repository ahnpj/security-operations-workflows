# Phishing Attachment Analysis Report

---

## Section 1: Email Description and Artifacts Collected

A suspicious email attachment was identified and analyzed as part of a phishing investigation. The attachment was presented as a standard file but was determined to be an HTML document designed to render a login interface when opened in a browser.

Upon execution, the attachment displayed a page impersonating a Microsoft Outlook / Office365 login portal. The page attempted to capture user credentials by prompting the user to enter their email and password, indicating a credential harvesting attempt.

### Collected Artifacts

**Attachment Name:**  
MICROINV-US1070822.html  

**File Type:**  
HTML Document (.html)  

**File Size:**  
14.6 KB  

**SHA256 Hash:**  
0FC57290189ADECFE17F6BACBC515ED080CD63C54B282FDEF4EF3DFC598CDD1E  

**Impersonated Service:**  
Microsoft Outlook / Office365  

**Pre-filled Victim Email:**  
contact@securityblue.team  

**Credential Submission Endpoint (sanitized):**  
hxxp://<malicious-domain>/off.php  

---

## Section 2: Artifact Analysis

### File and Metadata Analysis

Initial inspection of the attachment confirmed that the file uses an `.html` extension rather than a traditional document format. This indicates that the file is intended to be rendered in a browser rather than opened as a passive document.

The file size (14.6 KB) is consistent with a lightweight phishing page rather than a legitimate document containing meaningful content. A SHA256 hash was generated to uniquely identify the file and support future correlation or threat intelligence lookups.

---

### Rendered Page and Impersonation Analysis

When opened in a browser, the attachment rendered a login interface designed to mimic a Microsoft Outlook / Office365 authentication portal. The structure, layout, and overall design closely resembled a legitimate enterprise login experience.

The page included a pre-filled email address (`contact@securityblue.team`), indicating that the phishing artifact may be targeted rather than generic. Prepopulation of user data is a common tactic used to increase credibility and reduce friction during credential entry.

---

### Source Code and Decoding Analysis

Inspection of the page source revealed that the HTML content was encoded within a JavaScript function using URL encoding. This technique is commonly used to obscure the underlying structure of phishing pages and make manual inspection more difficult.

The encoded content was extracted and decoded using CyberChef, revealing the full HTML structure of the phishing page. Analysis of the decoded content identified references to Microsoft branding assets, reinforcing the conclusion that the page was intentionally designed to impersonate a legitimate Microsoft login portal.

---

### Credential Harvesting and Network Behavior

Using browser Developer Tools, network activity was monitored during interaction with the page. Upon entering test credentials and submitting the form, a network request was generated.

The request was observed sending user-supplied credentials to a server-side script (`off.php`) hosted on an external domain not associated with Microsoft.

This behavior confirms that the attachment is not merely a visual imitation of a login page, but an active credential harvesting mechanism designed to capture and transmit user credentials to attacker-controlled infrastructure.

---

### Correlation of Indicators

The following indicators collectively confirm malicious intent:

- HTML attachment designed to render a login interface
- Impersonation of Microsoft Outlook / Office365
- Pre-filled victim email indicating targeted phishing
- Encoded source content used to obscure page structure
- Submission of credentials to a non-legitimate external endpoint
- Mismatch between impersonated service and credential destination

---

## Section 3: Suggested Defensive Measures

### File-Based Controls

- Block the file hash within endpoint protection or EDR systems:
  - SHA256: `0FC57290189ADECFE17F6BACBC515ED080CD63C54B282FDEF4EF3DFC598CDD1E`

- Monitor for similar HTML attachments with comparable structure or naming conventions

---

### Web-Based Controls

- Block the malicious domain hosting the credential collection endpoint (`off.php`) at the web proxy or DNS layer

- Monitor for outbound connections to suspicious domains associated with credential harvesting activity

---

### Email-Based Controls

- Identify and block the originating email if available
- Monitor for similar phishing campaigns using HTML attachments
- Implement filtering for suspicious HTML-based attachments in inbound email traffic

---

### Detection and Monitoring Recommendations

- Search SIEM logs for any connections to the identified malicious domain
- Investigate whether any users opened the attachment or submitted credentials
- Initiate password resets and account monitoring for any potentially affected users
- Review email logs to identify additional recipients of the same attachment

---

## Conclusion

The analyzed attachment was determined to be a malicious HTML-based phishing artifact designed to impersonate a Microsoft login portal and harvest user credentials.

Through file analysis, rendered page inspection, source decoding, and network validation, it was confirmed that the attachment actively captures and transmits credentials to attacker-controlled infrastructure.

Immediate containment actions, including blocking the file hash and associated domain, are recommended to prevent further exposure and mitigate the risk of credential compromise.