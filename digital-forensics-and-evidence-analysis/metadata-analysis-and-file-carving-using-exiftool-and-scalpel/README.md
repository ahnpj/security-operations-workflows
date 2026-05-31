# Metadata Analysis and File Carving Using ExifTool and Scalpel

- **Category:** Digital Forensics and Evidence Analysis  
- **Primary Operational Focus:** Metadata analysis, file carving, deleted file recovery, and forensic artifact validation  
- **Operational Objectives Demonstrated:** Metadata Extraction, EXIF Analysis, PDF Metadata Review, File Carving, Deleted Image Recovery, Hash Validation, Terminal-Based Forensic Workflow Execution  
- **Primary Data Sources:** PDF document metadata, JPEG EXIF metadata, NTFS-formatted disk image, recovered carved image, and MD5 hash output

> **Terminology used:**  
> **Workflows** refer to repeatable digital forensic processes such as metadata extraction, file carving, deleted file recovery, and artifact validation.  
> **Executions** refer to the hands-on analysis performed using ExifTool, Scalpel, md5sum, and Linux terminal commands.  
> **Writeups** document how forensic workflows were performed, how tool outputs were interpreted, and how evidence findings were validated.

---

### Overview

This execution documents the practical use of ExifTool and Scalpel to perform metadata analysis and deleted file recovery from provided forensic evidence files.

The workflow includes two primary forensic techniques:

- metadata extraction from files,
- file carving from a disk image.

The metadata analysis portion focuses on using ExifTool to extract embedded metadata from:

- `dummy.pdf`
- `picture.jpg`

The file carving portion focuses on using Scalpel to recover a deleted image from:

- `carve1.img`

After the deleted image is carved, the recovered file is validated by generating its MD5 hash using `md5sum`.

This execution is designed to demonstrate that forensic evidence is not limited to files visible through normal browsing. Useful evidence may exist inside file metadata, deleted file remnants, unallocated storage regions, or recovered artifacts carved from raw disk data.

> 👉 **Follow the execution walkthrough first**  
Begin with `workflow-execution.md` inside this folder to see how metadata was extracted, how Scalpel was configured, how the deleted image was carved, and how the recovered file was hashed.

> 👉 **Review analytical reasoning and conceptual notes**  
Move to `analyst-notes.md` to understand why metadata matters, how file carving works, why deleted files may still be recoverable, and how this workflow connects to the previous file system identification workflow.

> 👉 **Review tooling and command usage details**  
See `tool-usage-notes.md` to understand how ExifTool, Scalpel, nano, grep, and md5sum were used during execution.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how digital forensic workflows are commonly documented.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

This file contains the hands-on walkthrough showing how ExifTool was used to extract metadata, how Scalpel was configured to carve files, and how the recovered image was hashed.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major learning points behind metadata, EXIF data, file carving, deleted file recovery, file signatures, and hash validation.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

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

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Linux-based digital forensic training environment |
| **Working Directory** | `/home/ubuntu/Desktop/Metadata and File Carving` |
| **Processed Assets** | PDF file, JPEG file, NTFS-formatted disk image, recovered carved image |
| **Execution Platform** | Linux terminal |
| **Primary Tools** | ExifTool, Scalpel, nano, grep, md5sum |
| **Operational Focus** | Extract metadata from files and recover a deleted image from a disk image using file carving techniques |

#### Data Sources, Evidence, and Analysis Techniques

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

---

### Intended Use

This execution is intended to demonstrate foundational digital forensic methodology involving metadata analysis and file carving.

The workflow reflects how analysts may answer questions such as:

- What metadata is embedded in this document?
- Who authored this file?
- What camera captured this image?
- Can deleted image data be recovered from this disk image?
- What is the hash of the recovered artifact?
- How does file carving differ from normal file browsing or file system-based recovery?

This process supports later forensic work involving document analysis, image attribution, deleted file recovery, evidence validation, and artifact handling.

---

### Relevance to Security Operations and Digital Forensics

Metadata analysis and file carving are relevant to digital forensics because they expose evidence that may not be visible through normal file browsing.

Metadata analysis can help identify hidden contextual information inside files, such as author names, device models, timestamps, and software details.

File carving can recover deleted files or file fragments from raw storage when the file system no longer presents the file as active.

Together, these methods support:

- evidence triage,
- document attribution,
- image source analysis,
- deleted file recovery,
- artifact validation,
- incident response evidence review,
- forensic reporting.

This workflow demonstrates that digital evidence may exist in visible content, embedded metadata, file system structures, and raw storage remnants.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational digital forensic methodology, metadata analysis, deleted file recovery, command-line forensic tooling, and professional documentation aligned with entry-level DFIR and SOC investigation workflows.
