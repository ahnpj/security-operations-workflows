# Windows Endpoint Triage and System Inspection Using PowerShell — Operational Execution

**Category:** Endpoint Triage & Host Investigation  
**Operational Focus:** Process inspection, filesystem validation, service analysis, network inspection, integrity validation, and remote execution using PowerShell  
**Primary Tools & Platforms:** Windows PowerShell, native Windows telemetry and services

This directory contains a complete, operationally structured record of a Windows endpoint triage execution performed entirely using native PowerShell functionality. The directory contains the execution writeups, screenshots, command outputs, and supporting analysis that together demonstrate how analysts can collect and correlate multiple categories of host evidence without relying on third-party forensic tools or graphical interfaces.

The goal of the execution is to practice structured endpoint investigation using object-based pipelines, where evidence from one telemetry domain is used to guide pivots into others. This reflects how analysts validate whether suspicious artifacts, services, or network activity represent meaningful compromise rather than isolated anomalies.

All investigative steps are performed using built-in PowerShell cmdlets and object inspection rather than text parsing, reinforcing how PowerShell supports deeper correlation across host telemetry using structured data.

Throughout this execution, I intentionally treated PowerShell as an investigation language rather than as an administrative scripting tool. I focused on using pipelines, property inspection, and filtering logic to answer investigative questions rather than to perform system configuration.

---

## How to Read This Folder

This folder is structured to separate execution steps from analytical reasoning and scripting reference material, similar to how SOC teams maintain investigation notes alongside reusable tooling documentation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to security operations tasks such as host triage, persistence validation, and integrity checking.  
> - **Executions** refer to the hands-on performance of those tasks using real systems and commands.  
> - **Writeups** document how tasks were performed, what decisions were made, and how results were interpreted operationally.

- The full operational walkthrough is recorded in **`workflow-execution.md`**.  
- This `README.md` explains investigative goals, methodology, and how evidence sources are correlated.  
- Supporting files capture analyst reasoning and technical command references separately from the execution timeline.

---

## What This Execution Is Specifically Doing

This execution models how analysts perform structured host triage using PowerShell to pivot across multiple evidence layers in a repeatable way.

First, the workflow begins with command discovery and documentation review using built-in help features. This establishes confidence that required commands and parameters can be identified quickly during investigations without external references.

Next, the execution moves into filesystem enumeration, including recursive searches, targeted filtering by file type and naming patterns, and direct inspection of file contents. This allows identification of suspicious artifacts and validation of their locations in user-accessible directories.

After establishing filesystem visibility, the workflow pivots into process analysis using object pipelines. Active processes are enumerated, sorted by resource usage, and inspected for suspicious execution paths or unusual characteristics. This step helps determine whether discovered artifacts are actively executing or whether anomalies exist in runtime behavior.

The workflow then expands into system and network inspection. System configuration, local user accounts, IP configuration, and active network connections are collected to establish host baseline and identify abnormal connectivity or account usage.

Following that, integrity validation steps are performed by hashing files and reviewing service metadata. This allows detection of tampering, unauthorized persistence mechanisms, and disguised services that may otherwise appear legitimate at first glance.

Finally, the execution demonstrates remote command execution using PowerShell remoting. This validates that investigative commands can be scaled across multiple hosts without manual logins, supporting enterprise-scale triage and incident scoping.

Each phase is designed to reinforce how analysts move from discovery into validation and then into correlation across evidence domains.

---

## Why These Tasks Matter in Host-Based Security Operations

Endpoint compromise rarely reveals itself through a single indicator. Effective triage requires analysts to confirm whether anomalies on disk, in memory, in services, or in network behavior are related and whether they represent active risk.

This workflow reinforces why analysts must:

- confirm whether suspicious files are executing,  
- determine whether services have been modified for persistence,  
- validate whether network activity maps back to suspicious processes, and  
- verify integrity of artifacts before assuming compromise.

PowerShell enables this style of investigation because object pipelines allow structured filtering and correlation without relying on fragile text parsing techniques.

---

## Environment, Operating System, and Tooling

### Execution Environment

All activity was performed on a Windows endpoint accessed through a remote PowerShell session. The environment reflects scenarios where analysts may have:

- remote command-line access,  
- administrative credentials, and  
- no ability to install additional forensic tools.

### Operating System and Shell

The system was a Windows workstation-style endpoint. All commands were executed within PowerShell, and no CMD-based utilities were required for investigation.

### Tooling Constraints

All investigative actions were performed using built-in PowerShell cmdlets and standard Windows system telemetry.

No third-party tools, EDR consoles, or external scripting frameworks were used. This ensures techniques remain applicable in locked-down enterprise environments and during early response phases when tooling may be unavailable.

---

## Data Sources Analyzed

The workflow inspects multiple categories of live endpoint telemetry:

- Files and directories to identify suspicious artifacts and staging locations.  
- Running processes to identify abnormal execution behavior.  
- Windows services to detect unauthorized persistence mechanisms.  
- Network connections to validate external communication.  
- Registry-backed auto-start locations and service metadata to confirm long-term persistence.  

By correlating across these domains, the workflow builds confidence in whether anomalies represent isolated artifacts or coordinated host compromise.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution reflects how structured host investigations are performed using scripting-capable tooling to enable repeatable and scalable triage.

In enterprise environments, analysts must often investigate dozens or hundreds of systems during outbreaks or credential compromise incidents. Manual investigation does not scale, making script-based validation critical.

The workflows demonstrated here show how PowerShell enables analysts to collect system state, inspect services, validate integrity, and perform remote execution using consistent logic across hosts.

Detection engineers rely on similar telemetry sources when building host-based detections for persistence mechanisms, service tampering, and unauthorized configuration changes.

This execution reinforces how endpoint triage techniques support both immediate investigation and longer-term detection strategy development.


---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how scalable host investigations are structured when scripting and remote execution are required.

### `README.md`

Provides investigative scope and explains how PowerShell is used to collect, validate, and correlate host telemetry.

### `workflow-execution.md`

Contains full command sequences, remote execution steps, integrity validation checks, and interpretation of collected system data. This file represents the complete operational investigation timeline.

### `analyst-notes.md`

Documents investigative reasoning, persistence hypotheses, and decisions related to scoping and escalation.

### `tool-usage-notes.md`

Provides technical reference material for PowerShell cmdlets, object pipelines, and filtering techniques used during host inspection.

### `images/`

Contains screenshots validating system inspection, service analysis, and correlation results.


---

## Skill Demonstration Context

This execution demonstrates practical PowerShell-based endpoint investigation skills, including structured evidence collection, object-based filtering, integrity validation, service inspection, and remote command execution.

More complex investigations and response workflows are documented separately in the **incident-response-and-investigations** repository. This execution focuses on host triage and evidence correlation fundamentals that support those deeper response efforts.
