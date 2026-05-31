# Analyst Notes — Metadata Analysis and File Carving Using ExifTool and Scalpel

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the metadata analysis and file carving workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding why metadata analysis matters, how file carving works, and how deleted data recovery connects to file systems and storage behavior.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**  
> - **Workflows** refer to structured forensic tasks such as metadata extraction, file carving, and hash validation.  
> - **Examinations** refer to the hands-on review of files and disk images using command-line forensic tools.  
> - **Conceptual Reviews** refer to the interpretation of metadata, EXIF data, file signatures, deleted data behavior, and carving methodology.

---

### Observations During Execution

One of the biggest realizations during this workflow was that digital evidence can exist in places that are not immediately visible when opening a file normally.

For the metadata questions, the visible contents of `dummy.pdf` and `picture.jpg` were not the primary focus. Instead, the important evidence was embedded information describing the files. ExifTool exposed metadata fields that could identify the author of a PDF and the camera model used to capture an image.

For the file carving question, the target evidence was not a visible file sitting in a normal folder. The image had been deleted from an NTFS-formatted memory stick image. Instead of browsing the file system and opening a normal file, Scalpel searched through raw disk image data for file signatures that indicated recoverable image content.

This helped reinforce that forensic analysis often requires looking beyond what the operating system chooses to show.

---

### Understanding Metadata

Metadata is often described as data about data.

Before this workflow, it was easy to think of a file as only the content visible when opening it. For example, a PDF appears to be a document and a JPEG appears to be a picture. However, files often contain additional embedded information that describes how, when, where, or by whom they were created.

A PDF may contain metadata such as:

- author,
- title,
- creator software,
- producer software,
- creation date,
- modification date.

A JPEG may contain EXIF metadata such as:

- camera make,
- camera model,
- capture timestamp,
- image dimensions,
- GPS coordinates,
- lens information,
- software used to process the image.

This matters because metadata may provide investigative context that is not visible in the file's normal content.

---

### Why PDF Author Metadata Matters

The first metadata question asked for the author of `dummy.pdf`.

At first, this may seem like a simple lookup. However, author metadata can be valuable during investigations because it may reveal information about the user account, application, organization, or system that created the document.

In a real investigation, PDF author metadata could help answer questions such as:

- Who may have created the document?
- Does the embedded author match the claimed author?
- Was the document produced by a known application?
- Does the metadata reveal a username or system identity?
- Does the file contain suspicious or unexpected metadata?

This does not automatically prove authorship by itself. Metadata can be edited, stripped, forged, or inherited from templates. However, it provides a useful investigative lead that can be correlated with other evidence.

---

### Understanding EXIF Metadata in Images

The second metadata question asked for the camera model used to take `picture.jpg`.

This introduced EXIF metadata. EXIF stands for Exchangeable Image File Format. EXIF metadata is commonly embedded into image files by cameras, smartphones, and image processing software.

Camera model metadata can help identify the device or type of device used to capture a photo. In some investigations, EXIF metadata may also reveal when the image was taken, whether GPS location data exists, and what software later modified the image.

This matters because images often appear simple on the surface, but their metadata can contain device and timeline clues.

The use of:

```bash
exiftool picture.jpg | grep camera
```

also demonstrated how command-line filtering can reduce noise. ExifTool may print many metadata fields. Piping the output into `grep camera` makes it easier to focus on camera-related fields.

---

### Understanding File Carving

The most important concept introduced during this workflow was file carving.

File carving is a recovery technique that searches raw storage data for recognizable file signatures. Instead of asking the file system where a file is located, the carving tool scans through the disk image looking for patterns that indicate the beginning and end of certain file types.

This is different from normal file recovery.

Normal file recovery may rely on file system records, directory entries, allocation tables, or metadata structures that still point to deleted files. File carving can sometimes recover files even when those file system records are missing, damaged, or no longer actively referenced.

In this workflow, Scalpel was configured to search for image file signatures. Once the JPEG signature was enabled, Scalpel searched `carve1.img` and recovered `00000000.jpg`.

---

### Connection to the Previous File System Workflow

This workflow connects directly to the previous file system identification workflow.

In the previous workflow, `carve1.img` was identified as an NTFS-formatted image. That helped establish how the memory stick image was organized at the file system level.

In this workflow, the same general concept becomes more practical. The question states that `carve1.img` is a memory stick image formatted in NTFS, but the recovery technique used is file carving. This means the workflow is not simply browsing visible NTFS files. Instead, it is searching raw storage data for a deleted image.

This distinction is important.

The file system may no longer show the deleted image as an active file. However, the raw data associated with the image may still exist inside the disk image. Scalpel can recover it by searching for image signatures.

This helped clarify the difference between:

- file system-based evidence,
- raw storage-based evidence,
- visible files,
- deleted file remnants,
- carved artifacts.

---

### Why Deleted Files May Still Be Recoverable

A deleted file does not always immediately disappear from storage.

When a file is deleted, the file system may mark the file's storage space as available for reuse. However, the actual data may remain in place until it is overwritten or otherwise erased.

This is why deleted file recovery and file carving can work. If the raw bytes of the deleted file still exist, a carving tool may be able to locate and reconstruct the file.

This also connects to the earlier discussion about NTFS, clusters, Logical Block Addresses, and storage behavior. The file system manages logical organization, but the underlying data may continue to exist on the storage medium after the file is removed from normal file listings.

On traditional storage media, recoverability often depends on whether the deleted file data has been overwritten. On SSDs, features such as TRIM and garbage collection may make recovery less predictable because the device may actively discard deleted data internally.

---

### Understanding Scalpel Configuration

A major learning point was understanding why Scalpel required configuration before carving.

Scalpel uses a configuration file that defines which file types it should search for. Many file signatures may be disabled by default through comments. A commented line begins with `#`, which tells the program to ignore that line.

To recover image files, the relevant graphics file signatures needed to be enabled by removing the `#` characters from the JPEG and GIF entries.

This made the workflow more understandable. Scalpel was not being asked to recover every possible type of file. It was being configured to search for specific file types based on their signatures.

This is important because file carving can produce false positives or unnecessary output if too many file types are enabled. A focused configuration helps target the recovery objective.

---

### Understanding File Signatures

A file signature is a recognizable pattern of bytes that identifies a file type.

For example, JPEG files commonly begin with a known header and end with a known footer. These byte patterns help tools identify where a JPEG may start and end inside raw storage.

File carving tools use these signatures to search disk images for recoverable files.

This is why Scalpel can recover a file even if the file name and directory entry are gone. It is not searching for the file name. It is searching for the structure of the file itself.

This was an important distinction because the recovered file was named `00000000.jpg` by Scalpel, not necessarily because that was the original filename. The original filename may have been lost, but the content was recoverable.

---

### Understanding MD5 Hashing

The final step required generating the MD5 hash of the recovered image.

A hash is a calculated value based on a file's contents. If the file changes, the hash should also change. Hashes are commonly used in digital forensics to identify files, compare artifacts, and verify integrity.

In this workflow, `md5sum` was used to calculate the MD5 hash of `00000000.jpg`.

The hash served as the final answer because it uniquely represented the recovered deleted image for the purposes of the exercise.

MD5 is no longer considered collision-resistant for modern cryptographic security purposes, but it is still commonly used in forensic exercises, malware repositories, and file identification workflows where the goal is practical file comparison rather than cryptographic assurance.

---

### Challenges and Confusion Encountered

The most confusing part of the workflow was understanding why multiple steps were necessary before running Scalpel.

At first, it seemed like Scalpel should simply run against the disk image. However, the configuration file controls what file types Scalpel searches for. If the image signatures are not enabled, the tool may not recover the target image.

Another confusing point was ownership and permissions. Copying `/etc/scalpel/scalpel.conf` with `sudo` can create a file owned by `root`. Changing ownership with `sudo chown ubuntu q3.conf` makes the copied file easier to edit as the normal `ubuntu` user.

The workflow also required careful directory awareness. Running commands from the wrong directory could cause errors because the target files, configuration file, or output directory may not be where the command expects them to be.

---

### Cross-Workflow Connections

This workflow connects to several other digital forensic and security operations workflows.

It connects to file system identification because file carving often occurs after understanding the structure of a disk image.

It connects to deleted file recovery because file carving can recover artifacts that no longer appear as active files.

It connects to metadata analysis because recovered files may later be examined for embedded metadata.

It connects to incident response because analysts often need to recover deleted evidence, validate artifacts, and extract metadata during investigations.

It also connects to malware analysis and threat hunting because hashes are often used to identify suspicious or malicious files across systems.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

- comparing ExifTool output before and after metadata removal,
- extracting GPS metadata from images,
- carving multiple file types from the same image,
- comparing Scalpel with Foremost or PhotoRec,
- validating recovered files using SHA256 in addition to MD5,
- inspecting recovered files with a hex editor,
- identifying JPEG headers and footers manually,
- comparing file carving success across NTFS, FAT32, and EXT3 images,
- documenting file recovery from allocated vs unallocated space,
- examining whether recovered images retain EXIF metadata.

These additions would expand the workflow from basic metadata and carving into deeper forensic recovery and artifact validation.

---

### Summary

This workflow reinforced that forensic evidence may exist in multiple layers.

Metadata analysis revealed information embedded inside files. File carving recovered deleted image data from raw storage. Hashing validated the recovered artifact.

Together, these techniques demonstrated that digital forensic analysis is not limited to visible files. Evidence can exist inside metadata, deleted storage regions, file signatures, and recovered artifacts.
