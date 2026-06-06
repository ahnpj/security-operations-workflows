# Metadata Analysis and File Carving Using ExifTool and Scalpel

### Overview

This execution documents the practical use of ExifTool and Scalpel to perform metadata analysis and deleted file recovery from forensic evidence files. The workflow focused on two primary forensic techniques: extracting embedded metadata from files and recovering deleted content through file carving. Metadata analysis was performed against `dummy.pdf` and `picture.jpg` using ExifTool, while Scalpel was used to recover a deleted image from the `carve1.img` disk image. Following recovery, the carved file was validated through MD5 hash generation to help verify its integrity.

The workflow demonstrates that valuable forensic evidence is not limited to files that are visible through normal operating system interfaces. Investigators frequently encounter useful information embedded within file metadata, deleted file remnants, unallocated storage space, and artifacts recovered directly from raw disk data. Together, these techniques reinforce foundational concepts in metadata analysis, file carving, deleted file recovery, evidence validation, artifact recovery, and forensic examination of storage media.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>

> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to see how metadata was extracted, how Scalpel was configured, how the deleted image was carved, and how the recovered file was hashed.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Move to `analyst-notes.md` to understand why metadata matters, how file carving works, why deleted files may still be recoverable, and how this workflow connects to the previous file system identification workflow.

> 👉 **Review tooling and feature usage details**</br>
> See `tool-usage-notes.md` to understand how ExifTool, Scalpel, nano, grep, and md5sum were used during execution.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Digital Forensics and Evidence Analysis  
- **Primary Operational Focus:** Metadata analysis, file carving, deleted file recovery, and forensic artifact validation  
- **Operational Objectives Demonstrated:** Metadata Extraction, EXIF Analysis, PDF Metadata Review, File Carving, Deleted Image Recovery, Hash Validation, Terminal-Based Forensic Workflow Execution  
- **Primary Data Sources:** PDF document metadata, JPEG EXIF metadata, NTFS-formatted disk image, recovered carved image, and MD5 hash output

> **Terminology used:**  
> **Workflows** refer to repeatable digital forensic processes such as metadata extraction, file carving, deleted file recovery, and artifact validation.  
> **Executions** refer to the hands-on analysis performed using ExifTool, Scalpel, md5sum, and Linux terminal commands.  
> **Writeups** document how forensic workflows were performed, how tool outputs were interpreted, and how evidence findings were validated.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how digital forensic workflows are commonly documented.

**`workflow-execution.md`** — **If you want to follow the investigation step by step**</br>
This file contains the hands-on walkthrough showing how ExifTool was used to extract metadata, how Scalpel was configured to carve files, and how the recovered image was hashed.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains the major learning points behind metadata, EXIF data, file carving, deleted file recovery, file signatures, and hash validation.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
This file explains how each tool was used and why specific commands were run.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational digital forensic documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured forensic walkthrough documenting metadata extraction and file carving execution. | Documents terminal navigation, ExifTool metadata extraction, camera model review, Scalpel configuration, image carving, recovered file review, and MD5 hash generation. |
| `images/` | Visual evidence supporting metadata and file carving findings. | Contains screenshots of terminal commands, ExifTool output, Scalpel configuration edits, carving output directories, recovered image files, and md5sum output. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of the workflow objective, forensic relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes recorded during execution. | Documents reasoning around metadata, EXIF data, file carving, file signatures, deleted file recovery, hashing, and connections to file system analysis. |
| `tool-usage-notes.md` | Technical reference for command-line tool usage. | Covers ExifTool, Scalpel, nano, grep, md5sum, command syntax, tool purposes, operational relevance, and common mistakes. |

---

### Environment, Data Sources, and Tools

The execution focuses on terminal-based forensic analysis using metadata extraction and file carving tools.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **Environment Type** | Linux-based digital forensic training environment |
| **Working Directory** | `/home/ubuntu/Desktop/Metadata and File Carving` |
| **Processed Assets** | PDF file, JPEG file, NTFS-formatted disk image, recovered carved image |
| **Execution Platform** | Linux terminal |
| **Primary Tools** | ExifTool, Scalpel, nano, grep, md5sum |
| **Operational Focus** | Extract metadata from files and recover a deleted image from a disk image using file carving techniques |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>

| Area | Details |
|--------|---------|
| **PDF Metadata Source** | `dummy.pdf` |
| **Image Metadata Source** | `picture.jpg` |
| **File Carving Source** | `carve1.img` |
| **Recovered Artifact** | `00000000.jpg` |
| **Metadata Technique** | ExifTool metadata extraction |
| **Filtering Technique** | `grep` filtering for camera-related metadata |
| **Carving Technique** | Scalpel file signature carving |
| **Validation Technique** | MD5 hashing using `md5sum` |

</details>

---

### Intended Use

This execution is intended to demonstrate foundational digital forensic methodology involving metadata analysis and file carving. This process supports later forensic work involving document analysis, image attribution, deleted file recovery, evidence validation, and artifact handling.

<details>
<summary><strong>▶ Investigative Questions Addressed</strong><br>
</summary><br>

- What metadata is embedded in this document?
- Who authored this file?
- What camera captured this image?
- Can deleted image data be recovered from this disk image?
- What is the hash of the recovered artifact?
- How does file carving differ from normal file browsing or file system-based recovery?

</details>

---

### Relevance to Security Operations and Digital Forensics

Metadata analysis and file carving are relevant to digital forensics because they expose evidence that may not be visible through normal file browsing. Metadata analysis can help identify hidden contextual information inside files, such as author names, device models, timestamps, and software details. File carving can recover deleted files or file fragments from raw storage when the file system no longer presents the file as active. This workflow demonstrates that digital evidence may exist in visible content, embedded metadata, file system structures, and raw storage remnants.


<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>

- evidence triage,
- document attribution,
- image source analysis,
- deleted file recovery,
- artifact validation,
- incident response evidence review,
- forensic reporting.

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational digital forensic methodology, metadata analysis, deleted file recovery, command-line forensic tooling, and professional documentation aligned with entry-level DFIR and SOC investigation workflows.
