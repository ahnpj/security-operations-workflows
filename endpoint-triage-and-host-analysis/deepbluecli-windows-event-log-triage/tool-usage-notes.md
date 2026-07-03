# Tool Usage Notes — Windows Event Log Triage Using DeepBlueCLI

## Overview

This document summarizes the tools, commands, workflows, and operational relevance of the utilities used during the DeepBlueCLI event log triage workflow.

The workflow focused on:

* detecting and quantifying a password spray attack,
* mapping detected activity to a MITRE ATT&CK technique,
* attributing activity to a responsible user and hostname,
* detecting unauthorized local account creation and group assignment,
* identifying PowerShell `Net.WebClient` download cradle activity,
* isolating the most recent of multiple detections using timestamp comparison,
* performing bulk log processing across an entire evidence folder,
* redirecting console output to a text file for offline search,
* researching a named tool against MITRE ATT&CK Software pages.

---

## Primary Log Triage Framework — DeepBlueCLI

### Purpose

DeepBlueCLI is an open-source PowerShell module, written by Eric Conrad, used to analyze Windows event logs — primarily Security and PowerShell Operational logs — for indicators of common attack techniques.

Unlike manually filtering Event Viewer by Event ID, DeepBlueCLI applies pre-built detection logic directly against `.evtx` files and reports findings such as password spray clusters, suspicious account management activity, and PowerShell download cradle usage, without requiring the analyst to know every relevant Event ID in advance.

DeepBlueCLI can process:

* a single `.evtx` file,
* an entire folder of `.evtx` files at once,
* live Windows Security and other event logs on a local system (not used in this workflow, which analyzed offline `.evtx` files only).

### How It Was Used

DeepBlueCLI was executed from a PowerShell console, from within the DeepBlueCLI folder on the Desktop, and used against:

```text
.\evtx\password-spray.evtx
.\evtx\new-user-security.evtx
.\evtx\powersploit-system.evtx
```

and subsequently against the entire evidence folder:

```text
.\evtx\
```

### Operational Relevance

DeepBlueCLI served as the primary investigative tool and was responsible for:

* detecting password spray activity and reporting the number of targeted accounts,
* identifying the account and hostname responsible for that activity,
* detecting account creation and security group membership changes,
* detecting PowerShell `Net.WebClient` download activity, including source URLs,
* processing an entire folder of event logs in a single execution.

Without DeepBlueCLI, each of these findings would have required manually identifying the relevant Event IDs (such as 4625, 4720, 4732, and 4104) and constructing individual Event Viewer filters or PowerShell `Get-WinEvent` queries for each one.

---

## Password Spray Detection — DeepBlue.ps1 (Single File)

### Purpose

Running `DeepBlue.ps1` against a single `.evtx` file applies DeepBlueCLI's full set of detection modules against that specific log, including its password spray detection logic.

### How It Was Used

Command used:

```powershell
.\DeepBlue.ps1 .\evtx\password-spray.evtx
```

### Command Breakdown

| Command Element | Meaning |
|---|---|
| `.\DeepBlue.ps1` | Launches DeepBlueCLI from the current directory |
| `.\evtx\password-spray.evtx` | Specifies the target `.evtx` file to analyze |

### Operational Relevance

Against this file, the tool identified:

* the total number of unique accounts targeted (`41`),
* the account responsible for generating the authentication attempts,
* the hostname associated with the activity.

These values established both the **scope** of the attack (how many accounts) and the **attribution** (who and what host).

---

## Account Creation Detection — DeepBlue.ps1 (Single File)

### Purpose

Running `DeepBlue.ps1` against a Security event log containing account management activity surfaces DeepBlueCLI's detection logic for newly created accounts and security-enabled group membership changes.

### How It Was Used

Command used:

```powershell
.\DeepBlue.ps1 .\evtx\new-user-security.evtx
```

### Operational Relevance

Against this file, the tool identified:

* the username of a newly created local account,
* the security group that account was subsequently added to.

This finding is typically backed by Windows Security Event ID `4720` (a user account was created) and Event ID `4732` (a member was added to a security-enabled local group), which DeepBlueCLI parses and summarizes automatically rather than requiring the analyst to locate and correlate both Event IDs manually.

---

## Download Cradle Detection — DeepBlue.ps1 (Single File)

### Purpose

Running `DeepBlue.ps1` against a PowerShell Operational event log surfaces DeepBlueCLI's detection logic for suspicious PowerShell activity, including use of `Net.WebClient` to download remote files.

### How It Was Used

Command used:

```powershell
.\DeepBlue.ps1 .\evtx\powersploit-system.evtx
```

### Operational Relevance

Against this file, the tool identified **two separate** `Net.WebClient` download detections, each including a timestamp and source URL. This required comparing both detections directly rather than assuming the first one listed was the most recent, since the question specifically asked for the most recently downloaded file.

This detection is typically backed by PowerShell Operational Event ID `4104` (script block logging), which can capture the literal PowerShell command text — including full download URLs — even when the resulting file never touches disk-based logging.

---

## Bulk Log Processing and Export — DeepBlue.ps1 (Folder + Redirection)

### Purpose

Rather than targeting a single `.evtx` file, DeepBlueCLI can be pointed at an entire folder, applying its full detection logic against every event log inside it in a single execution. Combined with PowerShell's output redirection operator, the combined results can be written to a text file for offline review and search.

### How It Was Used

Command used:

```powershell
.\DeepBlue.ps1 .\evtx\ > output.txt
```

### Command Breakdown

| Command Element | Meaning |
|---|---|
| `.\DeepBlue.ps1` | Launches DeepBlueCLI |
| `.\evtx\` | Targets the entire evidence folder rather than a single file |
| `>` | Redirects PowerShell console output |
| `output.txt` | Destination file, created in the DeepBlueCLI working directory |

### Operational Relevance

This command combined the password spray, account creation, and download cradle findings from all three individual `.evtx` files into a single consolidated `output.txt` file, which could then be searched directly using Ctrl+F rather than requiring the analyst to re-run DeepBlueCLI separately against each file to look for a specific artifact.

---

## Ctrl+F Text Search

### Purpose

Standard text search (Ctrl+F) was used to search the consolidated `output.txt` export for a specific known string.

### How It Was Used

Search term used:

```text
githubusercontent
```

### Operational Relevance

This search returned three matches within `output.txt`, all referencing the same underlying file download surfaced through different parts of DeepBlueCLI's combined output. This confirmed the presence of a specific downloaded `.ps1` script referenced across the evidence set, without needing to know in advance which individual `.evtx` file it originated from.

---

## MITRE ATT&CK (Research Reference)

### Purpose

The MITRE ATT&CK website (`attack.mitre.org`) is a knowledge base of adversary tactics, techniques, and known software/tools, used here as a research reference rather than an analysis tool.

### How It Was Used

Two separate lookups were performed:

1. Searching for **"password spray"** to identify the corresponding ATT&CK sub-technique.
2. Searching for **"Mimikatz MITRE ATT&CK"** to identify the tool's formal Software ID.

### Operational Relevance

The first lookup confirmed the technique:

```text
Brute Force: Password Spraying (T1110.003)
```

The second lookup confirmed the software identifier:

```text
Mimikatz (S0002)
```

MITRE ATT&CK maintains **separate reference catalogs** for techniques (identified with a `T`-prefixed ID) and known software/tools (identified with an `S`-prefixed ID). A single finding can potentially be tagged with both — the technique category the activity falls under, and the specific known tool used to carry it out.

---

## Evidence Files and Tool Mapping

| Evidence Source            | Tool Used              | Purpose                                                   |
| ---------------------------- | ------------------------- | ------------------------------------------------------------ |
| `password-spray.evtx`       | DeepBlueCLI               | Detect password spray, count targeted accounts, attribute activity |
| `new-user-security.evtx`    | DeepBlueCLI               | Detect account creation and privileged group assignment    |
| `powersploit-system.evtx`   | DeepBlueCLI               | Detect PowerShell Net.WebClient download cradle activity   |
| `.\evtx\` (entire folder)   | DeepBlueCLI + redirection | Bulk process all event logs into a single `output.txt` export |
| `output.txt`                | Ctrl+F                    | Search consolidated findings for a known indicator          |
| MITRE ATT&CK (technique)    | Manual research            | Identify Technique ID for password spraying (`T1110.003`)  |
| MITRE ATT&CK (software)     | Manual research            | Identify Software ID for Mimikatz (`S0002`)                 |

---

## Common Mistakes to Avoid

A common mistake is assuming the first detection listed in DeepBlueCLI's output is automatically the most relevant one. When multiple detections of the same type appear (such as the two `Net.WebClient` downloads in this workflow), timestamps should always be compared directly rather than assuming output order reflects chronological order.

Another mistake is treating account creation and group membership changes as two unrelated findings. These events are usually far more meaningful when interpreted together, since a new account paired with immediate privileged group assignment is a stronger persistence indicator than either event alone.

Another mistake is running DeepBlueCLI against individual files one at a time when a known indicator (such as a domain name or filename fragment) needs to be located across an entire evidence set. Bulk export followed by keyword search is significantly faster in that scenario.

Another mistake is confusing a MITRE ATT&CK **Technique ID** with a **Software ID**. Techniques (like `T1110.003`) describe a category of adversary behavior. Software IDs (like `S0002`) identify a specific known tool. A single finding can map to both, but they are not interchangeable identifiers.

Another mistake is forgetting to quote or properly reference file paths containing spaces when running DeepBlueCLI against evidence stored outside the default working directory; this did not apply directly in this lab since the `evtx` folder was a relative subdirectory, but it is a common source of path errors in similar PowerShell-based tooling.

---

## Suggested Screenshot Documentation

| Screenshot                                       | Purpose                                             |
| --------------------------------------------------- | -------------------------------------------------------- |
| PowerShell navigation to the DeepBlueCLI folder     | Supports environment preparation                        |
| Directory listing confirming DeepBlue.ps1 and evtx folder | Supports framework/evidence access                |
| DeepBlue.ps1 output against password-spray.evtx     | Supports password spray detection and account count      |
| MITRE ATT&CK sub-technique page (Password Spraying) | Supports technique attribution                           |
| DeepBlue.ps1 output showing responsible user/host    | Supports activity attribution                            |
| DeepBlue.ps1 output against new-user-security.evtx   | Supports account creation and group assignment findings  |
| DeepBlue.ps1 output against powersploit-system.evtx  | Supports download cradle detection                       |
| Comparison of both Net.WebClient detection timestamps | Supports isolating the most recent download              |
| Bulk DeepBlue.ps1 execution against the evtx folder  | Supports bulk log processing                              |
| output.txt search results for "githubusercontent"    | Supports keyword search and script identification         |
| MITRE ATT&CK Software page (Mimikatz)                | Supports software attribution and Software ID             |

---

## Summary

DeepBlueCLI served as the primary log triage tool throughout the investigation.

Running `DeepBlue.ps1` against individual `.evtx` files surfaced password spray activity, account creation and group assignment activity, and PowerShell download cradle activity — three distinct categories of suspicious behavior across three unrelated event logs. Running the same tool against the entire evidence folder at once, combined with output redirection and Ctrl+F search, made it possible to efficiently locate a specific known indicator across the full evidence set without re-running targeted analysis file by file.

Together, these tools demonstrated how a single, purpose-built PowerShell utility can dramatically accelerate Windows event log triage, while still requiring the analyst to compare timestamps, interpret related findings together, and independently validate results against authoritative references like MITRE ATT&CK.