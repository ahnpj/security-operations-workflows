# Tool Usage Notes — Disk Image Forensics and Artifact Analysis Using Autopsy

## Overview

This document summarizes the tools, views, artifact categories, workflows, and operational relevance of the utilities used during the disk image forensics and artifact analysis workflow.

The workflow focused on:

- creating a new Autopsy case,
- adding a forensic disk image as a data source,
- selecting and running ingest modules,
- identifying operating system information,
- identifying the system hostname,
- reviewing web download artifacts,
- recovering download source URLs,
- analyzing Recent Documents and Windows shortcut artifacts,
- navigating the virtual filesystem,
- inspecting directory metadata,
- reviewing local operating system account artifacts,
- correlating disk-based evidence from multiple artifact sources.

---

## Primary Disk Analysis Tool — Autopsy

### Purpose

Autopsy was used to analyze a forensic disk image through a graphical digital forensics interface.

Autopsy allows analysts to create cases, add evidence sources, process disk images, run ingest modules, review parsed artifacts, browse filesystems, inspect metadata, and correlate evidence recovered from storage media.

Disk artifacts can contain evidence of operating system configuration, hostname information, browser activity, downloaded files, source URLs, recently accessed documents, shortcut metadata, filesystem structure, installed program directories, local user accounts, and account activity timestamps.

### How It Was Used

Autopsy was opened from the forensic analysis environment and used to create a new case. The provided disk image was then added as the primary evidence source:

```text
Craig Tucker Desktop.E01
```

Autopsy was then used to process the disk image with the selected ingest module and review the resulting artifacts.

The workflow used Autopsy to examine:

- Operating System Information,
- Web Downloads,
- Recent Documents,
- Windows shortcut artifacts,
- the virtual filesystem,
- directory metadata,
- OS Accounts.

### Operational Relevance

Autopsy is valuable because it helps analysts examine disk images without interacting with the original live system.

A forensic disk image preserves the contents and structure of a storage device so evidence can be reviewed repeatedly while reducing the risk of altering the original source. Autopsy organizes recovered evidence into artifact categories and also allows direct filesystem navigation.

During this workflow, Autopsy was used to identify the suspect laptop's operating system, hostname, web download activity, recently accessed file paths, installed application directory metadata, and local Administrator account access information.

---

## Case Creation — Autopsy New Case Wizard

### Purpose

The Autopsy New Case Wizard creates a structured case container for forensic analysis.

A case stores the evidence source reference, analysis results, extracted artifacts, ingest output, settings, and other files generated during the investigation.

### How It Was Used

A new Autopsy case was created before adding the disk image.

The case was named using a descriptive analyst-selected name, and an empty base directory was selected for case storage. A new folder was created on the Desktop to serve as the base directory.

### Operational Relevance

Case creation is important because forensic work needs to remain organized and repeatable.

A case structure helps keep evidence, parsed artifacts, and analysis output grouped together. It also reduces confusion when reviewing multiple evidence sources or returning to an investigation later.

---

## Base Directory Selection

### Purpose

The base directory is the location where Autopsy stores the case files and generated output.

This directory can include internal databases, ingest output, extracted artifacts, reports, and other case-related files.

### How It Was Used

During case creation, the base directory was selected by browsing to the Desktop and creating a new empty folder.

The selected base directory was used by Autopsy to store the case structure and processing output.

### Operational Relevance

Using an empty base directory prevents Autopsy case data from mixing with unrelated files.

This helps preserve organization, reduces confusion during analysis, and supports cleaner documentation of where case output was stored.

---

## Evidence Import — Add Data Source

### Purpose

The Add Data Source workflow imports an evidence source into an Autopsy case.

A data source is the evidence item Autopsy will process and analyze. Examples include disk images, local disks, logical files, virtual machine images, and file collections.

### How It Was Used

After creating the case, the `Add Data Source` option was selected.

The default host option was left selected, and the disk image data source option was used. The disk image was selected from the laptop image directory:

```text
Autopsy For Disk Analysis\Laptop Image
```

The selected evidence file was:

```text
Craig Tucker Desktop.E01
```

### Operational Relevance

Adding the correct data source is a critical early step because all later findings depend on the evidence source being imported correctly.

The case itself is only the container. The disk image is the actual evidence. Until the image is added, Autopsy does not have filesystem or artifact data to parse.

---

## Forensic Disk Image — Craig Tucker Desktop.E01

### Purpose

The `.E01` file served as the forensic disk image analyzed during the workflow.

An E01 image is commonly used in digital forensics to store a copy of disk evidence along with image-related metadata, depending on how the image was created.

### How It Was Used

The disk image was selected as the Autopsy data source:

```text
Craig Tucker Desktop.E01
```

Autopsy processed the image and recovered artifacts related to operating system information, downloads, recent documents, filesystem structure, and local accounts.

### Operational Relevance

Disk images allow analysts to examine a preserved evidence source rather than the original device.

This matters because a live system can change as it is used. Files may be modified, timestamps may update, caches may change, and user activity may continue. A forensic image provides a stable source for repeatable analysis.

---

## Automated Processing — Ingest Modules

### Purpose

Ingest modules are Autopsy processing components that parse evidence and extract useful artifact categories.

Depending on the selected modules, Autopsy can recover activity records, web artifacts, operating system information, user account details, file metadata, embedded files, hash information, keyword hits, EXIF metadata, and other evidence.

### How It Was Used

During ingest configuration, all default modules were deselected first. The required module was then selected for the investigation.

The workflow used:

```text
Recent Activity
```

This module was selected because the investigation focused on operating system information, browser download activity, recently accessed files, and account artifacts.

### Operational Relevance

Ingest modules reduce the amount of manual parsing required during disk analysis.

Instead of manually locating and interpreting every browser database, registry-derived artifact, shortcut file, or account record, Autopsy can identify and organize many artifacts into reviewable categories.

Selecting only the relevant module also helps keep the analysis focused and avoids unnecessary processing.

---

## Operating System Identification — Operating System Information

### Purpose

The Operating System Information artifact category displays parsed system metadata recovered from the disk image.

This information can include the operating system name, version, system metadata, installation-related information, hostname values, and other system identity details.

### How It Was Used

The workflow reviewed:

```text
Data Artifacts > Operating System Information
```

The `Program Name` field was reviewed to identify the installed operating system.

The suspect laptop was identified as running:

```text
Windows 8.1 Pro
```

### Operational Relevance

Operating system identification establishes the environment being analyzed.

Knowing the OS version helps analysts interpret filesystem paths, user profile locations, artifact locations, application behavior, and expected Windows structures.

This is useful because disk artifacts should be interpreted in the context of the system that produced them.

---

## Hostname Identification — Operating System Information

### Purpose

Hostname identification helps determine the system name associated with the disk image.

The hostname can help connect the evidence source to inventory records, network logs, incident reports, or other systems reviewed during an investigation.

### How It Was Used

The Operating System Information artifacts were reviewed again to identify the system hostname.

The hostname value was found under the relevant system name field.

The hostname identified was:

```text
WIN-BK336TFMHLL
```

### Operational Relevance

Hostname artifacts provide device identity context.

This is useful when correlating disk evidence with external records, such as DHCP logs, authentication logs, endpoint management records, proxy logs, or incident response notes.

The hostname does not explain user activity by itself, but it helps identify which system the artifacts came from.

---

## Web Download Analysis — Web Downloads

### Purpose

The Web Downloads artifact category displays browser-related download records recovered from the disk image.

Download artifacts can include downloaded filenames, local paths, source URLs, timestamps, browser metadata, and other details depending on the evidence available.

### How It Was Used

The workflow reviewed:

```text
Data Artifacts > Web Downloads
```

A known timestamp was used to locate a specific download record:

```text
2013-12-18 20:05:57 GMT
```

The file associated with that timestamp was identified as:

```text
ALL COUPONS.rar
```

### Operational Relevance

Web download artifacts help reconstruct internet-based file acquisition.

They can answer questions such as what was downloaded, when it was downloaded, and sometimes where it came from.

This is important during forensic analysis because downloaded files may explain how content arrived on a system. In malware investigations, download artifacts may help identify the source of suspicious files.

---

## Download Source Review — URL Column

### Purpose

The URL field in Web Download artifacts can identify the remote source associated with a downloaded file.

A filename tells the analyst what object was downloaded, while the URL helps identify where it originated.

### How It Was Used

A second timestamp was used to locate another web download record:

```text
2013-12-18 03:02:50 GMT
```

The `URL` column was reviewed for the matching download record.

The full source URL identified was:

```text
http://download1346.mediafire.com/hn7hme361m4j/5ndp96wteag14q/Coupons.zip
```

### Operational Relevance

Source URLs provide important context that filenames alone cannot provide.

A URL can help analysts identify the download source, review domain reputation, correlate with proxy logs, perform threat intelligence enrichment, and determine whether the download came from a suspicious or expected location.

---

## Recent File Access Review — Recent Documents

### Purpose

The Recent Documents artifact category shows recently accessed files recovered from Windows user activity artifacts.

These artifacts often rely on Windows shortcut files, also known as `.lnk` files.

### How It Was Used

The workflow reviewed:

```text
Recent Documents
```

The investigation focused on the file:

```text
Pier.jpg
```

The relevant shortcut artifact appeared as:

```text
Pier.lnk
```

The shortcut metadata was reviewed to recover the original path associated with the image.

### Operational Relevance

Recent Documents artifacts are useful because they can preserve evidence that a user accessed or opened a file.

This can remain useful even when the analyst is not directly viewing the original file. A recent document record can provide file path evidence, timestamp context, and target metadata that supports user activity reconstruction.

---

## Shortcut Artifact Analysis — LNK Files

### Purpose

LNK files are Windows shortcut files that point to another file or location.

A shortcut does not contain the full target file itself. Instead, it may preserve metadata about the original target, such as the target path, timestamps, file size, volume information, drive information, and original location.

### How It Was Used

The workflow reviewed the shortcut artifact:

```text
Pier.lnk
```

The shortcut preserved the path associated with:

```text
Pier.jpg
```

The full path recovered was:

```text
C:\Users\Craig\Documents\My Pictures\Pier.jpg
```

### Operational Relevance

Shortcut artifacts can help reconstruct file access activity.

This is important because the shortcut may preserve evidence of a file path even if the original file was moved, deleted, or not immediately visible during review.

Analysts should remember that a shortcut artifact points to a target file. It does not prove the target file still exists at that location without additional validation.

---

## Filesystem Navigation — Virtual Filesystem

### Purpose

The virtual filesystem allows analysts to browse the filesystem structure recovered from the disk image.

Unlike parsed artifact views, the virtual filesystem provides direct access to volumes, partitions, directories, files, and metadata as represented in the forensic image.

### How It Was Used

The workflow moved from artifact-based review to filesystem-based review by browsing the disk image directly.

The relevant volume opened was:

```text
vol2
```

This volume contained the primary Windows filesystem structure, including familiar directories such as user profiles, operating system folders, and program directories.

### Operational Relevance

Filesystem navigation is important because not every investigation question can be answered from parsed artifact categories alone.

Analysts often need to manually locate files, inspect paths, validate artifact findings, compare directories, review metadata, and confirm whether specific files or folders exist within the disk image.

---

## Volume Review — vol2

### Purpose

Volume review helps analysts understand the partition or volume structure inside a disk image.

A disk image may contain multiple volumes, and each volume may contain different filesystem content.

### How It Was Used

Autopsy displayed multiple volume entries from the disk image, including entries such as:

```text
vol1
vol2
vol3
```

The workflow opened:

```text
vol2
```

This volume was selected because it contained the primary Windows filesystem structure needed for the investigation.

### Operational Relevance

Reviewing volumes prevents analysts from assuming that every partition contains the same type of evidence.

Some volumes may contain the main operating system, while others may contain recovery data, system-reserved areas, or other disk structures. Identifying the relevant volume helps keep the analysis focused.

---

## Application Directory Review — Program Files and GIMP 2

### Purpose

Application directory review allows analysts to inspect installed program folders and related filesystem metadata.

Program directories can contain application files, libraries, configuration data, plugins, logs, dependencies, and other software-related artifacts.

### How It Was Used

Inside the virtual filesystem, the workflow navigated to:

```text
vol2 > Program Files > GIMP 2
```

The directory structure inside `GIMP 2` was reviewed, and the `lib` directory was selected for metadata review.

### Operational Relevance

Reviewing application directories can help analysts understand what software existed on the system and how files were organized.

Even when the application itself is not suspicious, navigating to a known directory demonstrates how Autopsy can be used to inspect filesystem metadata directly from the forensic image.

---

## Directory Metadata Review — lib Directory Size

### Purpose

Directory metadata review helps analysts confirm properties associated with files or folders in the filesystem.

Metadata can include size, path, allocation status, timestamps, and other attributes depending on the tool and evidence source.

### How It Was Used

The workflow reviewed the `lib` directory inside:

```text
Program Files > GIMP 2
```

The size column was used to identify the directory size.

The directory size identified was:

```text
544 MB
```

### Operational Relevance

Directory metadata can help confirm file and folder properties during filesystem analysis.

This is useful when validating artifact findings, locating application components, identifying large directories, or documenting specific filesystem evidence.

---

## OS Account Review — OS Accounts

### Purpose

The OS Accounts artifact category displays local operating system account information recovered from the disk image.

Depending on the available evidence, account artifacts may include usernames, account types, account status, timestamps, security identifiers, domain or local account context, and other account metadata.

### How It Was Used

The workflow reviewed:

```text
OS Accounts
```

The local account of interest was:

```text
Administrator
```

The last accessed timestamp associated with the local Administrator account was identified as:

```text
2013-08-22 14:47:09 UTC
```

### Operational Relevance

OS Account artifacts support user and account activity analysis.

They help identify which accounts existed on the system and may provide timestamps that support timeline reconstruction. Account artifacts are especially useful when correlated with downloads, recent documents, filesystem activity, and other user activity evidence.

---

## Artifact Correlation

### Purpose

Artifact correlation combines evidence from multiple sources to build a stronger interpretation than any single artifact can provide alone.

Disk investigations often require combining system information, browser artifacts, shortcut metadata, filesystem evidence, and account records.

### How It Was Used

The workflow correlated the following evidence sources:

| Artifact Source | Finding |
|---|---|
| Operating System Information | `Windows 8.1 Pro` |
| Hostname | `WIN-BK336TFMHLL` |
| Web Downloads | `ALL COUPONS.rar` |
| Web Downloads URL | `http://download1346.mediafire.com/hn7hme361m4j/5ndp96wteag14q/Coupons.zip` |
| Recent Documents / LNK | `C:\Users\Craig\Documents\My Pictures\Pier.jpg` |
| Virtual Filesystem | `vol2 > Program Files > GIMP 2` |
| Directory Metadata | `lib` directory size: `544 MB` |
| OS Accounts | Administrator last accessed: `2013-08-22 14:47:09 UTC` |

### Operational Relevance

No single Autopsy view provided the full investigative picture.

Operating system artifacts identified the environment. Hostname artifacts identified the system. Web download artifacts showed internet-based file acquisition. Recent Documents and LNK artifacts showed file access evidence. Filesystem navigation confirmed directory structure and metadata. OS Account artifacts provided account-level context.

Together, these artifact sources supported a more complete disk image investigation.

---

## Common Mistakes to Avoid

- Assuming a disk image and a live system are the same thing.
- Adding the wrong data source and then trusting all later findings.
- Using a non-empty base directory and mixing case output with unrelated files.
- Selecting unnecessary ingest modules without understanding why they are needed.
- Reviewing artifacts before ingest processing has completed.
- Assuming the operating system version is known without verifying it in artifacts.
- Treating a hostname as proof of user identity.
- Confusing a downloaded filename with the source URL.
- Assuming a downloaded archive's filename reveals its internal contents.
- Ignoring timestamps when locating specific web download records.
- Assuming a Recent Documents shortcut is the original file itself.
- Forgetting that `.lnk` files point to target files and may preserve target metadata.
- Assuming a shortcut proves the target file still exists at the original path.
- Relying only on parsed artifacts and never checking the virtual filesystem.
- Assuming the largest or most obvious volume is always the correct one without review.
- Confusing file or directory metadata with proof of user intent.
- Treating account last accessed timestamps as a complete activity timeline by themselves.
- Failing to correlate operating system, browser, shortcut, filesystem, and account artifacts together.

---

## Summary

Autopsy was used to analyze a forensic disk image and recover disk-based artifacts related to system identity, browser download activity, recent file access, filesystem structure, directory metadata, and local account information.

The disk image analyzed was:

```text
Craig Tucker Desktop.E01
```

Autopsy case creation, data source import, ingest module processing, artifact review, virtual filesystem navigation, and account analysis were used together to answer the investigation questions.

The workflow demonstrated how disk image forensics can recover and correlate persistent evidence from a Windows system. Operating System Information identified the system environment, Web Downloads showed downloaded files and source URLs, Recent Documents and LNK artifacts preserved file access paths, the virtual filesystem allowed direct directory review, and OS Accounts provided local account activity context.

The key findings were:

```text
Operating System: Windows 8.1 Pro
Hostname: WIN-BK336TFMHLL
Downloaded File: ALL COUPONS.rar
Download URL: http://download1346.mediafire.com/hn7hme361m4j/5ndp96wteag14q/Coupons.zip
Pier.jpg Path: C:\Users\Craig\Documents\My Pictures\Pier.jpg
GIMP 2 lib Directory Size: 544 MB
Administrator Last Accessed: 2013-08-22 14:47:09 UTC
```

This workflow showed how Autopsy can be used not only to locate individual answers, but also to connect separate disk artifacts into a more complete forensic interpretation.
