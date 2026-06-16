# Detection Engineering and Sigma Rule Development for DNS Tunneling Activity

### Overview

This execution documents the practical review, modification, and validation of a Sigma detection rule using threat intelligence to improve visibility into DNS-based command-and-control activity. The objective is to analyze an existing DNS tunneling detection, identify detection gaps through intelligence analysis, and update the rule to detect known malicious infrastructure associated with the fictional TRANSPORTER malware family.

The execution focuses on understanding Sigma rule structure, reviewing detection logic, evaluating threat intelligence requirements, modifying detection criteria, and validating that the completed rule aligns with observed adversary behavior. Emphasis is placed on how threat intelligence can be translated into actionable detection content to improve SIEM monitoring and alerting capabilities.

> **Click the ▶ arrow to expand or collapse hidden sections and view additional information.**

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>

> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` inside this folder to see how an existing Sigma rule was reviewed, how threat intelligence requirements were analyzed, and how detection logic was modified and validated step by step.

> 👉 **Review analytical reasoning and detection engineering decision-making**</br>
> Move to `analyst-notes.md` to understand why specific indicators were selected, how detection gaps were identified, and how threshold decisions were made when modifying the Sigma rule.

> 👉 **Review tooling and detection content reference details**</br>
> See `tool-usage-notes.md` to understand Sigma rule structure, YAML-based detection formatting, ATT&CK mappings, and detection development concepts used throughout the execution.

> 👉 **Review supporting detection content and artifacts**</br>
> See the `supporting-files/` folder for the completed Sigma rule and any supporting detection content referenced during the workflow.

> 👉 **See what each execution file contains in full detail**</br>
> For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

* **Category:** SIEM Detections and Log Analysis
* **Primary Operational Focus:** Threat-informed detection engineering and Sigma rule development
* **Operational Objectives Demonstrated:** Detection Gap Analysis, Threat Intelligence Integration, Sigma Rule Development, Detection Validation
* **Primary Data Sources:** Threat intelligence reporting, Sigma detection content, DNS telemetry requirements

> **Terminology used:**
> **Workflows** refer to common detection engineering tasks such as rule development, tuning, and validation.
> **Executions** refer to the hands-on performance of those tasks through Sigma rule analysis and modification.
> **Writeups** document how detection content was developed, validated, and interpreted.

</details>

---

### How to Navigate This Current Folder

Documentation is separated into focused components to reflect how detection engineering and Sigma rule development activities are documented within operational security environments.

**`workflow-execution.md`** — **If you want to follow the execution step by step**</br>
This file contains the structured walkthrough showing how the Sigma repository was reviewed, how an existing DNS tunneling rule was analyzed, how threat intelligence requirements were evaluated, and how detection logic was modified and validated to improve detection coverage.

**`analyst-notes.md`** — **If you want to understand the reasoning behind the process**</br>
This file explains the major learning points behind Sigma rules, threat-informed detection engineering, DNS tunneling detections, ATT&CK mappings, threshold selection, detection tuning, and how intelligence can be translated into actionable monitoring content.

**`tool-usage-notes.md`** — **If you want to understand tool usage**</br>
This file explains Sigma rule structure, YAML formatting, detection fields, metadata usage, log source requirements, detection logic construction, and best practices for developing and maintaining reusable detection content.

**`supporting-files/`** — **If you want to review the completed detection content**</br>
This folder contains the modified Sigma rule and any supporting detection artifacts used throughout the execution.

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational detection engineering and SIEM documentation practices.

| File / Folder                                 | Purpose                                                                                                                          | Contents and Focus                                                                                                                                                                               |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `workflow-execution.md`                       | Structured detection engineering execution showing how threat intelligence is converted into actionable Sigma detection content. | Documents rule review, intelligence analysis, detection gap identification, rule modification, validation activities, and final detection outcomes.                                              |
| `supporting-files/`                           | Project-specific detection artifacts and supporting resources.                                                                   | Contains the completed Sigma rule, supporting detection content, reference material, and additional artifacts used throughout the workflow.                                                      |
| `images/`                                     | Visual evidence supporting detection development and validation activities.                                                      | Contains screenshots of Sigma rule review, intelligence analysis, detection logic modifications, and validation checkpoints supporting analytical conclusions.                                   |
| `README.md`                                   | Execution overview and operational summary.                                                                                      | Provides structured explanation of Sigma rule development objectives, threat intelligence integration, and detection engineering relevance aligned with SOC and detection engineering practices. |
| `analyst-notes.md`                            | Analytical reasoning and detection engineering insights derived from the execution.                                              | Documents detection decisions, threshold selection rationale, indicator analysis, intelligence interpretation, and lessons learned throughout the workflow.                                      |
| `tool-usage-notes.md`                         | Technical implementation reference for Sigma rule development.                                                                   | Covers Sigma rule structure, YAML formatting, ATT&CK mappings, detection logic construction, and best practices for maintaining reusable detection content.                                      |
| `automation-design-notes.md` *(when present)* | Detection content operationalization and automation planning.                                                                    | Documents considerations for converting Sigma rules into SIEM-specific detections, integrating detections into monitoring pipelines, and scaling detection coverage.                             |

---

### Environment, Data Sources, and Tools

The execution focuses on Sigma rule analysis, threat intelligence review, and detection development activities used to improve visibility into DNS-based command-and-control communications. The execution demonstrates how intelligence-driven detection engineering supports scalable SIEM monitoring and threat detection workflows.

<details>
<summary><strong>▶ Environment and Execution Scope (At a Glance)</strong><br>
</summary><br>

| Area                             | Details                                                                                      |
| -------------------------------- | -------------------------------------------------------------------------------------------- |
| **Environment Type**             | Detection engineering and Sigma rule development environment                                 |
| **Processed Assets**             | Sigma detection rules, threat intelligence reporting, DNS detection logic                    |
| **Execution Platform**           | Local analysis workstation and Sigma rule repository                                         |
| **Primary Platforms / Services** | Sigma detection framework, YAML-based detection content, SIEM-compatible detection workflows |
| **Operational Focus**            | Validate and improve detection coverage through threat-informed Sigma rule development       |

</details>

<details>
<summary><strong>▶ Data Sources, Evidence, and Analysis Techniques</strong><br>
</summary><br>

| Area                                | Details                                                                                                                                            |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Primary Intelligence Sources**    | Threat intelligence briefing describing TRANSPORTER malware behavior and infrastructure                                                            |
| **Detection Review Techniques**     | Sigma metadata review, log source validation, ATT&CK mapping analysis, and detection logic inspection                                              |
| **Detection Gap Analysis**          | Comparison of observed malware behavior against existing detection thresholds and monitoring criteria                                              |
| **Rule Modification Techniques**    | Indicator integration, threshold tuning, metadata updates, and ATT&CK mapping alignment                                                            |
| **Detection Validation Techniques** | Review of rule logic, threshold effectiveness, infrastructure indicators, and telemetry requirements                                               |
| **Threat Detection Heuristics**     | DNS tunneling behavior, command-and-control communications, known malicious infrastructure, and ATT&CK-aligned activity patterns                   |
| **Sigma Development Techniques**    | YAML rule analysis, metadata review, detection condition modification, and threat-informed detection engineering workflows                         |
| **Operational Workflow Context**    | Demonstrates detection engineering activities used by SOC analysts and detection engineers to improve monitoring coverage against emerging threats |

</details>

---

### Intended Use

The documented execution demonstrates Sigma rule development, detection tuning, and threat-informed detection engineering techniques. It reflects how detection engineers and SOC teams evaluate existing detection content, identify monitoring gaps, and improve detection coverage using actionable threat intelligence.

---

### Relevance to Security Operations

Threat intelligence is most valuable when it can be translated into practical detection content. Effective detection engineering requires continuous review of existing monitoring capabilities, identification of detection gaps, and adaptation of detection logic as new threats emerge.

<details>
<summary><strong>▶ Analyst Use Cases</strong><br>
</summary><br>

The execution demonstrates how detection engineering enables analysts to:

* Translate threat intelligence into actionable detections
* Identify gaps in existing monitoring coverage
* Improve visibility into known malicious infrastructure
* Validate detection logic before deployment
* Support scalable SIEM monitoring and threat detection workflows

</details>

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical Sigma rule development methodology, threat-informed detection engineering, and professional workflow documentation aligned with real-world SOC and detection engineering environments.
