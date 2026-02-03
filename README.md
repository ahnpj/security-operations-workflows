## Security Operations Workflows

This repository contains **hands-on security operations workflows** modeled after real SOC and blue-team execution. Each workflow captures how a common operational task is performed end-to-end — including the commands, pivots, validation logic, and analyst reasoning used to reach defensible conclusions.

Workflows are organized into category folders based on **primary operational domain** (endpoint triage, SIEM detections, network traffic analysis, identity and access, automation/log processing, and vulnerability management). Each workflow folder is self-contained and designed to be read like an internal runbook execution: *what you did, why you did it, what you saw, and what it means*.

These writeups emphasize evidence-based validation, repeatable analyst processes, and practical execution patterns — not just tool features.

> 👉 For **guidance on how workflows are grouped by operational domain and how to navigate the repository**, start with the **[Start Here: How to Navigate This Repository](#start-here-how-to-navigate-this-repository)** section below.

> 👉 To **understand how category folders are defined and what types of workflows belong in each**, see **[How This Repository Is Organized](#how-this-repository-is-organized)** and **[Category Folders and Current Workflows](#category-folders-and-current-workflows)** sections below

> 👉 For **details on how individual workflow folders are structured, including standard files and documentation flow**, see **[How Workflows Are Structured & Organized](#how-workflows-are-structured--organized)**, **[How These Workflows Are Designed](#how-these-workflows-are-designed)** and **[Standard Files in Each Workflow Folder](#standard-files-in-each-workflow-folder)** sections below.

---

### Start Here: How to Navigate This Repository
👉 **Browse by category folders**  
At the top level of this repository, you’ll see folders organized by **primary operational domain** (for example: endpoint triage, SIEM detections, network traffic analysis, identity and access). Start by choosing the category that best matches the type of task you want to practice or reference.

👉 **Each subfolder is one complete workflow execution**  
Inside each category, every workflow folder is a **self-contained operational task** documented end-to-end, including the execution steps and supporting notes.

👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see the step-by-step command execution, pivots, and validation logic.

👉 **Use analyst notes to understand decisions**  
Read `analyst-notes.md` to see assumptions, interpretation, edge cases, and why certain pivots or checks matter in real investigations.

👉 **Reference tool usage notes for syntax and patterns**  
Use `tool-usage-notes.md` as a reusable reference for command syntax, flags, parsing patterns, and “gotchas” you’ll want handy during real triage.

👉 **Check automation design notes when present**  
Some workflows include `automation-design-notes.md` to show how the workflow can be translated into repeatable scripts, parsing logic, or detection automation.

👉 **Use this repository as execution-based learning**  
These workflows are designed to be read like **SOC runbook executions**, showing not just *what* to run, but *how* an analyst validates results and avoids false conclusions.

---

### How This Repository Is Organized
This repository is organized into **category folders**, which are the top-level folders you see at the top when browsing the repository.

Each category represents a major security operations domain used to group related workflows. Workflows are placed into a category based on **what the analyst is primarily doing** (the operational job-to-be-done), not solely by which tools appear in the workflow.

Each workflow lives in its own folder inside a category and includes the documentation, notes, and supporting evidence needed to understand and repeat the task.

#### Category Folders (Top-Level Directories)
| Category Folder | Workflow Focus | What You Will Find Inside |
|---|---|---|
| **endpoint-triage-and-host-analysis/** | Endpoint triage and host investigation workflows focused on validating host state and suspicious behavior using native tools. | Workflows for Windows and Linux host triage: process/service inspection, filesystem artifact checks, network exposure validation, and quick health/persistence checks. |
| **siem-detections-and-log-analysis/** | SIEM-centered workflows for parsing, normalizing, and analyzing telemetry for investigations and detections. | Workflows for SPL-driven investigations, field extraction and normalization, ingestion corrections, and detection-ready data shaping (including VPN remote-access anomaly analysis). |
| **network-monitoring-and-traffic-analysis/** | Network monitoring workflows focused on packet capture, filtering, and protocol-level analysis. | Workflows for bounded packet capture with tcpdump and deeper PCAP analysis in Wireshark (filtering, session reconstruction, evidence extraction). |
| **identity-and-access-management/** | Identity and access management workflows focused on directory operations and permission validation. | Workflows for Active Directory user/group administration, delegation, OU management, and access control validation using standard enterprise tooling. |
| **detection-automation-and-log-processing/** | Automation and log-processing workflows focused on turning raw telemetry into repeatable detection logic. | Workflows for Python-based parsing/analysis, transforming logs into structured signals, and prototyping behavior-based detections. |
| **vulnerability-management-and-exposure-analysis/** | Vulnerability management workflows focused on exposure analysis, prioritization, and remediation tracking. | Category scaffold for future workflows (currently no workflow folders in this category). |

---

### How Workflows Are Structured & Organized
Workflows are organized using a two-level hierarchy:

1. **Category folders** — group related tasks by primary operational domain  
2. **Workflow folders** — each individual operational task lives in its own folder inside a category

Each workflow folder is self-contained and represents **one complete execution writeup**.

**Naming convention:** workflow folders use a **tool-first naming convention** followed by the operational focus (for example: `powershell-windows-endpoint-triage-and-system-inspection`, `tcpdump-targeted-packet-capture-and-filtering`, or `splunk-vpn-remote-access-anomaly-detection`). This makes it easy to scan the repo and immediately understand both the platform/tool and the task.

---

### How These Workflows Are Designed
This repository is **task-first**, not tool-documentation-first.

You will find:

- Step-by-step operational executions (`workflow-execution.md`) with commands and pivots
- Analyst reasoning and interpretation notes (`analyst-notes.md`)
- Tool/reference notes you can reuse in future work (`tool-usage-notes.md`)
- Supporting screenshots and evidence (`images/`)
- Where relevant, design notes for turning the workflow into automation (`automation-design-notes.md`)

The goal is to capture what a capable SOC analyst would record while executing a runbook: what was checked, why it was checked, how results were validated, and what conclusions can (and cannot) be drawn from the evidence.

---

### Standard Files in Each Workflow Folder
While not every workflow requires every file, workflow folders typically include the following supporting documents:

| File / Folder | Purpose | Contents and Focus |
|---|---|---|
| **Workflow README** (`README.md`) | Quick orientation for the workflow. | Defines the operational purpose, scope, assumptions, tooling, and what the workflow is meant to demonstrate. |
| **Workflow Execution** (`workflow-execution.md`) | Step-by-step hands-on execution. | The actual command flow and analyst pivots, including what to run, what to look for, and how to validate results. |
| **Analyst Notes** (`analyst-notes.md`) | Reasoning, interpretation, and edge cases. | Why specific checks are performed, how to avoid common mistakes, and how to interpret ambiguous outputs. |
| **Tool Usage Notes** (`tool-usage-notes.md`) | Reusable tool reference. | Flags, syntax patterns, parsing tips, and operational “gotchas” you can apply across multiple scenarios. |
| **Automation Design Notes** (`automation-design-notes.md`) | Turning execution into repeatable automation (when present). | Notes on parsing logic, data structures, detection logic shaping, and how to operationalize the workflow with scripts or scheduled jobs. |
| **Screenshots and Supporting Evidence** (`images/`) | Visual validation artifacts. | Screenshots referenced throughout the workflow execution and notes to support the documented conclusions. |

Together, these files separate **execution**, **reasoning**, and **reference material** into clear, reviewable components while keeping everything tied to the same operational task.

---

### Category Folders and Current Workflows
#### ▶️ Endpoint Triage and Host Analysis Workflows  
`endpoint-triage-and-host-analysis/`

Current workflows:

- **Linux Endpoint Triage and Script-Based Validation Logic Using Bash — Operational Execution**  
  Live host enumeration, filesystem inspection, shell environment awareness, and basic automation using Bash. Tools: Linux (Ubuntu), Bash shell, native command-line utilities, SSH-based access.  
  Folder: `endpoint-triage-and-host-analysis/bash-linux-endpoint-triage-and-validation-scripting/`

- **Windows Endpoint Triage and Network Exposure Validation Using CMD and PowerShell — Operational Execution**  
  Filesystem inspection, process enumeration, network correlation, and targeted containment using native Windows CLI tools. Tools: Windows CMD, PowerShell, native Windows system utilities.  
  Folder: `endpoint-triage-and-host-analysis/cmd-windows-process-and-network-triage/`

- **Windows Endpoint Triage and System Inspection Using PowerShell — Operational Execution**  
  Process inspection, filesystem validation, service analysis, network inspection, integrity validation, and remote execution using PowerShell. Tools: Windows PowerShell, native Windows telemetry and services.  
  Folder: `endpoint-triage-and-host-analysis/powershell-windows-endpoint-triage-and-system-inspection/`

#### ▶️ SIEM Detections and Log Analysis Workflows  
`siem-detections-and-log-analysis/`

Current workflows:

- **Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk — Operational Execution**  
  Ingestion pipeline design, event boundary correction, sensitive data masking, and structured field extraction for detection-ready telemetry. Tools: Splunk Enterprise, Linux command line, Splunk configuration files (`inputs.conf`, `props.conf`, `transforms.conf`, `fields.conf`).  
  Folder: `siem-detections-and-log-analysis/splunk-log-parsing-and-field-extraction/`

- **VPN Authentication and Remote Access Anomaly Analysis Using Splunk — Operational Execution**  
  Authentication analysis, geographic anomaly detection, session outcome analysis, and baseline development using VPN telemetry. Tools: Splunk Enterprise, SPL (Search Processing Language), JSON field extraction using `spath`.  
  Folder: `siem-detections-and-log-analysis/splunk-vpn-remote-access-anomaly-detection/`

#### ▶️ Network Monitoring and Traffic Analysis Workflows  
`network-monitoring-and-traffic-analysis/`

Current workflows:

- **Targeted Packet Capture and Traffic Filtering Using tcpdump — Operational Execution**  
  Interface validation, bounded packet capture, protocol filtering, and offline analysis preparation. Tools: Linux, tcpdump, native networking utilities.  
  Folder: `network-monitoring-and-traffic-analysis/tcpdump-targeted-packet-capture-and-filtering/`

- **Traffic Filtering, Protocol Dissection, and Session Reconstruction Using Wireshark — Operational Execution**  
  Protocol inspection, OSI-layer dissection, session reconstruction, and evidence extraction from packet captures. Tools: Wireshark, stored PCAP and PCAPNG capture files.  
  Folder: `network-monitoring-and-traffic-analysis/wireshark-traffic-analysis-and-session-reconstruction/`

#### ▶️ Identity and Access Management Workflows  
`identity-and-access-management/`

Current workflows:

- **Identity and Access Management Operations Using Active Directory — Operational Execution**  
  User and group administration, organizational unit management, delegation, and access control validation in an enterprise directory. Tools: Windows Server, Active Directory Domain Services, Active Directory Users and Computers (ADUC), Group Policy Management Console (GPMC).  
  Folder: `identity-and-access-management/active-directory-iam-operations-and-permission-validation/`

#### ▶️ Detection Automation and Log Processing Workflows  
`detection-automation-and-log-processing/`

Current workflows:

- **Log Parsing and Threat Detection Automation Using Python — Operational Execution**  
  Behavior-based detection logic prototyping from raw security telemetry across multiple environments. Tools: Python (standard library only), Linux shell environment (Google Cloud Shell), multi-format log datasets.  
  Folder: `detection-automation-and-log-processing/python-log-parsing-and-threat-detection/`

#### ▶️ Vulnerability Management and Exposure Analysis Workflows  
`vulnerability-management-and-exposure-analysis/`

Current workflows:

- *(No workflow folders in this category yet — planned for future additions.)*

---

### Overlap Between Categories

Overlap between operational domains is expected and intentional.

A single workflow may touch:

- Endpoint artifacts
- Identity context
- Network indicators
- SIEM queries and field extraction

Rather than duplicating workflows across multiple categories, each workflow is grouped by the **primary operational job-to-be-done**, while the workflow documentation can include supporting context from other domains where it is necessary to validate results.

---

### Relationship to Investigations and Case Work

This repository documents **how common SOC tasks are executed**.

Case-based incident writeups (full incident narratives, timelines, and findings) are maintained separately in the investigations repository so that this repo can remain focused on reusable operational execution patterns.

Where relevant, workflows can be used as building blocks inside investigations to demonstrate how repeatable operational procedures translate into real investigative decisions.

---

### Ongoing Development

Workflows may be expanded over time as additional tools, validation steps, and automation approaches are added. Updates are intended to reflect iterative improvement, similar to how real SOC runbooks mature through repeated use and post-incident learning.
