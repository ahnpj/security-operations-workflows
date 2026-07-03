# Windows Event Log Triage Using DeepBlueCLI

* **Category:** Security Operations and Windows Event Log Analysis
* **Primary Operational Focus:** Windows Security and PowerShell event log triage, password spray detection, privileged account creation detection, PowerShell download cradle identification, and MITRE ATT&CK technique/software attribution
* **Operational Objectives Demonstrated:** Automated Log Triage, Password Spray Detection, Account Creation Detection, PowerShell Download Cradle Identification, Bulk Log Export and Text Search, MITRE ATT&CK Technique Mapping, MITRE ATT&CK Software Attribution
* **Primary Data Sources:** Windows Security and PowerShell Operational `.evtx` event logs, DeepBlueCLI PowerShell output, and a consolidated `output.txt` export

> **Terminology used:**
> **Workflows** refer to repeatable log triage processes used to analyze Windows event logs and surface suspicious activity.
> **Executions** refer to the hands-on analysis performed using DeepBlueCLI and supporting PowerShell functionality.
> **Writeups** document how event log artifacts were analyzed, how findings were interpreted, and how results were mapped to MITRE ATT&CK.

---

### Overview

This execution documents the practical use of **DeepBlueCLI**, an open-source PowerShell module written by Eric Conrad, to triage Windows Security and PowerShell Operational event logs (`.evtx`) for signs of malicious activity.

The workflow focuses on several core log triage activities:

* Automated password spray detection,
* Responsible user and hostname attribution,
* Privileged/local account creation and group membership detection,
* PowerShell `Net.WebClient` download cradle identification,
* Bulk log processing and text-based search across a consolidated output file,
* MITRE ATT&CK technique and software attribution research.

The investigation begins by running DeepBlueCLI against an isolated Windows Security event log to detect and quantify a password spray attack, then pivots to identifying the responsible account and host. It then moves into a second event log to detect unauthorized local account creation and privileged group assignment. From there, the workflow shifts into a PowerShell Operational log to identify a `Net.WebClient`-based file download, isolating the most recently downloaded resource. Finally, the workflow demonstrates bulk log processing by running DeepBlueCLI against an entire directory of event logs at once, exporting the combined output to a text file, and using keyword search to identify a specific offensive PowerShell tool downloaded from GitHub, which is then researched and mapped to its corresponding MITRE ATT&CK Software ID.

This execution is designed to demonstrate how a single purpose-built PowerShell tool can rapidly surface high-value indicators across multiple unrelated Windows event logs — indicators that would otherwise require significant manual review using native Event Viewer filtering.

> 👉 **Follow the execution walkthrough first**
> Begin with `workflow-execution.md` to see how each event log was analyzed, how DeepBlueCLI output was interpreted, and how findings were researched and mapped to MITRE ATT&CK.

> 👉 **Review analytical reasoning and conceptual notes**
> Move to `analyst-notes.md` to understand DeepBlueCLI's detection logic, Windows event log fundamentals, and the investigative reasoning used throughout the workflow.

> 👉 **Review tooling and command usage details**
> See `tool-usage-notes.md` for detailed explanations of DeepBlueCLI, PowerShell syntax used, and how each `.evtx` file was processed.

> 👉 **See what each execution file contains in full detail**
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how Windows event log triage is commonly documented in SOC and blue-team workflows.

If you want to follow the investigation step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how each `.evtx` file was analyzed using DeepBlueCLI, how findings were interpreted, and how the final tool identified in the PowerShell download activity was researched against MITRE ATT&CK.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major learning points behind DeepBlueCLI's detection modules, Windows Security and PowerShell Operational log structure, password spray detection logic, account creation auditing, download cradle identification, and MITRE ATT&CK research.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

This file explains how DeepBlueCLI was invoked against each `.evtx` file, what each execution surfaced, and how bulk export and keyword search were used to identify a specific downloaded tool.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational SOC/blue-team documentation practices.

| File / Folder           | Purpose                                                                | Contents and Focus                                                                                                                        |
| ------------------------ | ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `workflow-execution.md`  | Structured log triage walkthrough documenting event log analysis.       | Documents password spray detection, account creation detection, download cradle identification, bulk export/search, and ATT&CK research.     |
| `images/`                | Visual evidence supporting investigative findings.                      | Contains screenshots of DeepBlueCLI output, PowerShell console activity, output.txt search results, and MITRE ATT&CK reference pages.        |
| `README.md`              | Execution overview and operational summary.                             | Provides structured explanation of workflow objectives, SOC relevance, repository navigation, and intended use.                              |
| `analyst-notes.md`       | Analyst observations, conceptual explanations, and learning notes.      | Documents DeepBlueCLI detection logic, event log concepts, investigative reasoning, and artifact interpretation.                              |
| `tool-usage-notes.md`    | Technical reference for tools and commands used throughout the workflow. | Covers DeepBlueCLI, PowerShell syntax, `.evtx` processing, bulk export, and common mistakes.                                                  |

---

### Environment, Data Sources, and Tools

The execution focuses on Windows event log triage using DeepBlueCLI from a PowerShell console.

#### Environment and Execution Scope (At a Glance)

| Area                   | Details                                                          |
| ----------------------- | -------------------------------------------------------------------- |
| **Environment Type**    | Windows-based lab environment                                       |
| **Working Directory**   | DeepBlueCLI folder (Desktop)                                        |
| **Processed Assets**    | Windows Security and PowerShell Operational `.evtx` event logs      |
| **Execution Platform**  | Windows PowerShell                                                   |
| **Primary Tools**       | DeepBlueCLI, Windows PowerShell, MITRE ATT&CK (research reference)  |
| **Operational Focus**   | Windows event log triage and rapid indicator identification          |

#### Data Sources, Evidence, and Analysis Techniques

| Area                                  | Details                                                            |
| --------------------------------------- | ---------------------------------------------------------------------- |
| **Password Spray Evidence**            | `password-spray.evtx`                                                 |
| **Account Creation Evidence**          | `new-user-security.evtx`                                              |
| **PowerShell Download Evidence**       | `powersploit-system.evtx`                                             |
| **Consolidated Export**                | `output.txt` (bulk export of all `.evtx` files in the evidence folder) |
| **Primary Tool**                       | DeepBlueCLI (`DeepBlue.ps1`)                                           |
| **Detection Technique — Password Spray** | Automated parsing of failed/successful authentication event clustering |
| **Detection Technique — Account Creation** | Automated parsing of Windows Security account management events    |
| **Detection Technique — Download Cradle** | Automated parsing of PowerShell Operational script block logging     |
| **Bulk Search Technique**              | Directory-wide DeepBlueCLI execution piped to a text file, searched via Ctrl+F |
| **Attribution Technique**              | Manual research against the MITRE ATT&CK technique and software pages |
| **Technique Identified**               | Password Spraying — `T1110.003`                                       |
| **Software Identified**                | Mimikatz — `S0002`                                                     |

---

### Intended Use

This execution is intended to demonstrate foundational Windows event log triage methodology using a purpose-built PowerShell log analysis tool.

The workflow reflects how analysts may answer questions such as:

* How many accounts were targeted in a suspected password spray attack?
* Which technique does this activity map to on MITRE ATT&CK?
* Which host and account were responsible for the activity?
* Was a new account created, and what privileges were assigned to it?
* What files were downloaded using PowerShell, and when?
* Can multiple event logs be processed and searched at scale?
* What offensive tooling was downloaded, and what is it formally known as?

This process supports later work involving SOC log triage, Windows event log analysis, threat hunting, incident response, and MITRE ATT&CK-aligned detection engineering.

---

### Relevance to Security Operations and Digital Forensics

Windows Security and PowerShell Operational event logs contain some of the highest-value telemetry available on a Windows host, but manually reviewing them via native Event Viewer filtering is slow and error-prone, particularly across multiple `.evtx` files.

DeepBlueCLI was built specifically to close this gap by applying known detection heuristics — including password spray clustering, suspicious account management activity, and PowerShell download cradle patterns — directly against raw `.evtx` files, surfacing results that would otherwise require significant manual correlation.

Together, these artifacts support:

* Windows event log triage,
* password spray and brute-force detection,
* privileged account abuse detection,
* PowerShell-based download cradle identification,
* threat hunting,
* incident response,
* MITRE ATT&CK-aligned detection engineering.

This workflow demonstrates how a single, purpose-built PowerShell tool can rapidly surface indicators across multiple unrelated Windows event logs and how those indicators can be researched and formally mapped to MITRE ATT&CK.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational Windows event log triage methodology, automated log analysis tooling, password spray and account abuse detection, PowerShell download cradle identification, investigative reasoning, and professional documentation aligned with entry-level SOC, threat hunting, and incident response workflows.