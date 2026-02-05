# Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk

- **Category:** SIEM Detections and Log Analysis  
- **Primary Operational Focus:** Log parsing validation, field extraction verification, and telemetry normalization for detection reliability  
- **Operational Objectives Demonstrated:** Field Extraction Validation, Log Normalization Verification, Detection Data Quality Assessment, Search Optimization  
- **Primary Data Sources:** Raw log events, parsed event datasets, and normalized telemetry fields within Splunk

> **Terminology used:**  
> **Workflows** refer to common security operations tasks (such as log parsing validation or detection data preparation).  
> **Executions** refer to the hands-on performance of those tasks using SIEM queries, field inspection, and telemetry analysis.  
> **Writeups** document how the task was performed and how outputs were validated and interpreted.

---

### Overview

This execution documents the practical performance of log parsing validation and field extraction verification using Splunk to ensure telemetry is structured and normalized for reliable detection and investigative analysis. The objective is to confirm that raw log events are correctly parsed, that relevant fields are consistently extracted, and that normalized telemetry supports accurate alerting and monitoring workflows.

The execution focuses on reviewing raw event structures, validating extracted fields, testing parsing logic, and verifying consistency across log sources. Emphasis is placed on how field extraction accuracy directly impacts detection reliability, search efficiency, and investigative pivoting within operational security environments.

> 👉 **Follow the execution walkthrough first**  
Begin with `workflow-execution.md` inside this folder to see how raw log events were analyzed, field extractions were validated, and telemetry normalization was verified step by step using Splunk queries.

> 👉 **Review analytical reasoning and detection engineering decision-making**  
Move to `analyst-notes.md` to understand why specific parsing validation techniques were selected, how field extraction accuracy was evaluated, and how results influence detection development and tuning decisions.

> 👉 **Review tooling and query reference details**  
See `tool-usage-notes.md` to understand Splunk query techniques, field extraction validation methods, and parsing verification procedures used during execution.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how log parsing validation and telemetry normalization tasks are documented within detection engineering and SOC operational environments.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational SIEM parsing validation and detection engineering documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured parsing validation execution showing how raw log telemetry is inspected and normalized to support reliable detection and investigation workflows. | Documents raw event inspection procedures, field extraction verification workflows, parsing logic validation, normalization consistency testing, and verification of detection-relevant telemetry attributes. Emphasizes repeatable parsing validation methodology and investigative accuracy. |
| `images/` | Visual evidence supporting parsing validation and telemetry inspection outcomes. | Contains screenshots of raw event views, field extraction results, parsing configuration validation, and telemetry normalization checkpoints supporting analytical conclusions. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of parsing validation objectives, telemetry normalization context, and detection engineering relevance aligned with SOC SIEM monitoring documentation practices. |
| `analyst-notes.md` | Analytical reasoning and detection engineering insights derived from parsing validation execution. | Documents parsing strategy selection, interpretation of extraction accuracy, investigative escalation considerations, and how telemetry normalization supports reliable detection and threat investigation. |
| `tool-usage-notes.md` | Technical implementation reference for Splunk parsing validation and query analysis. | Covers SPL query techniques, field extraction configuration validation, event parsing methodologies, and best practices for validating telemetry quality in Splunk environments. |
| `automation-design-notes.md` *(when present)* | Scalable telemetry normalization and detection data pipeline planning. | Documents architectural considerations for integrating parsing validation workflows into automated SIEM ingestion pipelines, detection validation systems, or telemetry enrichment workflows. |

---

### Environment, Data Sources, and Tools

The execution focuses on validation of log parsing and telemetry normalization within Splunk environments to support detection engineering and investigative workflows.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | SIEM telemetry validation and log parsing analysis environment |
| **Processed Assets** | Structured and unstructured log telemetry ingested into Splunk |
| **Execution Platform** | Splunk SIEM search and analysis interface |
| **Primary Platforms / Services** | Centralized log ingestion pipelines, field extraction configurations, and normalized telemetry indexing infrastructure |
| **Operational Focus** | Validate log parsing accuracy and ensure telemetry normalization supports reliable detection and monitoring |


#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Primary Telemetry Sources** | Raw log events containing structured and unstructured telemetry attributes, including authentication activity, system events, network telemetry, and application logging data |
| **Field Extraction Validation Techniques** | Inspection of parsed fields to confirm accurate extraction of timestamps, user identifiers, source addresses, event outcomes, and detection-relevant attributes |
| **Log Normalization Verification** | Evaluation of field naming consistency, data formatting, and event structure alignment to ensure telemetry supports standardized detection queries |
| **Parsing Logic Testing** | Validation of extraction rules, regex patterns, and event segmentation logic used to transform raw logs into searchable structured data |
| **Search Optimization Techniques** | Use of Splunk queries to verify index consistency, field availability, and event classification accuracy across multiple data sources |
| **Threat Detection Heuristics** | Evaluation of field completeness and parsing accuracy to ensure detection logic can reliably identify suspicious behavior or anomalous activity patterns |
| **Splunk Processing Techniques** | SPL query construction, raw event inspection, field metadata analysis, and parsing validation workflows designed to support repeatable telemetry verification |
| **Operational Workflow Context** | Demonstrates parsing validation procedures used by detection engineers and SOC analysts to ensure log telemetry supports accurate alerting and investigative triage workflows |

The execution demonstrates how telemetry normalization validation supports scalable detection engineering and improves reliability of SIEM monitoring and threat investigation workflows.

---

### Intended Use

The documented execution demonstrates SIEM parsing validation, field extraction verification, and telemetry normalization techniques using Splunk. It reflects how detection engineering and SOC teams validate telemetry quality to support monitoring, alerting, and investigative workflows.

---

### Relevance to Security Operations

Detection accuracy and investigative reliability depend heavily on telemetry quality and field extraction consistency.

The execution demonstrates how parsing validation enables analysts to:

- Confirm telemetry supports reliable detection logic  
- Identify parsing gaps or normalization inconsistencies  
- Improve search efficiency and monitoring accuracy  
- Support scalable detection engineering and investigative workflows  

Even environments with mature logging pipelines require ongoing parsing validation to maintain reliable detection and investigative capability.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical SIEM parsing validation methodology, telemetry normalization verification, and professional workflow documentation aligned with real operational detection engineering and SOC environments.



