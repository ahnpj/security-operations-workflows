# Analyst Notes — VPN Authentication and Remote Access Anomaly Analysis Using Splunk

This file captures analytical reasoning, investigative pivots, and operational observations made while analyzing VPN authentication and session telemetry in Splunk.

These notes emphasize how baseline development, aggregation strategies, and geographic context influence the ability to identify suspicious remote access behavior.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

---

### Observations During Execution

Applying `spath` early in nearly every query reinforced how essential field extraction is when working with JSON-formatted telemetry. Without structured fields, all subsequent aggregation would have required brittle string searches that would not scale to detection engineering or dashboards.

Aggregating activity by `UserName` and `Source_Country` quickly revealed that some accounts appeared across multiple countries, which is rarely expected in normal VPN usage patterns. Even without knowing which countries are "allowed," seeing multi-country access patterns for a single user immediately creates investigative leads.

Filtering out traffic from France before aggregating by user and country highlighted how exclusion filters can sharpen anomaly detection by removing known-benign or expected regions. This is similar to how SOC teams tune alerts to reduce noise from trusted networks.

The failed authentication aggregation by `UserName` and `Source_ip` showed how credential misuse patterns become obvious only when events are grouped. Individual failures look harmless, but repeated failures from the same source toward the same account resemble brute-force or misconfigured automation.

State-level aggregation within the United States demonstrated that even when country-level access appears normal, finer-grained geographic distribution can still surface anomalies. This supports use cases such as detecting impossible travel within short time windows or violations of regional access policies.

Session teardown analysis over TCP port 443 reinforced that encrypted traffic still provides meaningful behavioral signals. Even when payloads are unreadable, session outcomes and frequency can indicate scanning behavior, unstable tunnels, or misuse of VPN infrastructure.

Baseline outcome distribution using `stats count by action` helped contextualize all previous findings. Understanding whether failures or teardowns are rare or common informs how aggressively alerts should trigger in production environments.

---

### Challenges and Troubleshooting

One challenge was ensuring that all queries consistently applied `spath`. Forgetting to extract fields resulted in empty or misleading aggregation results, which could easily be mistaken for lack of suspicious activity rather than parsing failure.

Another difficulty was distinguishing between meaningful anomalies and artifacts of the simulated dataset. Without historical baselines, it becomes harder to judge whether counts are truly abnormal, reinforcing why baselining is essential before operationalizing detections.

Ensuring correct field names (`Source_ip` vs `source_ip`, `Source_Country` vs `source_country`) required careful attention, as JSON field capitalization must be referenced exactly in SPL.

When filtering for port 443, using both `port=443` and `dest_port=443` highlighted how different log formats may populate different fields for the same concept, requiring flexible search logic.

---

### Investigative Reasoning and Workflow Design

The workflow was structured to move from broad baseline understanding into more targeted anomaly detection:

1. Validate dataset and field extraction
2. Identify geographic anomalies by user
3. Identify authentication failures by account and source
4. Analyze regional behavior within trusted countries
5. Inspect encrypted session teardown behavior
6. Establish baseline outcome distributions

This progression mirrors SOC triage, where analysts first establish what is normal before deciding what is suspicious.

Geographic analysis was performed before authentication failure analysis because account compromise may present as legitimate successful logins from unexpected locations, not just repeated failures.

Session teardown analysis was placed after authentication review to determine whether abnormal network behavior occurred even after access was granted.

Baseline outcome analysis at the end helped contextualize whether earlier findings represented rare events or normal operational noise.

---

### Limitations of This Workflow

This workflow does not include:

- Time-window analysis for burst detection
- Correlation across multiple VPN appliances
- Integration with endpoint telemetry
- Detection of concurrent sessions from distant locations

As a result, the analysis focuses on static aggregation rather than temporal or cross-system correlation.

Additionally, the dataset does not represent production-scale VPN telemetry, where performance optimization and indexed fields become critical.

---

### Ideas for Expansion or Automation

Potential enhancements include:

- Detecting impossible travel using timestamp correlation
- Alerting on threshold-based failure rates per account
- Building dashboards showing geographic access heatmaps
- Correlating VPN logins with endpoint authentication logs
- Adding device fingerprint or client version analysis

These would extend exploratory analysis into continuous monitoring.

---

### Cross-Workflow Connections

This workflow directly supports:

- SIEM detection engineering workflows
- Identity abuse investigations
- Network perimeter monitoring
- Zero-trust access validation

Understanding VPN behavior improves confidence when investigating lateral movement and credential misuse alerts.

