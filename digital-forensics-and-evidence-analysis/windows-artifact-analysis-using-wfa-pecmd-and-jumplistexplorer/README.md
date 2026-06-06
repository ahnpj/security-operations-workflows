# Windows Artifact Analysis Using WFA, PECmd, and JumpList Explorer

### Overview

This execution documents the practical analysis of several common Windows forensic artifacts used to reconstruct activity on a Windows system. The workflow focused on examining Windows Shortcut (`.LNK`) files, Windows Prefetch (`.pf`) files, and Windows Jump List artifacts to identify evidence related to file access, application execution, and user activity. Throughout the investigation, shortcut artifacts were analyzed to determine the origin of a suspicious file, Prefetch artifacts were reviewed to identify applications that interacted with the file and provide execution evidence, and Jump List artifacts were examined to uncover browser-related activity and identify websites accessed by the user.

The workflow demonstrates how multiple Windows artifacts provide complementary perspectives during an investigation. Shortcut files help identify file locations and access patterns, Prefetch artifacts reveal application execution activity, and Jump Lists provide insight into recently accessed files and user interactions. Rather than relying on a single artifact source, investigators correlate findings across multiple evidence types to reconstruct events and develop a more complete understanding of user and application behavior. Together, these techniques reinforce foundational concepts in Windows artifact analysis, execution evidence identification, user activity reconstruction, artifact correlation, timeline development, and forensic investigations.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>

> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to see how shortcut files, Prefetch artifacts, and Jump Lists were analyzed throughout the investigation.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Move to `analyst-notes.md` to understand why these artifacts exist, what they reveal, how they differ from one another, and how they can be correlated during an investigation.

> 👉 **Review tooling and command usage details**</br>
> See `tool-usage-notes.md` for detailed explanations of WFA, PECmd, JumpList Explorer, Windows artifact types, and command usage.

> 👉 **See what each execution file contains in full detail**</br>
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Digital Forensics and Evidence Analysis
- **Primary Operational Focus:** Windows artifact analysis, activity reconstruction, execution evidence identification, and user activity analysis
- **Operational Objectives Demonstrated:** Shortcut Analysis, Prefetch Analysis, Execution Artifact Review, PowerShell Script Investigation, Jump List Analysis, Browser Activity Identification, Multi-Artifact Correlation
- **Primary Data Sources:** Windows Shortcut (`.LNK`) files, Windows Prefetch (`.pf`) files, Windows Jump List artifacts, application execution metadata, and browser-related activity records

> **Terminology used:**
> **Workflows** refer to repeatable forensic processes used to analyze Windows artifacts and reconstruct user or system activity.
> **Executions** refer to the hands-on analysis performed using Windows File Analyzer (WFA), PECmd, JumpList Explorer, and Windows command-line tools.
> **Writeups** document how artifacts were analyzed, how evidence was interpreted, and how findings were correlated across multiple artifact sources.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how digital forensic investigations are commonly documented.

**`workflow-execution.md`** — **If you want to follow the investigation step by step**</br>
This file contains the structured walkthrough showing how shortcut artifacts were analyzed, how Prefetch files were examined, how application associations were identified, and how browser activity was recovered through Jump List analysis.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains the major learning points behind Windows shortcuts, Prefetch files, Jump Lists, execution artifacts, user activity reconstruction, and artifact correlation.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
This file explains how each tool was used, why it was selected, and what evidence it helped uncover.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational digital forensic documentation practices.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured forensic walkthrough documenting Windows artifact analysis. | Documents shortcut analysis, archive identification, Prefetch review, application association discovery, Jump List analysis, and activity reconstruction. |
| `images/` | Visual evidence supporting investigative findings. | Contains screenshots of WFA output, PECmd analysis, Prefetch keyword searching, application identification, and JumpList Explorer findings. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, forensic relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents artifact behavior, investigative reasoning, execution concepts, and evidence interpretation. |
| `tool-usage-notes.md` | Technical reference for tools and artifacts used throughout the workflow. | Covers WFA, PECmd, JumpList Explorer, Windows artifacts, command syntax, operational relevance, and common mistakes. |

</details>

---

### Environment, Data Sources, and Tools

The execution focuses on Windows artifact analysis using forensic utilities designed to examine operating system artifacts and execution traces.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>
  
| Area | Details |
|--------|---------|
| **Environment Type** | Windows forensic training environment |
| **Working Directory** | `Windows Investigation One` |
| **Processed Assets** | Shortcut files, Prefetch files, Jump List artifacts |
| **Execution Platform** | Windows |
| **Primary Tools** | Windows File Analyzer (WFA), PECmd, JumpList Explorer, Command Prompt |
| **Operational Focus** | Identify suspicious files, execution-related activity, and browser-based user activity |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>
  
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

</details>

---

### Intended Use

This execution is intended to demonstrate foundational Windows forensic methodology involving artifact analysis and activity reconstruction. This process supports later forensic work involving malware investigations, user activity reconstruction, incident response, execution analysis, evidence triage, and forensic reporting.

<details>
<summary><strong>▶ Investigative Questions Addressed</strong><br>
</summary><br>

- What suspicious files existed on the system?
- Where did those files originate?
- Which applications interacted with those files?
- Is there evidence of execution-related activity?
- What browser activity can be identified?
- Which website was accessed?
- How can multiple artifact sources be correlated to reconstruct activity?

</details>

---

### Relevance to Security Operations and Digital Forensics

Windows systems continuously generate artifacts that record evidence of user and application activity. Shortcut files can reveal information about files that existed on the system and where those files originated. Prefetch files can reveal execution-related evidence, application usage, referenced files, and historical execution activity. Jump Lists can reveal resources accessed through specific applications, including files, folders, documents, and websites. This workflow demonstrates how multiple Windows artifact sources can be combined to produce a more complete understanding of activity occurring on a system.

<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>

- evidence triage,
- user activity reconstruction,
- malware investigation,
- execution analysis,
- incident response,
- forensic reporting,
- artifact correlation,
- timeline development.

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational Windows forensic methodology, artifact analysis, execution evidence review, user activity reconstruction, investigative reasoning, and professional documentation aligned with entry-level DFIR, SOC, and incident response workflows.
