# Email Security and Phishing Analysis/Triage
(`email-security-and-phishing-analysis/README.md`)

This folder contains workflow executions where the **primary focus is manually extracting, validating, and correlating phishing-related artifacts from suspicious email messages using analyst-driven techniques and enrichment tools**.

This includes reviewing visible email content, inspecting raw message data, extracting sender and infrastructure details, identifying web and file artifacts, and validating indicators using enrichment platforms such as WHOIS, PhishTool, VirusTotal, and local hashing utilities.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational execution scenario**. Each one documents how a suspicious email is analyzed from initial triage through artifact extraction, enrichment, and final investigative determination.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how email artifacts were extracted, validated, and interpreted step by step using Outlook, raw message inspection, and enrichment tools.

> 👉 **See what files exist in each workflow folder**  
For a complete breakdown of every standard file included in a workflow execution — including what each document contains, how it supports phishing analysis workflows, and how the documentation is structured — see the **Workflow Documentation Structure** section below.

> 👉 **See which workflow executions currently exist in this category**  
For a list of all phishing analysis executions currently included in this folder, along with a description of what each workflow demonstrates and which operational skills it focuses on, see the **What’s in This Folder** section below.

---

Each folder uses a **task-first, tool-supported naming convention**, emphasizing the operational objective (phishing artifact extraction and validation) while still reflecting the tools used (Outlook, Sublime Text, PhishTool, WHOIS, VirusTotal, PowerShell).

**Terminology used:**  
Workflows refer to common security operations tasks (such as phishing triage and artifact extraction).  
Executions refer to the hands-on performance of those tasks using real email messages and analyst tools.  
Writeups document how the task was performed and how artifacts were validated and interpreted.

---

### How Workflows Are Categorized

Workflows are grouped here when **manual phishing analysis and artifact extraction are the primary operational objective**, rather than automated detection engineering or full incident investigations.

Even though these workflows directly support incident response and detection development, they are categorized separately because their primary purpose is **understanding and validating suspicious email behavior at the artifact level**.

Workflows in this category typically focus on:

- Visible email triage  
- Raw email inspection  
- Sender and infrastructure validation  
- Web artifact extraction  
- Attachment analysis  
- Threat enrichment  
- Artifact correlation  

Although these workflows support investigations, they are organized here when the **core work involves analyzing and validating a suspicious email rather than responding to a full incident lifecycle.**

---

### Operational Context and Purpose

Manual phishing analysis workflows typically occur when automated email security controls are insufficient or require analyst validation.

At this stage of security operations, the objective is not just to detect an email as suspicious, but to **understand how it was constructed, how it was delivered, and what it is attempting to achieve**.

Analysts focus on:

- identifying inconsistencies between visible and actual sender information  
- extracting actionable indicators such as domains, IPs, and file hashes  
- validating whether infrastructure aligns with the claimed identity  
- determining whether embedded links or attachments are malicious  

The execution examples in this category reflect real SOC workflows where analysts validate reported phishing emails, investigate suspicious inbound messages, and extract indicators for detection, blocking, and scoping.

---

### What’s in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete phishing analysis scenario**, including artifact extraction, enrichment, validation, and investigative interpretation.

Current workflow executions include:

- Manual Phishing Email Artifact Extraction and Triage Using Outlook, Sublime Text, PhishTool, WHOIS, VirusTotal, and PowerShell  
  (`manual-phishing-email-artifact-extraction-using-outlook-and-phishtool`)  

Additional workflow executions will be added as coverage expands across different phishing techniques, email formats, and attacker behaviors.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how phishing analysis is performed in operational SOC environments.

File / Folder | Purpose | Contents and Focus  
workflow-execution.md | Operational execution walkthrough | Step-by-step phishing analysis workflow showing email review, header inspection, artifact extraction, enrichment, and validation  
README.md | Workflow context and operational objective | Describes phishing scenario, workflow scope, and how the execution supports triage and investigation workflows  
analyst-notes.md | Analytical reasoning and investigative considerations | Documents why specific artifacts were prioritized and how they support phishing determination  
tool-usage-notes.md | Tool and platform reference documentation | Explains how Outlook, text editors, hashing tools, and enrichment platforms are used during analysis  
images/ or screenshots/ | Validation and investigation artifacts | Contains screenshots of email content, raw headers, and extracted indicators  

---

### How These Execution Writeups Are Designed

This category is **execution-focused**, not theoretical.

You will find:

- Realistic phishing analysis walkthroughs  
- Manual artifact extraction processes  
- Validation checkpoints and enrichment steps  
- Analyst decision-making tied to evidence  
- Correlation of email, web, and file indicators  

Each workflow demonstrates not just how phishing analysis is performed, but why certain artifacts matter and how they support a defensible investigative conclusion.

---

### Relationship to Full Incident Investigations

Manual phishing analysis workflows directly support investigations but do not represent complete incident case studies on their own.

In operational environments, these workflows are often the **entry point into a broader investigation**, where extracted indicators are later used for:

- alerting and detection development  
- searching across environments for additional impact  
- incident scoping and containment  

Full incident lifecycle investigations are documented separately in the:

incident-response-and-investigations repository

This separation reflects how professional security teams distinguish between **initial triage and full incident response workflows**.

---

### Ongoing Development

Workflows in this category are continuously expanded as new phishing techniques, email delivery methods, and attacker behaviors evolve.

Some workflow directories may appear iterative or incomplete. This reflects active development and refinement rather than unfinished work.

The focus is on building a growing, reusable library of phishing analysis techniques rather than static one-time investigations.
