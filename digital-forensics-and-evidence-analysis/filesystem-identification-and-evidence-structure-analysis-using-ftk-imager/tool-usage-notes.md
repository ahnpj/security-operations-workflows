# Tool Usage Notes — File System Identification and Evidence Structure Analysis Using FTK Imager

## Overview

This document summarizes the tool usage, interface observations, evidence review process, and forensic relevance of FTK Imager during the file system identification workflow.

The workflow focused on:

- importing forensic disk images,
- reviewing Evidence Tree structures,
- identifying partitions,
- reading file system labels,
- documenting file system findings,
- connecting FTK Imager observations to forensic analysis concepts.

---

## Primary Examination Tool — FTK Imager

### Purpose

FTK Imager was used as the primary forensic examination tool for reviewing provided image files and identifying the file systems used by those images.

In this workflow, FTK Imager was not used to acquire a new forensic image. The image files were already provided. FTK Imager was used to import and examine those existing images.

### How It Was Used

Each image was imported using:

```text
File → Add Evidence Item → Image File
```

The following image files were reviewed:

- `carve1.img`
- `carve2.img`
- `disk1.img`

After each image was imported, the Evidence Tree was expanded to review:

- the image name,
- the partition,
- the volume label,
- the file system label.

### Operational Relevance

FTK Imager is commonly used in forensic workflows because it allows analysts to inspect disk images without modifying the original evidence. It supports evidence browsing, file previewing, metadata review, export operations, and initial triage.

In this workflow, its primary value was allowing the examiner to quickly identify how each image was structured and which file system governed the partition.

---

## Evidence Import Workflow

### Purpose

The evidence import workflow allowed each image file to be loaded into FTK Imager for review.

### Process Used

The same process was repeated for each image:

1. Open FTK Imager.
2. Select `File`.
3. Select `Add Evidence Item`.
4. Choose `Image File`.
5. Browse to the target image file.
6. Complete the import process.
7. Review the imported image in the Evidence Tree.

### Operational Relevance

Using the same import process across all images helped maintain consistency. In forensic work, repeatable methodology matters because it reduces confusion and supports clear documentation of how each finding was reached.

---

## Evidence Tree Review

### Purpose

The Evidence Tree was the main FTK Imager interface area used to identify file systems.

The Evidence Tree displayed the structure of each imported image. This included the image file, the partition, and the volume name with the file system label in brackets.

### Evidence Tree Findings

For `carve1.img`, FTK Imager displayed:

```text
carve1.img
└─ Partition 1 [101MB]
   └─ Carving1 [NTFS]
```

For `carve2.img`, FTK Imager displayed:

```text
carve2.img
└─ Partition 1 [101MB]
   └─ CARVE2 [FAT32]
```

For `disk1.img`, FTK Imager displayed:

```text
disk1.img
└─ Partition 1 [101MB]
   └─ NONAME [Ext3]
```

### Operational Relevance

The bracketed label beside the volume name was the key observation used to identify the file system.

This is important because the workflow did not require guessing the file system based on file contents. FTK Imager parsed the image and displayed the identified file system directly in the Evidence Tree.

---

## Partition Review

### Purpose

The partition was reviewed because file systems are typically applied to partitions.

A partition is a logical section of a disk. A single disk may contain one or more partitions, and each partition may contain a different file system. In this workflow, each image displayed `Partition 1 [101MB]`, indicating that the relevant storage structure existed inside the first partition.

### How It Was Used

For each imported image, the partition was expanded in the Evidence Tree. Expanding the partition revealed the volume underneath it, including the file system label.

For example, in `carve2.img`, expanding `Partition 1 [101MB]` revealed:

```text
CARVE2 [FAT32]
```

### Operational Relevance

Reviewing the partition helped clarify that the file system was not simply a property of the image filename. It was associated with the partition structure inside the image.

This distinction matters because in real forensic examinations, disks may contain multiple partitions, and different partitions may use different file systems.

---

## File System Identification Method

### Purpose

The purpose of file system identification was to determine how each evidence image organized files, directories, metadata, timestamps, and storage allocation information.

### How It Was Performed

File system identification was performed by reviewing the volume label shown under the partition in FTK Imager's Evidence Tree.

The file system was identified from the bracketed text beside the volume name:

| Image | Evidence Tree Volume Label | Identified File System |
|---|---|---|
| `carve1.img` | `Carving1 [NTFS]` | NTFS |
| `carve2.img` | `CARVE2 [FAT32]` | FAT32 |
| `disk1.img` | `NONAME [Ext3]` | EXT3 |

### Operational Relevance

The file system label provides immediate forensic context.

Identifying NTFS suggests a Windows-style file system environment. Identifying FAT32 may suggest removable or broadly compatible storage. Identifying EXT3 suggests a Linux-style file system environment.

This helps guide future analysis by shaping expectations about metadata, deleted file behavior, operating system artifacts, and available forensic structures.

---

## File List Pane

### Purpose

The File List pane displays files, folders, metadata entries, and other objects associated with the selected evidence item or partition.

### How It Appeared During Execution

When viewing the imported images, the File List pane showed entries associated with the selected item in the Evidence Tree. Depending on the selected object, it could display folders, root entries, file system metadata, unallocated space, slack space, or other structures.

### Operational Relevance

Although the File List pane was visible during the workflow, the primary file system identification came from the Evidence Tree rather than the File List pane.

The File List pane becomes more important during later forensic analysis, such as browsing files, reviewing directories, previewing artifacts, or exporting evidence.

---

## Hex View Pane

### Purpose

The Hex View pane displays raw hexadecimal and ASCII data from the selected evidence object.

### How It Appeared During Execution

The Hex View pane was visible in FTK Imager and showed raw data associated with selected evidence structures.

In the first image, the raw view displayed recognizable NTFS-related text in the ASCII side of the hex display. However, the file system finding was primarily documented from the Evidence Tree label rather than manual hex interpretation.

### Operational Relevance

The Hex View pane can be valuable during deeper forensic analysis because it allows analysts to inspect raw disk structures, file headers, metadata, and binary content.

For this workflow, the Hex View pane provided supporting context but was not the main source used to identify the file system.

---

## File System Findings

The final file system findings were:

| Evidence Image | Identified File System |
|---|---|
| `carve1.img` | NTFS |
| `carve2.img` | FAT32 |
| `disk1.img` | EXT3 |

These findings were based on the file system labels displayed by FTK Imager in the Evidence Tree.

---

## Tool Strengths Observed

FTK Imager was useful because it provided a clear and immediate view of the imported image structure.

Strengths observed during execution included:

- simple evidence import process,
- clear Evidence Tree hierarchy,
- visible partition structure,
- direct file system labels,
- ability to inspect raw data,
- ability to browse image contents without modifying evidence.

The tool made it possible to identify the file system without needing to manually parse boot sectors or file system metadata structures.

---

## Tool Limitations Observed

This workflow only used FTK Imager for initial file system identification. It did not validate the file system using additional tools or perform deeper analysis of file system internals.

Limitations of this execution included:

- no deleted file recovery performed,
- no timeline analysis performed,
- no manual parsing of metadata structures,
- no comparison with other forensic tools,
- no acquisition or hashing workflow documented,
- no examination of file contents beyond file system identification.

These limitations are acceptable for this specific workflow because the objective was file system identification, not full evidence analysis.

---

## Common Mistakes to Avoid

A common mistake would be treating the image filename as the source of the file system finding. The file system should be identified from the structure shown inside FTK Imager, not inferred from the image name.

Another mistake would be assuming the file system applies to the entire physical disk without reviewing partitions. In many cases, file systems are associated with partitions, and a disk can contain multiple partitions.

Another mistake would be assuming that identifying the file system means the examiner has completed the forensic analysis. File system identification is only an early orientation step. It tells the examiner how the evidence is organized so that deeper analysis can be performed correctly.

---

## Suggested Screenshot Documentation

Useful screenshots for this workflow include:

| Screenshot | Purpose |
|---|---|
| FTK Imager Add Evidence Item window | Shows the evidence import process |
| Source Evidence Type selection | Shows that Image File was selected |
| `carve1.img` Evidence Tree view | Shows `Carving1 [NTFS]` |
| `carve2.img` Evidence Tree view | Shows `CARVE2 [FAT32]` |
| `disk1.img` Evidence Tree view | Shows `NONAME [Ext3]` |
| Partition expansion view | Shows why Partition 1 was selected and reviewed |
| Optional Hex View screenshot | Shows raw data view supporting storage-level examination |

Screenshots should support the finding by showing where the file system label appeared in the FTK Imager interface.

---

## Summary

FTK Imager was used to import and review three forensic disk images. The Evidence Tree was the primary interface used to identify file systems from partition and volume labels.

The workflow demonstrated that file system identification is a foundational forensic task. It establishes how evidence is organized before deeper analysis begins.

The tool usage process was straightforward, but the forensic significance was broader: identifying NTFS, FAT32, or EXT3 helps an examiner understand the likely operating system context, metadata structures, deleted file behavior, and future artifact analysis direction.
