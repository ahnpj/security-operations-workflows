# Recycle Bin Analysis Using RBCmd and CSVQuickViewer

### Overview

This execution documents the practical analysis of Windows Recycle Bin artifacts to identify deleted files, determine user ownership, review deletion timestamps, and reconstruct deleted-file activity. The workflow focused on examining Recycle Bin artifacts, parsing Recycle Bin metadata, and reviewing structured output for correlation and analysis. Throughout the investigation, Recycle Bin metadata was processed using RBCmd to recover original file paths, deletion timestamps, file sizes, and user-associated artifact information, while CSVQuickViewer was used to review and correlate the parsed results. 

The workflow demonstrates how deleted-file activity can be reconstructed through the analysis of Recycle Bin artifacts. Rather than relying on a single piece of evidence, investigators correlate file paths, file sizes, deletion timestamps, Security Identifiers (SIDs), and user account information to determine what was deleted and who was associated with the activity. Together, these techniques reinforce foundational concepts in deleted-file investigations, artifact correlation, user attribution, metadata analysis, timeline reconstruction, and forensic examination of Windows operating system artifacts.


> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>

> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to see how Recycle Bin artifacts were parsed, reviewed, and correlated throughout the investigation.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Move to `analyst-notes.md` to understand how Recycle Bin artifacts work, what they preserve, how user attribution occurs, and why deleted-file metadata is valuable during investigations.

> 👉 **Review tooling and command usage details**</br>
> See `tool-usage-notes.md` for detailed explanations of RBCmd, CSVQuickViewer, Recycle Bin artifacts, SID folders, and command usage.

> 👉 **See what each execution file contains in full detail**</br>
> Review the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>
  
* **Category:** Digital Forensics and Evidence Analysis
* **Primary Operational Focus:** Recycle Bin artifact analysis, deleted-file investigation, user attribution, and deleted-file activity reconstruction
* **Operational Objectives Demonstrated:** Recycle Bin Analysis, Deleted-File Metadata Review, User Attribution, SID Analysis, Timestamp Analysis, Deleted-File Correlation, Evidence Interpretation
* **Primary Data Sources:** Windows Recycle Bin artifacts, SID-based Recycle Bin folders, deleted-file metadata, file size information, deletion timestamps, and original file path records

> **Terminology used:**
> **Workflows** refer to repeatable forensic processes used to analyze Recycle Bin artifacts and reconstruct deleted-file activity.
> **Executions** refer to the hands-on analysis performed using RBCmd, CSVQuickViewer, Windows Command Prompt, and Recycle Bin metadata.
> **Writeups** document how deleted-file artifacts were analyzed, how evidence was interpreted, and how findings were correlated across multiple metadata fields.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how digital forensic investigations are commonly documented.

**`workflow-execution.md`** — **If you want to follow the investigation step by step**</br>
This file contains the structured walkthrough showing how Recycle Bin artifacts were accessed, parsed using RBCmd, reviewed using CSVQuickViewer, and correlated to answer deleted-file investigation questions.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains the major learning points behind Recycle Bin artifacts, deleted-file metadata, SID attribution, timestamp interpretation, file ownership, and evidence correlation.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
This file explains how each tool was used, why it was selected, and what evidence it helped uncover.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational digital forensic documentation practices.

| File / Folder           | Purpose                                                                    | Contents and Focus                                                                                                                                                  |
| ----------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `workflow-execution.md` | Structured forensic walkthrough documenting Recycle Bin artifact analysis. | Documents Recycle Bin review, RBCmd parsing, CSV analysis, deleted-file identification, user attribution, timestamp review, SID analysis, and evidence correlation. |
| `images/`               | Visual evidence supporting investigative findings.                         | Contains screenshots of Recycle Bin review, RBCmd execution, CSVQuickViewer analysis, deleted-file records, timestamp review, and SID attribution.                  |
| `README.md`             | Execution overview and operational summary.                                | Provides structured explanation of workflow objectives, forensic relevance, repository navigation, and intended use.                                                |
| `analyst-notes.md`      | Analyst observations, conceptual explanations, and learning notes.         | Documents Recycle Bin behavior, deleted-file evidence concepts, SID attribution, investigative reasoning, and evidence interpretation.                              |
| `tool-usage-notes.md`   | Technical reference for tools and artifacts used throughout the workflow.  | Covers RBCmd, CSVQuickViewer, Recycle Bin artifacts, SID folders, command syntax, operational relevance, and common mistakes.                                       |

---

### Environment, Data Sources, and Tools

The execution focuses on Recycle Bin artifact analysis using forensic utilities designed to recover and interpret deleted-file metadata.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area                   | Details                                                                                |
| ---------------------- | -------------------------------------------------------------------------------------- |
| **Environment Type**   | Windows forensic training environment                                                  |
| **Working Directory**  | `Recycle-Bin-Analysis`                                                                 |
| **Processed Assets**   | Recycle Bin artifacts, deleted-file metadata, SID folders                              |
| **Execution Platform** | Windows                                                                                |
| **Primary Tools**      | RBCmd, CSVQuickViewer, Command Prompt                                                  |
| **Operational Focus**  | Identify deleted files, user ownership, deletion timestamps, and deleted-file activity |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>
  
| Area                             | Details                                 |
| -------------------------------- | --------------------------------------- |
| **Artifact Source**              | `C:\$Recycle.Bin`                       |
| **User Attribution Source**      | SID folders and original file paths     |
| **Metadata Source**              | Recycle Bin metadata parsed by RBCmd    |
| **Primary Review Tool**          | CSVQuickViewer                          |
| **Largest Deleted File**         | `kali-linux-2023.1-virtualbox-amd64.7z` |
| **Most Active User**             | `Simon.Leeves`                          |
| **Deleted-File Count**           | `24`                                    |
| **Timestamp Analysis Technique** | DeletedOn metadata review               |
| **User Attribution Technique**   | Original file path and SID correlation  |
| **SID Analysis Technique**       | Source path review and SID extraction   |

</details>

---

### Intended Use

This execution is intended to demonstrate foundational Windows forensic methodology involving deleted-file analysis and Recycle Bin artifact review. This process supports later forensic work involving user activity reconstruction, evidence triage, insider threat investigations, incident response, artifact analysis, and forensic reporting.

<details>
<summary><strong>▶ Investigative Questions Addressed</strong><br>
</summary><br>
  
* What files were deleted?
* Where did those files originally exist?
* When were those files deleted?
* Which user account was associated with the deleted files?
* Which deleted file was the largest?
* Which user had the most deleted files?
* What deleted files support a specific allegation?
* How can Recycle Bin artifacts be correlated to reconstruct activity?

</details>


---

### Relevance to Security Operations and Digital Forensics

Windows systems continuously generate artifacts that preserve evidence of user activity. Recycle Bin artifacts can preserve metadata about deleted files, including original file paths, file sizes, deletion timestamps, and user-specific ownership information. SID folders can help associate deleted-file evidence with Windows user accounts. Parsed Recycle Bin metadata can help investigators identify what was deleted, when it was deleted, where it originated, and who was associated with the deleted content. This workflow demonstrates how multiple Recycle Bin metadata fields can be combined to produce a more complete understanding of deleted-file activity occurring on a system.

<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>
  
* evidence triage,
* deleted-file analysis,
* user activity reconstruction,
* insider threat investigations,
* incident response,
* forensic reporting,
* artifact correlation,
* timeline development.

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational Windows forensic methodology, Recycle Bin artifact analysis, deleted-file evidence review, user attribution, investigative reasoning, and professional documentation aligned with entry-level DFIR, SOC, and incident response workflows.
