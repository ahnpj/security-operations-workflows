# Tool Usage Notes — Data Acquisition Using FTK Imager, ProcDump, and KAPE

## Overview

This document summarizes the tool usage, acquisition methods, interface observations, evidence collection process, and forensic relevance of FTK Imager, ProcDump, PowerShell, and KAPE during the data acquisition workflow.

The workflow focused on:

* acquiring volatile memory,
* acquiring process-specific memory,
* creating a forensic disk image,
* collecting browser-related artifacts,
* preserving evidence for future analysis,
* understanding acquisition-focused forensic workflows.

---

## Primary Acquisition Tool — FTK Imager

### Purpose

FTK Imager was used as the primary acquisition tool for capturing volatile memory and creating a forensic disk image.

Unlike analysis-focused workflows where FTK Imager is used to review evidence, this workflow focused on using FTK Imager to create new evidence files that could later be analyzed using forensic platforms such as Volatility, Autopsy, EnCase, FTK, or X-Ways.

### How It Was Used

FTK Imager was used in two separate acquisition activities:

1. Physical memory acquisition.
2. Forensic disk imaging.

For memory acquisition, FTK Imager's Capture Memory feature was used to create a memory dump from the analysis host.

For disk acquisition, FTK Imager's Create Disk Image wizard was used to acquire a secondary physical drive and save the resulting image in E01 format.

### Operational Relevance

FTK Imager is commonly used in DFIR investigations because it allows investigators to preserve evidence before it changes or is lost.

In this workflow, FTK Imager was responsible for acquiring two major evidence sources:

* volatile memory,
* persistent disk storage.

These evidence sources support different investigative objectives and often complement one another during forensic analysis.

---

## Memory Acquisition Workflow

### Purpose

The memory acquisition workflow was used to preserve volatile memory from a live Windows system.

### Process Used

The acquisition process consisted of:

1. Launching FTK Imager.
2. Selecting File → Capture Memory.
3. Configuring an output destination.
4. Executing the acquisition.
5. Confirming creation of the memory dump.

### Operational Relevance

Memory acquisition is often prioritized because RAM contents can disappear during shutdown, reboot, application closure, or normal system activity.

Memory images may contain:

* running processes,
* network connections,
* loaded modules,
* command-line arguments,
* credentials,
* encryption keys,
* malware artifacts.

Capturing memory before these artifacts disappear helps preserve a snapshot of the live system state.

---

## Disk Imaging Workflow

### Purpose

The disk imaging workflow was used to create a forensic image of a secondary physical storage device.

### Process Used

The disk imaging process consisted of:

1. Launching FTK Imager.
2. Selecting File → Create Disk Image.
3. Choosing Physical Drive as the source type.
4. Selecting the target drive.
5. Configuring an E01 image destination.
6. Starting acquisition.
7. Reviewing generated hash values.

### Operational Relevance

Disk imaging preserves evidence while allowing investigators to perform future analysis against a copy rather than the original storage device.

The E01 format was selected because it is widely supported by forensic tools and supports integrity verification features.

Hash values generated during acquisition help demonstrate that the evidence remains unchanged throughout later handling and analysis.

---

## Process Memory Acquisition Tool — ProcDump

### Purpose

ProcDump was used to create a memory dump of a specific running process.

Unlike FTK Imager, which captured memory from the entire system, ProcDump was used to target a single process.

### How It Was Used

ProcDump was executed from an elevated PowerShell session.

The process acquisition workflow consisted of:

1. Opening PowerShell as Administrator.
2. Navigating to the Acquisition Tools directory.
3. Launching Calculator as a benign process.
4. Identifying the process ID.
5. Executing ProcDump using the identified PID.
6. Reviewing the generated dump file.

The following command format was used:

```powershell
.\procdump.exe -ma <PID>
```

### Operational Relevance

Process memory acquisition allows analysts to focus on a specific application rather than acquiring all system memory.

This technique is commonly used during incident response investigations involving:

* suspicious executables,
* malware,
* browser processes,
* PowerShell activity,
* injected processes.

A process dump may contain evidence not visible within the original file stored on disk.

---

## Supporting Utility — PowerShell

### Purpose

PowerShell was used to interact with the Windows operating system and support process acquisition activities.

### How It Was Used

PowerShell was used to:

* navigate to acquisition tool locations,
* launch applications,
* identify running processes,
* locate process IDs,
* execute ProcDump.

Commands used during the workflow included:

```powershell
Get-Process
```

and

```powershell
Get-Process | findstr -I calc
```

### Operational Relevance

PowerShell provides a quick method for interacting with operating system processes and executing acquisition utilities.

In this workflow, PowerShell served as the bridge between process identification and process acquisition.

---

## Remote Access Technology — Remote Desktop Protocol (RDP)

### Purpose

Remote Desktop Protocol (RDP) was used to access a remote Windows host.

### How It Was Used

The analyst connected to the remote host using the provided IP address and authentication credentials.

Once connected, the remote system could be controlled as though the analyst were physically using it.

### Operational Relevance

Remote acquisition workflows are common during enterprise investigations because investigators are often required to collect evidence from systems located elsewhere on the network.

RDP enabled KAPE to be deployed and executed on the remote system without requiring physical access.

---

## Artifact Collection Tool — KAPE

### Purpose

KAPE was used to perform targeted forensic artifact collection from the remote Windows host.

Unlike FTK Imager, which focuses on broad acquisition methods such as memory and disk imaging, KAPE focuses on collecting specific artifacts from known locations.

### How It Was Used

KAPE was copied to the remote host and executed using the graphical interface, gkape.

The workflow consisted of:

1. Copying KAPE to the remote system.
2. Launching gkape.
3. Configuring the Target Source.
4. Configuring the Target Destination.
5. Selecting Chrome targets.
6. Selecting Chrome Extension targets.
7. Executing collection.
8. Reviewing the output folder.

### Operational Relevance

KAPE provides a fast method for collecting high-value forensic artifacts without imaging an entire disk.

This approach is useful during triage situations where investigators need evidence quickly.

---

## Browser Artifact Collection

### Purpose

Browser artifact collection focused on gathering Chrome-related evidence from the remote host.

### How It Was Performed

The following targets were selected:

* Chrome
* Chrome Extensions

KAPE automatically collected artifacts from known Chrome storage locations beneath the selected source drive.

Collected artifacts included:

* History
* Cookies
* Favicons
* Sessions
* Web Data
* Visited Links

### Operational Relevance

These artifacts help investigators reconstruct user activity.

Examples include:

* websites visited,
* download activity,
* browser sessions,
* extension usage,
* browsing timestamps,
* stored web data.

This information is frequently valuable during incident response, insider threat investigations, and user activity analysis.

---

## Evidence Outputs Created

The workflow generated multiple evidence outputs.

| Output                      | Tool       | Purpose                             |
| --------------------------- | ---------- | ----------------------------------- |
| Memory dump                 | FTK Imager | Full physical memory acquisition    |
| Process dump (.dmp)         | ProcDump   | Process-specific memory acquisition |
| E01 disk image              | FTK Imager | Forensic disk acquisition           |
| Browser artifact collection | KAPE       | Chrome artifact acquisition         |

Each output preserves a different category of evidence and supports different forensic objectives.

---

## Tool Strengths Observed

The following strengths were observed during execution:

### FTK Imager

* Simple acquisition workflows.
* Memory acquisition support.
* Disk imaging support.
* Hash generation and verification.
* Widely accepted forensic format support.

### ProcDump

* Fast process-specific acquisition.
* Lightweight execution.
* Useful for targeted investigations.
* Simple command-line operation.

### PowerShell

* Quick process enumeration.
* Native Windows administration capabilities.
* Flexible command execution.

### KAPE

* Rapid artifact collection.
* Extensive target library.
* Minimal configuration requirements.
* Effective triage capabilities.

---

## Tool Limitations Observed

This workflow focused only on acquisition.

Limitations included:

* No memory analysis performed.
* No Volatility analysis performed.
* No disk image analysis performed.
* No browser artifact parsing performed.
* No malware investigation performed.
* No timeline reconstruction performed.

The workflow demonstrated evidence collection rather than evidence interpretation.

---

## Common Mistakes to Avoid

A common mistake is assuming acquisition and analysis are the same activity. Acquisition preserves evidence; analysis interprets it.

Another mistake is failing to collect volatile memory before shutting down a system, which can permanently destroy valuable evidence.

Another mistake is acquiring the wrong process when using ProcDump. Analysts should verify the PID before creating a process dump.

Another mistake is assuming KAPE searches an entire drive manually. KAPE relies on predefined artifact definitions and known storage locations.

Another mistake is relying solely on a disk image while ignoring memory acquisition. Different evidence sources often reveal different aspects of system activity.

---

## Suggested Screenshot Documentation

Useful screenshots for this workflow include:

| Screenshot                          | Purpose                                 |
| ----------------------------------- | --------------------------------------- |
| FTK Imager Capture Memory window    | Shows memory acquisition setup          |
| Memory dump creation progress       | Shows volatile memory acquisition       |
| PowerShell process enumeration      | Shows PID identification                |
| ProcDump execution                  | Shows process acquisition               |
| ProcDump output file                | Shows successful dump creation          |
| FTK Imager Create Disk Image wizard | Shows disk imaging workflow             |
| E01 configuration screen            | Shows image format selection            |
| Hash verification results           | Shows evidence integrity validation     |
| RDP connection window               | Shows remote access workflow            |
| gkape interface                     | Shows artifact collection configuration |
| Chrome target selection             | Shows browser artifact collection       |
| Output folder contents              | Shows acquired evidence artifacts       |

---

## Summary

FTK Imager, ProcDump, PowerShell, RDP, and KAPE were used together to demonstrate multiple forms of forensic data acquisition.

The workflow showed how investigators can acquire:

* full system memory,
* process memory,
* forensic disk images,
* targeted browser artifacts.

Each tool served a different acquisition purpose, and together they demonstrated how digital evidence can be preserved before analysis begins.

The workflow reinforced a core DFIR principle: evidence must first be collected and preserved before meaningful forensic analysis can occur.
