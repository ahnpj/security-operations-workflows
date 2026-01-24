# SIEM Detections & Log Analysis — Operational Execution and Analyst Notes

(`siem-detections-and-log-analysis/README.md`)

This category contains hands-on execution examples and supporting documentation for security operations tasks focused on log analysis and detection engineering within SIEM platforms. The goal of these writeups is to demonstrate how analysts validate log quality, build and refine detection logic, and investigate suspicious activity using structured, query-driven analysis.

Rather than presenting abstract detection concepts, each folder documents the practical execution of specific SIEM-focused workflows, including the searches performed, the reasoning behind query construction, and how results are evaluated in an investigative context. The emphasis is on operational process and analyst decision-making, similar to what would be captured in internal SOC detection runbooks and investigation working notes.

Each folder also uses a tool-first naming convention, followed by the primary operational focus (for example, splunk-log-parsing or splunk-vpn-anomaly-analysis). This makes it easy to see which technologies are being used at a glance, while the documentation itself emphasizes analytical process and investigative decision-making rather than tool features.

> **Terminology used in this category:**  
> **Workflows** refer to common security operations tasks (such as traffic filtering or session reconstruction).  
> **Executions** refer to the hands-on performance of those tasks using packet capture tools and real traffic data.  
> **Writeups** document how the task was performed and how results were interpreted.

---

## Operational Context and Purpose

SIEM-based investigations and detections are central to day-to-day SOC operations, where analysts rely on log data to identify, validate, and respond to suspicious activity across the environment.

At this stage of security operations, the objective is to determine whether observed events represent benign behavior, misconfigurations, or indicators of malicious activity. Analysts must also assess whether detections are firing accurately, whether logs are being parsed correctly, and whether additional telemetry is needed to improve visibility.

The execution examples in this category reflect this reality by focusing on targeted log validation and detection development tasks that help ensure alerts are meaningful, actionable, and supported by reliable data.

---

## Scope of Workflows in This Category

The execution writeups in this category focus on log-based investigation and detection validation across common enterprise telemetry sources. These activities typically occur during alert triage, detection tuning, or proactive monitoring, when analysts need to verify whether logs support reliable security conclusions.

Common objectives demonstrated in these workflows include:

- **Log parsing and field validation**, where raw events are examined to confirm that important attributes are being extracted and normalized correctly for use in searches and detections.

- **Detection logic development and refinement**, where queries are constructed, tested, and adjusted to reduce false positives while preserving coverage of suspicious behavior.

- **Behavioral pattern analysis**, where event sequences and correlations are reviewed to distinguish expected operational activity from anomalous or risky behavior.

- **Alert validation and investigation support**, where triggered detections are investigated using deeper queries to determine whether escalation or response actions are warranted.

These tasks reflect the type of focused SIEM analysis SOC analysts and detection engineers routinely perform to maintain effective monitoring and response capabilities.

---

## How These Execution Writeups Are Structured

Each execution writeup (`workflow-execution.md`) documents a specific operational objective and the technical steps used to carry it out during traffic monitoring or packet analysis activities. The documentation is structured to resemble procedural runbooks rather than academic protocol walkthroughs.

Typical sections include:

- **Operational intent and focus**, which explains the network-related security question being addressed and why the analysis is being performed.

- **Environment and execution context**, which describes the network topology, capture points, and traffic sources required to reproduce the analysis steps correctly.

- **Step-by-step execution**, which captures capture filters, display filters, tool commands, and inspection steps in the order an analyst would realistically perform them.

- **Results and interpretation**, which explains what the observed traffic indicates and how it supports investigative conclusions.

- **Operational and defensive takeaways**, which connect traffic analysis findings to detection improvements, network control adjustments, or monitoring strategies.

This structure emphasizes methodical traffic inspection and evidence-driven interpretation rather than isolated tool usage.

---

## Ongoing Development and Iteration

The examples in this category represent hands-on practice executions of SIEM-based detection and investigation tasks, documented as detection logic and log understanding are built, tested, and refined over time.

Some directories may appear incomplete or in progress. This reflects active iteration and expansion of coverage rather than abandoned content. In professional SOC environments, detection rules and investigative methods are continuously tuned as new threats, data sources, and false positive patterns are discovered.

The focus is on building a growing set of reliable detection techniques and investigative workflows rather than producing static, one-time examples.

---

## Relationship to Full Incident Investigations

The practical execution of SIEM detection and log analysis tasks in this category is designed to support and complement larger investigations, but they are not complete end-to-end incident case studies on their own.

In practice, SIEM workflows such as those documented here are often used to validate alerts, identify affected systems or users, and provide initial scoping information before deeper host- or network-based analysis occurs.

Full investigative case studies — including timeline reconstruction, artifact analysis, MITRE ATT&CK mapping, root cause determination, and remediation planning — are documented separately in the dedicated `incident-response-and-investigations` repository, which focuses on complete incident lifecycles rather than individual detection or validation procedures.

This separation mirrors how professional SOC teams distinguish between detection engineering workflows and formal incident reporting.

---

## Current Workflow Executions in This Category

The following executions of SIEM-focused security operations tasks currently exist in this category and demonstrate different approaches to detection validation and log-driven investigation:

- **Splunk Log Parsing and Field Validation** (`splunk-log-parsing`)
Focuses on inspecting raw log events, validating field extractions, and ensuring normalization is adequate for reliable detection and investigation. Tasks include reviewing event structures, testing extraction logic, and confirming consistency across data sources.

- **Splunk VPN Anomaly Analysis** (`splunk-vpn-anomaly-analysis`)
Examines authentication and VPN activity to identify abnormal login behavior, geographic anomalies, and unusual access patterns that may indicate compromised credentials or unauthorized access attempts.

Each execution example emphasizes practical detection validation and investigative techniques that can be applied during alert triage, monitoring, and detection development.
---

## Documentation Files Included in Each Execution

Each practical execution of SIEM-based tasks is documented using multiple files, separated by purpose to reflect how security teams typically organize operational knowledge and detection engineering notes.

- **`README.md`**  
  Describes the detection or investigation objective, the operational context in which the task would occur, what skills are being exercised, and how the activity maps to real SOC or detection engineering responsibilities.

- **`workflow-execution.md`**  
  Documents the step-by-step analytical execution of the task, including queries, filters, field validation, screenshots, and investigative pivots. This serves as the reproducible record of how the detection or investigation was performed.

- **`analyst-notes.md`**  
  Captures analytical reasoning and operational takeaways, such as detection limitations, false positive considerations, logging gaps, and ideas for improving monitoring coverage.

- **`tool-usage-notes.md`**  
  Focuses on SIEM-specific query behavior, search optimizations, field usage, and syntax details that are useful when applying similar techniques in other detections or investigations.

- **`automation-design-notes.md`** (when present)  
  Used in workflows that involve exporting or processing log data for downstream automation, documenting parsing strategies and design considerations for integration into detection pipelines.
