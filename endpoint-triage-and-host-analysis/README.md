# Endpoint Triage & Host Analysis

(`endpoint-analysis-and-triage/README.md`)

This folder contains workflow executions and supporting documentation focused on **validating host state, investigating suspicious behavior, and collecting system artifacts during endpoint-focused security operations**. These workflows simulate how SOC analysts and security engineers perform targeted host-level validation using repeatable, tool-driven methods.

Rather than presenting abstract techniques, each folder documents practical execution of specific endpoint triage tasks, including commands used, analyst reasoning behind each step, and interpretation of results within an investigative context. The emphasis is on operational execution and analyst decision-making, similar to internal SOC runbooks and analyst working notes.

Each folder uses a tool-first naming convention followed by the primary operational focus (for example, `powershell-endpoint-triage-and-system-inspection` or `bash-linux-endpoint-triage-and-system-inspection`). This allows quick identification of technologies being used while keeping documentation centered on investigative workflow and analytical reasoning rather than tool functionality.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational execution scenario** documenting how endpoint triage or host validation tasks are performed from initial objective through execution and interpretation.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how commands, queries, and validation steps were performed in realistic investigative order.

> **Terminology used:**  
> **Workflows** refer to common security operations tasks (such as log normalization or detection preparation).  
> **Executions** refer to the hands-on performance of those tasks using scripts, tools, and real datasets.  
> **Writeups** document how the task was performed and how outputs were validated and interpreted.

---

### Terminology Used in This Category

- **Workflows** refer to common security operations tasks such as endpoint triage, system validation, or artifact collection.
- **Executions** refer to hands-on performance of those tasks using real tools, commands, and datasets.
- **Writeups** document how tasks were performed and how results were interpreted from an investigative perspective.

---

### Operational Context and Purpose

Endpoint triage and host analysis are typically performed early during alert investigation or incident scoping, when analysts must quickly determine whether a system shows signs of compromise, misconfiguration, or policy violations.

At this stage, the objective is not full forensic reconstruction but rapid validation of system health and behavior. Analysts focus on identifying suspicious execution patterns, persistence mechanisms, unauthorized configuration changes, or indicators suggesting broader environmental exposure.

The workflow executions in this category reflect this reality by focusing on targeted host-level validation tasks that help determine whether deeper investigation, containment, or escalation is required.

---

### Scope of Workflows in This Category

Workflow executions in this category focus on host-level investigation and validation across both Windows and Linux environments. These tasks typically occur during alert triage or early incident scoping when analysts need to rapidly assess system integrity and operational risk.

Common objectives demonstrated in these workflows include:

- **Process and service inspection**, reviewing running processes, parent-child relationships, and service states to identify suspicious execution behavior or unauthorized persistence mechanisms.

- **File system and artifact triage**, examining directories, binaries, scripts, scheduled tasks, and configuration files to detect abnormal modifications or potential malware placement.

- **Command-line and shell-based investigation**, leveraging native operating system tools and scripting techniques to collect system state without relying on heavyweight forensic tooling.

- **Initial response validation activities**, confirming whether indicators of compromise are isolated to a single system or suggest broader environmental exposure.

These tasks reflect routine endpoint validation activities performed by SOC analysts and security engineers while responding to alerts, supporting detections, or verifying defensive control effectiveness.

---

### What’s in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete operational endpoint triage scenario**, including execution walkthroughs, analytical reasoning, tooling references, and validation outcomes.

Current workflow executions include:

- **PowerShell Endpoint Triage and System Inspection**  
  (`powershell-endpoint-triage-and-system-inspection`)  
  Collects and validates Windows host state using native PowerShell commands. Tasks include inspecting running processes, services, network connections, user sessions, and configuration artifacts to determine whether suspicious activity or policy violations exist.

- **CMD Windows Process and Network Triage**  
  (`cmd-windows-process-and-network-triage`)  
  Uses standard Windows command-line utilities to inspect processes, services, and network activity without PowerShell. Demonstrates effective host validation techniques in restricted or limited-access environments.

- **Bash Linux Endpoint Triage and System Inspection**  
  (`bash-linux-endpoint-triage-and-system-inspection`)  
  Performs host triage on Linux systems using native shell utilities. Tasks include reviewing running processes, scheduled jobs, network connections, file system artifacts, and user activity to validate system integrity and detect anomalies.

Additional workflow executions may be added as endpoint triage techniques expand across operating systems, tooling, and detection support use cases.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how endpoint triage procedures are documented in operational SOC environments.

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|--------------------|
| **`workflow-execution.md`** | Operational execution walkthrough | Step-by-step endpoint triage execution including commands, queries, filters, scripts, screenshots, and validation checkpoints |
| **`README.md`** | Workflow context and operational objective | Describes the security objective, investigative context, skills exercised, and relationship to real SOC or security engineering responsibilities |
| **`analyst-notes.md`** | Analytical reasoning and operational insights | Documents why specific investigative approaches were selected, how results should be interpreted, detection and monitoring implications, and potential blind spots or false positives |
| **`tool-usage-notes.md`** | Tool reference and command documentation | Explains command options, filter logic, field definitions, syntax nuances, and practical usage considerations applicable to similar investigations |
| **`automation-design-notes.md`** *(when present)* | Automation and scalability planning | Documents scripting logic, parsing strategies, pipeline design considerations, and planning for reuse or integration into detection automation workflows |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains command outputs, system views, dataset validation results, and visual confirmation supporting workflow execution |

Together, these files separate **execution methodology**, **analytical reasoning**, **tool reference**, and **automation planning** into clearly reviewable components while remaining tied to the same operational workflow.

---

### How These Workflow Executions Are Designed

This category is **execution-focused**, emphasizing repeatable investigative procedures rather than theoretical instruction.

You will find:

- Realistic endpoint triage execution walkthroughs
- Command-line and scripting-based host inspection techniques
- Validation checkpoints and investigative interpretation of results
- Detection support and defensive insights derived from host validation
- Operational decision-making explaining why specific triage approaches are used

Each workflow demonstrates not only how host validation tasks are performed, but why those tasks are critical for rapid alert triage, investigation scoping, and incident response readiness.

Documentation is intentionally structured to resemble internal SOC procedural runbooks and analyst field notes rather than tutorial-style content.

---

### Relationship to Full Incident Investigations

Endpoint triage workflows directly support investigations but do not represent complete incident case studies on their own.

In operational environments, host validation tasks documented here are frequently performed during broader response efforts, including malware confirmation, persistence validation, lateral movement artifact detection, and configuration drift verification.

Full investigative case studies — including alert context, timeline reconstruction, MITRE ATT&CK mapping, root cause analysis, artifact correlation, and remediation planning — are documented separately in the:

`incident-response-and-investigations` repository

This separation reflects how professional SOC teams distinguish between reusable operational procedures and complete incident case reporting.

---

### Ongoing Development

Workflow executions in this category are continuously expanded as new triage techniques, operating system artifacts, tooling approaches, and detection validation methods are explored.

Some workflow directories may appear iterative or incomplete. This reflects active development and refinement rather than unfinished work. In professional security operations environments, triage procedures evolve as telemetry sources, operating systems, and detection strategies change.

The focus is on building a growing library of reusable endpoint validation techniques rather than static, one-time demonstrations.

