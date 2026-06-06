# File System Identification and Evidence Structure Analysis Using FTK Imager

### Overview

This execution documents the use of FTK Imager to identify file systems from acquired forensic disk images. The workflow focused on importing and examining three image files (`carve1.img`, `carve2.img`, and `disk1.img`), reviewing their partition structures, identifying the file systems present, and understanding the significance of those findings from a digital forensics perspective. The analysis determined that `carve1.img` contained an NTFS file system, `carve2.img` contained a FAT32 file system, and `disk1.img` contained an EXT3 file system.

Although the task itself was straightforward, file system identification represents one of the earliest and most important stages of a forensic examination. Before investigators can recover deleted files, interpret metadata, analyze timestamps, examine user artifacts, or reconstruct timelines, they must first understand how data is organized within the evidence source. The workflow also reinforced several foundational storage and forensic concepts, including partitions, clusters, Logical Block Addresses (LBAs), SSD controllers, Flash Translation Layers (FTLs), TRIM, garbage collection, and wear leveling. Together, these concepts help explain the relationship between how file systems organize evidence logically and how storage devices manage data physically behind the scenes.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>
  
> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to see how each image was imported into FTK Imager, how the partition structure was reviewed, and how each file system was identified.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Move to `analyst-notes.md` to understand the key learning points, confusion areas, and storage architecture concepts that became clearer during execution.

> 👉 **Review tooling and feature usage details**</br>
> See `tool-usage-notes.md` to understand how FTK Imager was used to import evidence images, review partitions, and identify file systems from the Evidence Tree.

> 👉 **See what each execution file contains in full detail**</br>
> Review the **Repository Structure & Supporting Documents** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Digital Forensics and Evidence Analysis  
- **Primary Operational Focus:** File system identification, forensic image examination, partition review, and storage organization analysis  
- **Operational Objectives Demonstrated:** Evidence Image Review, Partition Analysis, File System Identification, Digital Storage Fundamentals, Forensic Context Development  
- **Primary Data Sources:** Forensic disk images, partition structures, file system labels, and FTK Imager evidence tree views

> **Terminology used:**  
> **Workflows** refer to repeatable digital forensic processes such as importing evidence images, reviewing partitions, and identifying file systems.  
> **Executions** refer to the hands-on examination performed in FTK Imager against provided forensic image files.  
> **Writeups** document how the examination was performed, how file system findings were identified, and why those findings matter during forensic analysis.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how digital forensic workflows are commonly documented.

**`workflow-execution.md`** — **If you want to follow the investigation step by step**</br>
This file contains the hands-on walkthrough showing how the forensic images were imported and how FTK Imager displayed the file system labels.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains why file system identification matters, what the confusing parts were, and how concepts such as NTFS, FAT32, EXT3, partitions, clusters, LBAs, and SSD behavior relate to the workflow.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
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

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Environment Type** | Digital forensic image review environment |
| **Processed Assets** | Provided forensic disk image files |
| **Execution Platform** | FTK Imager |
| **Primary Tool** | AccessData FTK Imager |
| **Operational Focus** | Identify file systems from imported forensic images by reviewing partition information and Evidence Tree labels |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Primary Evidence Sources** | `carve1.img`, `carve2.img`, `disk1.img` |
| **Evidence Review Technique** | Import each image into FTK Imager as an Image File evidence item |
| **Partition Analysis Technique** | Expand the image in the Evidence Tree and review `Partition 1 [101MB]` |
| **File System Identification Technique** | Identify the file system label displayed beside the volume name in the Evidence Tree |
| **Identified File Systems** | NTFS, FAT32, EXT3 |
| **Operational Workflow Context** | Demonstrates how forensic examiners establish the organizational structure of evidence before deeper artifact analysis |

</details>

---

### Intended Use

This execution is intended to demonstrate practical digital forensic fundamentals by documenting how to identify file systems from forensic disk images. The workflow reflects how forensic examiners and incident responders may begin evidence review. This process supports later forensic work such as deleted file recovery, timeline analysis, metadata review, operating system artifact analysis, and user activity reconstruction.


<details>
<summary><strong>▶ Investigative Questions Addressed</strong><br>
</summary><br>

- What image am I examining?
- What partition exists within the image?
- What file system governs that partition?
- What does that file system suggest about the evidence?
- What should I expect during future analysis?

</details>

---

### Relevance to Security Operations and Digital Forensics

File system identification is relevant to digital forensics because the file system determines how evidence is organized. A forensic examiner who identifies NTFS can begin thinking in terms of Windows artifacts and NTFS metadata structures. An examiner who identifies FAT32 may consider removable media context and simpler allocation structures. An examiner who identifies EXT3 may begin thinking about Linux artifacts, journaling, and Linux directory structures. Even though this workflow is technically short, the underlying concepts are important. File systems are the layer that make stored data understandable to the operating system and to forensic tools. Without first identifying the file system, later analysis can lack the necessary context to interpret evidence correctly.


<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>

- digital evidence triage,
- forensic image review,
- incident response preparation,
- deleted file recovery planning,
- operating system context development,
- artifact analysis planning,
- storage behavior interpretation.

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational digital forensic methodology, evidence image review, file system identification, and professional documentation aligned with entry-level DFIR and SOC investigation workflows.
