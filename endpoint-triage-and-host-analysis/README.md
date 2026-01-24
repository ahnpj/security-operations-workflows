# Endpoint Triage & Host Analysis — Operational Execution and Analyst Notes

(`endpoint-analysis-and-triage/README.md`)

This category contains hands-on execution examples and supporting documentation for common endpoint-focused security operations tasks. The goal of these writeups is to demonstrate how analysts validate host state, investigate suspicious behavior, and collect system artifacts using repeatable, tool-driven methods.

Rather than presenting abstract techniques, each folder documents practical execution of specific endpoint triage activities, including the commands used, the reasoning behind each step, and how results are interpreted in an investigative context. The emphasis is on operational process and analyst decision-making, similar to what would be captured in internal SOC runbooks or analyst working notes.

Each folder also uses a tool-first naming convention, followed by the primary operational focus (for example, `powershell-endpoint-triage` or `bash-linux-host-inspection`). This makes it easy to see which technologies are being used at a glance, while the documentation itself emphasizes analytical process and investigative decision-making rather than tool features.

> **Terminology used in this category:**  
> **Workflows** refer to common security operations tasks (such as endpoint triage or log analysis).  
> **Executions** refer to the hands-on performance of those tasks using real tools and commands.  
> **Writeups** document how the task was performed and how results were interpreted.

---

## Operational Context and Purpose

Endpoint triage and host analysis are typically performed early during alert investigation or incident scoping, when analysts must quickly determine whether a system shows signs of compromise, misconfiguration, or policy violations.

At this stage of an investigation, the objective is not full forensic reconstruction, but rapid validation of host health and behavior. Analysts focus on identifying suspicious execution, persistence mechanisms, unauthorized configuration changes, or indicators that suggest broader environmental exposure.

The execution examples in this category reflect this reality by focusing on targeted host-level validation tasks that help determine whether deeper investigation or containment actions are required.

---

## Scope of Workflows in This Category

The execution writeups in this category focus on host-level investigation and validation across both Windows and Linux environments. These activities typically occur early in alert triage or during incident scoping, when analysts need to quickly determine whether a system shows signs of compromise, misconfiguration, or policy violations.

Common objectives demonstrated in these workflows include:

- **Process and service inspection**, where running processes, parent-child relationships, and service states are reviewed to identify suspicious execution patterns or unauthorized persistence mechanisms.

- **File system and artifact triage**, where directories, binaries, scripts, scheduled tasks, and configuration files are examined to detect abnormal changes or potential malware placement.

- **Command-line and shell-based investigation**, using native operating system tools and scripting to collect system state without relying on heavyweight forensic tooling.

- **Initial response validation actions**, such as confirming whether indicators of compromise are isolated to a single host or may represent broader environmental exposure.

These tasks reflect the type of focused endpoint validation work SOC analysts and security engineers routinely perform when responding to alerts, supporting detections, or verifying the effectiveness of defensive controls.


---

## How These Execution Writeups Are Structured

Each execution writeup (`workflow-execution.md`) documents a specific operational objective and the technical steps used to carry it out during endpoint triage activities. The documentation is structured to resemble procedural runbooks rather than narrative case studies.

Typical sections include:

- **Operational intent and focus**, which explains the security question being addressed and why the task is being performed.

- **Environment and execution context**, which describes the operating system, data sources, and assumptions required to perform the task correctly.

- **Step-by-step execution**, which captures commands, queries, tool usage, and validation steps in the order an analyst would realistically perform them.

- **Results and interpretation**, which explains what the observed data indicates and how it supports investigative conclusions.

- **Operational and defensive takeaways**, which connect technical findings to detection improvements, response readiness, or security control validation.

This structure emphasizes methodical problem-solving and repeatable procedures rather than isolated technical outputs.

---

## Ongoing Development and Iteration

The examples in this category represent hands-on practice executions of endpoint security tasks, documented as techniques are built, tested, and refined. Over time, additional approaches, tools, and supporting documentation are added as new methods are explored and incorporated.

Some directories may appear incomplete or in progress. This reflects active iteration and expansion of coverage rather than abandoned content. In professional environments, operational procedures are continuously updated as telemetry sources, tooling, and detection strategies evolve, and these writeups intentionally reflect that reality.

The focus is on building a growing set of reusable investigative techniques rather than producing static, one-time demonstrations.

---

## Relationship to Full Incident Investigations

The practical execution of specific endpoint triage activities in this category is designed to support and complement larger investigations, but they are not complete end-to-end incident case studies on their own.

In practice, endpoint validation tasks such as those documented here are frequently performed as components of broader response efforts, including confirming malware execution, validating persistence mechanisms, checking for lateral movement artifacts, or verifying configuration drift.

Full investigative case studies — including alert context, timeline reconstruction, MITRE ATT&CK mapping, root cause analysis, artifact analysis, and remediation recommendations — are documented separately in the dedicated `incident-response-and-investigations` repository, which focuses on complete incident lifecycles rather than individual operational procedures.

This separation mirrors how professional SOC teams distinguish between reusable procedural workflows and case-based investigation reporting.

---

## Current Workflow Executions in This Category

The following executions of specific endpoint triage activities currently exist in this category and demonstrate different approaches to endpoint triage across operating systems and tooling:

- **PowerShell Endpoint Triage and System Inspection** (`powershell-endpoint-triage-and-system-inspection`)  
  Focuses on collecting and validating host state on Windows systems using native PowerShell commands. Tasks include inspecting running processes, services, network connections, user sessions, and configuration artifacts to assess whether a system exhibits suspicious behavior or policy violations.

- **CMD Windows Process and Network Triage** (`cmd-windows-process-and-network-triage`)  
  Uses standard Windows command-line utilities to inspect processes, services, and network activity without relying on PowerShell. This demonstrates how analysts can perform effective host validation using minimal tooling in restricted environments.

- **Bash Linux Endpoint Triage and System Inspection** (`bash-linux-endpoint-triage-and-system-inspection`)  
  Demonstrates host-based triage on Linux systems using native shell utilities. Tasks include reviewing running processes, scheduled jobs, active network connections, file system artifacts, and user activity to assess system integrity and detect anomalies.

Each execution example emphasizes host validation techniques that can be applied during alert triage, detection support, and early-stage incident response.

---


## Documentation Files Included in Each Execution
Each practical execution of specific endpoint triage activities is documented using multiple files, separated by purpose to reflect how security teams typically organize operational knowledge and investigation notes.

- **`README.md`**  
  Describes the operational objective, the security context in which the task would occur, what skills are being exercised, and how the activity maps to real SOC or security engineering responsibilities.

- **`workflow-execution.md`**  
  Documents the step-by-step technical execution of the task, including commands, queries, filters, scripts, screenshots, and validation steps. This serves as the reproducible record of what actions were performed and in what sequence.

- **`analyst-notes.md`**  
  Captures analytical reasoning and operational takeaways, such as why specific approaches were chosen, how results should be interpreted, potential detection and monitoring implications, and areas where false positives or blind spots may exist.

- **`tool-usage-notes.md`**  
  Focuses on practical tool behavior and syntax, including command options, filter logic, field definitions, and usage nuances that are useful when applying the same tools in other investigations or operational tasks.

- **`automation-design-notes.md`** (when present)  
  Used in tasks that involve scripting or data processing to document parsing strategies, logic flow, and design considerations for scaling, reuse, or integration into larger detection or automation pipelines.