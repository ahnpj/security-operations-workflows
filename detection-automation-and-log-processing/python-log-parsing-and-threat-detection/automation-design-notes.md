# Automation Design Notes — Python-Based Log Parsing and Threat Detection Signals

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as log onboarding, behavioral validation, and signal interpretation.  
> - **Executions** refer to the hands-on parsing and aggregation of telemetry using custom Python scripts and raw data sources.  
> - **Writeups** document how detection-relevant behaviors were identified, validated, and reasoned about in an investigative context.

This document explores how the behaviors validated during the Python log-parsing execution could be translated into scalable detection logic and automated enrichment workflows in production environments. The goal is not to design a single monolithic alert, but to identify which behavioral signals are strong enough to operationalize, what data dependencies they require, and how they would realistically be implemented across SIEM, SOAR, and detection engineering pipelines.

Rather than treating automation as a replacement for analyst reasoning, this design focuses on preserving the same investigative pivots demonstrated in the execution and encoding them into repeatable, auditable, and monitorable detection components.

---

## From Manual Aggregation to Detection Signals

During the execution, raw logs were parsed and aggregated using Python dictionaries and counters in order to answer investigative questions such as:

- Which source IPs are generating unusually high volumes of requests?
- Which accounts are experiencing repeated authentication failures?
- Which cloud identities are performing repeated or sensitive actions?
- Which request paths or event types cluster around specific actors?

In production detection systems, these same questions are expressed using time-windowed aggregations, threshold logic, and entity-based correlation rather than one-off script outputs. The Python scripts serve as a functional prototype of detection logic by proving that signal exists in telemetry before formalizing it into alert rules.

---

## Candidate Detection Patterns Identified

### High-Volume Requests from Single Source

**Observed Behavior:** Certain IP addresses generated disproportionately high numbers of web requests.  
**Detection Translation:**

- SIEM search that aggregates requests by source IP over sliding time windows.
- Thresholds tuned using baseline traffic distributions.
- Alert enrichment with geolocation, ASN, and historical behavior.

**Operational Use Case:** Detection of scanning activity, scraping, brute-force attempts, or bot traffic.

---

### Repeated Authentication Failures

**Observed Behavior:** Multiple failed login attempts grouped by source IP and target account.  
**Detection Translation:**

- Correlation rule combining authentication failure events by account and source.
- Adaptive thresholds based on account role sensitivity.
- Optional lockout or MFA challenge workflows triggered via SOAR.

**Operational Use Case:** Credential stuffing, brute-force attempts, or compromised password reuse.

---

### Identity-Based Cloud Activity Clustering

**Observed Behavior:** Specific cloud identities performing repeated sensitive actions.  
**Detection Translation:**

- Identity-based aggregation of API calls and privileged operations.
- Mapping to MITRE ATT&CK cloud techniques.
- Alerts scoped by IAM role sensitivity.

**Operational Use Case:** Compromised service accounts, insider misuse, or automation abuse.

---

### Resource Access Concentration

**Observed Behavior:** Requests repeatedly targeting the same resources or endpoints.  
**Detection Translation:**

- Behavioral models that track repeated access to high-value assets.
- Correlation with endpoint, identity, and vulnerability context.

**Operational Use Case:** Data exfiltration staging or targeted reconnaissance.

---

## Detection Engineering Implementation Options

### SIEM-Based Search and Correlation Rules

For organizations using SIEM platforms:

- Python logic maps directly to:
  - `stats count by entity` queries
  - time-bucketed aggregations
  - conditional thresholds
- Field normalization is critical so that:
  - IPs
  - usernames
  - resource identifiers
  remain consistent across data sources

Detection lifecycle:

1. Validate signal exists using exploratory searches.
2. Define thresholds using baseline data.
3. Deploy rule in low-severity monitoring mode.
4. Adjust false positive rate before escalation.

---

### Streaming and Near-Real-Time Detection

For environments using streaming pipelines:

- Signals could be implemented as:
  - sliding window aggregations
  - stream joins across telemetry types
- Enrichment stages can attach:
  - threat intelligence
  - identity metadata
  - asset criticality

Streaming detection is especially relevant for high-volume web and API telemetry.

---

### SOAR and Automated Enrichment

Automation is most effective when focused on enrichment rather than immediate response.

Potential enrichment steps:

- IP reputation lookups
- Account privilege level queries
- Recent authentication history correlation
- Host association checks

This mirrors the investigative pivots performed manually during the execution but executes them automatically when alerts fire.

---

## Data Dependencies and Telemetry Requirements

Automation quality depends entirely on telemetry quality.

Minimum requirements:

- Consistent timestamp formatting
- Stable field naming for:
  - source IP
  - user identity
  - resource or endpoint
- Event type classification

Optional enhancements:

- GeoIP enrichment
- User role and group membership
- Asset inventory tagging

If telemetry is inconsistent, detection logic becomes brittle and high-noise.

---

## Risk of Over-Automation

Not all behaviors should immediately become blocking controls.

Risks include:

- Alert fatigue from poorly tuned thresholds
- Blocking legitimate automation traffic
- Escalation loops caused by benign anomalies

Recommended approach:

- Start with detection and visibility
- Validate investigative usefulness
- Introduce response automation only after stable baselines are established

This mirrors how mature SOC programs phase in automation.

---

## Feedback Loop Between Analysts and Automation

Detection engineering should remain tightly coupled to analyst workflows.

Recommended process:

1. Analysts validate new behaviors manually.
2. Detection engineers encode stable patterns into rules.
3. SOC validates alerts during triage.
4. Feedback is used to refine both parsing and logic.

This loop ensures that automation supports analysts rather than replacing them.

---

## Relevance to SOC and Detection Engineering Workflows

This execution demonstrates the early-stage phase of detection engineering where:

- telemetry is explored
- signal is validated
- false positives are understood
- detection hypotheses are tested

Many production detection failures occur because this exploratory phase is skipped. By proving signal validity through scripting, detection logic becomes defensible and explainable.

This approach aligns with:

- threat hunting workflows
- detection prototyping
- blue-team research pipelines

---

## Summary of Automation Design Considerations

- Python scripts serve as behavioral prototypes, not production detectors.
- Detection logic should preserve investigative intent, not just numeric thresholds.
- Enrichment often provides more value than immediate blocking.
- Telemetry consistency is more important than algorithm complexity.
- Human review remains critical during early detection lifecycle stages.

These principles guide how manual analysis transitions into scalable security automation.
