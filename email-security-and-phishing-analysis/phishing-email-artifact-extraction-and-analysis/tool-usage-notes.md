# Tool Usage Notes — Manual Phishing Email Artifact Extraction and Triage

This document explains how Outlook, raw email review, and supporting enrichment tools were used as a combined phishing analysis workflow to move from a suspicious message to validated email, web, and file artifacts. The emphasis is not on relying on one platform to provide the answer automatically, but on understanding how each tool contributes to a defensible investigation.

The tools used here complement each other. Outlook provides the initial visible message context, Sublime Text provides direct access to raw message content, PhishTool structures email metadata for analyst review, WHOIS provides infrastructure context, PowerShell enables safe artifact hashing, and VirusTotal helps validate known malicious reputation. Together, these utilities support a practical and repeatable phishing triage process.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as phishing triage, sender validation, and artifact extraction.  
> - **Executions** refer to the hands-on performance of those tasks using real email messages, local analysis tools, and enrichment utilities.  
> - **Writeups** document how artifacts were interpreted, how validation steps were applied, and how investigative conclusions were formed.

---

### Execution Platform and Operating Environment

#### ▶ Analyst Workstation Performing Manual Email Analysis

**Purpose:** Provide a controlled environment for reviewing suspicious messages, extracting raw metadata, and validating associated artifacts.  
**How It Was Used:** All analysis was performed using a local analyst workstation with Outlook for message review, a text editor for raw message inspection, PowerShell for hashing, and browser-based enrichment tools for validation.  
**Operational Relevance:** Reflects common SOC and IR workflows where analysts receive suspicious emails from users or ticketing systems and must validate them manually before escalating or containing them.

Environment characteristics:

- **Operating System:** Desktop operating system capable of running Outlook, Sublime Text, and PowerShell  
- **Analysis Mode:** Manual email review and artifact validation  
- **Primary Tool Set:** Outlook, Sublime Text, PhishTool, WHOIS, VirusTotal, PowerShell  
- **Interface Dependency:** Local email and file access plus web-based enrichment access  
- **User Context:** Standard analyst workstation session  

This mode ensures that all investigative actions are repeatable and that results can be documented clearly across multiple artifact types.

---

### Email Review and Raw Message Inspection

Before enrichment begins, analysts must validate what the message visibly shows and what the raw message data technically contains.

#### ▶ Outlook Message Review

**Purpose:** Capture the visible email context as presented to the target user.  
**How It Was Used:** Review the sending address, subject line, recipients, date and time, branding, and overall message theme directly within the email client.  
**Operational Relevance:** The visible presentation of the message reflects the social engineering component of the email and provides the baseline context for all later validation steps.

Visible review helps identify urgent themes, impersonation attempts, and surface-level sender anomalies before raw analysis begins.

#### ▶ Downloaded `.eml` or `.msg` Message File

**Purpose:** Preserve the suspicious email in a raw or semi-raw format suitable for direct review.  
**How It Was Used:** Save the suspicious email from Outlook and prepare it for text-based analysis.  
**Operational Relevance:** Preserving the raw message allows the analyst to re-open the evidence, search header values directly, and document findings without depending entirely on the email client’s rendering.

#### ▶ Sublime Text 2

**Purpose:** Review raw email content in a clean and searchable text format.  
**How It Was Used:** Open the downloaded message file and search for sender-related fields, reply-to values, delivery-related data, and embedded URLs.  
**Operational Relevance:** Text-based review makes it easier to locate fields that are hidden, condensed, or reformatted in the email client and supports manual interpretation of how the email was actually constructed and delivered.

---

### Sender and Infrastructure Validation

Understanding sender behavior requires separating identity fields from transport and delivery context.

#### ▶ Sending Address

**Purpose:** Identify the visible sender identity presented by the email.  
**How It Was Used:** Record the sending address from the visible email and compare it to the message theme, brand, or impersonated service.  
**Operational Relevance:** Sender-brand mismatches are one of the most common phishing indicators and often justify deeper header review.

#### ▶ Reply-To Address

**Purpose:** Determine where replies to the email would actually be directed.  
**How It Was Used:** Search raw message content for the reply-to field and compare it against the visible sender address.  
**Operational Relevance:** A mismatched reply-to may indicate redirection of user responses to an attacker-controlled inbox and can materially change the interpretation of a suspicious message.

#### ▶ Sending Server IP

**Purpose:** Identify the infrastructure that actually delivered the message into the mail flow.  
**How It Was Used:** Review delivery-related header data and structured analysis views to identify the server IP or sending service responsible for transmission.  
**Operational Relevance:** The sending server helps distinguish claimed identity from actual delivery infrastructure and can reveal third-party mail service abuse or unrelated hosting.

The sending server should not be confused with the visible sender address or a subdomain present in sender-related fields.

#### ▶ Return-Path-Related Data

**Purpose:** Review the SMTP envelope-sender or bounce-handling context associated with the message.  
**How It Was Used:** Examine raw message fields and structured tool output to understand where non-delivery reports or bounce handling would be directed.  
**Operational Relevance:** Return-path-related values can clarify the mail flow and sender-domain relationship, but they should not be treated as the same thing as the visible sender or the actual sending server.

#### ▶ SPF-Related Results

**Purpose:** Determine whether the identified sending infrastructure is authorized to send on behalf of the relevant sender domain or subdomain context.  
**How It Was Used:** Review PhishTool output and header-related authentication details to understand whether the message passed SPF and why.  
**Operational Relevance:** SPF can help explain whether infrastructure use is technically authorized, but an SPF pass does not prove legitimacy. Malicious emails can still pass SPF when the attacker controls the sending domain or uses an authorized delivery service.

---

### Web Artifact Extraction and Validation

Web destinations often provide some of the strongest phishing indicators in an email investigation.

#### ▶ Full URL Extraction

**Purpose:** Preserve the exact destination the user would have visited.  
**How It Was Used:** Extract complete URLs from the message body or raw content and document them in full.  
**Operational Relevance:** Full URLs can reveal deceptive subdomain structure, malicious paths, redirect patterns, campaign identifiers, or credential collection endpoints that would be lost if only the domain were recorded.

#### ▶ Root Domain Extraction

**Purpose:** Reduce a full URL to its primary domain for easier comparison and enrichment.  
**How It Was Used:** Strip away page paths, tracking values, and other URL components to isolate the root domain.  
**Operational Relevance:** Root domains make it easier to compare destinations against expected brands, perform lookups, and identify campaign reuse across multiple reported emails.

#### ▶ WHOIS

**Purpose:** Retrieve ownership or registration context associated with suspicious domains or related infrastructure.  
**How It Was Used:** Query suspicious root domains and compare the resulting context against the service or brand the email claimed to represent.  
**Operational Relevance:** WHOIS can support phishing conclusions when ownership or registration context is inconsistent with the message’s claimed identity.

WHOIS should be treated as contextual enrichment rather than a standalone maliciousness verdict.

---

### Structured Email Enrichment and Interpretation

#### ▶ PhishTool

**Purpose:** Provide structured parsing and interpretation of email metadata, URLs, and sender infrastructure.  
**How It Was Used:** Upload or review the suspicious email in PhishTool after manual extraction steps had already been performed, then compare structured analysis results against manually identified artifacts.  
**Operational Relevance:** PhishTool helps analysts review sender identity, reply-to, originating IP, SPF context, and URL data in a more organized format, which is especially useful for validation and documentation.

PhishTool is most valuable when the analyst already understands what each field means and uses the platform to confirm or refine conclusions rather than blindly trusting the interface.

---

### File Artifact Extraction and Validation

Potentially malicious attachments should be treated as independent artifacts that require careful handling.

#### ▶ PowerShell File Hashing

**Purpose:** Generate a stable cryptographic identifier for the suspicious attachment.  
**How It Was Used:** Use `Get-FileHash` to compute a SHA256 hash from the saved attachment file without opening or executing it.  
**Operational Relevance:** File hashes support IOC documentation, threat intelligence lookups, case correlation, and later integrity verification.

Hashing is one of the safest early pivots for attachment-based phishing analysis.

#### ▶ VirusTotal

**Purpose:** Review file reputation, vendor detections, and related artifact intelligence.  
**How It Was Used:** Submit the suspicious attachment hash to VirusTotal and review existing detections or related threat context.  
**Operational Relevance:** VirusTotal helps determine whether the file is already known as malicious and can connect the artifact to broader malware or phishing campaigns.

Reputation results should be interpreted in context. A clean result does not guarantee safety, especially for new or targeted phishing attachments.

---

### Performance and Evidence Handling Considerations

- Preserve the original suspicious email before making additional copies or edits.  
- Document the visible message state before relying on enrichment tools.  
- Keep sender identity, reply behavior, return-path-related data, and actual sending infrastructure conceptually separate.  
- Hash suspicious files before deeper handling whenever possible.  
- Record both full URLs and root domains because they serve different investigative purposes.  
- Use enrichment platforms to support conclusions, not replace analyst reasoning.

These practices support defensible phishing investigation workflows.

---

### Detection and Monitoring Relevance

Manual phishing analysis supports detection engineering and alert validation by helping analysts validate:

- secure email gateway verdicts  
- sender-domain and brand-mismatch detection logic  
- malicious URL identification  
- suspicious attachment triage procedures  
- reported phishing ticket escalation criteria  

Manual review often serves as the ground-truth reference when determining whether suspicious email detections are accurate and actionable.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Analyst workstation  
- **Primary Tool:** Outlook email client  
- **Data Sources:** Suspicious emails, raw message files, embedded URLs, and possible attachments  
- **Analysis Mode:** Manual email inspection and enrichment-driven validation  
- **Supporting Tools:** Sublime Text 2, PhishTool, WHOIS, PowerShell, VirusTotal  
- **Execution Method:** Visible message review, raw text inspection, infrastructure enrichment, and file hashing  
- **Analysis Method:** Email header interpretation, sender validation, URL analysis, and artifact reputation checking  
