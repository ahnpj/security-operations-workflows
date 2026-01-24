# Tool Usage Notes — Process and Network Triage Using Windows CLI (CMD) and PowerShell

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as endpoint triage, service exposure validation, and containment decision-making.  
> - **Executions** refer to the hands-on performance of those tasks using real operating system tools, commands, and live host telemetry.  
> - **Writeups** document how tasks were performed, what investigative pivots were made, and how results were interpreted in an operational context.

This document explains how native Windows command-line utilities and PowerShell cmdlets were used to perform structured endpoint triage without relying on third‑party forensic or EDR tooling. The goal of this execution is to demonstrate how analysts can correlate filesystem artifacts, running processes, and network exposure using only built‑in operating system telemetry.

All tooling used in this execution is included with standard Windows installations. This mirrors real-world triage scenarios where analysts may be limited to remote shells, restricted endpoints, or degraded security tooling during early incident response.

---

## Execution Platform and Operating Environment

### Windows Endpoint (Virtual System)

**Purpose:** Provide a realistic endpoint environment for practicing host-based investigation and containment validation.  
**How It Was Used:** All commands were executed directly on the endpoint through terminal sessions using CMD and PowerShell. Filesystem inspection, process enumeration, and network correlation were performed against live system state.  
**Operational Relevance:** Reflects how SOC analysts and incident responders frequently interact with suspected endpoints during early-stage triage.

Environment characteristics during execution:

- **Operating System:** Windows workstation environment  
- **Execution Interfaces:** Command Prompt (CMD) and Windows PowerShell  
- **User Context:** Local administrator privileges  
- **Access Method:** Terminal-based interactive sessions  
- **Tooling Constraints:** No third-party utilities or EDR consoles used  

This constraint forces reliance on core OS telemetry sources and reinforces investigative techniques that remain viable even when advanced security tooling is unavailable.

---

## Filesystem Enumeration and Artifact Validation

Filesystem inspection is typically the first investigative layer when triaging potential compromise, as attackers often drop tools, scripts, or persistence components onto disk.

### `dir` — Directory Enumeration

```cmd
dir
```

**Purpose:** List files and directories in the current working directory.  
**How It Was Used:** Establish initial visibility into directory contents and identify the presence of the `Treasure\Hunt` folder.  
**Operational Relevance:** Quickly highlights suspicious directories, staging locations, or unexpected executables during live response.

---

### `cd` — Directory Navigation

```cmd
cd Treasure\Hunt
```

**Purpose:** Change the current working directory.  
**How It Was Used:** Pivot into the directory containing the discovered artifact to perform focused inspection.  
**Operational Relevance:** Analysts commonly pivot into suspicious directories to scope investigation and avoid broad system scans that may disrupt production workloads.

---

### `type` — File Content Inspection

```cmd
type flag.txt
```

**Purpose:** Display text file contents directly in the terminal.  
**How It Was Used:** Validate the contents of the discovered file artifact without launching additional applications.  
**Operational Relevance:** Allows analysts to review dropped notes, scripts, or configuration files quickly during live investigations while minimizing system interaction.

---

## Process Enumeration and PID Collection

After confirming artifacts on disk, the next investigative step is determining whether related executables are actively running in memory.

### `tasklist` — Process Inventory

```cmd
tasklist
```

**Purpose:** Display all running processes on the system.  
**How It Was Used:** Establish a baseline view of system activity before isolating specific executables.  
**Operational Relevance:** Helps identify abnormal process counts, unfamiliar executables, and potential malware masquerading as legitimate services.

---

### `tasklist` with Filters — Process Isolation

```cmd
tasklist /FI "imagename eq sshd.exe"
```

**Purpose:** Filter the process list by executable name.  
**How It Was Used:** Isolate all running instances of `sshd.exe` and capture associated PIDs.  
**Operational Relevance:** Enables targeted correlation and containment actions when multiple service workers or instances are present.

Filtering avoids reliance on visual scanning and reduces the chance of missing relevant processes during high-noise investigations.

---

## Network Enumeration and Process-to-Port Correlation

Network exposure is a critical risk factor during endpoint triage. Mapping listening ports to owning processes helps determine which services are externally reachable.

### `netstat -abon` — Network Exposure Mapping

```cmd
netstat -abon
```

**Purpose:** Display all network connections and listening ports with owning executables and PIDs.  

**Flag Breakdown:**  
- `-a` — show all connections and listening ports  
- `-b` — show the executable responsible for each connection  
- `-o` — show owning process ID  
- `-n` — display numeric addresses and ports

**How It Was Used:** Confirm that `sshd.exe` was listening on TCP port 22 and identify which PID owned the listening socket.  
**Operational Relevance:** Provides direct correlation between exposed services and responsible processes without relying on EDR or firewall logs.

This step is essential when validating whether suspicious executables are actively exposing services to the network.

---

## Targeted Containment Actions

Containment during live response must be deliberate to avoid unnecessary disruption or loss of evidence.

### `taskkill` by PID — Controlled Process Termination

```cmd
taskkill /PID 540
```

**Purpose:** Terminate a specific process instance using its PID.  
**How It Was Used:** Demonstrate containment of a single `sshd.exe` instance rather than stopping the entire service.  
**Operational Relevance:** Allows analysts to reduce exposure while preserving legitimate services when multiple instances exist.

PID-based termination is safer than service-wide stops during uncertain investigation phases.

---

## Post-Containment Validation

Containment actions must always be followed by validation to confirm impact.

### Re-running Process Filters

```cmd
tasklist /FI "imagename eq sshd.exe"
```

**Purpose:** Confirm whether targeted processes are still running.  
**How It Was Used:** Verify that only the intended PID was terminated and other service instances remained.  
**Operational Relevance:** Prevents false assumptions about service state following containment.

---

### Re-running Network Enumeration

```cmd
netstat -abon
```

**Purpose:** Validate whether network exposure changed after containment.  
**How It Was Used:** Confirm that port 22 remained listening under a different PID.  
**Operational Relevance:** Ensures containment actions had measurable and expected effects.

Without re-validation, analysts may incorrectly assume that exposure has been eliminated.

---

## PowerShell Correlation Opportunities

Although most steps were performed using CMD, PowerShell supports more structured and scriptable versions of the same correlations.

### Process Filtering Using Objects

```powershell
Get-Process | Where-Object { $_.ProcessName -like "sshd*" }
```

**Operational Value:** Enables filtering using object properties rather than parsing string output, which supports scripting and reporting workflows.

---

### Network-to-Process Correlation

```powershell
Get-NetTCPConnection | Where-Object { $_.LocalPort -eq 22 }
Get-Process -Id <PID>
```

**Operational Value:** Allows programmatic correlation between listening ports and owning processes, enabling automated triage scripts and response playbooks.

PowerShell is commonly used in enterprise IR automation and SOAR tooling.

---

## Investigative Interpretation Guidance

Indicators that would warrant escalation during real investigations include:

- Services listening on unexpected or unauthorized ports  
- Executables running from user-writable directories  
- Processes that immediately respawn after termination  
- Network exposure inconsistent with asset role

These signals may indicate persistence mechanisms, unauthorized services, or active exploitation.

---

## Performance and Evidence Considerations

When performing live triage:

- Avoid excessive repeated `netstat` scans on heavily loaded systems  
- Record PIDs, timestamps, and observed ports before containment  
- Avoid mass service termination without understanding dependencies  

CMD utilities provide minimal logging, so analysts must manually capture evidence during investigations.

---

## Extension into Full Incident Response

This workflow represents early-stage triage. Full IR would expand into:

- Event log review using `wevtutil` or `Get-WinEvent`  
- Service configuration review using `sc query`  
- Scheduled task inspection using `Get-ScheduledTask`  
- Registry autorun analysis  
- Memory and disk forensic acquisition

These steps would be executed after initial exposure validation and containment.

---

## Summary of Tools, Platforms, and Data Sources

- **Platform:** Windows workstation endpoint  
- **Execution Interfaces:** CMD and PowerShell  
- **User Context:** Local administrator  
- **Filesystem Tools:** dir, cd, type  
- **Process Tools:** tasklist, taskkill  
- **Network Tools:** netstat  
- **PowerShell Cmdlets:** Get-Process, Get-NetTCPConnection, Where-Object  
- **Data Sources:** Live filesystem, running processes, active network listeners  
- **Execution Method:** Interactive command-line investigation  
- **Analysis Method:** Artifact discovery, PID correlation, network exposure validation, and post-containment verification

These tools collectively support realistic endpoint triage workflows used by SOC analysts and incident responders during early-stage investigations.
