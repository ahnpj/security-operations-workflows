# Tool Usage Notes — Python-Based Log Parsing and Threat Detection Validation

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to the hands-on performance of those tasks using real tools, commands, and datasets.  
> - **Writeups** document how tasks were performed, what decisions were made, and how results were interpreted in an operational context.

This document explains the tools, platforms, and data sources used to validate detection logic directly from raw logs using Python. The goal is not to replace SIEM tooling, but to demonstrate how analysts and detection engineers verify that suspicious behavior is observable and measurable before translating logic into production detections.

All tooling in this execution is intentionally limited to Python standard libraries and native Linux utilities. This mirrors real-world situations where analysts must work in constrained environments, validate telemetry outside centralized platforms, or rapidly prototype detection logic during investigations and threat hunting.

---

## Execution Platform and Operating Environment

### Google Cloud Shell (Linux Environment)

**Purpose:** Provide a temporary Linux environment for scripting, file handling, and command-line execution without requiring local software installation.  
**How It Was Used:** All datasets, Python scripts, and execution outputs were stored and accessed within the Cloud Shell filesystem. Scripts were executed directly from the terminal using the system Python interpreter.  
**Operational Relevance:** Reflects real scenarios where analysts work on jump hosts, cloud workstations, or restricted investigation environments that limit tool installation.

Capabilities leveraged during this execution include:
- Bash shell access for file navigation and script execution
- Preinstalled Python 3 runtime
- Standard Unix utilities for file inspection and validation

---

## Programming Language and Runtime

### Python 3

```bash
python3 <script_name>.py
```

**Purpose:** Provide lightweight scripting for parsing, transforming, and aggregating log data.  
**How It Was Used:** Separate Python scripts were written for each telemetry source so that parsing logic could be tailored to the structure of each dataset:
- `parser_apache.py` — web access log parsing and IP aggregation
- `parser_auth.py` — Linux authentication failure analysis
- `parser_windows.py` — Windows failed logon analysis from CSV exports
- `parser_cloudtrail.py` — AWS API activity and IAM behavior analysis

**Operational Relevance:** Analysts frequently write short scripts to validate detection ideas, analyze exported logs, and investigate suspicious activity when centralized detection tooling is unavailable or unreliable.

Scripts were executed repeatedly while adjusting regex patterns, field extraction logic, and grouping behavior to validate that aggregation outputs were accurate and meaningful.

---

## Data Sources and Telemetry Formats

This execution intentionally uses multiple log formats to reflect the heterogeneity of telemetry encountered in real SOC environments.

### Apache HTTP Access Logs

**Format:** Plain-text, space-delimited web server logs  
**Security Use Case:** Detection of reconnaissance, scanning behavior, and abnormal request volume  
**Parsing Approach:** Regex-based extraction of source IP addresses followed by frequency aggregation

---

### Linux Authentication Logs (`auth.log`)

**Format:** Plain-text system authentication records  
**Security Use Case:** Detection of brute-force SSH login attempts  
**Parsing Approach:** Regex-based IP extraction and event counting

---

### Windows Event Logs (CSV Export)

**Format:** CSV exported from Windows Security logs  
**Security Use Case:** Detection of failed logon attempts (Event ID 4625) grouped by account and source IP  
**Parsing Approach:** Field-based filtering using column headers via dictionary access

---

### AWS CloudTrail Logs

**Format:** JSON-formatted API event records  
**Security Use Case:** Detection of privilege escalation and logging tampering attempts in cloud environments  
**Parsing Approach:** Nested JSON traversal and grouping by IAM identity

---

## Python Libraries and Parsing Techniques

Only standard-library modules were used to ensure portability and realism.

### `re` — Regular Expressions

```python
import re
```

**Purpose:** Extract structured values from unstructured text logs.  
**Usage:** Identifying IPv4 addresses and other embedded fields in Apache and SSH logs.  
**Operational Context:** Regex-based parsing mirrors how SIEM field extractions and log ingestion pipelines normalize text-based telemetry.

Compiled regex patterns were reused to improve readability and performance when scanning large files.

---

### `csv` — Structured CSV Parsing

```python
import csv
reader = csv.DictReader(file_handle)
```

**Purpose:** Parse CSV rows into dictionaries keyed by column names.  
**Usage:** Extract Windows authentication fields such as event IDs, usernames, and IP addresses.  
**Operational Context:** Field-based access ensures scripts remain stable if column ordering changes, similar to schema-based log ingestion.

---

### `json` — Structured JSON Parsing

```python
import json
events = json.load(file_handle)
```

**Purpose:** Convert JSON files into nested Python objects for direct field access.  
**Usage:** Extract CloudTrail event attributes and IAM identity details.  
**Operational Context:** Mirrors how cloud SIEM connectors parse API telemetry into searchable fields.

---

### `collections.Counter`

```python
from collections import Counter
```

**Purpose:** Count frequency of repeated values such as IP addresses or user/IP combinations.  
**Usage:** Identify dominant sources of activity and repeated authentication failures.  
**Operational Context:** Mirrors `stats count by` and aggregation operations used in SIEM detection logic.

---

### `collections.defaultdict`

```python
from collections import defaultdict
```

**Purpose:** Simplify grouping logic by auto-initializing containers.  
**Usage:** Group CloudTrail actions by IAM identity.  
**Operational Context:** Supports behavioral profiling of users and services in cloud monitoring workflows.

---

## Defensive Parsing and Error Handling

### Safe Dictionary Access

```python
event.get("userIdentity", {}).get("userName", "UNKNOWN")
```

**Purpose:** Prevent runtime failures when expected fields are missing.  
**Usage:** Ensure scripts continue processing even if telemetry is incomplete or malformed.  
**Operational Context:** Real-world logs frequently contain missing or inconsistent fields, especially during incident collection.

Defensive parsing ensures investigations are not blocked by imperfect telemetry.

---

## Behavioral Classification Logic

### High-Risk API Action Sets

```python
risky_actions = {"AttachUserPolicy", "DeleteTrail", "PutUserPolicy"}
```

**Purpose:** Identify API operations associated with privilege escalation and evasion.  
**Usage:** Flag IAM users performing sensitive actions.  
**Operational Context:** Mirrors how cloud detections classify activity based on behavior rather than single events.

This approach aligns with MITRE ATT&CK techniques related to cloud persistence and defense evasion.

---

## Output Interpretation and Validation

### Terminal-Based Review

**Purpose:** Validate aggregation output immediately after script execution.  
**Usage:** Review counts, groupings, and flagged behaviors to confirm parsing accuracy.  
**Operational Context:** Analysts routinely validate detection logic manually before deploying automated rules.

Outputs could be exported to CSV or JSON for ingestion into SIEM platforms if operationalized.

---

## Production and Automation Considerations

While Python scripting is effective for validation and investigation, scalable monitoring requires centralized platforms.

In production environments, similar workflows would be implemented using:
- Log shippers such as Filebeat or Fluent Bit
- SIEM correlation searches and scheduled queries
- Cloud-native detection services
- SOAR playbooks for automated response

Python-based parsing is best suited for:
- detection prototyping
- forensic investigations
- telemetry validation during onboarding

Once validated, detection logic should be migrated into scalable monitoring pipelines.

---

## Summary of Tools, Platforms, and Data Sources

- **Platform:** Google Cloud Shell (Linux-based analysis environment)  
- **Operating System:** Linux  
- **Programming Language:** Python 3  
- **Libraries Used:** `re`, `csv`, `json`, `collections`  
- **Data Sources:** Apache access logs, Linux authentication logs, Windows authentication events (CSV), AWS CloudTrail JSON logs  
- **Execution Method:** Command-line script execution  
- **Analysis Method:** Behavioral aggregation, frequency analysis, and identity-based grouping

These tools collectively support end-to-end validation of detection concepts directly from raw security telemetry.
