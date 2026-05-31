# File System Identification and Evidence Structure Analysis Using FTK Imager

- **Category:** Digital Forensics and Evidence Analysis  
- **Primary Operational Focus:** File system identification, forensic image examination, partition review, and storage organization analysis  
- **Operational Objectives Demonstrated:** Evidence Image Review, Partition Analysis, File System Identification, Digital Storage Fundamentals, Forensic Context Development  
- **Primary Data Sources:** Forensic disk images, partition structures, file system labels, and FTK Imager evidence tree views

> **Terminology used:**  
> **Workflows** refer to repeatable digital forensic processes such as importing evidence images, reviewing partitions, and identifying file systems.  
> **Executions** refer to the hands-on examination performed in FTK Imager against provided forensic image files.  
> **Writeups** document how the examination was performed, how file system findings were identified, and why those findings matter during forensic analysis.

---

### Overview

This execution documents the use of FTK Imager to identify file systems from acquired forensic disk images. The workflow focused on importing three image files, reviewing their partition structures, identifying the file system displayed by FTK Imager, and documenting what those findings mean from a digital forensics perspective.

The examined images were:

- `carve1.img`
- `carve2.img`
- `disk1.img`

The file systems identified were:

- `carve1.img` — NTFS
- `carve2.img` — FAT32
- `disk1.img` — EXT3

Although this appears to be a simple evidence review task, file system identification is one of the earliest and most important steps in a forensic examination. Before an examiner can meaningfully recover deleted files, interpret metadata, analyze timestamps, review user artifacts, or build a timeline, they must first understand how the evidence is organized.

The workflow also reinforces several foundational storage concepts, including:

- file systems,
- partitions,
- clusters,
- Logical Block Addresses (LBAs),
- SSD controllers,
- Flash Translation Layers (FTLs),
- TRIM,
- garbage collection,
- wear leveling.

These concepts are important because file systems organize evidence logically, while the underlying storage device manages how data is physically stored.

> 👉 **Follow the execution walkthrough first**  
Begin with `workflow-execution.md` inside this folder to see how each image was imported into FTK Imager, how the partition structure was reviewed, and how each file system was identified.

> 👉 **Review analyst reasoning and conceptual notes**  
Move to `analyst-notes.md` to understand the key learning points, confusion areas, and storage architecture concepts that became clearer during execution.

> 👉 **Review tool-specific usage details**  
See `tool-usage-notes.md` to understand how FTK Imager was used to import evidence images, review partitions, and identify file systems from the Evidence Tree.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how digital forensic workflows are commonly documented.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

This file contains the hands-on walkthrough showing how the forensic images were imported and how FTK Imager displayed the file system labels.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains why file system identification matters, what the confusing parts were, and how concepts such as NTFS, FAT32, EXT3, partitions, clusters, LBAs, and SSD behavior relate to the workflow.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

This file explains how FTK Imager was used, what parts of the interface mattered, and how the Evidence Tree helped identify each file system.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational digital forensic documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured forensic evidence examination walkthrough documenting how FTK Imager was used to identify file systems from disk images. | Documents image import, partition review, Evidence Tree interpretation, file system identification, screenshot placeholders, and analyst interpretation for NTFS, FAT32, and EXT3. |
| `images/` | Visual evidence supporting file system identification findings. | Contains screenshots of FTK Imager showing imported images, partition structures, and file system labels such as `[NTFS]`, `[FAT32]`, and `[Ext3]`. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of the workflow objectives, digital forensics relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes recorded during execution. | Documents confusion points, reasoning development, file system concepts, storage architecture notes, and connections between file systems and SSD internals. |
| `tool-usage-notes.md` | Technical reference for FTK Imager usage during file system identification. | Covers evidence import, Evidence Tree review, partition selection, file system label identification, screenshot support, and tool limitations. |

---

### Environment, Data Sources, and Tools

The execution focuses on foundational digital forensic image review using FTK Imager.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Digital forensic image review environment |
| **Processed Assets** | Provided forensic disk image files |
| **Execution Platform** | FTK Imager |
| **Primary Tool** | AccessData FTK Imager |
| **Operational Focus** | Identify file systems from imported forensic images by reviewing partition information and Evidence Tree labels |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Primary Evidence Sources** | `carve1.img`, `carve2.img`, `disk1.img` |
| **Evidence Review Technique** | Import each image into FTK Imager as an Image File evidence item |
| **Partition Analysis Technique** | Expand the image in the Evidence Tree and review `Partition 1 [101MB]` |
| **File System Identification Technique** | Identify the file system label displayed beside the volume name in the Evidence Tree |
| **Identified File Systems** | NTFS, FAT32, EXT3 |
| **Operational Workflow Context** | Demonstrates how forensic examiners establish the organizational structure of evidence before deeper artifact analysis |

---

### Intended Use

This execution is intended to demonstrate practical digital forensic fundamentals by documenting how to identify file systems from forensic disk images.

The workflow reflects how forensic examiners and incident responders may begin evidence review by answering:

- What image am I examining?
- What partition exists within the image?
- What file system governs that partition?
- What does that file system suggest about the evidence?
- What should I expect during future analysis?

This process supports later forensic work such as deleted file recovery, timeline analysis, metadata review, operating system artifact analysis, and user activity reconstruction.

---

### Relevance to Security Operations and Digital Forensics

File system identification is relevant to digital forensics because the file system determines how evidence is organized.

A forensic examiner who identifies NTFS can begin thinking in terms of Windows artifacts and NTFS metadata structures. An examiner who identifies FAT32 may consider removable media context and simpler allocation structures. An examiner who identifies EXT3 may begin thinking about Linux artifacts, journaling, and Linux directory structures.

This execution demonstrates how foundational storage knowledge supports:

- digital evidence triage,
- forensic image review,
- incident response preparation,
- deleted file recovery planning,
- operating system context development,
- artifact analysis planning,
- storage behavior interpretation.

Even though this workflow is technically short, the underlying concepts are important. File systems are the layer that make stored data understandable to the operating system and to forensic tools. Without first identifying the file system, later analysis can lack the necessary context to interpret evidence correctly.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational digital forensic methodology, evidence image review, file system identification, and professional documentation aligned with entry-level DFIR and SOC investigation workflows.
