# Windows Endpoint Triage and System Inspection Using PowerShell

---

## Overview

This section establishes the investigative purpose of the workflow execution, the analytical techniques being exercised, and how native PowerShell capabilities support realistic endpoint triage and response scenarios.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

### Purpose and Analyst Focus

This workflow execution write-up documents a structured, repeatable approach to performing endpoint triage using only native PowerShell functionality. The primary focus is on collecting, validating, and correlating multiple categories of endpoint evidence directly from the host without relying on third-party forensic tools, EDR consoles, or graphical interfaces.

This constraint is intentional and reflects realistic incident response conditions where analysts may be limited to built-in administrative tooling, remote shell access, or restricted execution environments. By relying exclusively on native cmdlets and object-based output, the workflow execution demonstrates how meaningful investigative conclusions can still be reached using core operating system telemetry alone.

A secondary objective is to strengthen familiarity with PowerShell as an investigation language rather than treating it purely as an administrative scripting tool. Concepts such as object inspection, filtering, sorting, property expansion, and conditional logic are applied in ways that directly support investigative decision-making and evidence validation rather than system configuration tasks.

### What This Workflow Demonstrates

This workflow execution demonstrates how analysts can pivot across multiple host evidence surfaces using PowerShell pipelines and object inspection rather than isolated command execution.

Rather than executing disconnected commands, the workflow models how analysts move between evidence domains to answer investigative questions such as:

- What is currently executing on the system, and from what locations?
- Do any processes originate from unusual paths or show suspicious parent-child relationships?
- Are there indicators of unauthorized persistence via services or startup mechanisms?
- Is the system communicating externally in unexpected ways?
- Do files or services show evidence of tampering or unsigned binaries?

Each objective builds on findings from prior steps, reinforcing an investigative mindset where data from one domain informs pivots into another. This layered approach reflects how analysts escalate confidence in compromise rather than relying on single indicators.

The workflow is executed entirely through PowerShell using pipelines and object inspection, reinforcing how analysts can refine and correlate results across process, filesystem, network, service, and registry telemetry using consistent syntax and logic.

### Investigation and Detection Relevance

In enterprise SOC and incident response environments, PowerShell is frequently used during live response to:

- Identify suspicious or anomalous processes
- Validate file hashes and digital signatures
- Enumerate persistence mechanisms
- Inspect network connections and system configuration
- Collect host state rapidly when time and tooling are constrained

This workflow reflects how analysts often conduct rapid triage before escalating to deeper forensic acquisition or containment actions. The techniques demonstrated are directly transferable to real-world response scenarios where analysts must quickly determine whether a host is compromised, how persistent that compromise may be, and what additional evidence should be collected.

The workflow also reinforces detection engineering concepts, since many of the behaviors investigated here map directly to telemetry sources that drive host-based detection rules and SIEM correlations.

---

## Environment and Execution Context

This section documents the technical environment, access method, and operational constraints under which the workflow was executed, providing important context for how commands and scripts behaved during analysis.

### Execution Platform

All activity was performed on a Windows endpoint accessed through a remote shell session using PowerShell. The environment was intentionally restricted to native PowerShell tooling, simulating scenarios where analysts do not have the ability to install additional utilities or rely on graphical forensic tools.

The execution context reflects common enterprise response conditions where analysts may only have:

- Remote command-line access
- Administrative credentials
- Standard operating system tooling

Environment characteristics:

- **Operating System:** Windows endpoint (representative of enterprise workstation or server profiles)
- **Execution Method:** Remote PowerShell session simulating analyst response access
- **User Context:** Administrative privileges required for full service, registry, and process inspection

This setup reflects typical containment and triage phases of incident response where rapid host inspection is required before deeper forensic acquisition.

### Tooling Constraints

All investigative activity was performed using built-in PowerShell cmdlets and standard Windows utilities.

No third-party forensic tools, EDR consoles, scripting frameworks, or graphical interfaces were used. All filtering, sorting, and correlation was performed through object-based pipelines and cmdlet chaining.

This constraint ensures that:

- All techniques demonstrated are portable across most managed Windows systems
- Workflows remain usable in locked-down enterprise environments
- Analysts are not dependent on specialized tooling to perform meaningful triage

The exclusive use of object pipelines also reinforces PowerShell’s advantage over traditional text-based shells, allowing structured filtering and correlation across evidence domains.

### Data Sources Analyzed

The workflow inspects multiple categories of live endpoint telemetry, including:

- **Running processes** — used to identify active executables, abnormal resource usage, and suspicious execution paths  
- **File system metadata** — used to validate file locations, extensions, and potential staging directories  
- **Network configuration and active connections** — used to identify external communication and suspicious listening services  
- **Windows services** — used to detect unauthorized persistence mechanisms and modified service metadata  
- **Registry auto-start locations** — used to validate startup behavior and long-term persistence  

Each of these data sources contributes to forming a complete picture of host state rather than relying on any single indicator of compromise.

### Workflow Map (High-Level)

The workflow execution followed a structured investigative progression designed to reflect how analysts typically move through endpoint triage.

1. Establish command discovery and documentation habits so that unfamiliar commands can be quickly understood during investigations.
2. Enumerate and validate files and directories to identify potentially suspicious artifacts and understand filesystem layout.
3. Analyze running processes and file objects using object pipelines to correlate behaviors and attributes.
4. Inspect system configuration and networking to identify abnormal host settings or connectivity.
5. Validate file integrity and service state to detect tampering and persistence mechanisms.
6. Execute commands locally and remotely using scripts to demonstrate scalable investigation techniques.

Each phase is grouped by analytical objective rather than individual command usage, reinforcing how investigations are driven by questions rather than tools.

---

## Step-by-Step Execution

### Objective 1 — PowerShell Command Discovery and Built-In Help

**Goal:**  
Build confidence in discovering available commands, understanding syntax, and interpreting aliases directly within the shell. This reduces dependency on external documentation during live investigations and improves response speed.

The workflow execution began by confirming how to identify what commands are available on the system and how PowerShell categorizes different command types. Understanding this structure is critical when operating under time pressure and needing to quickly locate the correct tool for a task.

#### Objective 1.1 — Enumerating available commands and confirming core navigation

I started by enumerating all available commands to understand what capabilities were present in the environment. I treated this as the “baseline” step because before I can investigate anything else, I need to know what tools the environment actually provides. I also used this moment to re-orient myself to the shell context so I could avoid running later commands from the wrong directory or in the wrong session.

**What I did (execution actions):**
I confirmed basic PowerShell navigation and command discovery patterns that are critical under time pressure:
- I enumerated available commands and explored how PowerShell groups them by command type.
- I validated built-in help usage so I could confirm syntax and parameters without external references.
- I reviewed aliases so I could translate familiar CMD-style commands into the canonical PowerShell cmdlets.

<p align="left">
  <img src="images/powershell-endpoint-triage-basics.01.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 1</em>
</p>

**Evidence Capture Commands (examples)**
- `powershell`
- `Get-Command`
- `Get-Command -CommandType Function`
- `Get-Help Get-Date`
- `Get-Alias`
- `Find-Module -Name "PowerShell*"`
- `Install-Module -Name PowerShellGet`

#### Objective 1.2 — Understanding why I used these commands (and what they taught me)

I used `Get-Command` and `Get-Help` as my primary “documentation and discovery” loop. In a real investigation, I do not want to rely on memory for parameters—especially when the wrong parameter can produce incomplete output or cause me to miss evidence. Using built-in documentation is also realistic in restricted environments where internet access is blocked.

- **`Get-Command` and `Get-Help`** gave me immediate ways to discover and understand commands without external documentation.
  - I used `Get-Command` by itself to see everything available, and I also added filters such as `-CommandType Function` to focus on specific command types. This helped me quickly learn what was native to PowerShell versus what was defined as functions.
  - I used `Get-Help` to view syntax, parameters, and examples I could try out directly. That makes experimentation safe because I can validate expected behavior before applying a command during triage.
  - I ran `Get-Command -CommandType Function` specifically to display only available commands of type “function.” This reinforced that PowerShell environments can differ and may include additional functions depending on profile or installed modules.
  - I used `Get-Command -Name "Remove*"` to practice the `Verb-Noun` naming pattern and to see how related commands are grouped together. This matters because during investigations I usually remember intent (“remove,” “get,” “set”) before I remember exact cmdlet names.
  - I ran `Get-Alias` to connect familiar commands like `dir` to their actual PowerShell equivalents (`Get-ChildItem`), which made the transition from CMD to PowerShell more natural and reduced confusion when reading output.

- **`Find-Module` and `Install-Module`** helped me understand how PowerShell can be extended for enterprise administration tasks.
  - Even though the environment did not have internet access, I learned that I can still search for modules by partial names and wildcards when I don’t remember the exact module string.
  - For example, `Find-Module -Name "PowerShell*"` filters by the `Name` property and returns modules that begin with `PowerShell`. The wildcard (`*`) is useful for discovery when only part of a name is known.

#### Objective 1.3 — Step-by-step walkthrough of how I executed Objective 1

**(Step 1) Launching PowerShell**
I connected to the target virtual machine via SSH and started a PowerShell session. Once inside, I re-familiarized myself with PowerShell’s structure and syntax so I could work efficiently in later steps.

**(Step 2) Discovering commands and help**  
I reviewed how to locate and understand available commands using `Get-Command`, filtering by command type (functions, cmdlets, etc.), and using `Get-Help` to view syntax and examples. This reinforced the habit of using built-in help rather than relying on external documentation.

**(Step 3) Reviewing aliases and naming patterns** 
By running `Get-Alias`, I confirmed that familiar commands like `dir` and `cls` map to canonical cmdlets (`Get-ChildItem` and `Clear-Host`). Recognizing this mapping helps me translate between shells quickly and avoid misreading what a command is actually doing.

**(Step 4) Exploring modules**  
I practiced discovering and installing modules using `Find-Module` and `Install-Module`. Even in an isolated environment, understanding module discovery and installation matters because many enterprise workflows depend on modules for Active Directory, cloud automation, and admin tooling.

**Why this matters**  
This exercise helped me build the foundation for all future tasks. For example:  

This objective built the foundation for every later step. During investigations, I frequently need to run commands I do not use every day, and built-in discovery (`Get-Command`) plus documentation (`Get-Help`) makes that safe and repeatable. Aliases help bridge muscle memory from other shells, and the module system explains how PowerShell can scale to enterprise administration and security workflows.

---

### Objective 2: File & Directory Enumeration (Discovery + Validation)

**Goal:** Practice fast file discovery patterns used in endpoint triage, including recursive searches for indicators, controlled error handling to avoid noisy output, and targeted filtering by filename and extensions.

This objective focused on filesystem visibility because many malicious artifacts live in user‑writable locations. Being able to quickly enumerate directories, locate candidate artifacts (scripts, executables), and inspect contents is foundational to endpoint triage and evidence collection.

#### Objective 2.1 — Recursive searches with controlled error handling

I ran recursive searches from common roots and applied error-handling options to avoid “access denied” noise. In real endpoints, aggressive recursion can flood output with permission errors, so suppressing predictable access-denied messages keeps results usable and faster to review.

**Evidence Capture Commands (examples):**
- `Get-ChildItem C:\ -Recurse -ErrorAction SilentlyContinue`
- `Get-ChildItem C:\ -Recurse -Filter "*.exe" -ErrorAction SilentlyContinue`
- `Get-ChildItem "C:\Users" -Recurse -Filter "*.ps1" -ErrorAction SilentlyContinue`

#### Objective 2.2 — File navigation and equivalency mapping across shells (CMD/Bash/PowerShell)

I practiced navigation and file operations in PowerShell while explicitly mapping commands to equivalents I already knew from CMD and Linux Bash. This was helpful because it reinforced how I can transfer prior command-line fluency into PowerShell while still using the “PowerShell-native” cmdlets.

- **`Get-ChildItem` and `Set-Location`** allowed me to navigate directories like CMD or Bash, but with object-based output.
  - Similar to `dir` (CMD) or `ls` (Linux), `Get-ChildItem` lists files and directories. I used it both with and without a path to confirm default behavior (current working directory if no path is provided).
  - I used `Get-ChildItem -Path C:\Users` to reinforce explicit parameter usage and make the command structure unambiguous when copying steps into documentation later.

- **`New-Item`** simplified creating both files and directories in one consistent command.
  - This is the PowerShell-native way to do what might be split across commands in other shells (e.g., `mkdir` for directories, or different patterns for creating files).
  - Practicing `New-Item` reinforced how PowerShell makes filesystem object creation scriptable and consistent.

- **`Remove-Item`, `Copy-Item`, and `Move-Item`** gave me precise control over cleanup and reorganization.
  - These cmdlets map conceptually to `rm`, `cp`, and `mv` in Linux, or `del`, `copy`, and `move` in CMD.
  - The key difference is that PowerShell returns objects, which makes it easier to chain follow-on filtering and reporting steps.

- **`Get-Content`** was the core cmdlet for quickly reading text files such as logs or configuration files.
  - This is the equivalent of `cat` in Bash or `type` in CMD.
  - During incident response, this is often used to quickly inspect scripts, configuration files, and small log extracts without opening additional tools.

#### Objective 2.3 — Why this objective matters in real investigations

Revisiting file operations strengthened my efficiency with core PowerShell cmdlets. Knowing these well means I can navigate, collect evidence, or triage endpoints faster during investigations, especially when I have to do it over a remote shell.

- `New-Item` handles both files and folders, simplifying automation.  
- `Remove-Item`, `Copy-Item`, and `Move-Item` replace multiple traditional commands.  
- `Get-Content` is especially useful for reading text and log files — key in incident response and troubleshooting.

Most malicious artifacts live in user-writable locations. Fast discovery helps me:
- identify suspicious staging directories,
- find scripts or binaries used by an attacker,
- locate persistence artifacts and dropped files,
- and establish “what exists on disk” before pivoting into process, service, and network analysis.

#### Objective 2.4 — Step-by-step walkthrough of how I executed Objective 2

**(Step 1) Exploring directories**  
I used `Get-ChildItem` to view contents of directories and `Set-Location` to navigate between them. This reinforced how PowerShell supports familiar navigation patterns but returns objects that can be filtered and sorted later.

**(Step 2) Creating files and folders**  
I practiced using `New-Item` to create both files and directories. This simplified the workflow compared to other shells where different commands would be used for each type of object.

**(Step 3) Managing files**  
I tested file manipulation using `Copy-Item`, `Move-Item`, and `Remove-Item`. I treated these as “evidence handling” primitives because in real investigations I often need to copy artifacts to a safe location, move items into a staging folder, or clean up test artifacts.

**(Step 4) Reading file contents**  
I used `Get-Content` to quickly read text file contents. This is particularly useful for inspecting scripts, configuration files, and small logs that might contain indicators.

**Real-world value**  
This skill translates to log analysis, evidence collection, and forensic tasks where I need to quickly move through a file system, copy artifacts, and view contents without relying on external editors or tools.


---

### Objective 3 — Process Triage and Object-Pipeline Analysis

**Goal:** Practice object‑based investigation patterns that an analyst can use to quickly identify anomalies. This includes sorting and filtering file objects, selecting relevant properties, and searching file content for indicators, all in a way that can be reused during endpoint triage.

This objective emphasized how PowerShell pipelines pass objects (not just text), which makes it possible to filter, sort, and extract properties in a more structured way than traditional shells. I practiced process enumeration and context gathering:

- Enumerated active processes
- Captured executable paths and command lines (where available)
- Identified unusual parent-child process relationships

**Evidence Capture Commands (examples):**
- `Get-Process`
- `Get-Process | Sort-Object CPU -Descending | Select-Object -First 15`
- `Get-CimInstance Win32_Process | Select-Object Name, ProcessId, CommandLine`

#### Objective 3.1 — Building comfort with piping and object-based output

I practiced chaining commands together with pipes, sorting files by size, filtering by extension, and searching text within files. This is where PowerShell’s object-oriented design stood out the most.

- I learned that piping lets me connect commands together so the output of one becomes the input of the next, building a chain of operations.  
- The pipe symbol `|` works in Windows and Linux shells, but in PowerShell it’s more powerful because it passes objects, not just text.  
- Since those objects include properties and methods, I can filter, sort, and interact with the data directly instead of only passing along raw text.  

#### Objective 3.2 — Step 1: Sorting objects by size (Length)

I used `Get-ChildItem | Sort-Object Length` to sort files by size. I treated this as a quick anomaly-hunting technique because unusually large files in unexpected directories can indicate staging, exfil bundles, or dropped payloads.

**Commands I used (and why):**
- `Get-ChildItem | Sort-Object Length`
  - `Get-ChildItem` enumerates objects in the current directory.
  - `Sort-Object Length` sorts those objects by their `Length` property (file size in bytes), putting suspicious outliers in view quickly.

<p align="left">
  <img src="images/powershell-endpoint-triage-basics.02.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 2</em>
</p>

#### Objective 3.3 — Step 2: Filtering specific file types and name patterns

Using `Where-Object`, I filtered file objects based on properties like extension and name. This helped me narrow large listings into targeted sets—something that is critical when triaging endpoints that contain thousands of files.

**Commands I used (and why):**
- `Where-Object -Property Extension -eq ".txt"` to limit results to `.txt` files.
- `Get-ChildItem | Where-Object -Property Name -like "ship*"` to identify files with names that match a pattern.
  - The `-like` operator plus wildcard (`*`) is useful when I only know part of a filename.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.03.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 3</em>
</p>

#### Objective 3.4 — Step 3: Selecting key properties to reduce noise

I used `Select-Object` to output only the properties I needed (like file name and size). This reduces clutter and makes it easier to copy outputs into notes or reports during investigations.

**Commands I used (and why):**
- `Get-ChildItem | Select-Object Name, Length`
  - This returns a clean table of file names (`Name`) and sizes (`Length`) without extra properties.
  - During triage, this makes it easier to compare objects and spot anomalies.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.04.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 4</em>
</p>

#### Objective 3.5 — Step 4: Searching file content for keywords (Select-String)

I reviewed how `Select-String` scans files for keywords, similar to `grep` (Linux) or `findstr` (CMD). I treated this as a direct analog to scanning logs or scripts for indicators.

For instance: `Select-String -Path .\example.txt -Pattern "keyword"` returned matching lines along with their line numbers. I used `Select-String` to also look for text patterns inside files, similar to how `grep` works in Unix/Linux or `findstr` works in Windows CMD was chosen to replicate log searches. I figured this could be good in helping me locate strings of interest (like suspicious indicators).

**Commands I used (and why):**
- `Select-String -Path .\captain-hat.txt -Pattern "hat"`
  - This searches the file `captain-hat.txt` for the text `hat`.
  - The output includes matching lines and typically includes line numbers, which is useful when extracting evidence or referencing exact locations in a script/log.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.05.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 5</em>
</p>

#### Objective 3.6 — Step 5: Size-based filtering challenge (comparison operators)

To challenge myself, I built a filter to return only files above a size threshold. This reinforced comparison operators like `-gt`, `-ge`, `-lt`, and `-le`, which are commonly used when building hunts (e.g., “show me files bigger than X in user directories”).

**Commands I used (and what they mean):**
- `Get-Children | Where-Object -Property Length -gt 50` to return only files over 50 bytes.
  - `Get-ChildItem` → lists all files and directories in the current location (similar to dir in CMD or ls in Linux).
  - `|` (pipe) → takes the output of Get-ChildItem and sends it into the next command for filtering.
  - `Where-Object` → evaluates each item coming through the pipeline and only keeps those that meet a condition.
  - `-Property Length` → specifies the property to evaluate, in this case the file size (in bytes).
  - `-gt 50` → comparison operator meaning “greater than 100.” This ensures only items larger than 100 bytes are returned.

To challenge myself further, I wanted to retrieve only the items in the current directory with a size greater than 100. I approached this using the following command: 

```powershell
Get-Children | Where-Object -Property Length -gt 100
```

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.06.edited.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 6</em>
</p>

To make sure I could explain the command cleanly in reporting, I also broke down the components explicitly:

- `Get-ChildItem` → lists all files and directories in the current location (similar to dir in CMD or ls in Linux).
- `|` (pipe) → takes the output of Get-ChildItem and sends it into the next command for filtering.
- `Where-Object` → evaluates each item coming through the pipeline and only keeps those that meet a condition.
- `-Property Length` → specifies the property to evaluate, in this case the file size (in bytes).
- `-gt 100` → comparison operator meaning “greater than 100.” This ensures only items larger than 100 bytes are returned.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.07.edited.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 7</em>
</p>

#### Objective 3.7 — Why this objective matters in real investigations

Instead of manually checking each file, I automated common triage operations:
- Sorting by size helps surface unusually large or suspicious files quickly.
- Filtering by extension supports file-type-specific hunting (e.g., `.log`, `.ps1`, `.exe`).
- `Select-String` provides “native keyword hunting,” which is critical for scanning scripts and logs for indicators.

**Real-world value**  
In a SOC environment, I can use these same patterns to parse local artifacts, reduce noise, and search for suspicious strings without needing third‑party tooling.

---

### Objective 4 — System and Network Information Collection

**Goal:** Capture a host baseline (OS/hardware), identify local accounts, and collect network configuration details that are commonly used to scope incidents and correlate endpoint behavior with network telemetry.

I retrieved system information, checked for local users, and pulled detailed network configuration and IP assignments.

**Evidence Capture Commands (examples):**
- `Get-ComputerInfo`
- `Get-LocalUser`
- `Get-NetIPConfiguration`
- `Get-NetIPAddress`

#### Objective 4.1 — Collecting system information (baseline snapshot)

I ran `Get-ComputerInfo` to capture a snapshot of the machine’s hardware and OS version. I treat this as baseline evidence because it anchors later findings to a specific host configuration and can highlight anomalies tied to OS version, patch level, or device role.

**Commands I used:**
- `Get-ComputerInfo`

#### Objective 4.2 — Enumerating local users (account review)

Using `Get-LocalUser`, I reviewed which accounts existed and whether they were enabled or disabled, which let me identify which accounts exist and which accounts are enabled on the target machine (indicated by `True` or `False` under the `Enabled` column) — key for spotting suspicious or hidden users.  

**Commands I used:**
- `Get-LocalUser`

#### Objective 4.3 — Reviewing network configuration and IP assignments

I used `Get-NetIPConfiguration` and` Get-NetIPAddress` to confirm the network setup and active IPs. `Get-NetIPConfiguration` provided networking info at a glance, such as IP addresses, DNS servers, and configurations. `Get-NetIPAddress` gave me specific IP assignment details configured on the system, useful when investigating anomalies.

**Commands I used:**
- `Get-NetIPConfiguration`
- `Get-NetIPAddress`


#### Objective 4.4 — Verification Task: locating a hidden artifact in a user directory (multi-step)

At the end of this objective, I performed a targeted verification task to confirm that I could pivot from account-level context into user-profile filesystem evidence using only native PowerShell commands. I treated this the same way I would treat real-world user directory triage during an incident: begin from a known root directory, enumerate user profiles, identify any directories that do not match normal profile structure, and then inspect file contents to determine whether they represent meaningful artifacts.

This approach reflects how many endpoint investigations begin, since user home directories are common locations for dropped scripts, staging folders, and attacker-created files. Rather than searching blindly, the focus was on using structured navigation and enumeration to progressively narrow scope until the relevant artifact could be identified and validated.

The goal of this task was not simply to retrieve a value, but to demonstrate a repeatable investigation pattern:
navigate → enumerate → pivot → validate artifact content → capture evidence of completion.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.11.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 8</em>
</p>

#### Objective 4.4.1 — Step 4-a: navigate to the user root directory
I changed into `C:\Users` using `Set-Location` so I could locate the target user directory. I did this intentionally as a “known starting point” because user profiles are common staging areas for dropped files and scripts.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.08.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 9</em>
</p>

#### Objective 4.4.2 — Step 4-b: enumerate contents and pivot into the suspicious directory

I listed the contents with `Get-ChildItem` and identified a directory called `hidden-treasure-chest`. I then changed into that directory using `Set-Location`, ran `Get-ChildItem` again, and located the file `big-treasure.txt`. This is an example of how basic enumeration can quickly surface artifacts that do not match a typical user profile structure.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.09.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 10</em>
</p>

#### Objective 4.4.3 — Step 4-c: read the artifact content to retrieve the answer

I opened the file with `Get-Content big-treasure.txt` to retrieve the content. In a real investigation, this is analogous to quickly inspecting a suspicious text file, script, or note left in a staging directory to understand what it contains and whether it indicates further compromise.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.10.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 11</em>
</p>

#### Objective 4.4.4 — Step 4-d: confirmation of completion

I captured the completion state as proof that the artifact was located and read successfully. Even in real work, capturing completion evidence can be useful when documenting that a step was executed and what result it produced.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.12.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 12</em>
</p>

#### Objective 4.5 — Why this objective matters in real investigations

These commands provided a baseline snapshot and key pivots:
- `Get-ComputerInfo` captured OS/hardware baseline.
- `Get-LocalUser` provided account visibility for spotting suspicious users.
- `Get-NetIPConfiguration` and `Get-NetIPAddress` anchored the host’s network identity for correlation.

These commands provided me with:  
- A complete system snapshot (`Get-ComputerInfo`).  
- A list of all local accounts, which is critical for detecting hidden or unauthorized users.  
- Networking details including DNS and gateway configuration — the same data often checked during incident response.  

**Real-world value**  
This aligns with host auditing and reconnaissance. If I suspect persistence mechanisms or unauthorized accounts, these are among the first commands I would run to validate baseline state and identify anomalies.

---

### Objective 5 — Real-Time System Analysis and Integrity Validation

**Goal:** Review processes, services, and open connections, then validate file integrity using hashes. This objective focuses on correlating runtime behavior with previously discovered artifacts and verifying whether system components show signs of tampering.

At this stage, I transitioned from static enumeration into live system inspection. The emphasis was on understanding what the host was actively doing — which processes were running, which services were configured, and which network connections were open — and then validating whether files and services previously observed had been modified or misused.

This reflects common SOC triage flow after initial discovery: confirm whether suspicious artifacts are isolated or part of broader system activity.

**Evidence Capture Commands (examples):**
- `Get-Process`
- `Get-Service`
- `Get-NetTCPConnection`
- `Get-FileHash`

#### Objective 5.1 — Monitoring commands used and why they matter

I focused on four primary cmdlets that provide fast visibility into runtime state and common persistence mechanisms:

- `Get-Process` helped me monitor active processes, which seems to be helpful in spotting unusual resource usage.  
- `Get-Service` revealed a table of services that are running, stopped, or paused. Would be helpful in indentifying if attackers had tampered with system services.  
- `Get-NetTCPConnection` displayed all current TCP connections at the time, which essentially gives insights into both local and remote endpoints. Would be essential for identifying unauthorized or suspicious outbound network connections.  
- `Get-FileHash` allowed me to verify the integrity of key files, confirming whether they had been altered.

Together, these commands allow correlation between what is running, what is communicating, and whether key files appear to have been altered.

#### Objective 5.2 — Question set presented (evidence prompts)

In this section, I completed a set of verification checks designed to confirm that I could:

1. Generate a cryptographic hash for a previously identified artifact.
2. Identify which network connection property allows correlation back to owning processes.
3. Detect service metadata tampering through inspection of service DisplayNames.

Each verification required referencing earlier findings and validating them using live system telemetry.

**Verification Check 1 (rephrased):** What is the cryptographic hash of the previously identified artifact file?**

<p align="left">
  <img src="images/powershell-endpoint-triage-basics.13.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 13</em>

**Verification Check 2 (rephrased)** Which default property returned by `Get-NetTCPConnection` identifies the process that owns a given network connection?

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.15.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 14</em>

**Verification Check 3 (rephrased)** Which service name corresponds to a service whose DisplayName was modified to match the suspicious user’s motto?

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.16.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 15</em>
  
#### Objective 5.3 — Step 1: Artifact integrity validation using file hashing

To validate the integrity of the previously discovered artifact, I navigated back to the directory where the file was located and generated a cryptographic hash for big-treasure.txt. Hashing is commonly used during investigations to verify whether files have been modified and to support comparison against known indicators.

I executed the following sequence:

- Navigated to the target user profile using `Set-Location C:\Users\p1r4t3`.
- Listed directory contents with `Get-ChildItem` to confirm the staging folder location.
- Entered the staging directory using `Set-Location hidden-treasure-chest`.
- Generated the hash using `Get-FileHash big-treasure.txt`.

<p align="left">
  <img src="images/powershell-endpoint-triage-basics.14.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 16</em>

<p align="left">
  <img src="images/powershell-endpoint-triage-basics.13.answer.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 17 (Answer)</em>

This step demonstrates how filesystem discovery naturally flows into integrity validation during endpoint triage.

#### Objective 5.4 — Step 2: Correlating network connections to owning processes

This step required identifying which property in `Get-NetTCPConnection` output allows correlation between network connections and the processes responsible for creating them.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.15.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 18</em>

By reviewing cmdlet documentation and output structure, I confirmed that the relevant property is `OwningProcess`. This field provides the Process ID (PID) associated with each connection. That PID can then be mapped back to executable context using `Get-Process` or CIM queries, allowing analysts to determine which application initiated the traffic.

Rather than relying on guesswork, this was validated directly through documentation of default object properties.
 
 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.15.answer.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 19 (Answer)</em>

#### Objective 5.5 — Step 3: Detecting service metadata tampering

To verify whether service metadata had been altered, I enumerated services using Get-Service and reviewed DisplayName values for entries that did not match standard Windows naming conventions.

One DisplayName immediately stood out:

"A merry life and a short one" 

This aligned with the suspicious user motto observed earlier in the workflow. I then identified the underlying service name associated with that DisplayName:

**`p1r4t3-s-compass`**  

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.17.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 20</em>

This confirmed that service metadata had been modified, which is a common persistence and defense-evasion technique. Even when the service binary itself is not immediately suspicious, altered DisplayNames can indicate attacker attempts to disguise malicious services as benign system components.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.16.answer.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 21 (Answer)</em>

---

#### Objective 5.6 — Additional integrity validation note (hashing ship-flag.txt)

In addition to hashing `big-treasure.txt`, I practiced hashing another file (`ship-flag.txt`) as part of integrity validation habits. I navigated into the `captain-cabin` directory and enumerated files so I could identify the exact artifact to hash.

- I navigated into `captain\Documents\captain-cabin` using `Set-Location -Path captain\Documents\captain-cabin`.
- I listed all files with `Get-ChildItem`, which revealed three text files:
  - `captain-boots.txt`
  - `captain-hat.txt`
  - `ship-flag.txt`
- I generated a SHA256 hash for `ship-flag.txt` using `Get-FileHash -Path .\ship-flag.txt`.
- The command returned the hash value `54D2EC3C12BF3DBA25B95EB0BEB376052295AF53A22BA8E60D444880073D48F2`, which can be used to confirm authenticity or detect changes over time.

Using file hashing like this is valuable for real-time monitoring and analysis in security operations. By checking file hashes against known-good values (or comparing them to threat intelligence references), analysts can detect tampering, malware infection, or unauthorized file changes during investigations.


#### Objective 5.7 — Why this objective matters in real investigations

- `Get-Process` helps surface rogue processes via CPU/memory usage patterns.
- `Get-Service` can reveal services that are disabled, added, or modified—common persistence territory.
- `Get-NetTCPConnection` provides visibility into open connections and can be correlated back to owning processes.
- `Get-FileHash` supports integrity validation and is useful for comparing artifacts to known-good baselines or known-malicious indicators.

**Real-world value**  
This mirrors real triage work during threat hunting and incident response where validating “what is running,” “what is connecting out,” and “what has changed on disk” is often the fastest path to determining whether a host is compromised.

---

### Objective 6 — Scripting and Remote Execution

**Goal:** Practice PowerShell scripting concepts and demonstrate how to execute commands remotely using `Invoke-Command`. This supports scalable workflows where the same triage logic must be applied across multiple endpoints.

I wrapped up by practicing PowerShell scripting, focusing on how to automate repetitive tasks and even execute commands remotely with `Invoke-Command`.

**Evidence Capture Commands (examples):**
- `Get-Help Invoke-Command -examples`
- `Invoke-Command -FilePath script.ps1 -ComputerName Server01`
- `Invoke-Command -ComputerName Server01 -ScriptBlock { Get-Culture }`

**Why This Matters:**
- I learned how scripts can save time and reduce human error by automating repetitive tasks.  
- Remote execution is especially important: with `Invoke-Command`, I could push commands to multiple systems at once.  
- This is applicable in both defensive tasks (running IOC scans across a network) and offensive testing (enumerating systems).

#### Objective 6.1 — Reviewing examples for remote execution (built-in documentation)

I used `Get-Help Invoke-Command -Examples` to review real-world usage patterns. This helped me understand the different execution styles (`-FilePath` versus `-ScriptBlock`) and how remoting commands are structured.

**Commands I used:**
- `Get-Help Invoke-Command -examples`

#### Objective 6.2 — Remote execution with script files (FilePath)

I reviewed `Invoke-Command -FilePath` as a way to run full scripts remotely. In enterprise scenarios, this is useful when a script must be consistently applied across many machines (for example, running a standardized IOC scan or collecting the same set of artifacts).

**Commands I used:**
- `Invoke-Command -FilePath script.ps1 -ComputerName Server01`

#### Objective 6.3 — Remote execution with inline commands (ScriptBlock)

I reviewed `Invoke-Command -ScriptBlock` as a way to run ad-hoc commands on remote systems without deploying a full script. This is useful for quick checks like “pull service state,” “check a registry key,” or “confirm a running process” across a small set of endpoints.

**Commands I used:**
- `Invoke-Command -ComputerName Server01 -ScriptBlock { Get-Culture }`


#### Objective 6.4 — Challenge: running Get-Service on a remote host (RoyalFortune)

At the end of this objective, I performed a remote execution validation step to confirm that I could execute investigative commands on another host using PowerShell remoting. This capability is critical in SOC environments where analysts must collect data or run triage commands across multiple systems during an incident.

Rather than executing locally, I treated this as a simulation of distributed triage, where a single command can be pushed to a remote endpoint to retrieve system state without logging into each host individually.

The task was to execute the `Get-Service` cmdlet on a remote host named `RoyalFortune` and confirm that service data could be retrieved successfully.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.18.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 22</em>

I used the **Invoke-Command** cmdlet to run **Get-Service** on the remote host named *RoyalFortune*. To perform the remote execution, I used the following command:

```powershell
Invoke-Command -ComputerName RoyalFortune -ScriptBlock { Get-Service }
```

This command sends the `Get-Service` instruction to the remote host, executes it in that system’s context, and returns the output back to my session. The returned service list confirms that PowerShell remoting was functioning and that I could retrieve operational data from the remote endpoint.

 <p align="left">
  <img src="images/powershell-endpoint-triage-basics.18.answer.png" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 23 (Answer)</em>

**Why this matters**  
This demonstrates the ability to scale triage and collection tasks across systems. In real incidents, analysts often need to validate process state, service configuration, or registry settings across multiple endpoints simultaneously. Remote execution allows that work to be performed quickly and consistently without manual host-by-host access.

**Real-world value**  
This supports enterprise-scale investigation workflows, where scripts and commands can be deployed to many machines to confirm the scope of an incident, collect evidence, and support containment decisions.

---

## Results & Interpretation

Across Objectives 1–6, I moved from command discovery and filesystem visibility into pipeline-based analysis, system/network baselining, integrity validation, and remote execution. The workflow execution produced a repeatable triage routine that can be reused during investigations and adapted to different host contexts.

The most important result is that the workflow connects actions across domains rather than treating them as isolated tasks. For example, filesystem enumeration leads into hashing for integrity validation, and `Get-NetTCPConnection` results can be correlated back to processes via the `OwningProcess` PID. This “enumerate → validate → pivot” flow is the core of effective endpoint triage.

---

## Operational & Defensive Takeaways

PowerShell provides enough native capability to perform meaningful endpoint triage even in constrained environments. Object pipelines make filtering and sorting more precise than traditional text-based parsing. File hashing supports integrity validation and is a practical way to detect tampering. Service and network inspection provide fast visibility into persistence and suspicious connectivity, and `Invoke-Command` enables scaling the same triage logic across multiple systems.

---

## Reuse Pack (Quick Reference)

### Baseline and discovery

- `Get-Command` — discover available commands in the environment and confirm what tools exist on the endpoint. This reduces guesswork when moving between endpoints with different modules installed. It also helps confirm whether a cmdlet is available before relying on it during triage.
- `Get-Help <cmdlet>` — validate syntax and parameters quickly, especially when operating under time pressure. This prevents mistakes such as using the wrong parameter or misreading default output fields. It also provides examples that can be copied directly into investigation notes.
- `Get-Alias` — translate familiar shell shortcuts into canonical PowerShell cmdlets for consistent execution. This reduces confusion when reading past commands and makes documentation clearer for other readers. It also helps when converting ad-hoc command history into a reusable workflow.

### Filesystem discovery and inspection

- `Get-ChildItem -Recurse -ErrorAction SilentlyContinue` — enumerate directory trees while suppressing predictable access-denied noise. This keeps output readable and speeds up discovery on protected systems. It also helps surface artifacts in user-writable locations without getting stuck on system folders.
- `Where-Object -Property Extension -eq ".ps1"` — filter by extension when hunting scripts and artifacts. This is useful when narrowing large directory listings to a specific artifact type. It also supports repeatable hunts such as “show me all scripts in user profiles.”
- `Select-String -Path <file> -Pattern "<keyword>"` — scan file content for indicators (keywords, suspicious strings). This provides a native way to hunt for IOCs or suspicious commands within files. It is especially useful for quick triage of scripts and text logs.
- `Get-Content <file>` — quick read for text artifacts (configs, scripts, notes, log snippets). This supports fast understanding of what an artifact contains. It also helps validate whether a file should be escalated for deeper analysis.

### Process, service, and network triage

- `Get-Process` — identify running processes and baseline what is active. This helps quickly spot unexpected processes or abnormal resource usage. It also supports follow-on pivots into parent/child relationships when combined with other cmdlets.
- `Get-Service` — review service state and spot suspicious DisplayName modifications. Services are a common persistence layer and are frequently modified by attackers. Even a subtle DisplayName change can be a useful indicator when combined with other evidence.
- `Get-NetTCPConnection` — review active TCP connections and pivot via `OwningProcess` to find the responsible process. This is critical when investigating suspicious outbound connections. It also helps map network behavior to host behavior in a defensible way.

### Integrity validation

- `Get-FileHash -Path <file>` — generate hashes for integrity validation and tamper detection. Hashes provide a stable fingerprint of file content and can be compared over time. They can also be checked against known-good baselines or known-malicious indicators.

### Remote execution

- `Invoke-Command -ComputerName <host> -ScriptBlock { <cmd> }` — run ad-hoc triage commands remotely. This is useful for quick checks across a small set of endpoints. It also reduces manual effort when validating a hypothesis across multiple hosts.
- `Invoke-Command -ComputerName <host> -FilePath <script.ps1>` — execute standardized scripts remotely for scale. This supports consistent evidence collection at enterprise scale. It also reduces human error because the same script logic is applied everywhere.

---

## What I Learned (Skills Demonstrated)

This workflow execution strengthened practical endpoint triage skills using native PowerShell tooling. It reinforced command discovery habits (`Get-Command`, `Get-Help`), improved comfort with object pipelines for sorting and filtering, and built confidence with core evidence surfaces: filesystem, processes, services, and network connections.

It also reinforced integrity validation practices through hashing and introduced scalable response patterns through remoting (`Invoke-Command`). Overall, the workflow execution demonstrates that I can operate effectively in constrained environments and still produce evidence-backed, repeatable triage outputs.




