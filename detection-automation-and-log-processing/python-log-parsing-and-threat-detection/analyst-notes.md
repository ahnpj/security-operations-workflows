# Analyst Notes — Log Parsing and Threat Detection Automation Using Python

This file captures investigative reasoning, parsing decisions, and analytical patterns observed while building Python-based detection logic across multiple heterogeneous log sources.

These notes focus on how consistent behavioral detection concepts can be applied regardless of data format, and how small parsing mistakes can significantly affect detection accuracy.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

### Observations During Execution

Working across Apache logs, Linux auth logs, Windows authentication CSV exports, and CloudTrail JSON highlighted how different systems express similar behaviors using very different schemas. Despite format differences, the same analytical questions repeated across every dataset: who acted, from where, how often, and whether the action was risky or anomalous.

Creating synthetic datasets first was extremely helpful. Knowing exactly what suspicious behavior existed in each file made it much easier to validate whether parsing logic was working correctly. This mirrors how detection engineers often validate logic using controlled test events before deploying detection rules.

Regex-based IP extraction in the Apache and auth logs was effective and required minimal parsing logic. Once IP addresses were reliably extracted, behavioral grouping became trivial using Python’s Counter class.

The Windows CSV parsing step reinforced how structured logs dramatically simplify detection logic. Being able to filter directly on EventID and group by multiple fields without regex significantly reduced parsing complexity.

CloudTrail JSON parsing demonstrated how nested data structures require defensive coding. Using `.get()` with default values prevented script crashes when expected fields were missing, which is critical when processing real-world cloud telemetry.

---

### Challenges and Troubleshooting

Filename mismatches occurred multiple times when running scripts. These errors were not logic failures but operational issues, reinforcing the importance of verifying file paths and script names before debugging code.

When parsing Windows CSV logs, leftover heredoc wrapper lines caused `DictReader` to misinterpret the header row, resulting in missing keys and runtime errors. This highlighted how data cleanliness directly affects parser reliability.

Regex patterns had to be written carefully to avoid partial matches. Small mistakes in grouping could easily result in missed indicators or incorrect IP extraction.

CloudTrail parsing required careful handling of nested dictionaries. Attempting to directly access nested keys without checking for existence would have caused runtime errors if fields were missing.

---

### Investigative Reasoning and Detection Design

The workflow applied the same analytical model across every dataset:

1. Identify actor (IP, user, or account)
2. Group activity by actor
3. Count frequency of events
4. Flag risky or abnormal behaviors

This approach mirrors how SIEM correlation rules and UEBA systems operate, even though the implementation here is manual and script-based.

Rather than focusing on single suspicious events, the scripts emphasized repeated behavior, which is more reliable for identifying scanning, brute-force attempts, and privilege misuse.

Risk classification in CloudTrail analysis was performed using explicit action allowlists and denylists, reflecting how detection rules often flag sensitive API calls rather than generic activity.

---

### Limitations of This Workflow

This workflow does not include:

- Time-window correlation
- Cross-source correlation between datasets
- Geo-location analysis
- Baseline deviation modeling

The detection logic is also static and rule-based rather than adaptive.

Additionally, datasets are intentionally small and do not reflect production-scale log volumes, where performance optimization and streaming ingestion would be required.

---

### Ideas for Expansion or Automation

Future enhancements could include:

- Sliding time-window analysis for brute-force detection
- Correlation across web, auth, and cloud logs using shared IPs
- Exporting indicators into JSON for SIEM ingestion
- Adding severity scoring to detected behaviors
- Automated alert generation based on thresholds

These additions would move the workflow toward full detection engineering pipelines.

---

### Cross-Workflow Connections

This workflow supports and enhances:

- SIEM query development workflows
- Network traffic analysis workflows
- Endpoint investigation workflows
- IAM abuse investigations

Understanding how raw telemetry becomes detection logic improves alert validation and response quality.

