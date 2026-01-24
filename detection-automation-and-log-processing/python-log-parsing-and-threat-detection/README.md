# Log Parsing and Threat Detection Automation Using Python — Operational Execution

**Category:** Detection Automation & Log Processing  
**Operational Focus:** Behavior-based detection logic prototyping from raw security telemetry across multiple environments  
**Primary Tools & Platforms:** Python (standard library only), Linux shell environment (Google Cloud Shell), multi-format log datasets

This directory contains a complete, operationally structured record of a detection automation execution in which raw telemetry from multiple environments is parsed and analyzed using small Python scripts. The directory contains the execution writeups, scripts, screenshots, and supporting analysis that together show how detection logic can be validated directly from source logs before being translated into production monitoring systems.

The goal of the execution is not to generate “alerts” in a product UI, but to validate—directly from raw telemetry—that specific attacker behaviors are observable, measurable, and defensible enough to later translate into production detections such as SIEM searches, cloud-native detection rules, or automated enrichment pipelines.

At a practical level, the execution repeatedly follows the same SOC-style analytical rhythm across four telemetry types that analysts commonly encounter in real monitoring and investigation work:

- First, representative log data is prepared so that both normal and suspicious activity are present, allowing detection logic to be tested and sanity-checked against known behavior.  
- Next, Python parsing logic is written to extract only the fields required for behavioral analysis, such as source IP addresses, usernames, request paths, authentication status, event identifiers, and cloud API action names.  
- Then, events are aggregated using pivots that are operationally meaningful for triage and hunting, such as grouping by source IP, grouping by account name, or grouping actions by cloud identity.  
- Finally, outputs are interpreted using the same reasoning an analyst or detection engineer would apply during alert validation or threat hunting, where the focus is on repeated, targeted, or clustered behavior rather than isolated single events.

This execution is intentionally performed without external Python packages and without SIEM tooling. That constraint is deliberate and reflects real-world scenarios where analysts need portability, must work in restricted environments, or want to prove detection signal exists in telemetry before investing time in production implementations.

Throughout this execution, I intentionally wrote and reviewed each parser from scratch rather than relying on prebuilt tooling. Before performing the workflow, I refreshed Python fundamentals such as file handling, regular expressions, CSV parsing, JSON traversal, loops, and conditional logic so that every detection decision reflected deliberate design choices instead of copied patterns. This was done to strengthen my ability to translate detection ideas into working parsing logic and to better understand how raw logs become structured behavioral signals.

---

## How to Read This Folder

This folder is organized to separate operational execution from analytical reasoning and technical reference material, similar to how SOC teams separate investigation notes from automation playbooks and internal documentation.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to common security operations tasks such as log analysis, authentication review, or cloud activity validation.  
> - **Executions** refer to the hands-on performance of those tasks using real tools, scripts, and datasets.  
> - **Writeups** document how each task was performed, what decisions were made during analysis, and how results were interpreted in an operational context.

- The complete step-by-step operational process is recorded in **`workflow-execution.md`**, including dataset creation, script development, command execution, and troubleshooting steps.  
- This `README.md` explains what security problems are being evaluated, why those behaviors matter for detection, and how the execution artifacts are organized.  
- Supporting documentation files capture analyst reasoning, tool-specific notes, and forward-looking automation design ideas separately from the execution timeline.

---

## What This Execution Is Specifically Doing

This execution simulates how an analyst or detection engineer would validate potential detection ideas by working directly with raw telemetry before building any production alerting logic.

Rather than assuming that certain behaviors are detectable, the workflow explicitly proves that they are visible in logs and can be extracted using basic parsing logic. To do that, the execution performs the following concrete activities across multiple telemetry sources.

First, short but realistic datasets are created for each telemetry type. Each dataset contains a mixture of normal activity and suspicious behavior so that parsing and grouping logic can be validated against known-good and known-bad patterns. This ensures that detection logic can distinguish between baseline behavior and activity that would reasonably warrant investigation.

Next, individual Python scripts are written for each dataset, with each script focused on extracting only the fields needed for behavioral analysis. For web server logs, this includes extracting source IP addresses, request paths, and HTTP response codes. For Linux authentication logs, the scripts extract source IPs, targeted usernames, and authentication outcomes. For Windows authentication events exported to CSV, the scripts filter specifically for Event ID 4625 and extract both account names and source IP addresses. For CloudTrail JSON records, nested fields are traversed to extract user identity information and API action names.

After extraction, events are grouped using operational pivots that analysts commonly use during triage and threat hunting. These pivots include grouping requests by IP address to identify scanning behavior, grouping failed logins by source to identify brute-force attempts, grouping authentication failures by account to identify targeting patterns, and grouping cloud API actions by user identity to identify suspicious administrative behavior.

Once grouped, the scripts output summaries that make behavioral concentration visible, such as one IP generating multiple failed requests to administrative endpoints, repeated authentication failures from the same external source, or users performing actions that modify permissions or logging visibility. These summaries are interpreted using the same reasoning an analyst would apply when deciding whether activity represents automated attacks, credential abuse, or unauthorized administrative actions.

Finally, the execution includes troubleshooting and validation steps whenever errors occur, such as correcting filename mismatches, fixing CSV formatting problems that break parsers, and verifying that file contents match expected structures. These steps are preserved in the writeups because real-world detection work often involves resolving imperfect data and operational mistakes before analysis can proceed.

Overall, the execution demonstrates how raw telemetry can be converted into behavior-based detection signals using only simple scripting techniques, without relying on centralized detection platforms.

---

## Why These Tasks Matter in Detection Engineering and SOC Operations

Detection rules and alerts are only useful if they are based on behaviors that are consistently visible in telemetry and separable from normal operational activity.

In practice, teams often discover that logs lack necessary fields, that event volume is too high to evaluate manually, or that benign activity closely resembles malicious behavior. Without validating detection ideas against raw telemetry, production detections frequently generate false positives or fail to trigger at all.

This execution reflects how analysts and detection engineers validate detection ideas by confirming that required fields exist, verifying that suspicious behavior appears in clusters rather than isolated events, and determining whether grouping and frequency-based logic can meaningfully separate malicious behavior from normal usage.

By validating detection logic directly against telemetry, detection rules become easier to tune, easier to explain, and more defensible when escalations occur.

---

## Environment, Operating System, and Tooling

### Execution Environment

All analysis was performed inside Google Cloud Shell, which provides a browser-based Linux virtual machine. This environment simulates situations where analysts must work in restricted or temporary systems without installing additional tools.

The environment provided a Linux shell with no persistent storage between sessions, which required scripts and datasets to be recreated after reconnecting. External Python libraries could not be installed, enforcing reliance on the Python standard library only. These constraints mirror investigation environments such as response jump boxes, forensic sandboxes, or cloud-hosted analysis instances.

### Operating System and Shell

The operating system was a Linux distribution provided by Google Cloud Shell, with Bash used as the default shell environment. All file creation, script execution, and troubleshooting occurred through the command line.

### Programming Language and Libraries

All scripts were written in Python using only standard library modules, including regular expressions for plaintext parsing, CSV readers for structured authentication data, JSON parsing for CloudTrail records, and collection utilities for grouping and counting events.

No external packages or detection platforms were used, keeping the parsing logic portable and easy to translate into other environments or monitoring systems.

---

## Telemetry and Data Sources Used

All datasets were intentionally small and synthetic, but structured to reflect real log formats and realistic attacker behaviors.

### Web Server Logs

Apache-style access logs contained both normal browsing requests and repeated requests to administrative paths that generated 404 responses, allowing detection logic to identify scanning and enumeration patterns.

### Linux Authentication Logs

Linux `auth.log` entries simulated multiple failed SSH login attempts from the same external source, followed by a successful login from a different IP address, enabling grouping logic to identify brute-force behavior.

### Windows Authentication Events (CSV)

Windows authentication events were represented in CSV format with both failed and successful logon events, allowing Event ID filtering and grouping by account and source IP.

### AWS CloudTrail Logs

CloudTrail JSON records included normal API activity as well as actions that modify permissions or logging, allowing detection logic to flag potentially dangerous administrative behavior.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution reflects how SOC analysts and detection engineers validate potential detection ideas directly against raw telemetry before implementing them in production monitoring systems.

In operational environments, analysts frequently encounter suspicious patterns such as repeated authentication failures, scanning behavior against web services, or unusual cloud API activity. However, detection logic should not be created based solely on assumptions about how attackers behave. Instead, analysts must confirm that required fields are present in logs, that suspicious behavior appears in clusters rather than isolated events, and that telemetry supports meaningful aggregation.

The workflows demonstrated here mirror how detection engineers prototype detection logic by extracting only the fields needed for behavioral analysis and grouping events in ways that reflect real investigative pivots, such as grouping by source IP, targeted account, or cloud identity. These same pivots later become the basis for SIEM searches, detection rules, and enrichment pipelines.

This execution also reflects threat hunting practices where analysts explore datasets without predefined alerts in order to identify weak signals or emerging attack techniques. By working directly with raw logs and building custom parsers, analysts gain a deeper understanding of what behaviors are actually observable and which signals are reliable enough to operationalize.

In incident response contexts, similar parsing and grouping techniques are used when analysts must quickly evaluate log files collected from compromised systems or cloud environments where centralized logging may not yet be available. The ability to extract and interpret behavioral patterns directly from logs supports rapid triage and scoping decisions.

Overall, this execution demonstrates how detection ideas move from hypothesis to validated signal before becoming production monitoring logic.


---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how security teams separate hands-on detection validation, analytical reasoning, and technical reference material when developing and testing detection logic from raw telemetry.

### `README.md`

Provides high-level context for the detection automation workflow, explains what behaviors are being evaluated, and describes how this execution supports SOC analysis and detection engineering objectives.

### `workflow-execution.md`

Contains the complete step-by-step walkthrough of dataset preparation, Python script development, parsing logic, grouping operations, troubleshooting, and interpretation of output. This file represents the full operational execution timeline and preserves how detection ideas were validated directly from raw logs.

### `analyst-notes.md`

Documents analytical reasoning about which behaviors are considered suspicious, why certain aggregation pivots were chosen, and what limitations exist in the datasets. This file mirrors how analysts record investigative thinking during detection prototyping and threat hunting.

### `tool-usage-notes.md`

Provides technical reference material for Python parsing techniques, regular expressions, file handling, and aggregation logic used throughout the execution. This supports reuse of scripting patterns in future detection automation workflows.

### `automation-design-notes.md`

Explores how the validated behavioral signals could be implemented as SIEM searches, correlation rules, or automated enrichment pipelines. This file bridges manual analysis and scalable detection engineering.

### `images/`

Contains screenshots validating script execution and aggregation results, providing visual confirmation that detection logic was tested against real datasets.



---

## Skill Demonstration Context

This execution demonstrates practical SOC and detection engineering skills, including multi-format log parsing, behavior-based detection thinking, operational pivot design, scripting for detection prototyping, and structured security documentation.

Full end-to-end incident investigations are documented separately in the **incident-response-and-investigations** repository. This execution focuses on detection validation and telemetry analysis rather than case-based incident handling.
