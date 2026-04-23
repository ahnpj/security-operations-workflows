# Attachment-Based Phishing Artifact Analysis Using PowerShell, CyberChef, and Browser Developer Tools

- **Category:** Email Security Analysis and Phishing Triage  
- **Primary Operational Focus:** Manual analysis of phishing attachments that impersonate legitimate login portals and harvest user credentials  
- **Operational Objectives Demonstrated:** File Metadata Validation, Attachment Hashing, HTML Phishing Page Review, Obfuscated Source Decoding, Victim Target Identification, Credential Exfiltration Validation  
- **Primary Data Sources:** Suspicious HTML attachment, file metadata, rendered phishing page content, decoded source code, browser network requests, and attacker-controlled credential submission paths  

> **Terminology used:**  
> **Workflows** refer to common security operations tasks (such as phishing triage, attachment validation, or credential theft analysis).  
> **Executions** refer to the hands-on performance of those tasks using real artifacts, analyst tooling, and supporting investigative utilities.  
> **Writeups** document how the task was performed and how outputs were validated, interpreted, and documented.

---

### Overview

This execution documents the practical performance of analyzing a phishing email attachment that presents itself as a Microsoft Outlook / Office365 login page. The objective is to determine what the attachment actually is, how it behaves when rendered, what user or service it attempts to impersonate, and how entered credentials are ultimately transmitted to attacker-controlled infrastructure.

Unlike phishing workflows that begin with visible email message content and raw email header review, this execution begins with a suspicious attachment that must be treated as both a file artifact and an active credential harvesting interface. The workflow moves from safe file handling and metadata collection into HTML page inspection, source-code review, obfuscation decoding, and browser-based network validation. Emphasis is placed on understanding how the file works, what evidence it contains, and how an analyst can move from a suspicious attachment to a defensible phishing conclusion.

> 👉 **Follow the execution walkthrough first**  
Begin with `workflow-execution.md` inside this folder to see how the phishing attachment was identified, reviewed, decoded, and validated step by step using PowerShell, CyberChef, and browser inspection tools.

> 👉 **Review analytical reasoning and investigative decision-making**  
Move to `analyst-notes.md` to understand why file metadata, rendered page behavior, decoded source code, and network requests were reviewed in that order and how those findings support phishing determination.

> 👉 **Review tooling and implementation details**  
See `tool-usage-notes.md` to understand how PowerShell, CyberChef, Chrome Developer Tools, Windows file properties, and browser source inspection contributed to the workflow and why each tool matters during attachment-based phishing analysis.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how attachment-based phishing investigations are documented within SOC, email security, and incident response environments.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational attachment analysis and phishing investigation documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured phishing attachment analysis showing how the suspicious file was identified, reviewed, decoded, and validated as a credential harvesting mechanism. | Documents file property review, hash collection, rendered HTML inspection, targeted victim identification, source-code decoding, and network request validation. Emphasizes investigative precision and repeatable phishing analysis methodology. |
| `images/` | Visual evidence supporting attachment analysis outcomes. | Contains screenshots of file properties, PowerShell hash output, browser-rendered phishing page, CyberChef-decoded source, and Developer Tools network activity supporting analytical conclusions. |
| `README.md` | Execution overview and operational summary. | Provides a structured explanation of phishing attachment analysis objectives, credential harvesting context, and operational relevance aligned with SOC and email security documentation practices. |
| `analyst-notes.md` | Analytical reasoning and workflow-specific investigation insights derived from execution. | Documents why specific validation steps were chosen, how phishing indicators were prioritized, what conceptual distinctions mattered during analysis, and how results support escalation decisions. |
| `tool-usage-notes.md` | Technical implementation reference for phishing attachment analysis. | Covers file metadata review, hash generation, browser source inspection, CyberChef decoding, Developer Tools network validation, and best practices for analyzing HTML-based phishing attachments safely. |
| `automation-design-notes.md` *(when present)* | Scalable phishing attachment triage and enrichment workflow planning. | Documents architectural considerations for integrating attachment analysis steps into phishing reporting pipelines, detonation workflows, case enrichment logic, or detection engineering processes. |

---

### Environment, Data Sources, and Tools

The execution focuses on validating a suspicious attachment that acts as a credential harvesting webpage rather than a traditional document or executable.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Attachment-focused phishing analysis and credential harvesting validation environment |
| **Processed Assets** | Suspicious HTML attachment, rendered phishing page content, decoded source code, pre-filled victim data, and credential submission traffic |
| **Execution Platform** | Windows desktop environment with local file access, PowerShell, CyberChef, and browser-based inspection tools |
| **Primary Platforms / Services** | Windows file properties, PowerShell, CyberChef, Chrome browser, Chrome Developer Tools |
| **Operational Focus** | Analyze suspicious attachment behavior, validate impersonation, decode hidden content, and confirm how credentials are transmitted |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Primary Telemetry Sources** | Local suspicious attachment file, file metadata, browser-rendered HTML content, page source code, decoded source output, and outbound browser request data |
| **File Artifact Extraction Techniques** | Review of filename, file extension, file size, and cryptographic hash values to safely identify the attachment and create repeatable pivots |
| **Rendered Page Analysis Techniques** | Opening the attachment in a browser to inspect the phishing interface, determine the impersonated service, and evaluate what the victim would see |
| **Victim Target Identification Techniques** | Review of auto-filled form values to identify whether the phishing page was generic or preconfigured for a specific recipient |
| **Source Code Analysis Techniques** | Use of browser source inspection and CyberChef decoding to reveal hidden or encoded content and identify branding or phishing kit artifacts |
| **Credential Exfiltration Validation Techniques** | Use of browser Developer Tools to inspect network activity triggered by form submission and confirm how credentials are transmitted to the attacker |
| **Threat Detection Heuristics** | HTML attachment delivery, Microsoft brand impersonation, targeted victim prepopulation, encoded page content, suspicious credential submission path, and external attacker-controlled endpoint communication |
| **Operational Workflow Context** | Demonstrates how analysts validate phishing attachments that function as web-based credential theft mechanisms rather than relying only on email metadata or reputation tools |

The execution demonstrates how manual attachment analysis supports phishing validation and improves confidence when determining whether an artifact is merely suspicious or actively malicious.

---

### Intended Use

The documented execution demonstrates attachment-based phishing analysis, credential harvesting validation, HTML source review, and browser-based behavioral inspection using common analyst tools. It reflects how security operations teams evaluate suspicious file-based phishing artifacts to support alert validation, case escalation, and attacker infrastructure identification.

---

### Relevance to Security Operations

Attachment-based phishing remains a relevant credential theft technique, especially when attackers want to bypass user suspicion around direct links by embedding the phishing experience inside a local HTML file.

The execution demonstrates how attachment analysis enables analysts to:

- Validate whether a suspicious file is actually a rendered phishing interface rather than a normal document  
- Determine whether the page impersonates a legitimate service such as Microsoft Outlook / Office365  
- Identify whether the phishing artifact is generic or targeted to a specific user  
- Decode embedded or obfuscated content to understand how the page is structured  
- Confirm whether entered credentials are transmitted to attacker infrastructure and how that transmission occurs  

Even when an attachment appears simple, the rendered page, underlying source code, and outbound network behavior together may reveal a complete credential harvesting workflow.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical attachment-based phishing analysis methodology, HTML credential harvesting validation techniques, and professional workflow documentation aligned with real operational SOC and email security environments.
