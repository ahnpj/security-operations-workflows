# Tool Usage Notes — Windows Endpoint Triage and System Inspection Using PowerShell

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as endpoint triage, evidence collection, and validation of host integrity.  
> - **Executions** refer to the hands-on performance of those tasks using real operating system tools, cmdlets, and live host telemetry.  
> - **Writeups** document how tasks were performed, what investigative pivots were made, and how results were interpreted in an operational context.

This document serves as a technical reference for the PowerShell cmdlets, object pipelines, and investigative correlations used during the endpoint triage execution. It is intended to function as a mini runbook that explains not only which commands were used, but why they were selected and how their output supports security decision-making.

All tooling in this execution is native to Windows and PowerShell. No third-party forensic utilities, EDR consoles, or graphical tools were used. This reflects real-world scenarios where analysts may only have remote shell access and built-in administrative tooling during early incident response.

---

### Execution Platform and Operating Environment

#### ▶ Windows Endpoint (Remote PowerShell Session)

**Purpose:** Provide a realistic environment for practicing live endpoint investigation using only native operating system telemetry.  
**How It Was Used:** All commands were executed in PowerShell sessions connected to the endpoint. Filesystem, process, service, network, and registry-related telemetry were collected directly from the host.  
**Operational Relevance:** Mirrors common SOC and IR workflows where analysts remotely triage endpoints before deploying deeper forensic tooling.

Environment characteristics:

- **Operating System:** Windows endpoint (enterprise workstation or server profile)  
- **Execution Interface:** Windows PowerShell console  
- **User Context:** Administrative privileges  
- **Access Method:** Remote shell session  
- **Tooling Constraints:** Built-in cmdlets only, no third-party software

This constraint reinforces skills that remain viable even when security tooling is unavailable or degraded.

---

### Command Discovery and Built-In Documentation

Understanding which tools are available on the host is critical when operating in constrained or unfamiliar environments.

#### ▶ Get-Command — Discovering Available Cmdlets

```powershell
Get-Command
Get-Command -CommandType Function
Get-Command -Name "Get-*"
```

**Purpose:** Enumerate available cmdlets and functions on the system.  
**How It Was Used:** At the beginning of the execution to confirm that networking, process, and service-related cmdlets were present before attempting deeper investigation.  
**Operational Relevance:** Prevents wasted time attempting to use unavailable modules on hardened or restricted systems.

This step also reinforces PowerShell’s Verb-Noun naming convention, which helps analysts discover related commands based on intent.

#### ▶ Get-Help — On-Host Documentation

```powershell
Get-Help Get-Process
Get-Help Invoke-Command -Examples
```

**Purpose:** Review syntax, parameters, and usage examples directly on the endpoint.  
**How It Was Used:** To validate correct usage of cmdlets prior to execution, particularly for remoting commands.  
**Operational Relevance:** During incidents, internet access may be blocked, making built-in documentation essential.

Using `-Examples` accelerates learning by providing copy‑ready command patterns.

---

### Filesystem Enumeration and Artifact Discovery

Filesystem inspection is often the fastest way to identify dropped tools, scripts, or persistence components.

#### ▶ Get-ChildItem — Directory and File Enumeration

```powershell
Get-ChildItem
Get-ChildItem -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\Users -Recurse -File
```

**Purpose:** Enumerate directories and locate files that may represent attacker artifacts.  
**How It Was Used:** To explore user profiles and identify suspicious staging folders and files.  
**Operational Relevance:** User-writable locations are common targets for malware and persistence mechanisms.

**Performance Consideration:** Recursive scans should be scoped to avoid excessive disk traversal on production systems.

#### ▶ Get-Content — File Inspection

```powershell
Get-Content .\script.ps1
```

**Purpose:** View file contents directly in the console.  
**How It Was Used:** To inspect text artifacts such as scripts and notes before any execution.  
**Operational Relevance:** Prevents accidental execution of malicious scripts during response.

For large files, output can be piped to `Select-Object -First` to avoid flooding the terminal.

#### ▶ Select-String — Keyword Hunting

```powershell
Select-String -Path .\script.ps1 -Pattern "Invoke"
```

**Purpose:** Search file contents for suspicious keywords and functions.  
**How It Was Used:** To validate whether discovered scripts contained execution or networking logic.  
**Operational Relevance:** Supports IOC discovery in scripts, logs, and configuration files.

Outputs include line numbers, which helps document evidence locations in reports.

---

### Object-Based Filtering and Data Reduction

PowerShell pipelines pass structured objects, enabling more reliable filtering than text parsing.

#### ▶ Where-Object — Property-Level Filtering

```powershell
Where-Object { $_.Extension -eq ".exe" }
Where-Object { $_.Length -gt 1MB }
```

**Purpose:** Filter objects using properties such as size, extension, or name.  
**How It Was Used:** To isolate large or executable files that warranted closer inspection.  
**Operational Relevance:** Reduces noise and accelerates anomaly detection during triage.

#### ▶ Sort-Object — Outlier Detection

```powershell
Sort-Object Length -Descending
Sort-Object CPU -Descending
```

**Purpose:** Rank files or processes to surface abnormal values.  
**How It Was Used:** To prioritize investigation of unusually large files and high-CPU processes.  
**Operational Relevance:** Outliers often correlate with malware staging or cryptomining.

#### ▶ Select-Object — Evidence Scoping

```powershell
Select-Object Name, Length, FullName
```

**Purpose:** Reduce output to only fields needed for analysis and documentation.  
**How It Was Used:** Before capturing evidence screenshots and notes.  
**Operational Relevance:** Supports clean reporting and ticketing workflows.

---

### Process, Service, and Network Correlation

#### ▶ Get-Process — Runtime Process Enumeration

```powershell
Get-Process
```

**Purpose:** Identify active processes and resource usage.  
**How It Was Used:** To detect unexpected executables and correlate with network activity.  
**Operational Relevance:** Rogue or high-resource processes often indicate compromise.

For deeper context, CIM queries can expose command-line arguments and parent processes.

#### ▶ Get-Service — Persistence and Startup Analysis

```powershell
Get-Service
```

**Purpose:** Enumerate services and execution state.  
**How It Was Used:** To identify suspicious service DisplayName modifications and unauthorized services.  
**Operational Relevance:** Services are a common attacker persistence technique.

Both `Name` and `DisplayName` must be reviewed, as attackers may camouflage services.

#### ▶ Get-NetTCPConnection — Active Network Sessions

```powershell
Get-NetTCPConnection
```

**Purpose:** Identify listening ports and outbound connections.  
**How It Was Used:** To detect suspicious external communication and service exposure.  
**Operational Relevance:** Network visibility helps identify C2 traffic and unauthorized services.

#### ▶ PID Pivot — Linking Network to Process

```powershell
Get-Process -Id <PID>
```

**Purpose:** Correlate network connections to owning executables.  
**How It Was Used:** To identify which process created a suspicious connection.  
**Operational Relevance:** Attribution is necessary before containment decisions are made.

---

### File Integrity Validation

#### ▶ Get-FileHash — Cryptographic Verification

```powershell
Get-FileHash suspicious.txt
```

**Purpose:** Generate SHA256 hashes for artifacts.  
**How It Was Used:** To verify integrity of discovered files and capture forensic identifiers.  
**Operational Relevance:** Supports IOC sharing, malware identification, and timeline analysis.

Hashes allow comparison against known-good baselines or threat intelligence feeds.

---

### Remote Endpoint Validation and Scale

#### ▶ Invoke-Command — Distributed Triage

```powershell
Invoke-Command -ComputerName HOST -ScriptBlock { Get-Service }
Invoke-Command -ComputerName HOST -FilePath triage.ps1
```

**Purpose:** Execute commands and scripts on remote endpoints.  
**How It Was Used:** To demonstrate how triage steps can be applied across additional systems.  
**Operational Relevance:** Enables enterprise-wide validation when multiple endpoints may be affected.

**Prerequisites:** WinRM enabled, network connectivity, valid credentials.  
**Safety Note:** Execution should be rate-limited and logged during active incidents.

---

### Output Interpretation and Investigative Guidance

Indicators that would typically warrant escalation:

- Executables running from user profile directories  
- Services with altered DisplayNames or non-standard binaries  
- Outbound connections from non-browser processes  
- Hash mismatches against known-good baselines

In this execution, such indicators were treated as investigative leads rather than automatic proof of compromise.

---

### Performance and Evidence Handling Considerations

During live response:

- Avoid unscoped recursive filesystem searches  
- Capture evidence before containment actions  
- Prefer targeted queries over full system enumeration

PowerShell can easily generate excessive telemetry if not carefully scoped.

---

### Tools That Extend This Workflow into Full IR

Additional tooling often used after triage:

- `Get-WinEvent` — authentication and service logs  
- `Get-ScheduledTask` — persistence detection  
- Registry autorun inspection  
- Memory acquisition tools  
- EDR telemetry and isolation controls

These tools complement the discovery techniques demonstrated here.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Windows endpoint  
- **Shell:** Windows PowerShell  
- **User Context:** Administrative privileges  
- **Filesystem Tools:** Get-ChildItem, Get-Content, Select-String  
- **Process Tools:** Get-Process, CIM queries  
- **Service Tools:** Get-Service  
- **Network Tools:** Get-NetTCPConnection  
- **Integrity Tools:** Get-FileHash  
- **Remote Execution:** Invoke-Command  
- **Data Sources:** Live filesystem, running processes, services, network connections, registry-backed startup mechanisms  
- **Execution Method:** Interactive and remote PowerShell sessions  
- **Analysis Method:** Object-based filtering, correlation, and integrity validation

These tools collectively support structured endpoint triage workflows commonly used in SOC and incident response operations.

