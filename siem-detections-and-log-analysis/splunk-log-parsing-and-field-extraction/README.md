# Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk — Operational Execution

**Category:** SIEM Operations & Detection Engineering  
**Operational Focus:** Ingestion pipeline design, event boundary correction, sensitive data masking, and structured field extraction for detection-ready telemetry  
**Primary Tools & Platforms:** Splunk Enterprise, Linux command line, Splunk configuration files (`inputs.conf`, `props.conf`, `transforms.conf`, `fields.conf`)

This directory contains a complete, operationally structured record of a Splunk ingestion and parsing execution focused on transforming raw, machine-generated logs into detection-ready security events. The directory contains the execution writeups, configuration changes, scripts, screenshots, and supporting analysis that together demonstrate how analysts and detection engineers shape telemetry before it is trusted for monitoring and alerting.

The goal of the execution is not to build detection searches, but to ensure that logs are correctly ingested, safely stored, and consistently structured so that downstream detections, dashboards, and investigations operate on accurate and compliant data.

At a practical level, the execution reflects how SOC and detection teams onboard new telemetry sources:

- First, scripted log sources are ingested as recurring data inputs.  
- Next, event boundaries are corrected so each real-world action becomes a single searchable event.  
- Then, multi-line entries are preserved to maintain full investigative context.  
- Sensitive values are masked during ingestion to reduce compliance exposure.  
- Finally, structured fields are extracted and indexed so detection logic can rely on stable attributes rather than raw text searches.

Throughout this execution, I focused on understanding how Splunk’s parsing pipeline operates across configuration layers rather than relying on automatic field extraction. This reinforced how ingestion quality directly determines detection reliability.

---

## How to Read This Folder

This folder separates ingestion and parsing execution from analytical interpretation and configuration reference material, similar to how SOC teams maintain telemetry onboarding documentation alongside detection development workflows.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

- All configuration and validation steps are recorded in **`workflow-execution.md`**.  
- This `README.md` explains ingestion goals and how configuration choices affect detection reliability.  
- Supporting files capture configuration reference material and analyst reasoning separately from execution timelines.

---

## What This Execution Is Specifically Doing

This execution models how analysts prepare new log sources for security monitoring by correcting ingestion and parsing problems before any detection logic is written.

First, scripted log generators are staged and ingested using `inputs.conf` so that new data sources are consistently collected and indexed. This simulates onboarding of applications, security appliances, or custom services that do not have native Splunk add-ons.

Next, event boundary behavior is corrected using parsing rules in `props.conf` so that each logical activity is indexed as a single event. Without this correction, multiple VPN connection messages may collapse into one event, breaking time-based detection logic and inflating false negatives.

After that, multi-line authentication logs are preserved using proper merge and break logic so that authentication attempts are not split across multiple indexed events. This ensures that usernames, timestamps, and failure reasons remain correlated in a single record.

Then, ingestion-time masking is implemented using regex-based transforms so that sensitive values such as credit card numbers are replaced before indexing. This allows logs to remain useful for investigations while preventing sensitive data from being searchable or retained in raw indexes.

Finally, structured fields are extracted using regular expressions defined in `transforms.conf`, and selected fields are indexed using `fields.conf` so that detection searches can pivot efficiently on usernames, servers, IP addresses, and action types without relying on slow string matching.

Each configuration change is validated through Splunk searches to confirm that parsing behavior produces clean, consistent, and searchable events suitable for operational monitoring.

---

## Why These Tasks Matter in Detection Engineering and SOC Operations

Detection logic is only as reliable as the telemetry it operates on. If parsing is incorrect, detections may silently fail or produce misleading signals.

This execution reinforces that:

- Incorrect event breaking leads to missed detections and distorted baselines.  
- Missing fields force analysts to rely on fragile keyword searches.  
- Unmasked sensitive data introduces legal and compliance risk without improving security visibility.  
- Poor ingestion design creates downstream detection engineering debt that is difficult to correct later.

SOC teams regularly troubleshoot alerts that appear “broken” when the real issue is ingestion and parsing configuration rather than detection logic itself.

By validating telemetry structure first, detection engineers can confidently build correlation rules, anomaly detection, and dashboards that reflect real operational behavior rather than ingestion artifacts.

---

## Environment, Operating System, and Tooling

### Execution Environment

All work was performed on a Linux system hosting a local Splunk Enterprise instance. The environment simulates scenarios where analysts directly manage ingestion pipelines during onboarding or troubleshooting of new data sources.

### Operating System and Access

Configuration changes were performed via terminal access with administrative privileges, and Splunk services were restarted to apply parsing configuration updates.

### Splunk Components Used

The execution relied on:

- `inputs.conf` to define scripted and file-based data inputs  
- `props.conf` to control event breaking, line merging, and masking logic  
- `transforms.conf` to perform regex-based field extraction and replacement  
- `fields.conf` to define indexed field behavior  
- Splunk Web search interface to validate parsing results

No vendor-provided add-ons or field extraction apps were used.

---

## Data Sources and Evidence Types

The execution worked with multiple synthetic but realistic datasets, including:

- VPN connection and session logs requiring event boundary correction  
- Authentication logs requiring multi-line preservation  
- Transaction-style logs containing sensitive values requiring masking

Evidence artifacts included:

- indexed Splunk events after parsing  
- configuration files controlling parsing behavior  
- validation search results confirming field extraction success

These are the same data types and validation steps used during real telemetry onboarding.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution represents the telemetry engineering stage that precedes all effective detection and investigation workflows.

In real SOC environments, similar parsing work is performed when:

- new security tools are deployed,  
- application teams begin sending logs, or  
- ingestion pipelines are migrated between platforms.

Detection engineers rely on structured, consistent fields to implement:

- correlation searches,  
- behavioral detections, and  
- anomaly models.

SOC analysts rely on clean event boundaries and preserved context to perform:

- authentication investigations,  
- incident scoping, and  
- root cause analysis.

Failures at the ingestion layer propagate downstream into detection blind spots, alert fatigue, and unreliable dashboards. This workflow reinforces why parsing engineering is a core security function rather than a purely administrative task.

---

## Files and Documentation Structure

This execution is documented using multiple files to reflect how security teams separate ingestion configuration, operational validation, and analytical reasoning.

### `README.md`

Provides high-level context explaining ingestion goals, parsing scope, and operational relevance.

### `workflow-execution.md`

Contains the complete configuration walkthrough, including edits to Splunk configuration files, service restarts, and validation searches confirming correct parsing behavior.

### `analyst-notes.md`

Documents parsing design rationale, potential pitfalls, and considerations for production environments such as index volume and field cardinality.

### `tool-usage-notes.md`

Provides focused reference material on Splunk parsing configuration syntax, stanza behavior, and regex techniques used during ingestion.

### `images/`

Contains screenshots validating successful parsing, field extraction, and masking behavior in Splunk searches.

---

## Skill Demonstration Context

This execution demonstrates SIEM ingestion and detection engineering fundamentals including scripted data onboarding, event boundary correction, multi-line preservation, sensitive data handling, and structured field extraction.

These skills directly support reliable detection development, SOC investigation workflows, and regulatory compliance requirements in enterprise environments.
