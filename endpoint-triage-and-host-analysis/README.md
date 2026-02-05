# Endpoint Triage & Host Analysis

(`endpoint-analysis-and-triage/README.md`)

This folder contains workflow executions where the **primary focus is validating host state, investigating suspicious behavior, and collecting system artifacts during endpoint-focused security operations**. These workflows simulate how SOC analysts and security engineers perform targeted host-level validation using repeatable, tool-driven methods.

Rather than presenting abstract techniques, each folder documents practical execution of specific endpoint triage tasks, including the commands used, the reasoning behind each step, and how results are interpreted in an investigative context. The emphasis is on operational execution and analyst decision-making, similar to what would be captured in internal SOC runbooks and analyst working notes.

Each folder uses a tool-first naming convention followed by the primary operational focus (for example, `powershell-endpoint-triage-and-system-inspection` or `bash-linux-endpoint-triage-and-system-inspection`). This allows quick identification of technologies being used while documentation itself emphasizes analytical workflow and investigative reasoning rather than tool features.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational execution scenario** documenting how endpoint triage or host validation tasks are performed from initial objective through execution and interpretation.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how commands, queries, and validation steps were performed in realistic investigative order.

> 👉 **See what files exist in each workflow folder**  
For a complete breakdown of every standard file included in a workflow execution — including what each document contains, how it supports automation and detection engineering workflows, and how the documentation is structured — see the **[Workflow Documentation Structure](#workflow-documentation-structure)** section below.

> 👉 **See which workflow executions currently exist in this category**  
For a list of all automation and log processing executions currently included in this folder, along with a description of what each workflow demonstrates and which operational skills it focuses on, see the **[What’s in This Folder](#whats-in-this-folder)** section below.

> **Terminology used:**  
> **Workflows** refer to common security operations tasks (such as log normalization or detection preparation).  
> **Executions** refer to the hands-on performance of those tasks using scripts, tools, and real datasets.  
> **Writeups** document how the task was performed and how outputs were validated and interpreted.

---

### How Workflows Are Categorized

Workflows are grouped here when **host validation, endpoint triage, and system artifact inspection are the primary operational objectives**, rather than alert triage or full incident reconstruction.

Although these activities frequently support investigations and incident response, they are categorized separately because their primary purpose is rapid host-level validation and system health assessment before deeper investigative or containment actions occur.

Workflows in this category typically focus on:

- **Process and service inspection**, reviewing running processes, parent-child relationships, and service states to identify suspicious execution patterns or unauthorized persistence mechanisms.

- **File system and artifact triage**, examining directories, binaries, scripts, scheduled tasks, and configuration files to detect abnormal changes or potential malware placement.

- **Command-line and shell-based investigation**, using native operating system utilities and scripting to collect system state without relying on heavyweight forensic tooling.

- **Initial response validation actions**, confirming whether indicators of compromise are isolated to a single system or suggest broader environmental exposure.

Although these workflows support investigations, they are organized here when the **core work involves validating host integrity and collecting system artifacts rather than reconstructing a full incident.**

---

### Operational Context and Purpose

Endpoint triage and host analysis workflows typically occur early during alert investigation or incident scoping, when analysts must quickly determine whether a system shows signs of compromise, misconfiguration, or policy violations.

At this stage of security operations, the objective is not to perform full forensic reconstruction but to rapidly validate host health and behavior. Analysts focus on identifying suspicious execution patterns, persistence mechanisms, unauthorized configuration changes, or indicators suggesting broader environmental exposure.

The execution examples in this category reflect real SOC and security engineering tasks where targeted host-level validation determines whether escalation, containment, or deeper investigative analysis is required.

---

### What’s in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete operational endpoint triage scenario**, including execution walkthroughs, analytical reasoning, validation outcomes, and tooling references.

Current workflow executions include:

- **Windows Endpoint Triage and System Inspection Using PowerShell**  
  (`powershell-endpoint-triage-and-system-inspection`)  
  Focuses on collecting and validating host state on Windows systems using native PowerShell commands. Tasks include inspecting running processes, services, network connections, user sessions, and configuration artifacts to assess suspicious behavior or policy violations.

- **Process and Network Triage Using Windows CLI (CMD) + PowerShell**  
  (`cmd-windows-process-and-network-triage`)  
  Uses standard Windows command-line utilities to inspect processes, services, and network activity without PowerShell. Demonstrates effective host validation techniques in restricted or limited-access environments.

- **Linux Endpoint Triage and Script-Based Validation Logic Using Bash**  
  (`bash-linux-endpoint-triage-and-validation-scripting`)  
  Demonstrates host-based triage on Linux systems using native shell utilities. Tasks include reviewing running processes, scheduled jobs, active network connections, file system artifacts, and user activity to validate system integrity and detect anomalies.

Additional workflow executions will be added as endpoint triage techniques expand across operating systems, tooling, and detection support use cases.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how endpoint triage and host validation tasks are performed in operational SOC environments.

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|--------------------|
| **`workflow-execution.md`** | Operational execution walkthrough | Step-by-step endpoint triage workflow showing commands, tool usage, investigative pivots, and validation checkpoints |
| **`README.md`** | Workflow context and operational objective | Describes the host validation or triage objective, workflow scope, and how the execution supports detection and investigation workflows |
| **`analyst-notes.md`** | Analytical reasoning and operational considerations | Documents why specific triage approaches were selected and how collected host data supports investigative conclusions |
| **`tool-usage-notes.md`** | Command and tool reference documentation | Explains command syntax, filter logic, system utilities, and operational usage patterns relevant to similar host investigations |
| **`automation-design-notes.md`** *(when present)* | Automation and scalability planning | Documents scripting approaches, triage automation strategies, and design considerations for scaling or integrating host inspection into detection workflows |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains command outputs, system snapshots, artifact validation results, and visual confirmation of investigative findings |

Together, these files separate **endpoint triage execution**, **analytical reasoning**, **tool reference**, and **automation planning** into clearly reviewable components while remaining tied to the same operational workflow.

---

### How These Workflow Executions Are Designed

This category is **execution-focused**, not theoretical.

You will find:

- Realistic endpoint triage execution walkthroughs
- Command-line and scripting-based host inspection techniques
- Validation checkpoints and investigative interpretation of system artifacts
- Detection support and monitoring improvements tied to host validation outcomes
- Operational decision-making behind triage methodology and investigative pivots

Each workflow demonstrates not just how host validation is performed, but why specific triage strategies are critical for rapid alert response, investigation scoping, and incident readiness.

Documentation is intentionally structured to resemble internal SOC triage procedures and analyst runbooks rather than tutorial-style instruction.

---

### Relationship to Full Incident Investigations

Endpoint triage workflows directly support investigations but do not represent complete incident case studies on their own.

In operational environments, host validation tasks documented here are frequently performed during broader response efforts, including malware confirmation, persistence validation, lateral movement artifact identification, and configuration drift verification.

Full incident lifecycle investigations — including alert context, timeline reconstruction, MITRE ATT&CK mapping, artifact correlation, root cause analysis, and remediation planning — are documented separately in the:

`incident-response-and-investigations` repository

This separation reflects how professional security teams distinguish between triage procedures and full investigative case documentation.

---

### Ongoing Development

Workflows in this category are continuously expanded as new triage techniques, operating system artifacts, tooling approaches, and detection validation methods are explored.

Some workflow directories may appear iterative or incomplete. This reflects active development and refinement rather than unfinished work. In production security environments, triage procedures evolve as telemetry sources, operating systems, and detection strategies change.

The focus is on building a growing library of reusable endpoint validation and host analysis techniques rather than static one-time demonstrations.


