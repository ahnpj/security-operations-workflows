# Disk Image Forensics and Artifact Analysis Using Autopsy

### Overview

This execution documents the practical analysis of a Windows forensic disk image using Autopsy to recover operating system information, browser download activity, recent document artifacts, filesystem metadata, and local account information. The workflow focused on several key artifact categories, including operating system information, host identification, web download records, recent document and shortcut artifacts, virtual filesystem data, and operating system account information. Throughout the investigation, Autopsy was used to ingest and process a forensic disk image, organize recovered artifacts, and provide structured access to evidence sources distributed throughout the operating system.

The workflow demonstrates how multiple disk-based artifacts can be correlated to answer different investigative questions. Operating system metadata provides system context, browser artifacts reveal internet activity and downloaded content, shortcut files preserve evidence of file access, filesystem structures expose underlying storage organization, and account records help identify user-related activity. Rather than relying on a single artifact source, investigators correlate findings across multiple categories to develop a more complete understanding of activity occurring on the system. Together, these techniques reinforce foundational concepts in disk forensics, artifact correlation, browser forensics, filesystem analysis, user activity reconstruction, and forensic examination of Windows operating system evidence.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>

> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to see how the Autopsy case was created, how the disk image was processed, how artifacts were reviewed, and how findings were developed throughout the investigation.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Move to `analyst-notes.md` to understand disk forensic concepts, artifact interpretation, LNK analysis, browser artifact relevance, filesystem navigation concepts, and account activity analysis.

> 👉 **Review tooling and feature usage details**</br>
> See `tool-usage-notes.md` for detailed explanations of Autopsy, ingest modules, artifact categories, filesystem analysis techniques, and common investigation considerations.

> 👉 **See what each execution file contains in full detail**</br>
> Review the **Repository Structure & Supporting Documents** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Digital Forensics and Evidence Analysis
- **Primary Operational Focus:** Disk image forensics, artifact analysis, operating system identification, browser download analysis, Recent Documents analysis, virtual filesystem examination, and OS account review
- **Operational Objectives Demonstrated:** Forensic Case Creation, Disk Image Ingestion, Ingest Module Configuration, Operating System Identification, Hostname Identification, Web Download Analysis, URL Recovery, LNK Artifact Analysis, Filesystem Navigation, Directory Metadata Review, OS Account Analysis, Artifact Correlation
- **Primary Data Sources:** Forensic Disk Image Artifacts, Operating System Information, Hostname Records, Web Download Artifacts, Recent Documents Artifacts, LNK Shortcut Files, Virtual Filesystem Metadata, and OS Account Records

> **Terminology used:**
> **Workflows** refer to repeatable disk forensic processes used to examine storage media and recover evidence from forensic images.
> **Executions** refer to the hands-on analysis performed using Autopsy against a forensic disk image.
> **Writeups** document how artifacts were reviewed, how findings were interpreted, and how evidence was correlated to support investigative conclusions.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how digital forensic investigations are commonly documented.

**`workflow-execution.md`** — **If you want to follow the investigation step by step**</br>
This file contains the structured walkthrough showing how the disk image was imported, how ingest modules were selected, how artifacts were reviewed, and how findings were correlated throughout the investigation.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains the major learning points behind disk forensics, forensic imaging, browser artifacts, Recent Documents analysis, LNK files, filesystem navigation, account analysis, and evidence correlation.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
This file explains how Autopsy and its artifact views were used, why specific features were selected, and what evidence each analysis technique helped uncover.

---

### Repository Structure & Supporting Documents

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured forensic walkthrough documenting disk image analysis. | Documents case creation, evidence ingestion, operating system identification, browser download analysis, Recent Documents review, filesystem navigation, OS account analysis, and artifact correlation. |
| `images/` | Visual evidence supporting investigative findings. | Contains screenshots of Autopsy case creation, ingest configuration, artifact analysis, filesystem navigation, and account review. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, forensic relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents disk forensic concepts, artifact interpretation, investigative reasoning, filesystem analysis methodology, and evidence correlation. |
| `tool-usage-notes.md` | Technical reference for tools and artifacts used throughout the workflow. | Covers Autopsy, ingest modules, artifact categories, browser artifacts, LNK files, filesystem metadata, operational relevance, and common mistakes. |

---

### Environment, Data Sources, and Tools

The execution focuses on forensic disk image analysis using Autopsy to recover host-based artifacts and investigate user and system activity preserved on storage media.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Environment Type** | Windows forensic training environment |
| **Working Directory** | Autopsy For Disk Analysis |
| **Processed Assets** | Disk image artifacts, operating system metadata, download records, Recent Documents artifacts, filesystem metadata, and account records |
| **Execution Platform** | Windows |
| **Primary Tool** | Autopsy |
| **Operational Focus** | Disk image forensics and artifact analysis |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Primary Evidence Source** | `Craig Tucker Desktop.E01` |
| **Operating System Analysis** | Operating System Information artifacts |
| **Hostname Identification** | Operating System Information artifacts |
| **Download Analysis** | Web Downloads artifacts |
| **URL Recovery** | Web Downloads URL records |
| **Recent File Access Analysis** | Recent Documents artifacts |
| **Shortcut Analysis** | LNK (`.lnk`) artifacts |
| **Filesystem Analysis** | Virtual Filesystem navigation |
| **Account Analysis** | OS Accounts artifacts |
| **Operating System Identified** | `Windows 8.1 Pro` |
| **Hostname Identified** | `WIN-BK336TFMHLL` |

</details>

---

### Intended Use

This execution is intended to demonstrate foundational disk forensic methodology involving forensic image analysis and host artifact investigation. This process supports later forensic work involving disk analysis, incident response, endpoint investigations, evidence triage, user activity reconstruction, threat hunting, and forensic reporting.

<details>
<summary><strong>▶ Investigative Questions Addressed</strong><br>
</summary><br>

The workflow reflects how analysts may answer questions such as:

- What operating system was installed?
- What was the hostname of the system?
- What files were downloaded from the internet?
- What URL was used to download a file?
- What files were recently accessed?
- What was the original path of a recently accessed file?
- What directories existed within the filesystem?
- What metadata can be recovered from application folders?
- What local accounts existed on the system?
- When was the Administrator account last accessed?
- How can multiple disk artifacts be correlated to reconstruct activity?

</details>

---

### Relevance to Security Operations and Digital Forensics

Disk images preserve evidence that remains stored on a device after activity occurs. Operating system artifacts can establish system identity and environment context. Browser download artifacts can reveal downloaded content, source URLs, and internet-based activity. Recent Documents and LNK artifacts can preserve evidence of file access and original file locations. Filesystem metadata can reveal directory structures, installed applications, and storage-related information. OS Account artifacts can provide user and account activity context that supports timeline development and attribution efforts. This workflow demonstrates how multiple disk artifact sources can be combined to produce a more complete understanding of activity occurring on a Windows system.

<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>

- disk forensics,
- endpoint investigations,
- incident response,
- browser artifact analysis,
- filesystem analysis,
- account activity analysis,
- evidence triage,
- host activity reconstruction,
- artifact correlation,
- forensic reporting.

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational disk forensic methodology, forensic image analysis, browser artifact investigation, shortcut artifact analysis, filesystem examination, account activity review, investigative reasoning, and professional documentation aligned with entry-level DFIR, SOC, and incident response workflows.
