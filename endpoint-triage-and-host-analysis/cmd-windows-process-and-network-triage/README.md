# Windows Endpoint Triage and Network Exposure Validation Using CMD and PowerShell — Operational Execution

**Category:** Endpoint Triage & Host Investigation  
**Operational Focus:** Filesystem inspection, process enumeration, network correlation, and targeted containment using native Windows CLI tools  
**Primary Tools & Platforms:** Windows CMD, PowerShell, native Windows system utilities

This directory contains a complete, operationally structured record of a Windows endpoint triage execution focused on identifying suspicious artifacts, correlating them to active processes, mapping those processes to network exposure, and validating containment actions. The directory contains the execution writeups, command outputs, screenshots, and supporting analysis that together reflect how analysts investigate host exposure using only native operating system tooling.

The goal of the execution is not to perform full forensic analysis, but to practice and document the investigative flow used during early-stage incident response when analysts must quickly determine whether suspicious artifacts are actively contributing to host risk.

At a practical level, the execution models the same investigative rhythm SOC analysts follow when validating host compromise:

- First, suspicious artifacts are identified on disk.  
- Next, memory is inspected to determine whether those artifacts are actively executing.  
- Then, network telemetry is used to confirm whether those processes are externally exposed.  
- Finally, targeted containment actions are taken and system state is revalidated.

This execution is intentionally constrained to native CMD and PowerShell tooling. That limitation reflects real-world scenarios where analysts may have limited access to enterprise EDR platforms, restricted tooling environments, or only terminal-based access during containment and triage.

Throughout this execution, I intentionally pivoted between CMD and PowerShell depending on which interface best supported each investigative step. This reinforced how analysts can adapt to multiple shell environments while maintaining a consistent investigation strategy rather than relying on a single tool or interface.

---

## How to Read This Folder

This folder is organized to separate operational execution from analytical reasoning and technical reference material, similar to how SOC teams separate investigation notes from response playbooks and tooling documentation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to security operations tasks such as endpoint triage, process investigation, and exposure validation.  
> - **Executions** refer to the hands-on performance of those tasks using real systems and commands.  
> - **Writeups** document how tasks were performed, what decisions were made, and how results were interpreted operationally.

- The complete command-by-command investigation is recorded in **`workflow-execution.md`**.  
- This `README.md` explains what investigative goals were pursued and how the workflow progresses across evidence layers.  
- Supporting files capture analyst observations and command usage references separately from the execution timeline.

---

## What This Execution Is Specifically Doing

This execution simulates how an analyst would investigate potential host exposure starting from filesystem artifacts and progressively correlating findings across memory and network telemetry.

First, the workflow begins with filesystem enumeration to identify files that may represent suspicious or staged artifacts. Directory contents are reviewed and files are inspected directly from the command line to confirm that artifacts exist and can be accessed without launching additional tools.

Next, the investigation pivots into memory by enumerating running processes. Instead of reviewing the full process list blindly, filtering is applied to isolate processes associated with specific executables of interest. Process identifiers (PIDs) are captured so that later steps can correlate execution context with network activity.

After identifying active processes, the workflow moves into network inspection using native network utilities. Listening ports and active connections are enumerated, and each entry is mapped to both executable name and PID. This allows confirmation of whether a given process is externally exposed and whether it is operating as a service or listener.

Once exposure is confirmed, targeted containment actions are performed by terminating a specific process instance using its PID rather than disabling entire services. This demonstrates controlled remediation where analysts minimize collateral impact while validating the effect of containment actions.

Finally, system state is revalidated by re-running both process and network enumeration commands. This confirms whether exposure has changed and whether containment actions affected only the intended process instance.

The full workflow demonstrates how analysts pivot between disk, memory, and network telemetry to determine whether suspicious artifacts are merely present or actively contributing to host risk.

---

## Why These Tasks Matter in Host-Based Security Operations

During incident response, analysts must quickly determine whether suspicious artifacts represent real risk or dormant files that are not currently active.

If investigations stop at filesystem discovery alone, analysts may overestimate risk. If investigations only examine processes without understanding network exposure, analysts may underestimate attacker access.

This workflow reinforces why effective endpoint triage must:

- validate whether suspicious artifacts are actively executing,  
- determine whether those processes are exposed to external connectivity, and  
- confirm that containment actions actually change host behavior.

These same data sources also feed many host-based detections in SIEM and EDR platforms, making this workflow relevant to both triage and detection engineering contexts.

---

## Environment, Operating System, and Tooling

### Execution Environment

All activity was performed on a Windows endpoint accessed through terminal-based command execution using both CMD and PowerShell. The environment simulates a workstation-style endpoint commonly investigated during SOC triage.

The execution context reflects scenarios where analysts may have:

- command-line access to the host,  
- administrative privileges, and  
- no access to graphical forensic tooling or EDR consoles.

### Operating System and Shell Interfaces

The system was a Windows workstation environment. Both CMD and PowerShell were used depending on task requirements and command availability.

CMD was used primarily for quick filesystem inspection, process listing, and containment actions, while PowerShell supported structured inspection and scripting where object-based output was helpful.

### Tooling Constraints

All investigative actions were performed using built-in Windows utilities and PowerShell cmdlets only.

No third-party tools, endpoint detection platforms, or forensic suites were used. This ensures all techniques are portable across most enterprise-managed Windows systems and remain usable during constrained response scenarios.

---

## Data Sources Analyzed

The workflow inspects multiple categories of live endpoint telemetry:

- Filesystem artifacts used to identify suspicious files and validate their locations.  
- Running processes used to determine whether artifacts are actively executing.  
- Process identifiers used as pivots between execution context and network telemetry.  
- Network listeners and connections used to identify externally exposed services.

By correlating across these domains, the workflow confirms whether artifacts are merely present or actively contributing to attack surface.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution reflects how analysts validate whether suspicious artifacts on disk are actively contributing to host risk during early-stage incident response.

SOC analysts routinely investigate hosts based on alerts generated from antivirus, EDR, or log-based detections. However, alerts alone cannot confirm whether malicious code is actively executing or whether services are exposed to external access.

The workflows demonstrated here mirror how responders correlate filesystem artifacts with running processes and network listeners to determine whether a host is actively compromised or simply contains dormant files.

Detection engineers rely on similar correlations when designing host-based detections that link file creation events, process launches, and network activity into meaningful behavioral detections.

This execution reinforces how endpoint investigations must pivot across multiple telemetry sources rather than relying on single indicators of compromise.


---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how endpoint investigations are separated into execution timelines, analytical reasoning, and command reference material in SOC operations.

### `README.md`

Provides investigative scope and explains how filesystem, process, and network telemetry are correlated during endpoint triage.

### `workflow-execution.md`

Contains command-by-command execution steps including artifact discovery, process identification, network correlation, containment actions, and post-remediation validation. This file represents the full investigative workflow.

### `analyst-notes.md`

Documents investigative hypotheses, reasoning behind containment actions, and criteria for escalation or additional evidence collection.

### `tool-usage-notes.md`

Provides reference material on CMD and PowerShell command usage patterns for process inspection, network validation, and system interrogation.

### `images/`

Contains screenshots validating each stage of the investigation and containment process.


---

## Skill Demonstration Context

This execution demonstrates practical endpoint triage skills, including filesystem inspection, process analysis, network exposure validation, targeted containment, and operational documentation.

More complex investigations and response scenarios are documented separately in the **incident-response-and-investigations** repository. This execution focuses on host exposure validation and containment fundamentals that support those deeper workflows.
