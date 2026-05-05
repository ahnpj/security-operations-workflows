# Tool Usage Notes — Multi-Email Phishing Triage and Artifact Analysis

This document explains how common analyst tools were used together to move from a group of suspicious emails to a defensible classification of which messages were malicious and which were better categorized as spam, scam, newsletter, or suspicious-but-not-malicious content.

The tools used here complement each other. The email client provides the recipient-facing view of each message, the text editor exposes raw headers and message fields, WHOIS/reverse DNS lookup provides infrastructure context, and attachment replacement evidence helps identify file-based threats even when the original file has been removed.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as suspicious email triage, phishing validation, and artifact extraction.  
> - **Executions** refer to the hands-on performance of those tasks using real emails, analyst tools, and supporting investigation utilities.  
> - **Writeups** document how artifacts were interpreted, how validation steps were applied, and how investigative conclusions were formed.

---

### Execution Platform and Operating Environment

#### ▶ Analyst Workstation Performing Manual Email Triage

**Purpose:** Provide a controlled environment for reviewing suspicious emails, extracting raw message artifacts, and documenting evidence that supports phishing classification.  
**How It Was Used:** Analysis was performed using an email client for visible review and Sublime Text for raw `.eml` inspection. WHOIS or DomainTools-style lookup was used to review sending IP and reverse DNS context when needed.  
**Operational Relevance:** Reflects common SOC and email security workflows where analysts review suspicious message queues, classify emails, and extract IOCs for containment or escalation.

Environment characteristics:

- **Operating System:** Desktop operating system capable of local email file review and text editing  
- **Analysis Mode:** Manual multi-email triage and artifact extraction  
- **Primary Tool Set:** Email client, Sublime Text, WHOIS/DomainTools-style lookup, raw header search  
- **Interface Dependency:** Email client rendering plus raw text review  
- **User Context:** Standard analyst workstation session  

This mode ensures that each email can be reviewed from both the victim-facing perspective and the raw technical perspective.

---

### Visible Message Review and Initial Classification

Before raw extraction begins, each message should be reviewed as the user would see it.

#### ▶ Email Client Review

**Purpose:** Understand what the message claims to be and identify visible social engineering indicators.  
**How It Was Used:** Each of the five emails was reviewed for sender presentation, subject, greeting, formatting, branding, body language, links, calls to action, and attachments.  
**Operational Relevance:** The email client view shows the recipient-facing experience and helps identify phishing themes, impersonation attempts, urgency, and suspicious user prompts.

Visible review is especially useful for identifying:

- brand impersonation  
- generic greetings  
- poor formatting or grammar  
- call-to-action buttons  
- suspicious attachment prompts  
- mismatched sender names and domains  
- spam/scam themes that may not require the same response as confirmed phishing  

This step should guide deeper analysis, not replace it.

---

### Raw Email Review and Header Extraction

Raw message inspection provides fields that may not be obvious in the email client.

#### ▶ Sublime Text / Text Editor Review

**Purpose:** Inspect raw `.eml` content to extract sender, subject, recipient, date, reply behavior, and delivery-related fields.  
**How It Was Used:** The emails identified as malicious were opened in a text editor. Search was used to locate fields such as `From`, `Subject`, `To`, `Reply-To`, `Date`, and sender infrastructure references.  
**Operational Relevance:** Text editor review helps distinguish friendly display names from actual sending addresses and provides evidence for reporting, scoping, and containment.

Useful search terms include:

```text
From
Subject
To
Reply-To
Reply
Date
Sender
Received
spf
```

This approach is useful because phishing emails often rely on user-facing deception while the raw message reveals the actual address or delivery context.

#### ▶ Friendly Name vs Actual Sending Address

**Purpose:** Avoid mistaking display text for the true sender address.  
**How It Was Used:** The `From` header was reviewed carefully, with attention to the address contained inside angle brackets.  
**Operational Relevance:** Attackers can set friendly display names to resemble trusted brands, but the actual email address is the artifact that should be documented and evaluated.

In Email One, the display presentation attempted to look Amazon-related, while the actual sender was `QPE77756@mun.ca`. That mismatch supported the phishing conclusion.

---

### Reply-To Review

#### ▶ Reply-To Header Search

**Purpose:** Determine whether replies would go somewhere other than the visible sender.  
**How It Was Used:** Raw email content was searched for `Reply-To` and `reply`.  
**Operational Relevance:** A Reply-To header can redirect responses to a different mailbox, which is common in some phishing and scam workflows.

In the malicious emails reviewed during this lab, no Reply-To header was found. This means replies would default to the From address. The absence of Reply-To does not make the email safe, but it is still an important behavior to document.

---

### Sending Infrastructure and Reverse DNS Review

#### ▶ Sending Server IP Extraction

**Purpose:** Identify the infrastructure used to deliver the message.  
**How It Was Used:** Raw headers were searched for sender infrastructure references and SPF-related delivery context to identify the sending server IP.  
**Operational Relevance:** Sending IPs help analysts understand delivery infrastructure and provide pivots for enrichment, scoping, and historical context.

For Email One, the sending server IP was identified as:

```text
68.114.190.29
```

The sending IP should not be treated as proof of maliciousness by itself. It is an infrastructure artifact that must be interpreted alongside sender identity, message content, and other indicators.

#### ▶ WHOIS / DomainTools-Style Lookup

**Purpose:** Review ownership, hosting, and reverse DNS context for sending infrastructure.  
**How It Was Used:** The sending IP was checked using a WHOIS/DomainTools-style lookup, and raw email headers were also searched for preserved hostname information.  
**Operational Relevance:** WHOIS and reverse DNS context can support sender validation, but live lookups may differ from historical email header data because IP ownership and DNS records can change.

In Email One, the live lookup did not provide the hostname needed, but the original email headers preserved the relevant sending context. This is a useful reminder that the raw email is often the best historical source for what happened at delivery time.

---

### URL Extraction and Web Artifact Handling

#### ▶ Email Client Link Extraction

**Purpose:** Retrieve the actual link destination from the malicious email.  
**How It Was Used:** The link in Email One was right-clicked in the email client and copied directly.  
**Operational Relevance:** Email files often contain many HTTP/HTTPS references, including images, tracking resources, and formatting assets. Copying the target link from the rendered email can be faster and less error-prone when extracting the main user-facing call-to-action URL.

The extracted URL should be documented in defanged form, for example:

```text
hxxp://example[.]com/path
```

Defanging prevents accidental clicks while keeping the indicator readable for reports and tickets.

#### ▶ Credential Harvesting Interpretation

**Purpose:** Determine whether the URL appears likely to lead to credential theft.  
**How It Was Used:** The URL theme, formatting, and parameters were interpreted alongside the Amazon impersonation context.  
**Operational Relevance:** URLs that appear to pass recipient information into a login-style page may indicate pre-filled credential harvesting behavior.

This type of conclusion should be phrased carefully unless the destination has been validated. It is reasonable to describe the URL as likely credential-harvesting based on context, while noting that further validation would confirm behavior.

---

### Attachment Replacement and File Artifact Review

#### ▶ Gateway-Replaced Attachment Review

**Purpose:** Identify details of a malicious attachment that was removed by email security controls.  
**How It Was Used:** The replacement text attachment was opened because it was a safe text file and contained information about the original removed file.  
**Operational Relevance:** Email gateways may strip malicious attachments and replace them with notification files. These replacement artifacts can still provide useful details such as the original filename and file hash.

In Email Three, the displayed attachment was:

```text
MALICIOUS ATTACHMENT REMOVED - SBT.txt
```

The replacement file indicated that the original attachment was an executable disguised as a PDF using a `.pdf.exe` double extension.

#### ▶ Double Extension Review

**Purpose:** Identify attempts to make an executable appear like a document.  
**How It Was Used:** The original filename from the replacement notice was reviewed for extension behavior.  
**Operational Relevance:** Double extensions such as `.pdf.exe` are commonly used to trick users into believing they are opening a document while actually executing a program.

This was one of the strongest malicious indicators in Email Three.

#### ▶ SHA256 Hash Documentation

**Purpose:** Record a stable identifier for the removed malicious file.  
**How It Was Used:** The SHA256 hash provided in the lab’s replacement notice was documented because the original file was no longer available for hashing.  
**Operational Relevance:** Hashes support IOC sharing, endpoint detection, file reputation lookup, and case correlation.

If the original file were available in a real environment, it should be handled in an isolated analysis environment and hashed using tools such as PowerShell:

```powershell
Get-FileHash <filename> -Algorithm SHA256
```

---

### Performance and Evidence Handling Considerations

- Review all suspicious emails broadly before investing time in deep artifact extraction.  
- Do not classify an email as malicious based on one weak indicator alone.  
- Preserve the difference between display name, actual sender address, Reply-To, return path, and sending infrastructure.  
- Use raw email headers to validate what the email client does not clearly expose.  
- Defang URLs before adding them to reports or tickets.  
- Treat gateway replacement notices as useful evidence rather than ignoring them as “not the real file.”  
- Document missing fields, such as absent Reply-To headers, because absence can still clarify behavior.  
- Use live WHOIS/reverse DNS lookups carefully because infrastructure ownership and records can change after delivery.

These practices support defensible phishing triage workflows and reduce the likelihood of both false positives and missed malicious indicators.

---

### Detection and Monitoring Relevance

Manual multi-email triage supports detection engineering and alert validation by helping analysts validate:

- Amazon or NHS-style impersonation themes  
- suspicious sender mismatch patterns  
- malicious links embedded behind call-to-action buttons  
- emails with gateway-replaced malicious attachments  
- executable files disguised with document-style double extensions  
- sender IP and reverse DNS infrastructure that does not align with the claimed sender  
- spam/scam messages that should not be escalated as confirmed malicious incidents  

Manual review often serves as the ground-truth reference when determining whether a suspicious email is merely unwanted or requires containment.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Analyst workstation  
- **Primary Artifacts:** Five suspicious email messages  
- **Data Sources:** Visible email content, raw `.eml` headers, URLs, sender fields, recipient fields, attachment replacement notice, IP/reverse DNS context  
- **Analysis Mode:** Manual triage and artifact extraction  
- **Supporting Tools:** Email client, Sublime Text, WHOIS/DomainTools-style lookup, safe text attachment review  
- **Execution Method:** Visible review, raw header search, URL extraction, sender infrastructure validation, attachment evidence extraction  
- **Analysis Method:** Malicious-vs-spam classification, sender mismatch analysis, call-to-action interpretation, URL review, double-extension detection, and file hash documentation
