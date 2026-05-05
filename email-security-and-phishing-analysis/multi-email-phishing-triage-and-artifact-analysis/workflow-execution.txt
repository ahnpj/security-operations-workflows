# Multi-Email Phishing Triage and Artifact Analysis

This workflow demonstrates manual triage of five suspicious emails to determine which messages are malicious and which should be classified as spam, scam, newsletter, or suspicious-but-not-malicious content. It highlights how analysts move from visible message review into raw header inspection, artifact extraction, sender validation, URL review, attachment analysis, and final classification.

### Overview

This workflow documents practical phishing email triage using an email client, raw message inspection, text-based searching, and supporting infrastructure lookup. The workflow focuses on reviewing multiple suspicious messages, identifying which emails require escalation, and extracting actionable artifacts from the confirmed malicious messages.

The analysis progresses through structured phases beginning with broad review of all five emails, then narrowing into deeper extraction from the two messages classified as malicious. The workflow demonstrates that phishing triage is not only about identifying suspicious indicators, but also about avoiding over-classification when an email is better categorized as spam, scam, newsletter, or low-risk unwanted content.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**
> - **Workflows** refer to operational tasks such as suspicious email review, phishing triage, and email artifact extraction.
> - **Executions** refer to hands-on performance of those tasks using real emails, analyst tools, and supporting investigation utilities.
> - **Writeups** document extraction decisions, classification reasoning, and investigative conclusions.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshots**, see the **[Step-by-Step Execution](#step-by-step-execution)** section below.

---

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to triage a set of suspicious emails, determine which are malicious, and extract the artifacts needed to support defensive action. The workflow focuses on understanding each email’s visible story, validating the true sender information through raw message review, extracting URLs and attachment details, and separating malicious phishing from spam or scam-themed content.

The workflow demonstrates how email triage supports alert validation, phishing classification, and investigation scoping. It emphasizes how analysts use visible message content, raw headers, sender infrastructure, URLs, and attachment details together to reach defensible conclusions.

#### ▶ Analyst Focus

The analyst focus is on evaluating multiple emails without treating every suspicious message as malicious. This includes identifying social engineering techniques, sender mismatches, generic addressing, urgent call-to-action language, suspicious URLs, and malicious attachment behavior.

The workflow reflects responsibilities commonly performed by SOC analysts, email security analysts, and incident responders when reviewing reported emails or triage queues. It reinforces the importance of classification discipline: phishing, spam, scam, newsletter, and malicious attachment delivery are related but not identical outcomes.

---

### What This Workflow Demonstrates

This workflow demonstrates an end-to-end suspicious email review process across five messages. Specifically, it demonstrates how to:

- Review multiple suspicious emails and classify them based on evidence rather than suspicion alone.
- Distinguish malicious phishing from spam, scam-themed emails, newsletters, and suspicious-but-not-malicious content.
- Record visible email artifacts such as sender, subject line, recipient, and message theme.
- Open raw email content in a text editor and search for header fields such as `From`, `Subject`, `To`, `Reply-To`, `Date`, and sender infrastructure references.
- Identify when a friendly display name differs from the actual sending address.
- Determine whether a Reply-To address is present or whether replies default to the visible sending address.
- Extract full URLs from phishing emails using the email client when raw files contain many unrelated links.
- Review sending server IP and reverse DNS context using raw headers and WHOIS-style lookup.
- Identify malicious attachment replacement behavior where an email gateway removes a dangerous file and replaces it with a notification text file.
- Document original attachment filename and SHA256 hash when the real malicious attachment is no longer available.

---

### Investigation and Detection Relevance

Multi-email triage is common in SOC environments because reported-message queues often contain a mix of true phishing, spam, scams, newsletters, marketing emails, and suspicious-but-non-malicious content. Analysts need to identify which messages require containment and which do not.

Manual artifact extraction is especially useful when:

- a group of suspicious messages must be reviewed and classified,
- some messages contain obvious social engineering but no malware or malicious link,
- raw header fields are needed to identify the real sender,
- URLs must be extracted safely for validation,
- attachments have been removed or replaced by an email gateway,
- and defensive measures depend on accurate artifact collection.

The workflow’s steps map directly to common security questions, including:

- Which emails are actually malicious?
- Which suspicious emails are spam or scam rather than phishing?
- What sender address, subject line, recipient, and date/time should be documented?
- Is a Reply-To address present, or do replies default to the From address?
- What infrastructure delivered the malicious email?
- Does the email contain a credential-harvesting link?
- Was a malicious attachment removed by the email gateway, and what was the original filename/hash?

By documenting repeatable procedures for message review, raw parsing, enrichment, and final classification, this workflow supports defensible reporting and consistent triage decisions.

---

### Environment and Execution Context

This section documents the tools, evidence sources, and constraints used to perform the workflow so that results can be interpreted consistently and reproduced by another analyst.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on software, tools, environment, data sources, and more.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

Email triage was performed using an email client for visible message review and local file-based inspection for raw message analysis. Supporting enrichment was performed using text search and WHOIS/reverse DNS lookup where appropriate.

</details>

<details>
<summary><strong>▶ Tooling and Constraints</strong><br>
</summary><br>

- **Primary email client:** Thunderbird or comparable email client  
- **Raw message inspection:** Sublime Text for reviewing downloaded `.eml` files  
- **Infrastructure lookup utility:** WHOIS / DomainTools-style lookup  
- **Attachment review:** Safe review of gateway-replaced attachment text file  
- **Hash handling:** SHA256 value provided in the lab after attachment removal  

The workflow intentionally relies on manual review and text-based extraction so that each field can be understood and validated rather than blindly accepted from a tool output.

</details>

<details>
<summary><strong>▶ Data Sources Analyzed</strong><br>
</summary><br>

The workflow used the following evidence sources:

- **Five suspicious email messages viewed in an email client**
- **Downloaded raw email files** opened locally for text-based review
- **Visible email content** including sender presentation, branding, language, and calls to action
- **Email header fields** including From, Subject, To, Reply-To, Date, sending IP, and delivery-related values
- **Embedded URLs** extracted from malicious email content
- **Attachment replacement notice** used to identify the original malicious attachment name and SHA256 hash
- **WHOIS/reverse DNS context** for sending server interpretation

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

1. Review all five emails visually and classify each message based on theme, sender, formatting, call-to-action, links, and attachment behavior.
2. Identify Email One and Email Three as malicious and separate the remaining messages as spam/scam/newsletter/suspicious-but-not-malicious.
3. Open the first malicious email in a text editor and extract sender, subject, recipient, Reply-To, date/time, sending server IP, reverse DNS context, and full URL.
4. Open the second malicious email in a text editor and extract sender, subject, recipient, Reply-To, date/time, sending server IP, reverse DNS context, original attachment filename, and SHA256 hash.
5. Interpret the evidence to determine how each malicious email attempts to affect the user.
6. Document defensive measures such as sender/domain blocking, URL blocking, hash-based blocking, and additional scoping.
7. Correlate findings into a final phishing triage conclusion.

</details>

---

### Step-by-Step Execution

This section documents the workflow in the same order an analyst would realistically perform multi-email phishing triage. The process begins with broad classification across all five emails, then narrows into detailed artifact extraction from the two confirmed malicious messages.

Each phase captures both the technical actions performed and the investigative reasoning behind those actions. The workflow intentionally progresses from visible message review into deeper raw header extraction and artifact validation, mirroring how analysts move from initial suspicion to evidence-driven phishing classification.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 1 — Initial Multi-Email Triage and Classification</strong><br>
→ reviewing all five messages and separating malicious phishing from spam, scam, newsletter, and suspicious-but-not-malicious content
</summary><br>

This phase establishes the initial classification outcome across the full set of five emails. Rather than jumping immediately into header extraction, each message was first reviewed for visible indicators such as sender presentation, impersonation theme, formatting, generic addressing, calls to action, URLs, and attachments.

<blockquote>
I started with visible review across all five emails because the first job in a triage queue is not to deeply analyze every message immediately. The first job is to decide which messages deserve deeper analysis and which ones are better classified as spam, scam, newsletter, or unwanted content.
</blockquote>

##### 🔷 Phase 1.1 — Review Email One and identify Amazon-themed phishing indicators

Email One was classified as malicious phishing based on the visible sender mismatch, Amazon impersonation theme, generic greeting, poor grammar, inconsistent formatting, and a call-to-action button associated with a refund/help-page theme.

The message attempted to present itself as an Amazon-related communication, but the actual sending address did not match Amazon. The display/friendly name appeared to reference an Amazon-style domain, while the actual sender was identified as `QPE77756@mun.ca`. This distinction matters because friendly display names can be manipulated, while the actual address inside the angle brackets is the sender artifact that needs to be documented.

Key indicators included:

- Amazon-themed impersonation
- Actual sending address not associated with Amazon
- Generic greeting such as “Amazon user”
- Formatting and styling inconsistencies
- Poor grammar and unnatural wording
- Call-to-action button encouraging the user to click a “Help Page - Refund Form” style link

<p align="left">
  <img src="images/multi-email-phishing-triage-01.png"
       alt="Email One visible phishing indicators"
       style="border: 2px solid #444; border-radius: 6px;"
       width="800"><br>
  <em>Figure 1: Reviewing Email One and identifying Amazon-themed phishing indicators.</em>
</p>

<blockquote>
The important point was not that the email simply looked ugly or poorly written. The stronger conclusion came from the combination of brand impersonation, sender mismatch, generic targeting, and a link-based call to action that appeared designed to move the user toward a credential or refund-themed page.
</blockquote>

##### 🔷 Phase 1.2 — Review Email Two and classify it as spam/scam rather than malicious phishing

Email Two was suspicious, but it was not classified as malicious phishing in this triage. The message used a lottery/scam theme and attempted to convince the recipient to contact a Gmail address, but the URL referenced in the body was not hyperlinked and appeared to reference a legitimate news site.

This distinction matters because not every suspicious or dishonest email is necessarily malicious in the same way as credential phishing or malware delivery. The email appeared to be a scam/spam message attempting to socially engineer the recipient into sharing personal details, but the evidence did not support the same containment urgency as the confirmed malicious emails.

Key observations included:

- Lottery/scam-themed narrative
- Generic targeting rather than personal addressing
- Non-hyperlinked reference to a legitimate news site
- Different contact address provided in the body
- No direct malicious attachment or confirmed malicious URL behavior identified during this phase

<blockquote>
This email was useful because it forced me not to rush. It looked suspicious and should not be trusted, but the available evidence supported spam/scam classification rather than confirmed malicious phishing.
</blockquote>

##### 🔷 Phase 1.3 — Review Email Three and identify malicious attachment delivery indicators

Email Three was classified as malicious because it claimed to be from the UK National Health Service while using a Gmail sender, generic stock imagery, generic addressing, urgency-based language, and an attachment that had been removed or replaced by the email gateway.

The visible message created urgency by telling the recipient to open an attachment and act quickly or lose a slot. The attachment displayed as `MALICIOUS ATTACHMENT REMOVED - SBT.txt`, which indicates that an email security control likely stripped the original attachment and replaced it with a warning or safe text file.

Key indicators included:

- NHS-themed impersonation using a Gmail sender
- Generic image not aligned with official NHS/Government branding
- Generic greeting such as “Sir/Madam”
- Urgent call to action to open an attachment
- Gateway-replaced attachment indicating the original file was malicious
- Later confirmation that the original file used a double extension: `.pdf.exe`

<p align="left">
  <img src="images/multi-email-phishing-triage-02.png"
       alt="Email Three malicious attachment indicators"
       style="border: 2px solid #444; border-radius: 6px;"
       width="800"><br>
  <em>Figure 2: Reviewing Email Three and identifying malicious attachment delivery indicators.</em>
</p>

<blockquote>
The attachment replacement was one of the strongest signals in the set. It showed that the email gateway had already taken action against the attachment, and the replacement text gave me a safe way to identify the original file without handling the original executable.
</blockquote>

##### 🔷 Phase 1.4 — Review Email Four and classify it as spam/newsletter content

Email Four contained links and had a marketing/newsletter-style purpose, but it did not present the same malicious indicators as Email One or Email Three. Based on sender, styling, theme, and purpose, it was classified as spam/newsletter rather than confirmed malicious phishing.

Further analysis could still be performed on embedded links using URL reputation checks, URL2PNG, or WHOIS if needed, but no immediate evidence elevated it to the same priority as the malicious emails.

<blockquote>
This email reinforced that links alone are not enough to classify an email as malicious. Context, intent, sender alignment, and what the email is trying to make the user do all matter.
</blockquote>

##### 🔷 Phase 1.5 — Review Email Five and classify it as spam/cryptocurrency-themed content

Email Five contained suspicious elements, including a misleading investment or cryptocurrency theme and an odd Reply-To display name. However, the evidence supported classification as spam or unwanted promotional/scam content rather than confirmed malicious phishing.

The message appeared designed to convince users to sign up for cryptocurrency or trading-related activity, but it did not contain the same confirmed malicious attachment behavior or credential-harvesting indicators as the emails classified as malicious.

<blockquote>
This email was suspicious and should be avoided, but suspicious is not the same as confirmed malicious. That distinction is important when analysts are working through large message queues and need to prioritize real containment work.
</blockquote>

##### 🔷 Phase 1.6 — Confirm malicious email selection

After reviewing all five messages, the two emails classified as malicious were:

- **Email One** — Amazon-themed phishing with sender mismatch and suspicious URL/call-to-action behavior
- **Email Three** — NHS-themed malicious attachment delivery with gateway-replaced executable evidence

The remaining emails were classified as spam, scam, newsletter, or suspicious-but-not-malicious content based on the evidence available during triage.

</details>

<details>
<summary><strong>▶ Phase 2 — Email One Raw Header Review and Artifact Extraction</strong><br>
→ extracting sender, recipient, date, reply behavior, sending infrastructure, and full URL from the first malicious email
</summary><br>

This phase focuses on opening Email One in a text editor and extracting the artifacts needed to support the phishing classification.

<blockquote>
Once Email One was identified as malicious, I moved into raw message review. The visible email told me what the user saw, but the raw message data was needed to document the actual sender, recipient, timestamp, reply behavior, and delivery infrastructure.
</blockquote>

##### 🔷 Phase 2.1 — Extract the actual sending address from the `From` header

Email One was opened in Sublime Text and searched for the `From` field. The actual sender was identified inside the angle brackets at the end of the line, while the preceding friendly name was treated as user-controlled display text.

**Sending Address:**  
`QPE77756@mun.ca`

This matters because the email appeared to reference an Amazon-like identity, but the actual sending address was not associated with Amazon. The mismatch between brand presentation and true sender identity supported the phishing conclusion.

<p align="left">
  <img src="images/multi-email-phishing-triage-03.png"
       alt="Email One From header extraction"
       style="border: 2px solid #444; border-radius: 6px;"
       width="800"><br>
  <em>Figure 3: Extracting the actual sending address from the raw From header.</em>
</p>

<blockquote>
I treated the friendly name and the actual sender as separate values. That matters because attackers can make the display portion look like a trusted brand, while the address inside the angle brackets reveals the actual sender artifact.
</blockquote>

##### 🔷 Phase 2.2 — Extract the subject line

The `Subject` field was reviewed a few lines below the `From` header or located using text search.

**Subject Line:**  
`[INSERT SUBJECT LINE FROM EMAIL ONE]`

The subject line should be documented exactly as it appears because it may be useful for email gateway searches, recipient scoping, detection logic, and analyst reporting.

##### 🔷 Phase 2.3 — Extract the recipient

The `To` field was reviewed to identify the recipient of the malicious email.

**Recipient:**  
`jack.tractive@abcindustries.co.uk`

Recording the recipient supports scoping and allows analysts to determine which user was targeted by the message.

##### 🔷 Phase 2.4 — Check for Reply-To behavior

The raw email was searched for `Reply-To` and `reply` values. No Reply-To header was found.

**Reply-To:**  
`none`

Because no Reply-To field was present, replies would default to the From address rather than being redirected to a separate mailbox.

<blockquote>
The absence of Reply-To was still worth documenting. Attackers sometimes use Reply-To to redirect responses away from the visible sender, so confirming that it is not present prevents assumptions about reply behavior.
</blockquote>

##### 🔷 Phase 2.5 — Extract the date and time from the raw `Date` header

The raw message was searched for `Date` to identify the timestamp of the email.

**Date and Time:**  
`[INSERT DATE/TIME FROM EMAIL ONE]`

This timestamp supports reporting, scoping, and correlation with email gateway logs or SIEM events.

##### 🔷 Phase 2.6 — Extract the sending server IP

The raw message was searched for sender infrastructure references. The sending server IP was identified in multiple header references, including SPF-related delivery context.

**Sending Server IP:**  
`68.114.190.29`

This value is important because it reflects infrastructure involved in delivering the email, which can be used for enrichment, reverse DNS review, and investigation context.

##### 🔷 Phase 2.7 — Review reverse DNS hostname context

A WHOIS/DomainTools-style lookup did not return a current hostname for the sending IP, and the IP ownership context appeared to have changed. The original reverse DNS or hostname evidence was therefore taken from the preserved raw email headers.

**Reverse DNS / Hostname:**  
`[INSERT REVERSE DNS HOSTNAME FROM EMAIL ONE RAW HEADER]`

This is an important reminder that live infrastructure lookups can change over time. The raw email file preserves the historical delivery context at the time the message was sent.

<blockquote>
I did not rely only on the live lookup because IP ownership and reverse DNS context can change. The original message headers are often more useful for reconstructing what was true when the email was delivered.
</blockquote>

##### 🔷 Phase 2.8 — Extract the full URL from the malicious email

The full URL was extracted by right-clicking the link in the email client and copying the link location. This was more efficient than searching raw email content because email files often contain many unrelated HTTP/HTTPS references.

**Full URL (sanitized):**  
`[INSERT FULL DEFANGED URL FROM EMAIL ONE]`

The URL appeared consistent with a credential-harvesting flow because it contained formatting and parameters that suggested the user’s email address may be passed into the phishing page to auto-fill a fake Amazon login experience.

<p align="left">
  <img src="images/multi-email-phishing-triage-04.png"
       alt="Email One full URL extraction"
       style="border: 2px solid #444; border-radius: 6px;"
       width="800"><br>
  <em>Figure 4: Extracting the full URL from Email One using the email client.</em>
</p>

<blockquote>
At this point, I treated the credential-harvesting conclusion as likely but still dependent on further validation. The link formatting and email theme strongly suggested credential theft, but a full investigation would still validate the destination before finalizing containment actions.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 3 — Email Three Raw Header and Attachment Artifact Extraction</strong><br>
→ extracting sender, recipient, date, reply behavior, sending infrastructure, attachment name, and SHA256 hash from the second malicious email
</summary><br>

This phase focuses on opening Email Three in a text editor and extracting the artifacts needed to support the malicious attachment classification.

<blockquote>
Email Three required a slightly different focus from Email One. The key issue was not only sender impersonation, but also the attachment behavior and the evidence that the original file had been removed by a security control.
</blockquote>

##### 🔷 Phase 3.1 — Extract the actual sending address

Email Three was opened in a text editor and the `From` field was reviewed.

**Sending Address:**  
`[INSERT SENDING ADDRESS FROM EMAIL THREE]`

The sender was a Gmail address despite the message claiming to represent the UK National Health Service. That mismatch supported the impersonation assessment.

<p align="left">
  <img src="images/multi-email-phishing-triage-05.png"
       alt="Email Three From header extraction"
       style="border: 2px solid #444; border-radius: 6px;"
       width="800"><br>
  <em>Figure 5: Extracting the actual sender from Email Three.</em>
</p>

##### 🔷 Phase 3.2 — Extract the subject line

The `Subject` field was reviewed directly or located through text search.

**Subject Line:**  
`[INSERT SUBJECT LINE FROM EMAIL THREE]`

The subject line should be preserved exactly because it may be useful for email gateway searches and recipient scoping.

##### 🔷 Phase 3.3 — Extract the recipient

The `To` field was reviewed to identify the recipient.

**Recipient:**  
`[INSERT RECIPIENT FROM EMAIL THREE]`

Recording the recipient supports user-level scoping and helps determine who may need follow-up if the message bypassed controls.

##### 🔷 Phase 3.4 — Check for Reply-To behavior

The raw email was searched for `Reply-To` and `reply` values. No Reply-To header was found.

**Reply-To:**  
`none`

Because no Reply-To field was present, replies would default to the sender address.

##### 🔷 Phase 3.5 — Extract the date and time from the raw `Date` header

The raw message was searched for the `Date` field.

**Date and Time:**  
`[INSERT DATE/TIME FROM EMAIL THREE]`

This value supports timeline reconstruction and correlation with mail gateway logs.

##### 🔷 Phase 3.6 — Extract the sending server IP

The raw message was searched for sender infrastructure references to identify the sending server IP.

**Sending Server IP:**  
`[INSERT SENDING SERVER IP FROM EMAIL THREE]`

The sending server IP was then used for reverse DNS or WHOIS-style review.

##### 🔷 Phase 3.7 — Review reverse DNS hostname context

The sending IP resolved to a Gmail sending server owned by Google.

**Reverse DNS / Hostname:**  
`[INSERT GMAIL REVERSE DNS HOSTNAME FROM EMAIL THREE]`

This supported the finding that the email was delivered using Gmail infrastructure even though it claimed to represent the NHS.

<blockquote>
A Gmail sending server is not inherently malicious, but it is suspicious in this context because the message was claiming to represent a UK public health organization. The infrastructure did not match the claimed sender identity.
</blockquote>

##### 🔷 Phase 3.8 — Identify the removed malicious attachment and original filename

The attachment displayed in the email client as a replacement text file, indicating that the original attachment had likely been removed by an email gateway.

**Displayed Attachment:**  
`MALICIOUS ATTACHMENT REMOVED - SBT.txt`

The replacement text file was safe to open as a text file and provided the original malicious filename.

**Original Attachment Filename:**  
`[INSERT ORIGINAL MALICIOUS ATTACHMENT FILENAME - .pdf.exe]`

The original filename used a double extension ending in `.pdf.exe`, indicating an executable disguised as a PDF. This is a strong malicious attachment indicator because it attempts to make the file look like a document while preserving executable behavior.

<p align="left">
  <img src="images/multi-email-phishing-triage-06.png"
       alt="Email Three attachment replacement notice"
       style="border: 2px solid #444; border-radius: 6px;"
       width="800"><br>
  <em>Figure 6: Reviewing the gateway-replaced attachment text file and original malicious filename.</em>
</p>

##### 🔷 Phase 3.9 — Document the provided SHA256 hash

Because the original malicious attachment had been removed, the file could not be hashed directly in the lab environment. Instead, the SHA256 value provided inside the replacement text file was documented.

**SHA256 Hash:**  
`[INSERT SHA256 HASH FROM EMAIL THREE]`

In a real investigation, if the original attachment were available, it should be handled in an isolated analysis environment and hashed using PowerShell or Linux CLI tooling.

<blockquote>
The key point here was not to try to recover or execute the original file. The email gateway had already removed it, so the safer and more realistic action was to document the original filename and provided hash from the replacement notice.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 4 — Final Correlation and Investigative Determination</strong><br>
→ tying together visible message review, raw headers, URLs, and attachment evidence to reach a final triage conclusion
</summary><br>

This phase focuses on correlating all extracted artifacts and determining the overall outcome of the five-email triage exercise.

<blockquote>
By the end of the workflow, I wanted to make sure I was not just collecting answers. The goal was to explain why Email One and Email Three crossed the threshold into malicious activity while the other messages stayed in spam, scam, or newsletter categories.
</blockquote>

##### 🔷 Phase 4.1 — Correlate malicious indicators for Email One

Email One was classified as malicious due to the combination of:

- Amazon brand impersonation
- Actual sending address not associated with Amazon
- Generic greeting and poor grammar
- Suspicious refund/help-page call to action
- Full URL likely associated with credential-harvesting behavior
- Sender infrastructure context that did not support legitimate Amazon communication

This combination supports a phishing classification rather than simple spam.

##### 🔷 Phase 4.2 — Correlate malicious indicators for Email Three

Email Three was classified as malicious due to the combination of:

- NHS impersonation
- Gmail sender despite public-health organization branding
- Generic stock imagery and weak branding alignment
- Generic greeting and urgency-based messaging
- Call to action instructing the recipient to open an attachment
- Email gateway replacement of the original malicious attachment
- Original filename using `.pdf.exe` double-extension behavior
- Provided SHA256 hash for the removed malicious file

This combination supports a malicious attachment delivery classification.

##### 🔷 Phase 4.3 — Preserve classification discipline for non-malicious messages

Email Two, Email Four, and Email Five were not classified as malicious based on the evidence available during triage. They may be unwanted, suspicious, misleading, or risky for users, but they did not contain the same confirmed malicious indicators as Email One and Email Three.

This distinction matters because over-classifying every suspicious message as malicious can create unnecessary noise and reduce triage quality.

##### 🔷 Phase 4.4 — Determine recommended next steps

Recommended next steps include:

- block or quarantine Email One and Email Three based on confirmed malicious classification,
- search mail logs for the same sender, subject line, URL, attachment filename, and hash,
- block or monitor the malicious URL from Email One after validation,
- add the SHA256 hash from Email Three to endpoint or email gateway controls,
- alert affected recipients if messages were delivered,
- review whether any user clicked the Email One link or attempted to open the Email Three attachment,
- and maintain lower-priority handling for spam/scam/newsletter messages that do not meet malicious criteria.

</details>

---

### What I Learned (Skills Demonstrated)

Through this workflow, I learned how to:

- Review multiple suspicious emails and classify them based on evidence rather than instinct.
- Separate malicious phishing from spam, scam, newsletter, and suspicious-but-not-malicious content.
- Use visible message indicators to guide deeper raw header analysis.
- Extract sender, subject, recipient, Reply-To, date/time, sending IP, and reverse DNS context from raw email files.
- Identify when a display name or friendly sender value differs from the actual email address.
- Understand why the absence of a Reply-To header still matters during phishing triage.
- Extract full URLs from an email client when raw files contain many unrelated links.
- Recognize email gateway attachment replacement behavior and safely document original malicious attachment details.
- Identify double-extension behavior where an executable is disguised as a PDF.
- Correlate visible message content, raw header fields, URL behavior, and attachment evidence into a defensible phishing determination.

Overall, this workflow strengthened my understanding of how multi-email phishing triage works in practice. It also reinforced that suspicious emails should not all be treated the same way: some require containment, some require user awareness, and some should be classified as spam or scam without escalating them into confirmed phishing incidents.
