# Digital Forensics

(`digital-forensics-and-evidence-analysis/README.md`)

This folder contains workflow executions where the **primary focus is examining digital evidence, analyzing storage media, understanding file systems, reviewing forensic artifacts, and applying foundational digital forensic methodologies to acquired evidence sources**. These workflows simulate how digital forensic analysts, DFIR practitioners, SOC analysts, and incident responders interact with storage devices, forensic images, operating system artifacts, and evidence repositories to understand how data is stored, recovered, and interpreted during investigations.

Rather than presenting digital forensics as a collection of theoretical concepts, each folder documents practical execution of specific forensic workflows, including how evidence was examined, how forensic conclusions were reached, how storage structures were interpreted, and how investigative context was established. The emphasis is placed on analyst reasoning, evidence interpretation, storage architecture understanding, and repeatable forensic methodology similar to what would be documented within internal DFIR procedures, forensic examination notes, or evidence review runbooks.

Each folder uses a task-first naming convention followed by the primary operational focus (for example, `filesystem-identification-using-ftk-imager`). This allows quick identification of the workflow objective while documentation itself emphasizes evidence examination, forensic reasoning, and investigative methodology rather than tool features alone.

> 👉 **Each folder represents one complete workflow execution**</br>
Every subfolder here is a **fully self-contained digital forensic execution scenario** documenting how evidence was reviewed, how findings were identified, how conclusions were reached, and how the workflow contributes to broader forensic understanding.

> 👉 **Follow the workflow execution first**</br>
Begin with `workflow-execution.md` inside a workflow folder to see how evidence was examined step by step, what observations were made, and how forensic conclusions were reached.

> 👉 **See what files exist in each workflow folder**</br>
For a complete breakdown of every standard file included in a workflow execution — including what each document contains, how it supports forensic analysis workflows, and how the documentation is structured — see the **Workflow Documentation Structure** section below.

> 👉 **See which workflow executions currently exist in this category**</br>
For a list of all digital forensics executions currently included in this folder, along with a description of what each workflow demonstrates and which operational skills it focuses on, see the **What's in This Folder** section below.

> **Terminology used:**</br>
> **Workflows** refer to operational digital forensic tasks such as file system identification, evidence review, artifact analysis, timeline reconstruction, or storage media examination.</br>
> **Executions** refer to the hands-on examination and analysis performed using forensic tools and acquired evidence.</br>
> **Writeups** document how evidence was examined, how findings were interpreted, and how forensic conclusions were reached.

---

### What's in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete digital forensic examination scenario**, including execution walkthroughs, analyst reasoning, tooling references, and forensic context.

Current workflow executions include:

- **File System Identification And Evidence Structure Analysis Using FTK Imager**</br>
  (`filesystem-identification-and-evidence-structure-analysis-using-ftk-imager`)</br>
  Focuses on using FTK Imager to import forensic disk images, review partition structures, and identify underlying file systems from acquired evidence. Demonstrates foundational digital forensic concepts including partition analysis, file system identification, evidence organization, and storage architecture interpretation. Covers practical examination of NTFS (NT File System), FAT32 (File Allocation Table 32), and EXT3 (Third Extended Filesystem) volumes while reinforcing the relationship between file systems, partitions, clusters, Logical Block Addresses (LBAs), SSD controllers, Flash Translation Layers (FTLs), TRIM operations, garbage collection, and wear leveling. Emphasizes how file system identification establishes critical investigative context before performing deleted file recovery, metadata analysis, artifact examination, or timeline reconstruction.

- **Metadata Analysis and File Carving Using ExifTool and Scalpel**</br>
  (`metadata-analysis-and-file-carving-using-exiftool-and-scalpel`)</br>
  Focuses on extracting and analyzing metadata from PDF and JPEG files, identifying document authorship and image capture device information, and recovering deleted image artifacts through file carving techniques. Covers practical examination of PDF metadata, EXIF (Exchangeable Image File Format) metadata, file signatures, deleted file recovery, and evidence validation using MD5 hashing. Reinforces foundational digital forensic concepts including metadata analysis, file attribution, file carving methodology, file system-independent recovery techniques, unallocated data recovery, forensic artifact validation, and the relationship between deleted files, raw storage data, and recoverable evidence. Demonstrates the use of ExifTool for metadata extraction, Scalpel for signature-based file recovery, and command-line forensic workflows for evidence examination and validation.

- **Hash Analysis and File Integrity Validation**</br>
  (`hash-analysis-and-file-integrity-validation`)</br>
  Focuses on generating, comparing, and validating cryptographic hash values using common Linux and Windows utilities. Demonstrates foundational digital forensic concepts including evidence integrity verification, file fingerprinting, hash comparison, artifact identification, and algorithm analysis. Covers practical use of MD5 (Message Digest Algorithm 5), SHA1 (Secure Hash Algorithm 1), and SHA256 (Secure Hash Algorithm 256-bit) hashing for both text strings and file-based artifacts while reinforcing the distinction between hashing and encryption. Explores hash lookup methodologies, evidence validation workflows, chain-of-custody considerations, and the operational role of hashing within forensic acquisitions, malware investigations, incident response activities, and threat intelligence sharing. Emphasizes how cryptographic hashes serve as repeatable digital fingerprints that support artifact correlation, forensic verification, and evidence preservation throughout the investigative lifecycle.

- **Data Acquisition Using FTK Imager, ProcDump, and KAPE**</br>
  (`data-acquisition-using-ftk-imager-procdump-and-kape`)</br>
  Focuses on acquiring and preserving digital evidence from live and remote systems using FTK Imager, ProcDump, PowerShell, Remote Desktop Protocol (RDP), and KAPE. Covers practical acquisition of volatile memory, process-specific memory, forensic disk images, and browser-related artifacts while reinforcing foundational digital forensic concepts including evidence preservation, volatile versus persistent data, process acquisition, forensic imaging methodologies, hash-based integrity verification, remote evidence collection, and targeted artifact acquisition. Demonstrates how investigators preserve evidence prior to analysis, highlights the operational differences between full memory acquisition, process dumping, disk imaging, and artifact collection, and emphasizes the role of acquisition workflows in supporting later memory forensics, malware analysis, browser forensics, incident response investigations, and digital evidence validation.

- **Windows Artifact Analysis Using WFA, PECmd, and JumpList Explorer**</br>
  (`windows-artifact-analysis-using-wfa-pecmd-and-jumplistexplorer`)</br>
  Focuses on analyzing Windows forensic artifacts to identify suspicious files, determine file origins, review execution-related evidence, and reconstruct user activity. Demonstrates practical examination of Windows Shortcut (.LNK) files, Prefetch (.pf) files, and Jump List artifacts using Windows File Analyzer (WFA), PECmd, and JumpList Explorer. Covers identification of suspicious PowerShell-related files, archive relationship analysis, application association discovery, execution evidence review, browser activity identification, and artifact correlation. Reinforces foundational DFIR concepts including file access versus execution, application attribution, user activity reconstruction, execution artifact analysis, browser artifact interpretation, and multi-artifact evidence correlation used throughout digital forensic and incident response investigations.

- **Browser History Analysis Using Browser History Viewer**</br>
(`browser-history-analysis-with-browser-history-viewer`)</br>
Focuses on examining browser artifacts to reconstruct user activity, identify downloaded files, analyze cached web content, and investigate browser-based evidence. Demonstrates practical analysis of Website History records, Cached Images, browser profiles, keyword filtering, timeline reconstruction, download attribution, and source identification using Browser History Viewer (BHV). Covers identification of browser usage, social media activity analysis, cached content examination, downloaded file investigation, remote URL identification, delivery source attribution, and browser artifact correlation. Reinforces foundational DFIR concepts including browser history analysis, browser cache interpretation, local versus remote artifact differentiation, user activity reconstruction, timeline analysis, browser-based evidence review, and multi-artifact evidence correlation used throughout digital forensic and incident response investigations.

- **Recycle Bin Analysis Using RBCmd and CSVQuickViewer**</br>
  (`recycle-bin-analysis-using-rbcmd-and-csvquickviewer`)</br>
  Focuses on analyzing Windows Recycle Bin artifacts to identify deleted files, determine user ownership, review deletion timestamps, and reconstruct deleted-file activity. Demonstrates practical examination of Recycle Bin metadata, Security Identifier (SID) attribution, original file path analysis, file size correlation, and deleted-file investigation using RBCmd and CSVQuickViewer. Covers identification of notable deleted files, user-specific deletion activity, metadata interpretation, deletion timestamp analysis, and evidence correlation across multiple artifact fields. Reinforces foundational DFIR concepts including deleted-file analysis, user attribution, metadata examination, artifact persistence, evidence interpretation, and the distinction between file deletion and evidence removal while highlighting how structured artifact parsing supports forensic investigations and user activity reconstruction.

- **Memory Forensics and Process Analysis Using Volatility**</br>
(`memory-forensics-and-process-analysis-using-volatility`)</br>
Focuses on analyzing Windows memory images to identify operating system metadata, enumerate active processes, investigate process relationships, review process privileges, and examine command-line execution artifacts using Volatility Workbench and the Volatility Framework. Demonstrates practical examination of volatile memory evidence through memory image loading, process enumeration, Windows operating system identification, process tree analysis, privilege attribution, PID-based investigation, and command-line artifact recovery. Covers identification of the parent process of PowerShell, privilege attribute analysis, process-focused memory examination, execution context review, and memory artifact correlation across multiple Volatility plugins. Reinforces foundational DFIR concepts including volatile versus persistent evidence, process lineage analysis, parent-child process relationships, process capability interpretation, command-line artifact analysis, memory artifact interpretation, evidence correlation, and host activity reconstruction. Highlights how operating system metadata, process listings, process trees, privileges, and command-line artifacts can be combined to support forensic investigations and reconstruct activity occurring at the time memory was acquired.

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
