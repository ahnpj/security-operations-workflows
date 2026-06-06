# Multi-Email Phishing Triage and Artifact Analysis Using Thunderbird, Sublime Text, WHOIS, and Email Header Review

### Overview

This execution documents the practical performance of triaging five suspicious emails to determine which messages are malicious, which messages are spam or scam-themed, and which messages do not meet the threshold for malicious phishing classification. The objective is to move beyond simply flagging anything unusual and instead apply analyst judgment to determine which emails contain malicious intent, actionable indicators, and escalation-worthy artifacts.

The workflow begins with visible email review to understand each message’s theme, sender presentation, formatting, call-to-action, and user-facing intent. It then moves into raw message inspection using a text editor to extract sender addresses, subject lines, recipients, dates, reply-to behavior, sending server IPs, reverse DNS context, embedded URLs, and attachment-related artifacts. This project is intentionally useful because it requires comparative judgment across multiple emails. Email One and Email Three were identified as malicious, while Email Two, Email Four, and Email Five were treated as spam, scam, newsletter, or suspicious-but-not-malicious content based on the evidence available during triage.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>

> 👉 **Follow the execution walkthrough first**</br>
Begin with `workflow-execution.md` inside this folder to see how the five emails were reviewed, classified, and investigated step by step using visible message review, raw header inspection, URL extraction, WHOIS/reverse DNS context, and attachment artifact validation.

> 👉 **Review analytical reasoning and investigative decision-making**</br>  
Move to `analyst-notes.md` to understand why only two emails were classified as malicious, how spam/scam messages were separated from phishing, and why specific artifacts were prioritized during the investigation.

> 👉 **Review tooling and implementation details**</br>  
See `tool-usage-notes.md` to understand how Thunderbird, Sublime Text, WHOIS/DomainTools-style lookup, raw header search, and safe attachment review contributed to the workflow.

> 👉 **See what each execution file contains in full detail**</br> 
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Email Security Analysis and Phishing Triage  
- **Primary Operational Focus:** Manual triage of multiple suspicious emails to distinguish malicious phishing from spam, scams, newsletters, and non-malicious suspicious content  
- **Operational Objectives Demonstrated:** Multi-Email Triage, Malicious Email Identification, Spam vs Phishing Differentiation, Raw Header Review, Sender Artifact Extraction, URL Extraction, Attachment Artifact Review, Double-Extension Detection  
- **Primary Data Sources:** Five suspicious email messages, visible email content, raw `.eml` message data, sender headers, recipient fields, embedded URLs, attachment replacement notices, and supporting WHOIS/reverse DNS context  

> **Terminology used:**  
> **Workflows** refer to common security operations tasks (such as phishing triage, suspicious email review, or artifact extraction).  
> **Executions** refer to the hands-on performance of those tasks using real email messages, analyst tooling, and supporting investigative utilities.  
> **Writeups** document how the task was performed and how outputs were validated, interpreted, and documented.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how multi-email phishing triage and email artifact extraction are documented within SOC, email security, and incident response environments.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`** — **If you want to follow the investigation step by step**</br>
This file contains the structured walkthrough showing how multiple suspicious emails were reviewed, how malicious messages were distinguished from spam, scam, and newsletter content, how sender and infrastructure artifacts were extracted, and how URLs and attachment evidence were analyzed to support final classifications.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains the major learning points behind phishing triage, email classification, sender validation, header analysis, social engineering indicators, URL assessment, malicious attachment identification, and evidence-based email investigations.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
This file explains how email clients, raw message inspection, text-based searching, infrastructure lookups, and attachment analysis techniques were used, why specific review methods were selected, and what evidence each approach helped uncover during phishing triage.


---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational phishing triage and investigation documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured multi-email phishing triage walkthrough showing how five emails were reviewed and classified. | Documents visible email review, malicious vs spam classification, raw header artifact extraction, URL extraction, reverse DNS review, attachment replacement analysis, and SHA256 hash documentation. Emphasizes investigative judgment and repeatable triage methodology. |
| `phishing-analysis-report.md` | Formal phishing analysis and incident reporting. | Documents the investigation outcome, identified malicious emails, collected artifacts, supporting analysis, and recommended defensive actions for containment, detection, and follow-up review. |
| `README.md` | Execution overview and operational summary. | Provides a structured explanation of multi-email phishing triage objectives, artifact extraction scope, operational relevance, and how each supporting document contributes to the lab. |
| `analyst-notes.md` | Analytical reasoning and workflow-specific investigation insights derived from execution. | Documents why specific emails were classified as malicious or non-malicious, how phishing indicators were weighed, and how raw header artifacts supported conclusions. |
| `tool-usage-notes.md` | Technical implementation reference for multi-email phishing triage. | Covers visible email review, raw `.eml` inspection, text search strategies, sender and recipient extraction, URL collection, WHOIS/reverse DNS review, and safe attachment artifact handling. |
| `images/` | Visual evidence supporting triage and artifact extraction outcomes. | Contains screenshots of email content, raw header searches, extracted URLs, sender IP evidence, WHOIS/reverse DNS results, attachment replacement evidence, and hash values. |

---

### Environment, Data Sources, and Tools

The execution focuses on validating five suspicious emails and extracting artifacts from the two messages determined to be malicious. The execution demonstrates how manual triage supports phishing validation and helps analysts avoid treating every suspicious email as malicious without evidence.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Environment Type** | Multi-email phishing triage and manual artifact extraction environment |
| **Processed Assets** | Five suspicious email messages, raw `.eml` files, visible email bodies, headers, URLs, sender infrastructure details, and attachment-related evidence |
| **Execution Platform** | Email client plus local text editor-based raw message review |
| **Primary Platforms / Services** | Thunderbird or comparable email client, Sublime Text, WHOIS/DomainTools-style lookup, local attachment review, and file/hash evidence supplied by the lab |
| **Operational Focus** | Identify malicious emails, distinguish phishing from spam/scam content, extract IOCs, and document sender, URL, and attachment artifacts |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Primary Telemetry Sources** | Visible email content, raw message headers, sender fields, recipient fields, date/time headers, URLs, attachment replacement text, and IP/reverse DNS context |
| **Email Artifact Extraction Techniques** | Review of From, Subject, To, Reply-To, Date, sending IP, and reverse DNS fields using email client review and text-based searching |
| **URL Artifact Extraction Techniques** | Right-click/copy-link review from the email client, followed by documentation of full URLs and interpretation of likely phishing destination behavior |
| **Attachment Artifact Extraction Techniques** | Review of stripped attachment replacement notice, identification of the original malicious filename, and documentation of the provided SHA256 hash |
| **Classification Techniques** | Comparison of sender identity, brand impersonation, call-to-action, grammar, formatting, generic addressing, attachment behavior, URL presence, and malicious intent |
| **Threat Detection Heuristics** | Brand impersonation, suspicious sender mismatch, generic greetings, urgency, credential-harvesting style links, executable disguised as PDF, email gateway attachment replacement, and mismatched infrastructure |
| **Operational Workflow Context** | Demonstrates how analysts triage multiple emails without over-flagging spam, while extracting actionable artifacts from confirmed phishing messages |

</details>

---

### Intended Use

The documented execution demonstrates multi-email phishing triage, malicious email identification, raw header review, URL extraction, attachment artifact validation, and defensive recommendation development using commonly available analyst workflows. It reflects how SOC analysts evaluate reported or queued suspicious emails to determine which messages require escalation, containment, and IOC documentation.

---

### Relevance to Security Operations

Security teams regularly receive suspicious emails that range from clearly malicious phishing to low-quality spam, scam attempts, newsletters, marketing content, and suspicious-but-non-malicious messages. This execution is useful because it demonstrates that phishing analysis is not only about finding bad indicators; it is also about making defensible classification decisions. Even when several emails appear suspicious, only some may represent malicious phishing requiring immediate containment. That distinction is central to practical SOC triage.

<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>
  
The execution demonstrates how multi-email triage enables analysts to:

- Identify truly malicious messages without over-escalating every spam or scam email  
- Extract email, URL, and attachment artifacts from confirmed malicious messages  
- Distinguish visible sender presentation from the actual sending address found in raw headers  
- Validate sending infrastructure using IP and reverse DNS context  
- Recognize attachment replacement behavior from an email gateway  
- Document why specific messages should be treated as phishing and others should not  

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical multi-email phishing triage methodology, raw header artifact extraction, malicious-vs-spam decision-making, and professional workflow documentation aligned with real operational SOC and email security environments.
