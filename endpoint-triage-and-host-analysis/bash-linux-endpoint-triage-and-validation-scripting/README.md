# Linux Endpoint Triage and Script-Based Validation Logic Using Bash — Operational Execution

**Category:** Endpoint Triage & Host Investigation  
**Operational Focus:** Live host enumeration, filesystem inspection, shell environment awareness, and basic automation using Bash  
**Primary Tools & Platforms:** Linux (Ubuntu), Bash shell, native command-line utilities, SSH-based access

This directory contains a complete, operationally structured record of a Linux endpoint triage and automation execution performed on a live system. The directory contains the execution writeups, command outputs, scripts, screenshots, and supporting analysis that together reflect how analysts interact with hosts during early-stage investigation and response.

The goal of the execution is not to perform deep forensic analysis, but to practice and document the foundational host interaction skills that almost every endpoint investigation depends on, including system context validation, filesystem inspection, permission analysis, shell awareness, and small-scale automation using scripting.

At a practical level, the execution follows the same progression an analyst would typically perform after first connecting to a Linux system during triage:

- First, system context is established by identifying operating system details, kernel version, network configuration, disk usage, memory usage, and current user identity to confirm what environment the analyst is operating in.  
- Next, the filesystem is explored to review directory structures, inspect file contents, search within files, and interpret permission and ownership settings that could reveal misconfigurations or suspicious artifacts.  
- Then, shell environments are enumerated and compared by identifying the active shell, listing available login shells, and temporarily switching shells to understand how command behavior and scripting compatibility can differ.  
- Finally, Bash scripts are written to automate simple validation logic using user input, variables, loops, and conditional checks, culminating in a multi-input validation flow that simulates basic access-control logic.

Throughout this execution, I intentionally treated Bash scripting as an extension of investigation work rather than as a separate programming exercise. I focused on understanding how terminal commands and scripting structures can be combined to automate checks that analysts routinely perform by hand, reinforcing how scripting supports faster and more consistent triage during real response scenarios.

---

## How to Read This Folder

This folder is structured to separate execution steps from analytical reasoning and scripting reference material, similar to how SOC teams maintain investigation notes alongside reusable tooling documentation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to security operations tasks such as host triage, file inspection, and access validation.  
> - **Executions** refer to the hands-on performance of those tasks using real systems and commands.  
> - **Writeups** document how tasks were performed, what decisions were made, and how results were interpreted operationally.

- The full command-by-command execution is recorded in **`workflow-execution.md`**.  
- This `README.md` explains what tasks were performed, why they matter for endpoint investigations, and how supporting files are organized.  
- Supporting files capture analyst reasoning and technical reference material separately from the execution flow.

---

## What This Execution Is Specifically Doing

This execution follows the exact sequence of actions an analyst would typically perform immediately after logging into a Linux system during triage or response, gradually moving from situational awareness into basic automation.

First, system context is established by running commands that identify the operating system version, kernel details, network configuration, disk usage, memory usage, and the active user account. These checks are used to confirm what environment the analyst is connected to and whether anything about the system state appears unexpected or abnormal at first glance.

Next, the workflow moves into filesystem navigation and file inspection. This includes listing directory contents, viewing file contents directly from the terminal, searching for specific strings within files, and interpreting Linux permission and ownership settings. These actions reflect how analysts locate configuration files, scripts, or artifacts that may require closer inspection during investigations.

After that, the execution examines shell environments by determining which shell is currently active, listing which shells are available on the system, and temporarily switching between shells. This demonstrates how shell differences can affect script compatibility and user experience, and why analysts need to understand which shell environment is in use when running commands or deploying scripts.

Finally, the execution builds several Bash scripts that automate small validation and interaction tasks. These scripts progressively introduce user input handling, variable storage, loop constructs, and conditional logic. The final script combines all of these elements into a simple multi-input validation flow that simulates access control checks by requiring correct values before allowing progression.

Each phase of the execution reflects realistic investigation behavior, where analysts start by gathering information, validate host state, and then build small tools to reduce repetitive manual checks.

---

## Why These Tasks Matter in Host-Based Security Operations

When responding to incidents or validating suspicious activity on endpoints, analysts must quickly understand system context, inspect files and permissions, and often deploy small scripts to automate repetitive checks.

If analysts are uncomfortable working directly in the shell or writing basic scripts, investigations become slower, more error-prone, and harder to scale across multiple systems.

This execution reinforces those foundational skills in a controlled environment so that command usage, file inspection, and scripting logic become routine rather than risky during real response scenarios.

---

## Environment, Operating System, and Tooling

### Execution Environment

All activity was performed on a remote Linux virtual machine accessed through SSH. The system was ephemeral, meaning files and scripts were lost when sessions ended, IP addresses changed between sessions, and system context had to be re-established each time.

This mirrors investigation environments such as cloud-hosted response systems, sandbox machines, and incident response jump boxes.

### Operating System and Shell

The system was running Ubuntu Linux, using Bash as the default shell. All interactions occurred through terminal sessions without graphical tools.

### Command-Line Tools Used

Only native Linux utilities were used for system enumeration, filesystem inspection, network configuration review, and process visibility. No external forensic or monitoring tools were involved, keeping the workflow aligned with minimal-access response scenarios.

---

## How Bash Scripting Is Used in This Execution

Scripting is introduced as part of operational problem-solving rather than as a standalone programming topic.

The scripts written during this execution demonstrate how analysts can collect user input, store values in variables, repeat actions using loops, and enforce validation logic using conditional statements. Execution permissions are explicitly managed so that scripts behave like small operational tools rather than ad-hoc command snippets.

The final script brings all of these elements together into a simple validation workflow that requires correct inputs before granting access, modeling how authentication checks and configuration validation tools are structured in real environments.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution reflects foundational host-based workflows that SOC analysts and incident responders perform during early-stage endpoint triage and validation.

In real investigations, analysts are often required to rapidly assess system state using only command-line access, especially when responding to alerts on servers, cloud workloads, or restricted systems without graphical interfaces. The enumeration techniques demonstrated here — including review of kernel information, memory usage, disk utilization, network configuration, and active processes — represent the first validation steps used to determine whether a host is behaving normally or requires escalation.

Filesystem navigation and permission inspection are critical for identifying suspicious scripts, unauthorized binaries, or misconfigurations that may enable persistence or privilege escalation. Analysts must be able to quickly inspect file contents, search for indicators, and interpret permission models to assess whether files could be executed by unauthorized users or abused by attackers.

From a detection engineering perspective, many host-based detections are built around behaviors that originate from shell activity, script execution, and abnormal process launches. Understanding how Bash scripts are executed, how permissions control execution, and how environment variables influence behavior helps detection engineers design rules that identify suspicious script-based activity, unauthorized automation, and abuse of system utilities.

The scripting workflows demonstrated in this execution also reflect how analysts build lightweight automation during investigations, such as scripts that validate system configuration, collect artifacts, or enforce containment steps. These same scripting constructs later evolve into forensic triage scripts, response playbooks, and scheduled monitoring jobs.

Finally, shell environment awareness supports detection of attacker tooling, which often relies on shell-based payloads, environment manipulation, and execution permission changes. Analysts who understand how shells behave can better identify abnormal command usage, malicious script execution, and persistence mechanisms tied to startup scripts or scheduled jobs.

Overall, this execution demonstrates how basic Linux and Bash skills directly support SOC triage, incident response automation, and development of host-based detection logic.

---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how security teams separate operational execution steps, analytical reasoning, and technical reference material when conducting host-based investigations and building automation.

### `README.md`

Provides high-level operational context, explains the investigative focus on Linux host triage and shell-based automation, and describes how this execution supports SOC and detection engineering objectives.

### `workflow-execution.md`

Contains the complete step-by-step walkthrough of system enumeration, filesystem inspection, shell environment evaluation, script development, input validation logic, and execution testing. This file represents the primary operational timeline and preserves command output and screenshots for reproducibility.

### `analyst-notes.md`

Documents investigative observations, reasoning behind enumeration choices, interpretation of system state, and considerations for how similar workflows would be applied during real incident response scenarios.

### `tool-usage-notes.md`

Provides technical reference material for Bash syntax, permission handling, shell behavior, and command-line utilities used throughout the execution. This supports reuse of scripting techniques in future investigations and automation tasks.

### `images/`

Contains screenshots validating command execution, script behavior, and environment configuration. These images provide visual confirmation that workflows were performed on live systems and support auditability of the execution steps.


---

## Skill Demonstration Context

This execution demonstrates foundational security operations skills, including Linux host triage and enumeration, filesystem and permission analysis, shell environment awareness, Bash scripting for validation and automation, and structured operational documentation.

More complex endpoint investigations and response scenarios are documented separately in the **incident-response-and-investigations** repository. This execution focuses on the host interaction and automation fundamentals that support those deeper workflows.
