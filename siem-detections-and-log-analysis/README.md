# SIEM Detections & Log Analysis

(`siem-detections-and-log-analysis/README.md`)

This folder contains workflow executions where the **primary focus is validating log quality, developing and refining detection logic, and investigating suspicious activity using SIEM platforms**. These workflows simulate how SOC analysts and detection engineers analyze telemetry, construct queries, and validate alerts using structured, log-driven analysis.

Rather than presenting abstract detection concepts, each folder documents practical execution of SIEM-focused workflows, including searches performed, analyst reasoning behind query construction, and interpretation of results within an investigative context. The emphasis is on operational execution and analyst decision-making, similar to internal SOC detection runbooks and investigation working notes.

Each folder uses a tool-first naming convention followed by the primary operational focus (for example, `splunk-log-parsing` or `splunk-vpn-anomaly-analysis`). This allows quick identification of technologies being used while documentation itself emphasizes investigative workflow and analytical reasoning rather than SIEM tool functionality alone.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational execution scenario**. Each one documents how a specific detection validation or log analysis task is performed from initial objective through validation and operational interpretation.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how logs were analyzed, detection queries were built, and suspicious activity was validated step by step using SIEM search techniques and investigative methodology.


> **Terminology used in this category:**  
> **Workflows** refer to common security operations tasks (such as traffic filtering or session reconstruction).  
> **Executions** refer to the hands-on performance of those tasks using packet capture tools and real traffic data.  
> **Writeups** document how the task was performed and how results were interpreted.

---

### How Workflows Are Categorized

Workflows are grouped here when **log analysis, detection validation, and SIEM query development are the primary operational objectives**, rather than alert triage or full incident reconstruction.

Although these activities frequently support investigations and incident response, they are categorized separately because their primary purpose is validating telemetry quality, refining detection logic, and improving alert reliability before deeper investigative or containment actions occur.

Workflows in this category typically focus on:

- **Log parsing and field validation**, examining raw events to confirm important attributes are extracted and normalized correctly for use in searches and detections.

- **Detection logic development and refinement**, constructing, testing, and tuning queries to reduce false positives while maintaining coverage of suspicious or malicious behavior.

- **Behavioral pattern analysis**, reviewing event sequences and correlations to distinguish expected operational activity from anomalous or risky behavior.

- **Alert validation and investigation support**, analyzing triggered detections using deeper queries to determine whether escalation or response actions are warranted.

Although these workflows support investigations, they are organized here when the **core work involves validating telemetry and detection logic rather than reconstructing complete incident timelines.**

---

### Operational Context and Purpose

SIEM detection development and log analysis workflows typically occur during alert investigation, detection tuning, proactive monitoring, or telemetry validation.

At this stage of security operations, the objective is to determine whether observed events represent benign activity, misconfiguration, or indicators of malicious behavior. Analysts also evaluate whether detection rules are functioning accurately, whether logs are parsed correctly, and whether additional telemetry is required to improve visibility.

The execution examples in this category reflect real SOC and detection engineering tasks where targeted log validation and query development ensure alerts are meaningful, actionable, and supported by reliable data.

---

### What’s in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete operational SIEM detection or log analysis scenario**, including execution walkthroughs, analytical reasoning, validation outcomes, and tooling references.

Current workflow executions include:

- **Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk**  
  (`splunk-log-parsing-and-field-extraction`)  
  Focuses on inspecting raw log events, validating field extractions, and confirming normalization is adequate for reliable detection and investigation. Tasks include reviewing event structures, testing extraction logic, and verifying consistency across data sources.

- **VPN Authentication and Remote Access Anomaly Analysis Using Splunk**  
  (`splunk-vpn-remote-access-anomaly-detection`)  
  Examines authentication and VPN activity to identify abnormal login behavior, geographic anomalies, and unusual access patterns that may indicate compromised credentials or unauthorized access attempts.

Additional workflow executions will be added as monitoring coverage expands across telemetry sources, detection strategies, and investigation use cases.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how SIEM detection validation and log analysis tasks are performed in operational SOC environments.

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|--------------------|
| **`workflow-execution.md`** | Operational execution walkthrough | Step-by-step SIEM analysis workflow showing queries, filters, investigative pivots, and validation checkpoints |
| **`README.md`** | Workflow context and operational objective | Describes the detection or log validation objective, workflow scope, and how the execution supports detection and investigation workflows |
| **`analyst-notes.md`** | Analytical reasoning and operational considerations | Documents why specific detection or analysis approaches were selected and how log patterns support investigative conclusions |
| **`tool-usage-notes.md`** | Tool and query reference documentation | Explains SIEM query syntax, search optimization techniques, field usage, and analysis strategies applicable to similar detection workflows |
| **`automation-design-notes.md`** *(when present)* | Automation and scalability planning | Documents scripted data extraction, parsing strategies, and design considerations for integrating log analysis into automated detection pipelines |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains search results, event visualizations, field extraction validation, and visual confirmation of detection analysis findings |

Together, these files separate **SIEM detection execution**, **analytical reasoning**, **tool reference**, and **automation planning** into clearly reviewable components while remaining tied to the same operational workflow.

---

### How These Workflow Executions Are Designed

This category is **execution-focused**, not theoretical.

You will find:

- Realistic SIEM detection development and investigation walkthroughs
- Query construction and telemetry validation techniques
- Validation checkpoints and investigative interpretation of log behavior
- Detection support and monitoring improvements tied to log analysis outcomes
- Operational decision-making behind detection tuning and investigative pivots

Each workflow demonstrates not just how SIEM detections are developed and validated, but why reliable telemetry and well-tuned detection logic are critical for effective alert triage, threat detection, and monitoring coverage.

Documentation is intentionally structured to resemble internal SOC detection engineering procedures and analyst runbooks rather than tutorial-style instruction.

---

### Relationship to Full Incident Investigations

SIEM detection workflows directly support investigations but do not represent complete incident case studies on their own.

In operational environments, log validation and detection tuning tasks documented here are frequently performed during broader response efforts, including alert validation, incident scoping, user or host identification, and detection coverage improvement.

Full incident lifecycle investigations — including alert context, timeline reconstruction, MITRE ATT&CK mapping, artifact correlation, root cause analysis, and remediation planning — are documented separately in the:

`incident-response-and-investigations` repository

This separation reflects how professional security teams distinguish between detection engineering workflows and full investigative case documentation.

---

### Ongoing Development

Workflows in this category are continuously expanded as telemetry sources, detection strategies, threat techniques, and monitoring requirements evolve.

Some workflow directories may appear iterative or incomplete. This reflects active development and refinement rather than unfinished work. In production SOC environments, detection rules and investigative techniques are constantly tuned as new threats, data sources, and false positive patterns emerge.

The focus is on building a growing library of reusable detection validation and log analysis techniques rather than static one-time demonstrations.

