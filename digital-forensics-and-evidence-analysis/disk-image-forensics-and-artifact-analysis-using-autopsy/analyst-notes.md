# Analyst Notes — Disk Image Forensics and Artifact Analysis Using Autopsy

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the disk image forensics and artifact analysis workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding how disk artifacts work, what evidence they preserve, and how multiple artifact sources can be correlated during an investigation.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**
> - **Workflows** refer to structured forensic tasks such as disk image analysis, operating system identification, browser artifact review, Recent Documents analysis, filesystem examination, and account analysis.
> - **Examinations** refer to the hands-on review of disk artifacts using Autopsy.
> - **Conceptual Reviews** refer to the interpretation of disk artifacts, user activity evidence, filesystem metadata, account records, and evidence correlation.

---

### Observations During Execution

One of the biggest realizations during this workflow was how much information can be recovered from a disk image without ever interacting with the original system.

Before completing this workflow, it was easy to think of a disk image as simply a copy of a hard drive.

However, the investigation demonstrated that a forensic image preserves multiple categories of evidence simultaneously, including:

- operating system metadata,
- browser download records,
- hostname information,
- Recent Documents artifacts,
- filesystem metadata,
- account activity records.

The workflow showed that a single forensic image can contain evidence from many different sources that later need to be correlated together.

---

### Understanding Forensic Disk Images

One of the earliest concepts reinforced during this workflow was the purpose of a forensic disk image.

At first glance, a disk image can seem similar to a backup file or a copy of a drive.

However, a forensic image is intended to preserve evidence in a way that allows analysis to occur without modifying the original device.

Unlike a live system, the image provides a preserved snapshot that can be examined repeatedly.

Examples of evidence recovered from disk images may include:

```text
Operating System Metadata
Browser Artifacts
Downloaded Files
Recent Documents
Filesystem Metadata
Account Records
```

This reinforced an important forensic concept:

```text
Live System
        ≠
Forensic Disk Image
```

Both may contain similar information, but a forensic image allows investigation without altering the original evidence source.

---

### Understanding Autopsy

Prior to this workflow, it was easy to think of Autopsy as simply a file browser.

However, the workflow demonstrated that Autopsy is much more than filesystem navigation.

Autopsy can:

- ingest evidence,
- parse artifacts,
- organize findings,
- extract metadata,
- present evidence in structured categories.

This became especially clear when reviewing artifact categories such as:

```text
Operating System Information
Web Downloads
Recent Documents
OS Accounts
```

Many of these artifacts would be significantly more difficult to locate manually without automated parsing.

---

### Understanding Case Creation

The case creation phase initially appeared administrative rather than investigative.

The task simply involved creating a case and selecting a base directory.

However, this phase reinforced an important lesson.

Forensic work must be organized and repeatable.

The case structure provides a location to store:

- evidence references,
- ingest results,
- extracted artifacts,
- analysis output.

This reinforced another investigative principle:

```text
Evidence Analysis
        ≠
Evidence Organization
```

Both are necessary parts of a forensic workflow.

---

### Understanding Ingest Modules

One concept that became clearer during execution was the purpose of ingest modules.

Initially, it seemed like ingest modules simply controlled what Autopsy processed.

However, the workflow demonstrated that ingest modules determine what artifacts are extracted and organized for review.

The selected module helped identify:

- operating system information,
- hostname data,
- browser downloads,
- Recent Documents,
- account-related artifacts.

This reinforced an important lesson:

```text
Disk Image
        ≠
Parsed Artifacts
```

The image contains the evidence.

The ingest process helps make that evidence easier to locate and interpret.

---

### Understanding Operating System Information

Initially, identifying the operating system appeared to be a simple question.

The workflow only required determining:

```text
Windows 8.1 Pro
```

However, the exercise highlighted why system metadata matters.

The operating system establishes context for every artifact examined afterward.

Without knowing the operating system, it becomes more difficult to interpret:

- filesystem locations,
- application paths,
- account structures,
- browser artifacts,
- user activity evidence.

This reinforced the idea that context often matters as much as the evidence itself.

---

### Understanding Hostnames

The hostname initially seemed less important than other artifacts.

However, the workflow demonstrated why hostnames matter during investigations.

The hostname:

```text
WIN-BK336TFMHLL
```

helped identify the specific system associated with the disk image.

This reinforced another distinction:

```text
Operating System
        ≠
System Identity
```

The operating system describes the platform.

The hostname helps identify the specific device.

---

### Understanding Web Download Artifacts

The download analysis phase introduced one of the most useful user activity artifact categories.

Initially, it seemed that download records only identified filenames.

However, the workflow demonstrated that download artifacts often preserve:

- filenames,
- timestamps,
- source URLs,
- browser-related metadata.

For example:

```text
ALL COUPONS.rar
```

was associated with a specific download event.

This reinforced another investigative principle:

```text
Downloaded File
        ≠
Download Source
```

The filename identifies what was obtained.

The URL helps identify where it came from.

---

### Understanding URLs as Evidence

One important realization involved the difference between a file and its source.

The workflow recovered:

```text
http://download1346.mediafire.com/.../Coupons.zip
```

The URL itself is evidence.

It may support:

- threat intelligence enrichment,
- browser history review,
- proxy log correlation,
- download source identification.

This reinforced another distinction:

```text
File Present
        ≠
Origin Known
```

The URL helps bridge that gap.

---

### Understanding Recent Documents

The Recent Documents phase introduced an artifact source that initially seemed deceptively simple.

At first glance, Recent Documents appeared to be nothing more than a list of recently opened files.

However, the workflow demonstrated that these artifacts can preserve valuable evidence about user activity.

Recent Documents helped identify:

```text
Pier.jpg
```

and the associated path.

This reinforced another lesson.

Evidence of file access can sometimes remain available even when the original file is not immediately being examined.

---

### Understanding LNK Files

One confusion point involved shortcut files.

Initially, it was tempting to think that:

```text
Pier.lnk
```

and

```text
Pier.jpg
```

were effectively the same thing.

The workflow demonstrated that they are different.

The shortcut references the target file and may preserve metadata such as:

- original path,
- timestamps,
- volume information,
- file details.

This reinforced an important concept:

```text
Shortcut File
        ≠
Target File
```

The shortcut can provide evidence about the target without containing the target itself.

---

### Understanding Virtual Filesystem Navigation

Another learning point involved the virtual filesystem.

Initially, it was easy to think of artifact views as the primary source of evidence.

However, the workflow demonstrated that analysts often need to examine the filesystem directly.

The virtual filesystem allowed navigation through:

```text
vol2
Program Files
GIMP 2
```

This reinforced another distinction:

```text
Parsed Artifact
        ≠
Filesystem Evidence
```

Both perspectives are valuable during an investigation.

---

### Understanding Volumes and Partitions

One area of confusion involved the volume structure.

Entries such as:

```text
vol1
vol2
vol3
```

may initially appear arbitrary.

However, the workflow demonstrated that each volume can represent a different area of the disk image.

This reinforced another lesson:

```text
Disk Image
        ≠
Single Filesystem
```

A single image may contain multiple partitions, volumes, and storage structures.

---

### Understanding Directory Metadata

The filesystem phase also introduced the idea that directories contain evidence beyond their names.

The workflow required identifying the size of:

```text
Program Files\GIMP 2\lib
```

This reinforced that metadata itself can be evidence.

Examples include:

- file size,
- folder size,
- timestamps,
- paths,
- ownership information.

This introduced another distinction:

```text
Directory Name
        ≠
Directory Metadata
```

Both may have investigative value.

---

### Understanding OS Accounts

The OS Account phase introduced account-level evidence.

Initially, the timestamp associated with the Administrator account seemed like a simple value.

However, the workflow demonstrated how account artifacts can support:

- timeline development,
- user attribution,
- account activity analysis.

For example:

```text
2013-08-22 14:47:09 UTC
```

provided context about account activity on the system.

This reinforced another lesson:

```text
User Exists
        ≠
User Activity Known
```

Account metadata helps bridge that gap.

---

### Understanding Evidence Correlation

The biggest lesson from the workflow involved understanding how disk artifacts work together.

No single Autopsy artifact category answered every investigative question.

Instead:

- Operating System Information identified the platform.
- Hostname artifacts identified the device.
- Web Downloads identified downloaded content.
- URLs identified download sources.
- Recent Documents identified file access.
- Filesystem navigation validated structure and metadata.
- OS Accounts identified account activity.

Each artifact contributed one piece of the overall investigation.

This reinforced that forensic investigations rely on correlation rather than isolated findings.

---

### Challenges and Confusion Encountered

The most confusing portions of the workflow included:

- understanding the difference between a live system and a forensic image,
- understanding how ingest modules affect artifact extraction,
- distinguishing filenames from source URLs,
- understanding what LNK files actually represent,
- distinguishing artifact views from filesystem navigation,
- understanding why hostname information matters,
- interpreting account timestamps without overestimating their significance,
- understanding how multiple disk artifacts relate to one another.

These confusion points ultimately became useful learning opportunities because they required deeper understanding of what disk artifacts can and cannot prove.

---

### Cross-Workflow Connections

This workflow connects to several other digital forensic and security operations workflows.

It connects to incident response because disk artifacts often support post-incident investigations.

It connects to browser forensics because download records and URLs help reconstruct internet activity.

It connects to user activity reconstruction because Recent Documents and account artifacts preserve evidence of user behavior.

It connects to endpoint investigations because operating system, filesystem, and account artifacts provide host-level context.

It connects to digital forensics because forensic images represent a primary evidence source during investigations.

It connects to evidence correlation because browser, filesystem, operating system, and account artifacts often need to be analyzed together.

---

### Summary

This workflow reinforced that disk evidence exists in multiple layers.

Operating system artifacts helped establish system context.

Hostname artifacts helped identify the device.

Web Downloads helped identify downloaded files and their origins.

Recent Documents helped reveal file access activity.

Filesystem navigation helped validate evidence and examine metadata.

OS Account artifacts helped provide account activity context.

Together, these artifacts demonstrated how investigators use multiple disk artifact sources to reconstruct activity and better understand host behavior.

Perhaps the most important lesson was that disk artifacts rarely provide complete answers individually. Effective investigations require understanding what each artifact reveals, what it does not reveal, and how multiple findings can be combined into a coherent investigative narrative.
