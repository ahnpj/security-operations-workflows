# Digital Forensics

(`digital-forensics-and-evidence-analysis/README.md`)

This folder contains workflow executions where the **primary focus is examining digital evidence, analyzing storage media, understanding file systems, reviewing forensic artifacts, and applying foundational digital forensic methodologies to acquired evidence sources**. These workflows simulate how digital forensic analysts, DFIR practitioners, SOC analysts, and incident responders interact with storage devices, forensic images, operating system artifacts, and evidence repositories to understand how data is stored, recovered, and interpreted during investigations.

Rather than presenting digital forensics as a collection of theoretical concepts, each folder documents practical execution of specific forensic workflows, including how evidence was examined, how forensic conclusions were reached, how storage structures were interpreted, and how investigative context was established. The emphasis is placed on analyst reasoning, evidence interpretation, storage architecture understanding, and repeatable forensic methodology similar to what would be documented within internal DFIR procedures, forensic examination notes, or evidence review runbooks.

Each folder uses a task-first naming convention followed by the primary operational focus (for example, `filesystem-identification-using-ftk-imager`). This allows quick identification of the workflow objective while documentation itself emphasizes evidence examination, forensic reasoning, and investigative methodology rather than tool features alone.

> 👉 **Each folder represents one complete workflow execution**
Every subfolder here is a **fully self-contained digital forensic execution scenario** documenting how evidence was reviewed, how findings were identified, how conclusions were reached, and how the workflow contributes to broader forensic understanding.

> 👉 **Follow the workflow execution first**
Begin with `workflow-execution.md` inside a workflow folder to see how evidence was examined step by step, what observations were made, and how forensic conclusions were reached.

> 👉 **See what files exist in each workflow folder**
For a complete breakdown of every standard file included in a workflow execution — including what each document contains, how it supports forensic analysis workflows, and how the documentation is structured — see the **Workflow Documentation Structure** section below.

> 👉 **See which workflow executions currently exist in this category**
For a list of all digital forensics executions currently included in this folder, along with a description of what each workflow demonstrates and which operational skills it focuses on, see the **What's in This Folder** section below.

> **Terminology used:**
> **Workflows** refer to operational digital forensic tasks such as file system identification, evidence review, artifact analysis, timeline reconstruction, or storage media examination.
> **Executions** refer to the hands-on examination and analysis performed using forensic tools and acquired evidence.
> **Writeups** document how evidence was examined, how findings were interpreted, and how forensic conclusions were reached.

---

### What's in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete digital forensic examination scenario**, including execution walkthroughs, analyst reasoning, tooling references, and forensic context.

Current workflow executions include:

- **File System Identification And Evidence Structure Analysis Using FTK Imager**
  (`filesystem-identification-and-evidence-structure-analysis-using-ftk-imager`)
  Focuses on using FTK Imager to import forensic disk images, review partition structures, and identify underlying file systems from acquired evidence. Demonstrates foundational digital forensic concepts including partition analysis, file system identification, evidence organization, and storage architecture interpretation. Covers practical examination of NTFS (NT File System), FAT32 (File Allocation Table 32), and EXT3 (Third Extended Filesystem) volumes while reinforcing the relationship between file systems, partitions, clusters, Logical Block Addresses (LBAs), SSD controllers, Flash Translation Layers (FTLs), TRIM operations, garbage collection, and wear leveling. Emphasizes how file system identification establishes critical investigative context before performing deleted file recovery, metadata analysis, artifact examination, or timeline reconstruction.

Additional workflow executions will be added as digital forensic methodologies, artifact analysis techniques, storage examination workflows, evidence recovery procedures, and DFIR-focused investigations expand.

---

### Workflow Documentation Structure

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|--------------------|
| **`workflow-execution.md`** | Operational forensic walkthrough | Step-by-step evidence examination workflow documenting how evidence was reviewed, interpreted, and analyzed |
| **`README.md`** | Workflow context and operational objective | Describes the forensic objective, workflow scope, investigative context, and relevance to DFIR operations |
| **`analyst-notes.md`** | Analytical reasoning and forensic observations | Documents analyst thinking, conceptual learning, storage architecture notes, and lessons learned |
| **`tool-usage-notes.md`** | Tooling reference and examination methodology | Explains how forensic tools were used, where findings were identified, and how evidence was interpreted |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains screenshots supporting findings and forensic conclusions |

---

### Ongoing Development

Workflows in this category are continuously expanded as new digital forensic methodologies, storage technologies, artifact analysis techniques, and evidence examination procedures are explored.

The focus is on building a growing library of reusable digital forensic workflows that reinforce both practical execution skills and conceptual understanding of how digital evidence is stored, organized, recovered, and interpreted during investigations.
