# Tool Usage Notes — Windows Artifact Analysis Using WFA, PECmd, and JumpList Explorer

## Overview

This document summarizes the tools, commands, workflows, and operational relevance of the utilities used during the Windows artifact analysis workflow.

The workflow focused on:

- analyzing Windows shortcut (`.LNK`) artifacts,
- analyzing Windows Prefetch (`.pf`) artifacts,
- identifying execution-related evidence,
- searching Prefetch artifacts for suspicious script references,
- reviewing Jump List artifacts,
- identifying browser-related activity,
- correlating findings across multiple Windows artifact sources.

---

## Primary Shortcut Analysis Tool — Windows File Analyzer (WFA)

### Purpose

Windows File Analyzer (WFA) was used to examine Windows shortcut (`.LNK`) files.

Shortcut files are small Windows files that point to another file, folder, or program. Although they are often created for convenience, they can preserve useful forensic information such as filenames, file paths, timestamps, volume information, and historical locations.

### How It Was Used

WFA was launched as Administrator and used to analyze:

```text
C:\Users\BTLOTest\Desktop\Windows Investigation One\Shortcuts
```

The analysis focused on:

- Filename column
- Linked Path column
- Timestamp information
- Volume metadata

### Operational Relevance

Shortcut files may preserve evidence even when the original file no longer exists.

During this workflow, WFA helped identify:

- `PlagueRat.ps1.lnk`
- `2020_Summer_Photos.zip`
- `Invoice537-PR-06-05-2020.docx`

This provided the initial lead that drove the remainder of the investigation.

---

## Primary Prefetch Analysis Tool — PECmd

### Purpose

PECmd was used to parse Windows Prefetch artifacts.

Prefetch files are automatically created by Windows when applications execute. Their primary purpose is performance optimization, but they are also valuable forensic artifacts because they can reveal:

- application execution activity,
- execution timestamps,
- run counts,
- referenced files,
- referenced directories.

### How It Was Used

Single-file analysis:

```cmd
PECmd.exe -f "C:\Users\BTLOTest\Desktop\Windows Investigation One\Prefetch\CMD.EXE-89305D47.pf"
```

Directory-wide search:

```cmd
PECmd.exe -k "plaguerat.ps1" -d "C:\Users\BTLOTest\Desktop\Windows Investigation One\Prefetch\"
```

### Command Breakdown

| Command Element | Meaning |
|---|---|
| `PECmd.exe` | Runs the Prefetch parsing utility |
| `-f` | Parse a specific Prefetch file |
| `-d` | Parse all Prefetch files in a directory |
| `-k` | Highlight matching keyword references |
| `plaguerat.ps1` | Suspicious script being searched for |

### Operational Relevance

PECmd helped move the investigation from file-origin evidence into execution-related evidence.

Key findings included:

- `PlagueRat.bat`
- Full path to the batch file
- References to `PlagueRat.ps1`
- Associated applications

---

## Windows Command Prompt (CMD.EXE)

### Purpose

Command Prompt is the traditional Windows command-line interpreter.

It allows users, administrators, and attackers to execute commands, launch applications, and run scripts.

### How It Was Used

The workflow reviewed:

```text
CMD.EXE-89305D47.pf
```

This artifact was specifically examined because batch files (`.bat`) commonly execute through Command Prompt.

### Operational Relevance

The Prefetch artifact revealed:

```text
PlagueRat.bat
```

and preserved its full path.

This established a relationship between Command Prompt activity and the suspicious file.

---

## Windows PowerShell (POWERSHELL.EXE)

### Purpose

PowerShell is Microsoft's task automation and scripting platform.

Unlike traditional Command Prompt, PowerShell can execute scripts, interact with Windows APIs, automate administration, and perform complex system operations.

### How It Was Used

Directory-wide Prefetch searching identified:

```text
POWERSHELL.EXE
```

as an application associated with:

```text
PlagueRat.ps1
```

### Operational Relevance

PowerShell is frequently encountered during:

- system administration,
- automation,
- malware execution,
- attacker post-exploitation activity.

Finding a PowerShell artifact associated with a suspicious script increased the significance of the evidence.

---

## Notepad (NOTEPAD.EXE)

### Purpose

Notepad is a basic Windows text editor.

It is commonly used to create, view, or modify text-based files and scripts.

### How It Was Used

The directory-wide Prefetch search identified:

```text
NOTEPAD.EXE
```

as another executable associated with:

```text
PlagueRat.ps1
```

### Operational Relevance

This finding suggested the script may have been opened or reviewed through Notepad.

While this does not prove execution, it does demonstrate interaction with the suspicious file.

---

## JumpList Explorer

### Purpose

JumpList Explorer was used to review Windows Jump List artifacts.

A Jump List is a Windows artifact that records files, folders, documents, and websites recently accessed through a specific application.

### How It Was Used

Jump List artifacts were loaded from the investigation dataset and browser-related entries were reviewed.

Particular attention was given to:

```text
Microsoft Edge
```

entries.

### Operational Relevance

Jump Lists provide application-specific user activity that may not appear in other artifact sources.

During this workflow, JumpList Explorer revealed:

```text
discordapp.com
```

and associated the activity with:

```text
Microsoft Edge
```

---

## Evidence Files and Tool Mapping

| Evidence Source | Tool Used | Purpose |
|---|---|---|
| Shortcut artifacts | WFA | Identify suspicious files and archive origins |
| CMD.EXE Prefetch | PECmd | Identify execution-related file references |
| Multiple Prefetch files | PECmd | Identify applications associated with PlagueRat |
| Jump Lists | JumpList Explorer | Identify website and browser activity |

---

## Common Mistakes to Avoid

A common mistake is assuming shortcut artifacts prove execution. Shortcut files typically indicate access or existence, not necessarily execution.

Another mistake is assuming every red-highlighted PECmd result is relevant. The output should be reviewed carefully for explicit references to the keyword being investigated.

Another mistake is confusing the executable name with the Prefetch filename. The executable and the Prefetch artifact are related, but they are not the same thing.

Another mistake is reviewing only a single browser-related Jump List entry. Different entries may contain different information and should be examined individually.

---

## Suggested Screenshot Documentation

| Screenshot | Purpose |
|---|---|
| WFA shortcut analysis view | Supports shortcut findings |
| Linked Path review | Supports archive identification |
| CMD.EXE Prefetch output | Supports PlagueRat.bat finding |
| Full file path in Prefetch output | Supports path identification |
| POWERSHELL.EXE Prefetch section | Supports PowerShell association |
| NOTEPAD.EXE Prefetch section | Supports Notepad association |
| JumpList Explorer browser entry | Supports browser identification |
| JumpList URL entry | Supports domain identification |

---

## Summary

Windows File Analyzer was used to examine shortcut artifacts. PECmd was used to analyze Prefetch artifacts and identify execution-related evidence. JumpList Explorer was used to review application-specific user activity.

Together, these tools demonstrated how Windows forensic artifacts can be correlated to identify suspicious files, trace execution-related activity, and reconstruct user actions from multiple independent evidence sources.
