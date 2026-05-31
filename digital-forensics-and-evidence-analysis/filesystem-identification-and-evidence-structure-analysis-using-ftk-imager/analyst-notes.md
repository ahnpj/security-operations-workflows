# Analyst Notes — File System Identification and Evidence Structure Analysis Using FTK Imager

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the FTK Imager file system identification workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding why file system identification matters and how the workflow connects to broader digital forensic and storage architecture concepts.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**  
> - **Workflows** refer to structured forensic tasks such as importing image files, reviewing partitions, and identifying file systems.  
> - **Examinations** refer to the hands-on review of forensic disk images using FTK Imager.  
> - **Conceptual Reviews** refer to the interpretation of file systems, partitions, clusters, Logical Block Addresses, SSD behavior, and forensic relevance.

---

### Observations During Execution

One of the biggest realizations during this workflow was that file system identification is not just a technical label shown inside FTK Imager. Initially, it was easy to look at entries such as `[NTFS]`, `[FAT32]`, or `[Ext3]` and treat them as simple answers to record. However, the workflow became more meaningful after understanding that the file system determines how the evidence is organized.

A storage device does not naturally understand files, folders, filenames, timestamps, permissions, or directory structures. Those concepts exist because a file system provides organization. Without a file system, the storage device would only contain raw data without a meaningful structure for an operating system or forensic tool to navigate.

This changed how I viewed the FTK Imager Evidence Tree. The labels displayed beside the volumes were not random technical details. They were identifying the organizational method used by each image:

- `carve1.img` contained `Carving1 [NTFS]`
- `carve2.img` contained `CARVE2 [FAT32]`
- `disk1.img` contained `NONAME [Ext3]`

The Evidence Tree therefore acted as the main source for identifying how each image was structured.

---

### Understanding Why File System Identification Matters

At first, I found myself asking why identifying a file system mattered at all. The task seemed simple because FTK Imager displayed the answer directly in the Evidence Tree. However, file system identification is important because it establishes the context for everything that may happen later in a forensic examination.

Before an examiner can recover deleted files, analyze metadata, interpret timestamps, build a timeline, or review user activity, they need to know how the evidence is organized. NTFS, FAT32, and EXT3 all store and manage data differently. Each file system has different capabilities, metadata structures, recovery implications, and operating system associations.

In simple terms, identifying the file system is like identifying the filing system used inside a records room. If the examiner does not understand the filing system, it becomes harder to know where records are stored, how they are labeled, what supporting metadata exists, or what might remain after something is deleted.

This workflow helped reinforce that file system identification is one of the first steps in evidence orientation. It tells the examiner what kind of structure they are working with before deeper analysis begins.

---

### Understanding Partitions During the Workflow

Another important learning point was understanding why I clicked `Partition 1 [101MB]` inside FTK Imager.

A partition is a logical division of a storage device. A single disk can contain one partition or multiple partitions. Each partition functions like a separate storage area and can be formatted with its own file system. The file system is usually applied to the partition, not simply to the entire disk as one undifferentiated object.

This mattered during the workflow because FTK Imager displayed each image with a partition beneath it. Expanding that partition revealed the volume label and file system type. For example, `carve2.img` showed:

```text
carve2.img
└─ Partition 1 [101MB]
   └─ CARVE2 [FAT32]
```

That structure showed that the image contained a partition, and that partition contained a FAT32-formatted volume. Understanding this helped clarify why the partition needed to be reviewed before identifying the file system.

---

### FAT16 and FAT32 Confusion

FAT16 and FAT32 were initially confusing because the names sound technical and similar. The simplest way to understand them is that both are older Microsoft file systems based on a File Allocation Table.

The File Allocation Table acts like a map or index that tells the operating system which storage areas belong to which files. FAT16 is the older version and was designed for much smaller storage devices. FAT32 is a later version that supports larger partitions and became widely used because it is simple and compatible with many devices.

FAT32 is still commonly found on USB drives, SD cards, cameras, embedded devices, game consoles, and removable media because many operating systems and devices can read it. However, it lacks many advanced features found in NTFS, such as journaling, stronger metadata support, built-in permissions, and modern security features.

The key takeaway was that FAT32 is not “bad” simply because it is older. It is still useful because it is widely compatible. From a forensic perspective, seeing FAT32 may suggest removable media or portable storage context.

---

### NTFS Understanding

NTFS stands for NT File System and is Microsoft's modern Windows file system.

Compared to FAT32, NTFS is more advanced and stores more detailed information about files. It supports journaling, permissions, access control lists, larger volumes, better reliability features, and richer metadata. In a forensic context, this matters because NTFS can provide more artifact and metadata opportunities during analysis.

When FTK Imager showed `Carving1 [NTFS]` for `carve1.img`, that provided immediate context. It suggested that the image was formatted using a Windows-style file system and that later analysis may involve NTFS-specific structures such as the Master File Table, timestamps, deleted file entries, Windows-style directory structures, and possibly Windows operating system artifacts.

This helped clarify that NTFS is not just the answer to the question. It is a clue about the environment and the kind of evidence an examiner may expect.

---

### EXT3 Understanding

EXT3 stands for Third Extended Filesystem and is commonly associated with Linux systems.

The important concept with EXT3 is journaling. Journaling means the file system records changes before fully committing them to disk. This improves reliability and helps the system recover after crashes or unexpected shutdowns.

When FTK Imager showed `NONAME [Ext3]` for `disk1.img`, that suggested a Linux-based file system environment. From a forensic perspective, this changes what an examiner may expect to review later. Instead of Windows artifacts, the examiner may begin thinking about Linux user directories, shell history, system logs, cron jobs, configuration files, and Linux permissions.

This reinforced that file system identification helps orient the examiner before deeper artifact review.

---

### Connecting File Systems to Clusters and Logical Block Addresses

A major conceptual challenge was understanding the relationship between files, clusters, and Logical Block Addresses.

The file system works with allocation units such as clusters. A cluster is a chunk of storage that the file system assigns to files. For example, a file may occupy Cluster 100, Cluster 101, and Cluster 102. The file system keeps track of which clusters belong to which file.

Beneath the file system layer, the operating system communicates with the storage device using Logical Block Addresses, or LBAs. Logical Block Addresses are the logical locations used to read and write data on the storage device.

The relationship can be simplified as:

```text
File
↓
File System
↓
Clusters
↓
Logical Block Addresses
↓
Storage Device
```

This was important because clusters and LBAs are not the same thing. Clusters are file system allocation units. LBAs are logical storage addresses used by the operating system and storage stack.

---

### Connecting File Systems to SSD Behavior

The workflow also connected to a deeper storage concept: the file system is not the same thing as the physical storage device.

For example, NTFS organizes files and directories, but it does not know the exact physical flash pages and blocks where data lives inside an SSD. If the underlying device is an SSD, the SSD controller uses a Flash Translation Layer to map Logical Block Addresses to physical flash locations.

A simplified SSD storage stack looks like:

```text
File
↓
File System
↓
Clusters
↓
Logical Block Addresses
↓
SSD Controller
↓
Flash Translation Layer
↓
Pages and Blocks
↓
Flash Memory Cells
```

This helped clarify why modern storage is layered. The operating system sees files and logical addresses. The file system sees metadata and clusters. The SSD controller sees physical pages and blocks. Each layer hides complexity from the layer above it.

---

### TRIM, Garbage Collection, and Wear Leveling Notes

A major point of confusion was how deleted data behaves on SSDs.

When a user deletes a file, the file system usually marks the file record as deleted and makes the associated clusters available for reuse. On an SSD, the operating system may also send a TRIM command telling the SSD that the Logical Block Addresses associated with that deleted file are no longer needed.

TRIM does not necessarily erase the data immediately. Instead, it tells the SSD that the data can be discarded. The SSD controller then marks the associated physical pages as invalid through its internal mapping system.

Garbage collection is the SSD maintenance process that later cleans up invalid pages. Because SSDs erase data in blocks rather than individual pages, garbage collection may copy valid pages elsewhere, erase an entire block, and return that block to available space.

Wear leveling is another SSD process that spreads writes across the drive so the same flash cells are not worn out too quickly. Wear leveling may move data to different physical locations even though the operating system still sees the same Logical Block Address.

This is important in forensic work because deleted data on SSDs can behave differently from deleted data on traditional hard drives. The file system may show that something was deleted, but the underlying SSD may trim, invalidate, relocate, or erase the physical data through its own internal processes.

---

### Challenges and Confusion Encountered

The most difficult part of the workflow was not clicking through FTK Imager. The difficult part was understanding why the task mattered.

The hands-on action was straightforward: import an image, expand the Evidence Tree, review the partition, and read the file system label. The confusing part was understanding the meaning behind labels such as NTFS, FAT32, and EXT3.

Another challenge was separating layers that initially seemed like the same thing. At first, it was easy to confuse the file system with the SSD controller, the Flash Translation Layer, partitions, clusters, and physical storage blocks. Over time, the structure became clearer:

- The file system organizes files.
- A partition is a logical section of a disk.
- Clusters are file system allocation units.
- Logical Block Addresses are how the operating system talks to storage.
- The SSD controller manages physical flash behavior.
- The Flash Translation Layer maps logical addresses to physical pages.
- TRIM, garbage collection, and wear leveling manage SSD data lifecycle behavior.

This helped turn the workflow from a memorization task into a real conceptual foundation for later digital forensics work.

---

### Cross-Workflow Connections

This workflow connects directly to other areas of digital forensics and security operations.

File system identification supports:

- deleted file recovery,
- file carving,
- timeline analysis,
- metadata review,
- disk image triage,
- incident response evidence handling,
- user activity reconstruction,
- malware persistence investigation,
- removable media analysis.

It also connects to broader cybersecurity concepts involving data sanitization, residual data, slack space, deleted file recovery, and SSD behavior.

Understanding file systems provides the foundation needed to interpret evidence correctly in later workflows involving endpoint triage, malware analysis, incident response, and forensic artifact collection.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by performing deeper analysis after identifying the file system.

Potential expansions include:

- comparing deleted file recovery behavior across NTFS, FAT32, and EXT3,
- examining NTFS Master File Table records,
- reviewing FAT32 allocation structures,
- analyzing EXT3 journaling behavior,
- recovering deleted files from each image,
- comparing file slack and unallocated space across file systems,
- documenting timestamp differences across file systems,
- using additional forensic tools such as Autopsy or The Sleuth Kit,
- validating file system findings with command-line tools,
- comparing HDD and SSD forensic recovery behavior.

These additions would help bridge the gap between file system identification and deeper evidence recovery workflows.

---

### Summary

This workflow reinforced that file system identification is a foundational digital forensic task.

The key lesson was that FTK Imager does not merely show a file system label for convenience. That label tells the examiner how the evidence is organized and what kinds of artifacts may be available during later analysis.

The workflow also helped clarify the layered relationship between files, file systems, partitions, clusters, Logical Block Addresses, SSD controllers, Flash Translation Layers, TRIM, garbage collection, and wear leveling.

By identifying NTFS, FAT32, and EXT3 from the provided forensic images, this execution established the organizational context needed for future forensic analysis.
