# Analyst Notes — Data Acquisition Using FTK Imager, ProcDump, and KAPE

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the Data Acquisition workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding why evidence acquisition matters and how memory acquisition, process dumping, disk imaging, and artifact collection support later forensic analysis.

> **Workflow vs Acquisition vs Analysis (Terminology Used Here)**  
> - **Workflows** refer to structured forensic acquisition tasks such as memory capture, process dumping, disk imaging, and artifact collection.  
> - **Acquisition** refers to the process of collecting and preserving evidence from a system.  
> - **Analysis** refers to the later examination of acquired evidence using forensic tools and investigative techniques.

---

### Observations During Execution

One of the biggest realizations during this workflow was understanding that acquisition and analysis are not the same activity.

At first, it was easy to think of tools such as FTK Imager, ProcDump, and KAPE as analysis tools because they produce outputs that investigators later examine. However, this workflow reinforced that these tools were primarily being used to preserve evidence rather than interpret it.

The workflow did not focus on answering investigative questions such as:

- What happened on the system?
- Was malware present?
- Which websites were visited?
- What user activity occurred?

Instead, the workflow focused on preserving evidence so those questions could potentially be answered later.

This changed how I viewed the acquisition process. The goal was not to find answers immediately. The goal was to preserve evidence before evidence changed, disappeared, or became unavailable.

---

### Understanding Why Memory Acquisition Matters

Initially, the FTK Imager memory capture felt like a simple export operation.

The workflow became more meaningful after understanding that RAM contains information that may never exist on disk in the same form.

Memory can contain:

- running processes,
- active network connections,
- loaded DLLs,
- command-line arguments,
- encryption keys,
- credentials,
- malware operating entirely in memory,
- user activity remnants.

Unlike files stored on disk, memory is volatile.

If a system shuts down or reboots, the contents of RAM are typically lost.

This helped explain why memory acquisition is often one of the first tasks performed during incident response.

The memory image created by FTK Imager was not important because it was a file. It was important because it preserved the state of a live system at a specific moment in time.

---

### Understanding the Difference Between Full Memory Acquisition and Process Memory Acquisition

One concept that was initially confusing was why the workflow collected memory twice.

The first acquisition used FTK Imager to collect memory from the entire system.

The second acquisition used ProcDump to collect memory from a single running process.

At first, these seemed redundant.

The distinction became clearer after understanding the scope of each acquisition method.

A simplified comparison looks like:

```text
FTK Imager Memory Acquisition
↓
Entire System RAM

ProcDump Process Acquisition
↓
Single Running Process
```

FTK Imager preserves the broader system state.

ProcDump preserves the state of one selected process.

This helped reinforce that acquisition methods can be broad or targeted depending on investigative goals.

---

### Understanding Why ProcDump Needed a PID

Another learning point involved Process IDs.

The workflow required locating the Calculator process using:

```powershell
Get-Process | findstr -I calc
```

and then identifying the PID before executing ProcDump.

Initially, it was not obvious why the PID mattered.

The PID acts as a unique identifier assigned to a running process.

Multiple applications may be running simultaneously, so ProcDump needs a way to determine exactly which process should be dumped.

Without a PID, ProcDump would not know which process memory should be preserved.

This reinforced that process acquisition requires process identification before evidence collection can occur.

---

### PowerShell as a Forensic Utility

Before this workflow, PowerShell was often viewed primarily as a scripting or administration tool.

The workflow demonstrated that PowerShell can also support forensic acquisition activities.

PowerShell was used to:

- launch applications,
- enumerate running processes,
- identify process IDs,
- navigate directories,
- execute acquisition tools.

This highlighted how common administrative tools can play a role in forensic workflows even when they are not forensic tools themselves.

---

### Understanding Disk Imaging

The FTK Imager disk imaging exercise initially felt similar to copying files.

The conceptual difference became clearer during execution.

Copying files transfers visible files from one location to another.

Disk imaging preserves an entire storage source, including information that may not be visible through normal file browsing.

A forensic image may contain:

- active files,
- deleted files,
- file system metadata,
- partition information,
- unallocated space,
- slack space.

This helped reinforce why forensic imaging is often preferred over ordinary file copying during investigations.

The goal is to preserve as much evidence as possible rather than only preserving currently visible files.

---

### Understanding E01 Images

Another point of confusion involved the E01 format.

Initially, E01 appeared to be just another file extension.

The workflow helped clarify that E01 is a forensic image format designed specifically for evidence acquisition.

Unlike simple file copies, E01 supports:

- metadata storage,
- compression,
- segmentation,
- integrity verification.

This reinforced that forensic image formats are designed to support evidence preservation rather than general file storage.

---

### Understanding Hash Values

The generated hash values initially seemed like additional information produced by FTK Imager.

The importance became clearer after understanding evidence integrity.

A hash functions like a digital fingerprint.

If the image changes, the hash value changes.

This allows investigators to demonstrate that evidence has remained unchanged after acquisition.

The concept can be simplified as:

```text
Original Evidence
↓
Acquisition
↓
Hash Generated
↓
Future Verification
```

If the future hash matches the original hash, confidence increases that the evidence has not been modified.

This helped explain why hashing is a fundamental part of forensic acquisition workflows.

---

### Understanding Why KAPE Can Find Browser Artifacts

One of the biggest conceptual questions during the workflow involved KAPE.

At first, it seemed almost magical that KAPE could retrieve Chrome artifacts simply by selecting Chrome targets and pointing the tool at the system drive.

The important realization was that KAPE does not discover browser artifacts randomly.

Instead, KAPE uses predefined artifact definitions.

These definitions tell KAPE where common forensic artifacts are normally stored.

For Chrome, KAPE knows the standard locations used by Chrome profiles and browser databases.

This means KAPE is not searching the entire drive manually.

Instead, it is following known paths and collecting known evidence sources.

This helped clarify why selecting:

```text
Chrome
Chrome Extensions
```

was enough for KAPE to locate browser-related evidence.

---

### Connecting KAPE to Browser History

A related learning point involved browser history.

Earlier questions during the workflow focused on how browser history could be recovered from a system.

The KAPE output helped answer that question.

Chrome stores information in multiple files rather than one simple text file.

Examples collected during the workflow included:

- History
- Cookies
- Favicons
- Sessions
- Web Data
- Visited Links

These artifacts can later be parsed using forensic tools to reconstruct browser activity.

This reinforced that browser history is usually recovered from artifact databases and supporting files rather than from a single human-readable document.

---

### Remote Acquisition Observations

The workflow also introduced the concept of collecting evidence from a remote host.

Instead of running KAPE locally, the tool was copied to another Windows system through an RDP session.

This demonstrated an important operational concept.

Investigators are not always physically present at the system being examined.

Remote collection methods may allow evidence acquisition without direct access to the endpoint.

This highlighted why remote administration technologies can be important during enterprise investigations and incident response engagements.

---

### Connecting Acquisition to Later Analysis

Another important realization was that every output created during the workflow is intended for later analysis.

Examples include:

```text
Memory Dump
↓
Volatility Analysis

Process Dump
↓
Malware Analysis

Disk Image
↓
Autopsy / EnCase Analysis

Browser Artifacts
↓
Browser Forensics
```

This helped reinforce that acquisition is only one phase of the investigative lifecycle.

The workflow focused on evidence preservation, while future workflows would focus on interpreting that evidence.

---

### Challenges and Confusion Encountered

The most difficult part of the workflow was understanding why different acquisition methods existed.

Initially, it seemed unnecessary to acquire:

- full memory,
- process memory,
- a disk image,
- browser artifacts.

Over time, it became clearer that each method preserves a different category of evidence.

Memory acquisition preserves volatile system state.

Process acquisition preserves a targeted application.

Disk imaging preserves persistent storage.

Artifact collection preserves selected evidence sources quickly.

Understanding those distinctions made the workflow feel much more practical and realistic.

---

### Cross-Workflow Connections

This workflow connects directly to multiple DFIR disciplines.

The acquired evidence could support:

- memory forensics,
- malware analysis,
- browser forensics,
- endpoint investigations,
- user activity reconstruction,
- incident response investigations,
- threat hunting,
- evidence preservation activities.

The workflow also connects to broader concepts involving:

- volatile evidence,
- chain of custody,
- evidence integrity,
- digital evidence handling,
- forensic imaging,
- artifact-based triage.

Understanding acquisition provides the foundation required before any deeper forensic analysis can begin.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

- analyzing the FTK Imager memory dump using Volatility,
- examining the ProcDump output for strings and modules,
- loading the E01 image into Autopsy,
- reviewing Chrome history databases,
- reconstructing browser activity timelines,
- comparing memory and disk artifacts,
- identifying evidence persistence across acquisition sources,
- documenting chain-of-custody procedures,
- validating image hashes using separate tools.

These additions would help bridge the gap between evidence acquisition and evidence analysis.

---

### Summary

This workflow reinforced that evidence acquisition is the foundation of digital forensic investigations.

The key lesson was that investigators cannot analyze evidence that has not first been preserved.

FTK Imager, ProcDump, PowerShell, RDP, and KAPE each supported a different acquisition objective, but all contributed to the same goal: preserving evidence before it changes or disappears.

The workflow also helped clarify the relationship between memory acquisition, process acquisition, disk imaging, browser artifact collection, evidence integrity, and future forensic analysis.

By collecting memory, process dumps, disk images, and browser artifacts, this execution established the evidence foundation needed for later investigative workflows.
