# VPN Authentication and Remote Access Anomaly Analysis Using Splunk — Operational Execution

**Category:** SIEM Investigations & Access Monitoring  
**Operational Focus:** Authentication analysis, geographic anomaly detection, session outcome analysis, and baseline development using VPN telemetry  
**Primary Tools & Platforms:** Splunk Enterprise, SPL (Search Processing Language), JSON field extraction using `spath`

This directory contains a complete, operationally structured record of an investigation-style analysis of VPN authentication and session telemetry using Splunk. The directory contains execution writeups, SPL queries, screenshots, and supporting analysis that demonstrate how analysts identify abnormal remote access behavior using aggregated log data.

The goal of the execution is not to respond to a confirmed breach, but to practice how analysts establish behavioral baselines, identify outliers, and surface accounts or sources that warrant deeper investigation before escalation.

At a practical level, the execution reflects how SOC analysts evaluate remote access infrastructure:

- First, raw JSON telemetry is validated and normalized so that fields can be queried consistently.  
- Next, access patterns are grouped by identity and geography to establish baseline behavior.  
- Then, authentication failures are isolated to identify possible brute-force or credential misuse.  
- Session lifecycle events are analyzed to identify abnormal termination behavior.  
- Aggregation is used to highlight high-risk users and source IP addresses for escalation.

Throughout this execution, I focused on analyst-driven exploration rather than alert-driven response. This reinforced how detection engineering often begins with manual pattern discovery before formal thresholds and alerts are created.

---

## How to Read This Folder

This folder separates SPL execution from analytical interpretation and investigative reasoning, similar to how SOC teams document hunts and anomaly investigations.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to investigative tasks such as access anomaly detection and authentication validation.  
> - **Executions** refer to hands-on analysis using SPL queries and structured field extraction.  
> - **Writeups** document analytical decisions, investigative pivots, and interpretation of results.

- All queries and investigative pivots are recorded in **`workflow-execution.md`**.  
- This `README.md` explains investigative goals, methodology, and relevance to access monitoring.  
- Supporting files capture analytical observations and SPL reference material.

---

## What This Execution Is Specifically Doing

This execution models how analysts evaluate VPN telemetry to identify misuse of remote access services.

First, JSON-formatted VPN logs are validated using `spath` to extract nested fields such as usernames, source IP addresses, countries, states, transport protocols, ports, and session outcomes. This ensures that all later aggregation steps operate on structured attributes rather than raw message text.

Next, events are aggregated by user and country to identify accounts generating sessions across multiple geographic regions. This establishes baseline access behavior and highlights users whose access patterns deviate from expected travel or corporate geography profiles.

After that, authentication failures are isolated and grouped by user and source IP address. This allows identification of repeated failures against specific accounts or from specific sources, which may indicate brute-force attempts, credential stuffing, or unstable client configurations.

Then, access events are filtered to only United States traffic and grouped by state-level metadata. This provides more granular geographic baselining and supports detection of unusual regional access patterns that may not appear suspicious at the country level.

Following that, session termination events over encrypted protocols are isolated by filtering on action types and transport ports. Grouping these events by source IP reveals systems that frequently tear down encrypted sessions, which may indicate scanning behavior, unstable tunnels, or misuse of VPN infrastructure.

Finally, overall session outcomes are aggregated across the entire dataset to establish distribution baselines for accepted, failed, and terminated sessions. These distributions support future threshold tuning and alert severity calibration.

Each step mirrors how analysts move from broad distribution analysis into targeted investigative pivots.

---

## Why These Tasks Matter in SOC Operations

VPN infrastructure represents a primary ingress point into enterprise networks. Compromised credentials and remote access abuse are common precursors to lateral movement and data exfiltration.

SOC teams analyze VPN telemetry to detect:

- credential compromise,  
- unauthorized geographic access,  
- automated authentication attempts, and  
- misuse of encrypted tunnels.

However, these detections are only effective if:

- fields are reliably extracted,  
- baselines are understood, and  
- aggregation logic reflects realistic attacker behavior.

This workflow reinforces how simple statistical aggregation can surface meaningful security signals when logs are properly structured.

---

## Environment, Operating System, and Tooling

### Execution Environment

All analysis was performed in a controlled Splunk environment using simulated VPN logs designed to reflect realistic authentication and session telemetry.

### Data Format

Logs were stored in JSON format, requiring explicit field extraction using `spath` before aggregation and filtering.

### Tools Used

The execution relied on:

- Splunk Enterprise search interface  
- SPL aggregation commands such as `stats`, `sort`, and `search`  
- JSON parsing using `spath`

No enrichment feeds, identity providers, or endpoint telemetry were used in this workflow.

---

## Data Sources and Evidence Types

Evidence analyzed included:

- VPN authentication attempts  
- VPN session lifecycle events  
- geolocation metadata embedded in connection records  
- source IP addresses and transport-layer details

These are the same telemetry categories used by enterprises to monitor remote workforce access.

---

## Operational Relevance to SOC and Detection Engineering Workflows

This execution represents the exploratory analysis phase that precedes formal detection rule development.

In real SOC environments, analysts perform similar investigations when:

- onboarding new VPN platforms,  
- responding to account compromise alerts, or  
- validating unusual access reports from users or IT staff.

Detection engineers use the results of these analyses to:

- design anomaly detection thresholds,  
- define correlation rules across identity and network telemetry, and  
- prioritize which behaviors should trigger alerts versus informational dashboards.

The same aggregation patterns demonstrated here often become:

- scheduled searches,  
- risk scoring inputs, and  
- identity-based alerting logic.

Without this validation step, detection logic may be built on assumptions that do not match real traffic patterns, leading to alert fatigue or missed intrusions.

---

## Files and Documentation Structure

This execution is documented using multiple files to mirror how SOC teams separate investigation timelines, analytical reasoning, and query reference material.

### `README.md`

Provides investigative scope, analytical objectives, and operational relevance.

### `workflow-execution.md`

Contains the full SPL query sequence, screenshots, aggregation outputs, and validation steps demonstrating each investigative pivot.

### `analyst-notes.md`

Documents hypotheses, interpretation of anomalies, and considerations for escalation or additional telemetry correlation.

### `tool-usage-notes.md`

Provides SPL reference material and explanations of query techniques used for field extraction and aggregation.

### `images/`

Contains screenshots validating query outputs and anomaly detection results.

---

## Skill Demonstration Context

This execution demonstrates SOC-relevant investigation skills including JSON field extraction, authentication anomaly detection, geographic baselining, session lifecycle analysis, and iterative investigative pivoting using SPL.

These techniques directly support monitoring of remote access infrastructure and early detection of credential misuse.
