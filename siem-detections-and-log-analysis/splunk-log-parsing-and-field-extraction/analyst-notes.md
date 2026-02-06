# Analyst Notes — Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk

This file captures investigative reasoning, configuration decisions, and operational insights developed while onboarding and normalizing custom log sources in Splunk.

These notes focus on why specific parsing controls were applied, how ingestion problems were identified, and how normalization enables reliable detection engineering.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

### Observations During Execution

The most immediate takeaway was how quickly unusable data becomes when event boundaries are incorrect. In the VPN logs, multiple connection events collapsing into a single Splunk event made it impossible to count sessions, detect bursts, or correlate disconnect behavior. This reinforced that parsing problems are not cosmetic — they directly create detection blind spots.

The authentication logs demonstrated how dangerous default line-breaking behavior can be for multi-line records. Splitting authentication attempts across multiple events removes the narrative of what actually occurred during a login attempt. Merging based on a stable header token (`[Authentication]`) proved to be far more reliable than trying to detect line endings.

Masking credit card numbers at ingestion time felt significantly more responsible than attempting to clean data later. Once sensitive data is indexed, it becomes difficult to guarantee full removal across hot, warm, and cold buckets. Applying `SEDCMD` before indexing ensured compliance without reducing investigative value.

Field extraction on VPN logs highlighted how detection logic fundamentally changes once data is structured. Instead of searching for string fragments, it became possible to write searches based on `Username`, `Server`, and `Action` fields, which is how correlation rules and dashboards are typically built.

Creating a minimal Splunk app to hold scripts and configuration reinforced that good operational hygiene matters even in small environments. Keeping parsing rules isolated from system defaults reduces the risk of unintended side effects and makes configuration easier to track and migrate.

---

### Challenges and Troubleshooting

Early troubleshooting required separating ingestion problems from parsing problems. Data was arriving in Splunk, but formatting was wrong. This reinforced that inputs validation must always happen before modifying parsing logic.

Regex development was the most error-prone step. Small mistakes in grouping or greediness could result in partial matches or missed captures. Testing extraction logic repeatedly through Splunk searches was necessary to confirm success.

Restarting Splunk after every configuration change was easy to forget but absolutely necessary. Several apparent failures were resolved simply by restarting the service so new stanzas were loaded.

When multiple configuration files interact (`inputs.conf`, `props.conf`, `transforms.conf`, `fields.conf`), it became important to track which file was responsible for which behavior. Without that mental separation, debugging would have been much slower.

---

### Investigative Reasoning and Parsing Strategy

The workflow followed a deliberate sequence:

1. Ensure data ingestion works
2. Fix event boundaries
3. Preserve multi-line context
4. Reduce compliance risk
5. Normalize into fields
6. Validate through searches

Event boundary correction was prioritized first because incorrect events undermine every other parsing improvement.

Sensitive data masking was placed before field extraction so that extracted values would never contain protected information.

Field extraction was only attempted after event structure was stable, preventing regex from operating across malformed records.

This mirrors real-world onboarding processes where reliable event modeling precedes detection development.

---

### Limitations of This Workflow

This workflow did not include:

- Timestamp correction or timezone normalization
- Index-time versus search-time extraction comparisons
- Parsing performance optimization
- Scaling to high-volume forwarder pipelines

The environment also did not simulate ingestion failures, forwarder misconfigurations, or indexer clustering behavior.

---

### Ideas for Expansion

Potential future improvements include:

- Adding time parsing with `TIME_PREFIX` and `TIME_FORMAT`
- Implementing indexed fields for performance benchmarking
- Simulating broken forwarder configurations
- Correlating VPN and authentication logs by username
- Creating dashboards using extracted fields

These would further connect parsing work to detection and monitoring use cases.

---

### Cross-Workflow Connections

This workflow directly supports:

- SIEM detection engineering workflows
- Authentication abuse investigations
- Network access anomaly detection
- Log ingestion troubleshooting playbooks

Strong parsing discipline improves every downstream SOC function.

