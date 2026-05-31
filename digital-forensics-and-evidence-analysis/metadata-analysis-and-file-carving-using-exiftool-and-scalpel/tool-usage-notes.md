# Tool Usage Notes — Metadata Analysis and File Carving Using ExifTool and Scalpel

## Overview

This document summarizes the tools, commands, workflows, and operational relevance of the utilities used during the metadata analysis and file carving execution.

The workflow focused on:

- extracting metadata from a PDF,
- extracting EXIF metadata from a JPEG,
- filtering metadata output,
- configuring Scalpel,
- carving a deleted image from an NTFS disk image,
- hashing the recovered artifact.

---

## Primary Metadata Tool — ExifTool

### Purpose

ExifTool was used to extract metadata from files.

Metadata is information about a file rather than the visible contents of the file itself. ExifTool can parse many file types and reveal embedded information such as authorship, timestamps, camera details, software identifiers, and other file properties.

### How It Was Used

ExifTool was used against:

```text
dummy.pdf
picture.jpg
```

Commands used:

```bash
exiftool dummy.pdf
exiftool picture.jpg
```

For the JPEG image, output could also be filtered using:

```bash
exiftool picture.jpg | grep camera
```

### Operational Relevance

ExifTool is useful in forensic workflows because files often contain metadata that is not visible when opened normally.

For example:

- a PDF may contain author metadata,
- a JPEG may contain camera metadata,
- an image may contain timestamps or GPS coordinates,
- a document may reveal creator software.

This information may support attribution, timeline development, device identification, and investigative correlation.

---

## Command-Line Filtering — grep

### Purpose

`grep` was used to filter command output.

ExifTool may return many metadata fields. Filtering allows the analyst to focus on lines containing a specific keyword.

### How It Was Used

The guide suggested:

```bash
exiftool picture.jpg | grep camera
```

This command sends the output of ExifTool into `grep`, which searches for lines containing the word `camera`.

### Operational Relevance

Filtering is useful when tool output is large or noisy.

Instead of manually reviewing every line, analysts can filter for relevant terms such as:

- camera,
- author,
- model,
- date,
- GPS,
- creator,
- software.

This supports faster triage while still allowing full output review when needed.

---

## Primary File Carving Tool — Scalpel

### Purpose

Scalpel was used to carve deleted image data from a disk image.

File carving is a technique used to recover files from raw storage data based on recognizable file signatures. It does not require the file to appear as an active file in the file system.

### How It Was Used

The workflow used Scalpel against:

```text
carve1.img
```

The command used was:

```bash
scalpel -c q3.conf carve1.img -o outputq3
```

Command breakdown:

| Command Element | Meaning |
|---|---|
| `scalpel` | Runs the Scalpel file carving tool |
| `-c q3.conf` | Uses the custom Scalpel configuration file |
| `carve1.img` | Specifies the source disk image to search |
| `-o outputq3` | Specifies the output directory for recovered files |

### Operational Relevance

Scalpel is useful when files have been deleted or when file system records are unavailable, damaged, or incomplete.

Instead of relying on directory entries, Scalpel searches raw storage for file headers and footers. This can allow recovery of deleted files if the underlying data still exists and has not been overwritten or discarded.

---

## Scalpel Configuration File

### Purpose

The Scalpel configuration file controls which file types Scalpel searches for.

The default configuration file is located at:

```text
/etc/scalpel/scalpel.conf
```

A working copy was created as:

```text
q3.conf
```

### How It Was Used

The default configuration was copied:

```bash
sudo cp /etc/scalpel/scalpel.conf ./q3.conf
```

Ownership was changed:

```bash
sudo chown ubuntu q3.conf
```

The file was edited:

```bash
nano q3.conf
```

The relevant graphics file signatures were enabled by removing `#` characters from JPEG and GIF entries.

### Operational Relevance

Scalpel only searches for file types enabled in its configuration file.

If the JPEG signature is commented out, Scalpel may not recover JPEG images. Editing the configuration file is therefore part of defining the recovery scope.

This is similar to telling Scalpel:

> Search this disk image for these specific file types.

---

## Text Editor — nano

### Purpose

`nano` was used to edit the Scalpel configuration file.

### How It Was Used

The configuration file was opened with:

```bash
nano q3.conf
```

After editing, the file was saved using:

```text
CTRL + X
Y
ENTER
```

### Operational Relevance

Editing configuration files is common in Linux-based forensic workflows.

In this case, `nano` allowed the analyst to enable the file signatures needed for image recovery.

---

## Ownership and Permissions — chown

### Purpose

`chown` was used to change the ownership of the copied Scalpel configuration file.

### How It Was Used

```bash
sudo chown ubuntu q3.conf
```

### Operational Relevance

Because the configuration file was copied from a privileged system location using `sudo`, the copied file may be owned by `root`.

Changing ownership to `ubuntu` allowed the normal user to edit the file more easily.

This is important because permission issues can interrupt forensic workflows if files are not readable, writable, or executable by the current user.

---

## Output Directory Creation — mkdir

### Purpose

`mkdir` was used to create an output directory for recovered files.

### How It Was Used

```bash
mkdir outputq3
```

### Operational Relevance

Carving tools should write recovered files to a dedicated output directory.

This keeps recovered artifacts organized and prevents confusion between source evidence, configuration files, and output files.

---

## Hashing Tool — md5sum

### Purpose

`md5sum` was used to calculate the MD5 hash of the recovered image.

### How It Was Used

```bash
md5sum 00000000.jpg
```

### Operational Relevance

Hashing is commonly used in digital forensics to identify files and validate integrity.

A hash can be used to:

- identify a recovered artifact,
- compare files,
- document evidence,
- confirm that a file has not changed,
- support reporting.

In this workflow, the MD5 hash of the carved image was the final answer for the file carving question.

---

## Evidence Files and Tool Mapping

| Evidence File | Tool Used | Purpose |
|---|---|---|
| `dummy.pdf` | ExifTool | Identify PDF author metadata |
| `picture.jpg` | ExifTool, grep | Identify camera model metadata |
| `carve1.img` | Scalpel | Recover deleted image data |
| `00000000.jpg` | md5sum | Generate MD5 hash of recovered image |

---

## Common Mistakes to Avoid

A common mistake would be running commands from the wrong directory. If the terminal is not inside the `Metadata and File Carving` directory, commands such as `exiftool dummy.pdf` or `scalpel -c q3.conf carve1.img -o outputq3` may fail because the files are not found.

Another mistake would be editing the system Scalpel configuration directly instead of creating a working copy. Creating `q3.conf` keeps the workflow self-contained and avoids unnecessary changes to the default configuration.

Another mistake would be forgetting to uncomment the relevant image signatures. If JPEG carving is not enabled, Scalpel may not recover the image needed for the question.

Another mistake would be hashing the wrong file. The MD5 hash must be generated from the recovered image file, not the original disk image, output directory, or configuration file.

---

## Suggested Screenshot Documentation

Useful screenshots for this workflow include:

| Screenshot | Purpose |
|---|---|
| Terminal inside `Metadata and File Carving` directory | Shows correct working location |
| `exiftool dummy.pdf` output | Supports author metadata finding |
| `exiftool picture.jpg` output | Supports camera model finding |
| `grep camera` filtered output | Shows narrowed camera metadata review |
| `q3.conf` inside nano | Shows image signatures enabled |
| Scalpel execution command | Shows carving operation against `carve1.img` |
| `outputq3` directory | Shows recovered output location |
| `00000000.jpg` file location | Shows recovered carved image |
| `md5sum 00000000.jpg` output | Supports final MD5 hash finding |

---

## Summary

ExifTool was used to extract metadata from files. Scalpel was used to recover deleted image data from a disk image. md5sum was used to generate a hash of the recovered artifact.

Together, these tools demonstrated three important forensic activities:

- identifying hidden metadata,
- recovering deleted file content,
- validating recovered evidence with a hash.
