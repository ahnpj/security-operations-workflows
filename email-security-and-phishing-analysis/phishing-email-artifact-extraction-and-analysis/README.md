# Manual Phishing Email Artifact Extraction and Analysis

- **Category:** Email Security Analysis and Phishing Triage  
- **Primary Operational Focus:** Manual extraction and validation of email, web, and file artifacts from suspicious phishing emails  
- **Operational Objectives Demonstrated:** Email Header Review, Artifact Extraction, Sender Infrastructure Validation, URL and Domain Analysis, Attachment Hashing, Reputation Checking  
- **Primary Data Sources:** Suspicious email messages, raw email header data, embedded URLs, sender infrastructure records, and potentially malicious file attachments derived from phishing email analysis  

> **Terminology used:**  
> **Workflows** refer to common security operations tasks (such as phishing triage, artifact extraction, or sender validation).  
> **Executions** refer to the hands-on performance of those tasks using real email artifacts, analysis tools, and supporting investigation utilities.  
> **Writeups** document how the task was performed and how outputs were validated, interpreted, and documented.

---

### Overview

This execution documents the practical performance of manual phishing email analysis using Outlook, raw email message inspection, and supporting enrichment tools to extract and validate suspicious email artifacts. The objective is to review suspicious messages, identify indicators of phishing, manually extract key email, web, and file artifacts, and determine whether the message represents malicious or deceptive activity.

The execution focuses on manually reviewing visible email fields, inspecting raw message content through `.eml` or `.msg` files in a text editor, identifying sender infrastructure details, extracting embedded URLs and root domains, and validating suspicious artifacts with supporting tools such as WHOIS, VirusTotal, and PhishTool. Emphasis is placed on understanding what each field means, why each artifact matters, and how a SOC analyst can move from a suspicious email to a defensible phishing determination.

> 👉 **Follow the execution walkthrough first**  
Begin with `workflow-execution.md` inside this folder to see how suspicious email artifacts were identified, extracted, and validated step by step using Outlook, text-based header review, and enrichment tools.

> 👉 **Review analytical reasoning and investigative decision-making**  
Move to `analyst-notes.md` to understand why specific fields were reviewed, how suspicious indicators were prioritized, and how findings influence phishing triage and escalation decisions.

> 👉 **Review tooling and artifact extraction reference details**  
See `tool-usage-notes.md` to understand how Outlook, Sublime Text, PhishTool, WHOIS, PowerShell, and VirusTotal were used during execution and why each tool matters during phishing investigations.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how phishing email investigations and artifact extraction tasks are documented within SOC and email security operational environments.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational phishing analysis and investigative email triage documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured phishing analysis execution showing how suspicious email artifacts are extracted, reviewed, and validated to determine whether a message is malicious. | Documents visible email field review, raw email parsing, sender infrastructure analysis, URL and root domain extraction, WHOIS review, hash collection, and reputation validation. Emphasizes investigative precision and repeatable phishing triage methodology. |
| `images/` | Visual evidence supporting phishing analysis outcomes. | Contains screenshots of Outlook message views, raw email header review, PhishTool analysis panels, WHOIS lookups, VirusTotal checks, and validation checkpoints supporting analytical conclusions. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of phishing analysis objectives, artifact extraction context, and email investigation relevance aligned with SOC monitoring documentation practices. |
| `analyst-notes.md` | Analytical reasoning and phishing investigation insights derived from execution. | Documents triage strategy selection, interpretation of suspicious sender behavior, investigative escalation considerations, and how email, web, and file artifacts support threat validation and scoping. |
| `tool-usage-notes.md` | Technical implementation reference for phishing triage and artifact extraction. | Covers Outlook-based review, raw header inspection methodology, domain and sender validation techniques, attachment hashing, and best practices for performing manual phishing analysis using common analyst tools. |
| `automation-design-notes.md` *(when present)* | Scalable phishing triage and enrichment workflow planning. | Documents architectural considerations for integrating manual email review steps into ticketing, SIEM enrichment, SOAR workflows, or phishing reporting pipelines. |


---

### Environment, Data Sources, and Tools

The execution focuses on inspection and validation of suspicious email artifacts to support phishing triage and investigative workflows.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Email security analysis and phishing triage environment |
| **Processed Assets** | Suspicious emails, embedded URLs, attachment files, raw header content, and sender infrastructure details |
| **Execution Platform** | Outlook email client, local text editor, and web-based enrichment platforms |
| **Primary Platforms / Services** | Outlook, Sublime Text, PhishTool, WHOIS lookups, VirusTotal, and PowerShell |
| **Operational Focus** | Analyze suspicious email communications, extract artifacts, validate sender infrastructure, and identify phishing indicators |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Primary Telemetry Sources** | Suspicious email messages, visible email metadata, raw message headers, embedded URLs, and attached files |
| **Email Artifact Extraction Techniques** | Manual review of sender address, subject line, recipients, date and time, sending server IP, reply-to address, and return-path-related header values |
| **Web Artifact Extraction Techniques** | Identification of full embedded URLs, extraction of root domains, and validation of web destinations against expected brand or service ownership |
| **File Artifact Extraction Techniques** | Retrieval of attachment hashes using PowerShell, local file validation, and reputation-based enrichment through VirusTotal |
| **Infrastructure Validation Techniques** | WHOIS review, reverse DNS interpretation when applicable, and sender service identification through PhishTool or raw header analysis |
| **Email Parsing Techniques** | Review of `.eml` or `.msg` data in a text editor to identify fields not immediately visible in the email client |
| **Threat Detection Heuristics** | Brand mismatch, sender-domain inconsistency, suspicious reply paths, third-party sending service abuse, URL mismatch, and malicious attachment indicators |
| **Operational Workflow Context** | Demonstrates phishing triage procedures used by SOC analysts and email security teams to investigate suspicious emails, validate deceptive infrastructure, and scope possible compromise |

The execution demonstrates how manual artifact extraction supports scalable phishing validation and improves reliability of investigative email triage workflows.

---

### Intended Use

The documented execution demonstrates manual phishing email triage, artifact extraction, sender validation, and malicious indicator identification techniques using Outlook, raw message inspection, and enrichment tools. It reflects how security operations teams perform detailed email analysis to support alert validation, phishing investigation, and suspicious communication interpretation.

---

### Relevance to Security Operations

Manual phishing analysis remains a critical capability for email threat investigation and alert validation.

The execution demonstrates how phishing artifact extraction enables analysts to:

- Validate whether a suspicious email is impersonating a legitimate brand or service  
- Identify sender infrastructure and distinguish claimed identity from actual sending systems  
- Extract malicious URLs, suspicious domains, and file-based indicators for further investigation  
- Support investigative scoping, user notification, and containment decision-making  

Even environments with secure email gateways and automated phishing controls still rely on manual email review to validate borderline cases, analyze targeted messages, and understand how deceptive messages bypass automated defenses.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical phishing analysis methodology, manual artifact extraction techniques, and professional workflow documentation aligned with real operational SOC and email security environments.



