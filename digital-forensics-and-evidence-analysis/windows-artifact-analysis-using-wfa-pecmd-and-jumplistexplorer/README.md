# Windows Artifact Analysis Using WFA, PECmd, and JumpList Explorer

- **Category:** Digital Forensics and Evidence Analysis
- **Primary Operational Focus:** Windows artifact analysis, activity reconstruction, execution evidence identification, and user activity analysis
- **Operational Objectives Demonstrated:** Shortcut Analysis, Prefetch Analysis, Execution Artifact Review, PowerShell Script Investigation, Jump List Analysis, Browser Activity Identification, Multi-Artifact Correlation
- **Primary Data Sources:** Windows Shortcut (`.LNK`) files, Windows Prefetch (`.pf`) files, Windows Jump List artifacts, application execution metadata, and browser-related activity records

> **Terminology used:**
> **Workflows** refer to repeatable forensic processes used to analyze Windows artifacts and reconstruct user or system activity.
> **Executions** refer to the hands-on analysis performed using Windows File Analyzer (WFA), PECmd, JumpList Explorer, and Windows command-line tools.
> **Writeups** document how artifacts were analyzed, how evidence was interpreted, and how findings were correlated across multiple artifact sources.

---

### Overview

This execution documents the practical analysis of several common Windows forensic artifacts to reconstruct activity that occurred on a Windows system.

The workflow focuses on three primary artifact sources:

- Windows Shortcut (`.LNK`) files,
- Windows Prefetch (`.pf`) files,
- Windows Jump List artifacts.

The investigation begins with shortcut analysis to identify a suspicious file and determine its origin.

The workflow then moves into Prefetch analysis to identify applications that interacted with the suspicious file and to locate execution-related evidence.

Finally, Jump List analysis is performed to identify browser-related user activity and determine which website was accessed and which browser was used.

This execution is designed to demonstrate how different Windows artifacts answer different investigative questions. No single artifact provides a complete picture of activity. Instead, investigators correlate findings across multiple sources to reconstruct events and develop a more complete understanding of user and application behavior.

> 👉 **Follow the execution walkthrough first**
> Begin with `workflow-execution.md` to see how shortcut files, Prefetch artifacts, and Jump Lists were analyzed throughout the investigation.

> 👉 **Review analytical reasoning and conceptual notes**
> Move to `analyst-notes.md` to understand why these artifacts exist, what they reveal, how they differ from one another, and how they can be correlated during an investigation.

> 👉 **Review tooling and command usage details**
> See `tool-usage-notes.md` for detailed explanations of WFA, PECmd, JumpList Explorer, Windows artifact types, and command usage.

> 👉 **See what each execution file contains in full detail**
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how digital forensic investigations are commonly documented.

If you want to follow the investigation step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how shortcut artifacts were analyzed, how Prefetch files were examined, how application associations were identified, and how browser activity was recovered through Jump List analysis.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major learning points behind Windows shortcuts, Prefetch files, Jump Lists, execution artifacts, user activity reconstruction, and artifact correlation.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

This file explains how each tool was used, why it was selected, and what evidence it helped uncover.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational digital forensic documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured forensic walkthrough documenting Windows artifact analysis. | Documents shortcut analysis, archive identification, Prefetch review, application association discovery, Jump List analysis, and activity reconstruction. |
| `images/` | Visual evidence supporting investigative findings. | Contains screenshots of WFA output, PECmd analysis, Prefetch keyword searching, application identification, and JumpList Explorer findings. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, forensic relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents artifact behavior, investigative reasoning, execution concepts, and evidence interpretation. |
| `tool-usage-notes.md` | Technical reference for tools and artifacts used throughout the workflow. | Covers WFA, PECmd, JumpList Explorer, Windows artifacts, command syntax, operational relevance, and common mistakes. |

---

### Environment, Data Sources, and Tools

The execution focuses on Windows artifact analysis using forensic utilities designed to examine operating system artifacts and execution traces.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Windows forensic training environment |
| **Working Directory** | `Windows Investigation One` |
| **Processed Assets** | Shortcut files, Prefetch files, Jump List artifacts |
| **Execution Platform** | Windows |
| **Primary Tools** | Windows File Analyzer (WFA), PECmd, JumpList Explorer, Command Prompt |
| **Operational Focus** | Identify suspicious files, execution-related activity, and browser-based user activity |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Shortcut Artifact Source** | `Shortcuts` directory |
| **Prefetch Artifact Source** | `Prefetch` directory |
| **Jump List Artifact Source** | `Jump List` directory |
| **Suspicious File Identified** | `PlagueRat.ps1.lnk` |
| **Associated Archive** | `2020_Summer_Photos.zip` |
| **Associated Document** | `Invoice537-PR-06-05-2020.docx` |
| **Execution Analysis Technique** | Prefetch artifact review using PECmd |
| **Keyword Hunting Technique** | Directory-wide Prefetch searching |
| **Browser Activity Technique** | Jump List analysis using JumpList Explorer |
| **Browser Identified** | Microsoft Edge |
| **Domain Identified** | `discordapp.com` |

---

### Intended Use

This execution is intended to demonstrate foundational Windows forensic methodology involving artifact analysis and activity reconstruction.

The workflow reflects how analysts may answer questions such as:

- What suspicious files existed on the system?
- Where did those files originate?
- Which applications interacted with those files?
- Is there evidence of execution-related activity?
- What browser activity can be identified?
- Which website was accessed?
- How can multiple artifact sources be correlated to reconstruct activity?

This process supports later forensic work involving malware investigations, user activity reconstruction, incident response, execution analysis, evidence triage, and forensic reporting.

---

### Relevance to Security Operations and Digital Forensics

Windows systems continuously generate artifacts that record evidence of user and application activity.

Shortcut files can reveal information about files that existed on the system and where those files originated.

Prefetch files can reveal execution-related evidence, application usage, referenced files, and historical execution activity.

Jump Lists can reveal resources accessed through specific applications, including files, folders, documents, and websites.

Together, these artifacts support:

- evidence triage,
- user activity reconstruction,
- malware investigation,
- execution analysis,
- incident response,
- forensic reporting,
- artifact correlation,
- timeline development.

This workflow demonstrates how multiple Windows artifact sources can be combined to produce a more complete understanding of activity occurring on a system.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational Windows forensic methodology, artifact analysis, execution evidence review, user activity reconstruction, investigative reasoning, and professional documentation aligned with entry-level DFIR, SOC, and incident response workflows.
