# Phishing Analysis Report — Multi-Email Triage and Artifact Extraction

---

## Section 1: Email Description and Artifacts Collected

A set of five suspicious emails was reviewed as part of a phishing triage exercise. Each email was analyzed to determine whether it should be classified as malicious phishing, spam, scam, newsletter, or suspicious-but-not-malicious content.

After reviewing visible email content and supporting raw message data, **Email One** and **Email Three** were identified as malicious. Email One was consistent with an Amazon-themed phishing message that used sender impersonation and a suspicious call-to-action URL. Email Three was consistent with an NHS-themed malicious attachment delivery attempt where the original attachment had been removed by an email gateway.

Email Two, Email Four, and Email Five were not classified as malicious based on the evidence available during triage. They contained spam, scam, newsletter, or suspicious promotional characteristics, but did not contain the same confirmed malicious indicators as the two phishing emails.

---

### Malicious Email One — Collected Artifacts

**Classification:**  
Amazon-themed phishing / suspected credential harvesting

**Sending Address:**  
`QPE77756@mun.ca`

**Claimed / Friendly Sender Theme:**  
Amazon-related sender presentation using `auto-confirm.info-amazon.co.uk` style branding

**Subject Line:**  
`[INSERT SUBJECT LINE FROM EMAIL ONE]`

**Recipient:**  
`jack.tractive@abcindustries.co.uk`

**Reply-To:**  
`none`

**Date and Time:**  
`[INSERT DATE/TIME FROM EMAIL ONE]`

**Sending Server IP:**  
`68.114.190.29`

**Reverse DNS / Hostname:**  
`[INSERT REVERSE DNS HOSTNAME FROM EMAIL ONE RAW HEADER]`

**Full URL (sanitized):**  
`[INSERT FULL DEFANGED URL FROM EMAIL ONE]`

---

### Malicious Email Three — Collected Artifacts

**Classification:**  
NHS-themed malicious attachment delivery

**Sending Address:**  
`[INSERT SENDING ADDRESS FROM EMAIL THREE]`

**Claimed Sender Theme:**  
UK National Health Service / health slot notification

**Subject Line:**  
`[INSERT SUBJECT LINE FROM EMAIL THREE]`

**Recipient:**  
`[INSERT RECIPIENT FROM EMAIL THREE]`

**Reply-To:**  
`none`

**Date and Time:**  
`[INSERT DATE/TIME FROM EMAIL THREE]`

**Sending Server IP:**  
`[INSERT SENDING SERVER IP FROM EMAIL THREE]`

**Reverse DNS / Hostname:**  
`[INSERT GMAIL REVERSE DNS HOSTNAME FROM EMAIL THREE]`

**Displayed Attachment:**  
`MALICIOUS ATTACHMENT REMOVED - SBT.txt`

**Original Attachment Filename:**  
`[INSERT ORIGINAL MALICIOUS ATTACHMENT FILENAME - .pdf.exe]`

**SHA256 Hash:**  
`[INSERT SHA256 HASH FROM EMAIL THREE]`

---

## Section 2: Artifact Analysis

### Multi-Email Triage Outcome

The five emails were reviewed and classified based on visible message content, sender identity, formatting, language, calls to action, URLs, and attachment behavior.

The malicious messages identified were:

- **Email One** — Amazon-themed phishing email
- **Email Three** — NHS-themed malicious attachment email

The remaining emails were classified as non-malicious within the scope of this triage:

- **Email Two** — spam/scam-themed lottery message
- **Email Four** — spam/newsletter-style content
- **Email Five** — cryptocurrency/investment-themed spam or suspicious promotional content

This classification is important because suspicious emails should not all be escalated as confirmed malicious. Analysts need to distinguish between unwanted content and messages that contain active phishing or malware-delivery behavior.

---

### Email One — Sender and Impersonation Analysis

Email One attempted to appear Amazon-related, but the actual sending address was identified as `QPE77756@mun.ca`. The visible/friendly sender presentation referenced an Amazon-style identity, but the actual sender did not belong to Amazon.

This sender mismatch is a strong phishing indicator because the email attempted to borrow trust from a known brand while using unrelated sender infrastructure.

Additional suspicious indicators included:

- inconsistent formatting and styling,
- generic addressing such as “Amazon user,”
- unnatural grammar and wording,
- and a call-to-action button leading to a refund/help-page themed link.

These indicators collectively support classification as phishing rather than ordinary spam.

---

### Email One — URL Analysis

The full URL was extracted from the email client by copying the link destination from the call-to-action button.

The URL appeared consistent with a likely credential-harvesting flow because it was tied to an Amazon-themed message and appeared to pass recipient-related information into the destination. This behavior can make phishing pages appear more legitimate by pre-filling user information or presenting a personalized login/refund experience.

The URL should be validated and blocked after confirmation, and any user activity associated with the destination should be reviewed in proxy, DNS, or SIEM logs.

---

### Email One — Sending Infrastructure Analysis

The sending server IP was identified as `68.114.190.29`. A live WHOIS/DomainTools-style lookup did not provide a useful current hostname and suggested that ownership context may have changed. Because infrastructure ownership and reverse DNS can change over time, the raw email headers should be treated as the preserved source of delivery context.

This reinforces that analysts should not rely only on live infrastructure lookups when reviewing historical emails. Header data captured at delivery time may preserve context that is no longer visible through current lookups.

---

### Email Three — Sender and Impersonation Analysis

Email Three claimed to represent the UK National Health Service, but the sender was a Gmail address. A Gmail sender is not inherently malicious, but in this context it is suspicious because it does not align with the claimed identity of a public health organization.

Additional suspicious indicators included:

- generic stock imagery rather than official NHS or UK Government branding,
- generic addressing such as “Sir/Madam,”
- urgency designed to pressure the recipient,
- and a call to action instructing the recipient to open an attachment.

This pattern is consistent with malicious attachment delivery using public-service impersonation.

---

### Email Three — Attachment Analysis

The attachment displayed as `MALICIOUS ATTACHMENT REMOVED - SBT.txt`, indicating that an email gateway or security control had removed the original attachment and replaced it with a safe text notice.

The replacement file indicated that the original attachment was an executable disguised as a PDF using a `.pdf.exe` double extension.

This is a strong malicious indicator because it attempts to make a file appear like a document while preserving executable behavior. Users may see the `.pdf` portion and assume the file is safe, especially if file extensions are hidden in Windows.

Because the original attachment was removed, the SHA256 hash provided in the replacement notice should be documented and used for detection and blocking.

---

### Correlation of Indicators

The following indicators collectively support the final classification:

#### Email One

- Amazon-themed impersonation
- Actual sender not associated with Amazon
- Generic greeting and poor grammar
- Suspicious refund/help-page call to action
- Full URL likely associated with credential harvesting
- Sending infrastructure not aligned with legitimate Amazon communication

#### Email Three

- NHS-themed impersonation
- Gmail sender inconsistent with claimed public health organization identity
- Generic greeting and urgency-based wording
- Call to action instructing the user to open an attachment
- Gateway-replaced malicious attachment
- Original attachment using `.pdf.exe` double-extension behavior
- Provided SHA256 hash for removed malicious file

Taken together, these findings support classification of Email One and Email Three as malicious.

---

## Section 3: Suggested Defensive Measures

### Email-Based Controls

For Email One:

- Block or quarantine messages matching the sender address:
  - `QPE77756@mun.ca`
- Search email logs for messages matching the same subject line, sender, recipient, and URL.
- Consider subject-line or content-based detection if the campaign appears to have reached multiple users.

For Email Three:

- Block or quarantine messages matching the sender address:
  - `[INSERT SENDING ADDRESS FROM EMAIL THREE]`
- Search for messages with similar NHS-themed language, attachment names, or gateway replacement notices.
- Monitor for messages containing the same original attachment filename or hash.

---

### Web-Based Controls

For Email One:

- Validate and block the full malicious URL and root domain after confirmation.
- Search proxy, DNS, or SIEM logs for user connections to the destination.
- Identify whether the recipient clicked the URL or submitted information.

Because the URL appears related to a likely credential-harvesting flow, any confirmed user interaction should trigger account review and possible credential reset workflows.

---

### File-Based Controls

For Email Three:

- Block the SHA256 hash associated with the removed malicious attachment:
  - `[INSERT SHA256 HASH FROM EMAIL THREE]`
- Add the original filename to detection or hunting logic:
  - `[INSERT ORIGINAL MALICIOUS ATTACHMENT FILENAME - .pdf.exe]`
- Monitor for other attachments using double extensions such as `.pdf.exe`, `.doc.exe`, or `.xlsx.exe`.

---

### Detection and Monitoring Recommendations

- Search mail logs for all recipients of Email One and Email Three.
- Review proxy/DNS/SIEM telemetry for connections to the Email One URL.
- Review endpoint telemetry for any execution attempts related to the Email Three attachment hash or filename.
- Confirm whether the email gateway removed the attachment before delivery or whether any user received the original executable.
- Notify affected users if malicious messages were delivered to inboxes.
- Initiate credential reset and account monitoring if any user interacted with the phishing URL or attachment.

---

## Conclusion

The multi-email triage identified **Email One** and **Email Three** as malicious. Email One was consistent with Amazon-themed phishing and likely credential harvesting, while Email Three was consistent with NHS-themed malicious attachment delivery involving an executable disguised as a PDF.

The remaining emails were suspicious or unwanted but did not meet the threshold for confirmed malicious classification within this triage exercise.

Immediate response actions should focus on blocking or quarantining the confirmed malicious messages, validating and blocking the malicious URL from Email One, documenting the removed attachment filename and SHA256 hash from Email Three, and reviewing logs to determine whether any users interacted with the malicious artifacts.
