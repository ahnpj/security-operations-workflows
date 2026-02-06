# Tool Usage Notes — VPN Authentication and Remote Access Anomaly Analysis Using Splunk

This document explains how Splunk SPL and JSON field extraction were used to analyze VPN authentication and session telemetry in order to surface anomalous remote access behavior. The focus is on transforming raw JSON logs into structured, queryable datasets and then applying investigative pivots that mirror how SOC analysts triage potential credential misuse, brute-force attempts, and abnormal encrypted session behavior.

Rather than relying on prebuilt detections or alerts, this execution emphasizes analyst-driven exploration, where baselines are first established and then deviations from those baselines are identified using targeted aggregations. This approach mirrors real-world SOC workflows where detection logic is often refined only after analysts understand what “normal” looks like for a given environment.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as onboarding remote access telemetry, validating authentication behavior, and establishing access baselines.  
> - **Executions** refer to the hands-on performance of those tasks using Splunk SPL, JSON field extraction, and statistical aggregation against VPN datasets.  
> - **Writeups** document how queries were constructed, what investigative pivots were used, and how results were interpreted in a SOC context.

---

### Execution Platform and Operating Environment

#### ▶ Splunk Enterprise Analysis Environment

**Purpose:** Provide a centralized platform for ingesting, parsing, and querying VPN telemetry.  
**How It Was Used:** All searches and field extraction were performed using Splunk Web against a locally indexed dataset containing JSON-formatted VPN events.  
**Operational Relevance:** Reflects common SOC and detection engineering workflows where analysts validate parsing and detection logic directly in SIEM search interfaces before automating alerts.

Environment characteristics:

- **Platform:** Splunk Enterprise (single-instance deployment)  
- **Operating System:** Linux-based host running Splunk services  
- **Access Method:** Web browser for SPL execution, terminal for service control  
- **Data Format:** JSON-formatted VPN authentication and session logs  
- **Index:** `main` index used for all test telemetry

Using JSON-formatted logs introduces the same parsing challenges encountered when onboarding cloud services, modern VPN appliances, and SaaS authentication providers.

---

### Dataset Validation and JSON Field Extraction

Before meaningful analysis can occur, analysts must confirm that telemetry is both indexed and structurally accessible.

#### ▶ Broad Dataset Validation Search

```spl
index="main"
```

**Purpose:** Confirm that VPN telemetry is present and searchable.  
**How It Was Used:** Executed as the initial sanity check before applying any filters or aggregations.  
**Operational Value:** Differentiates ingestion failures from parsing or query logic issues.

If events do not appear at this stage, parsing and detection tuning are irrelevant until ingestion problems are resolved.

#### ▶  spath — Structured JSON Field Extraction

```spl
| spath
```

**Purpose:** Parse nested JSON fields into searchable Splunk fields.  
**How It Was Used:** Applied to nearly all searches to expose fields such as `UserName`, `Source_ip`, `Source_Country`, `source_state`, `protocol`, `port`, `dest_port`, and `action`.  
**Operational Relevance:** Enables reliable field-based filtering and aggregation instead of brittle keyword matching.  
**Detection Impact:** Detection logic that depends on structured fields will fail silently if JSON extraction is not validated.

Many cloud and VPN data sources require explicit field extraction before any detection engineering can begin.

---

### Geographic and User-Based Baseline Analysis

Establishing baseline access patterns is a prerequisite for identifying anomalies.

#### ▶ Aggregation by User and Country

```spl
index="main"
| spath
| stats count by UserName, Source_Country
| sort - count
```

**Purpose:** Determine how frequently each user authenticates from each country.  
**How It Was Used:** Identify accounts associated with multiple geographic regions.  
**Investigative Value:** Surfaces potential credential compromise, VPN credential sharing, or unexpected travel-related access patterns.

This pivot is commonly used in SOC environments to flag “impossible travel” or cross-border access anomalies.

#### ▶ Excluding Expected Regions

```spl
| search Source_Country!="France"
```

**Purpose:** Remove known or trusted locations from the dataset.  
**How It Was Used:** Narrow analysis to highlight foreign access activity.  
**Operational Relevance:** Mirrors how detections are tuned to reduce benign noise and focus on higher-risk behavior.

Exclusion filters are often refined iteratively during baseline development.

---

### Failed Authentication and Brute-Force Indicators

Authentication failures are strong signals when correlated across accounts and source addresses.

#### ▶ Aggregating Failed Logins by User and Source IP

```spl
index="main" action=failed
| spath
| stats count by UserName, Source_ip
| sort - count
```

**Purpose:** Identify repeated failed login attempts targeting specific users.  
**How It Was Used:** Surface combinations of accounts and IP addresses with high failure counts.  
**Investigative Value:** Supports detection of brute-force attempts, credential stuffing, and misconfigured client behavior.  
**Operational Insight:** Differentiating by both user and IP prevents misinterpreting noisy clients as targeted attacks.

This pattern frequently forms the basis of automated brute-force detections.

---

### Regional Baseline Analysis Within Trusted Countries

Country-level analysis may still mask abnormal regional access patterns.

#### ▶ State-Level Aggregation Within the United States

```spl
index="main" Source_Country="United States"
| spath
| stats count by source_state
| sort - count
```

**Purpose:** Establish geographic distribution of VPN access within a trusted country.  
**How It Was Used:** Identify states generating disproportionate or unexpected access volume.  
**Investigative Value:** Supports detection of compromised credentials even when country appears benign.

State-level analysis is especially relevant for organizations with geographically localized workforces.

---

### Encrypted Session Termination Behavior

Session teardown patterns can indicate instability, scanning behavior, or misuse of encrypted channels.

#### ▶ Filtering Secure Teardown Events

```spl
index="main" action=teardown protocol=tcp (port=443 OR dest_port=443)
| spath
| stats count by Source_ip
| sort - count
```

**Purpose:** Identify IPs frequently closing encrypted VPN sessions.  
**How It Was Used:** Surface potential scanning behavior, unstable tunnels, or abnormal encrypted traffic flows.  
**Operational Relevance:** Teardown frequency can indicate misconfigured clients, reconnaissance, or traffic inspection issues.

Checking both `port` and `dest_port` accounts for variability in vendor log formats.

---

### Session Outcome Baseline Distribution

Understanding normal outcome ratios helps tune alert thresholds.

#### ▶ Aggregating Session Outcomes

```spl
index="main"
| spath
| stats count by action
| sort - count
```

**Purpose:** Determine how frequently sessions are accepted, failed, or torn down.  
**How It Was Used:** Establish baseline ratios for session outcomes.  
**Detection Relevance:** Enables tuning of alert thresholds based on expected failure and teardown rates.

Sudden shifts in outcome distribution often precede incident escalation.

---

### Search Optimization and Field Usage Considerations

#### ▶ Field-Based vs String-Based Filtering

Field-based searches:

```spl
action=failed
```

are more reliable and performant than string searches:

```spl
"failed"
```

**Operational Benefit:** Reduces false positives and improves scalability of detection searches.  
**Detection Engineering Impact:** Field-based logic is required for accurate alerting and dashboard consistency.

---

### Interpretation and Investigative Follow-Up

Indicators that warrant escalation or correlation:

- Users authenticating from multiple countries within short time windows  
- High failure counts from single IPs against specific accounts  
- Repeated encrypted session teardowns from uncommon sources  
- Regional access patterns inconsistent with workforce distribution

These signals should be correlated with:

- Identity provider logs  
- Endpoint detection telemetry  
- Firewall and proxy logs  
- User travel and access context

Single telemetry sources rarely provide sufficient confidence for attribution.

---

### Operational Safety and Best Practices

- Always validate JSON field extraction before building detections  
- Normalize field naming during onboarding to simplify correlation  
- Establish baselines before enforcing static thresholds  
- Document assumptions about expected access behavior  
- Validate queries against both benign and malicious scenarios

These practices reduce false positives and detection blind spots.

---

### Detection Engineering and Monitoring Relevance

Well-parsed VPN telemetry enables:

- Credential abuse detection  
- Impossible travel detection  
- Anomalous session behavior alerts  
- User-based access anomaly modeling

Most remote-access detections depend on consistent authentication metadata and reliable session outcome fields.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Splunk Enterprise on Linux  
- **Primary Tools:** Splunk Web, SPL, spath JSON extraction  
- **Data Sources:** VPN authentication and session telemetry in JSON format  
- **Execution Method:** Interactive SPL-based investigation  
- **Analysis Techniques:** Statistical aggregation, geographic pivots, outcome distribution analysis  
- **Operational Focus:** Baseline development and anomaly identification

These tools and techniques support SOC-level investigation of remote access abuse and credential compromise scenarios.

