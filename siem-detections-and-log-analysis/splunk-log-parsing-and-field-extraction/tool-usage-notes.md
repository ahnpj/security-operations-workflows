# Tool Usage Notes — Log Parsing, Event Normalization, and Field Extraction Using Splunk

This document explains how Splunk’s ingestion and parsing toolchain was used to convert raw scripted log output into structured, detection-ready events. The focus is on controlling event boundaries, preserving multi-line context, protecting sensitive values, and extracting consistent fields that downstream searches and detections can reliably reference.

All tooling used in this execution is native to Splunk Enterprise and standard Linux utilities. No forwarders, external parsers, or add-ons were used. This mirrors early-stage onboarding and troubleshooting scenarios where analysts must directly manipulate configuration files to correct parsing behavior before production pipelines can be trusted.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry, validating parsing behavior, and protecting sensitive data during ingestion.  
> - **Executions** refer to the hands-on configuration and validation of Splunk ingestion and parsing pipelines using real log generators and configuration files.  
> - **Writeups** document configuration decisions, troubleshooting steps, validation checks, and how parsing outcomes affect detection reliability.

---

### Execution Platform and Operating Environment

#### ▶ Local Splunk Enterprise Instance on Linux

**Purpose:** Provide a full parsing pipeline where ingestion, transformation, and validation can be tested end-to-end.  
**How It Was Used:** Splunk was installed locally and configured through direct edits to configuration files, followed by service restarts and validation searches in Splunk Web.  
**Operational Relevance:** Mirrors how analysts and detection engineers often validate parsing logic in test environments before promoting configurations to production.

Environment characteristics:

- **Operating System:** Ubuntu-based Linux distribution  
- **Splunk Version:** Splunk Enterprise (single-instance deployment)  
- **Access Method:** Local terminal for configuration, browser for Splunk Web  
- **User Context:** Root or Splunk service account for file edits and service control  
- **Execution Mode:** Direct modification of `.conf` files rather than UI-based inputs

This setup exposes the full ingestion pipeline and makes parsing behavior transparent and debuggable.

---

### Splunk App Framework and Configuration Isolation

#### ▶ Custom App Container (`DataApp`)

**Purpose:** Provide an isolated location to store scripted inputs and related parsing configurations.  
**How It Was Used:** A minimal Splunk app named `DataApp` was created to house scripts and associated `inputs.conf` stanzas rather than modifying global system defaults.  
**Operational Relevance:** In production environments, isolating parsing logic by app supports version control, deployment automation, and rollback strategies.

App-based configuration also reduces the risk that experimental parsing changes will affect unrelated data sources.

---

### Scripted Data Generation and Ingestion

#### ▶ Script-Based Telemetry Generators

**Purpose:** Simulate realistic log sources that commonly cause parsing problems.  
**How It Was Used:** Three executable scripts were staged in the app’s `bin` directory to generate:  
- VPN connect and disconnect records  
- Multi-line authentication records  
- Purchase logs containing sensitive numeric values  

**Operational Relevance:** Many real-world telemetry sources are not cleanly formatted syslog feeds and require custom ingestion strategies.

#### ▶ inputs.conf — Scripted Input Stanzas

```conf
[script:///opt/splunk/etc/apps/DataApp/bin/vpnlogs]
index = main
source = vpn
sourcetype = vpn_logs
interval = 5
```

**Purpose:** Execute scripts on a schedule and treat standard output as event data.  
**How It Was Used:** Each generator script was assigned its own stanza with consistent metadata fields.  
**Operational Relevance:** Mirrors how API pollers, collectors, and scheduled scripts ingest data in production SIEM pipelines.

Setting explicit `source` and `sourcetype` values ensures downstream parsing rules apply consistently.

---

### Event Boundary Control and Multi-Line Handling

Correct event breaking is foundational for detection accuracy, correlation, and timeline analysis.

#### ▶ MUST_BREAK_AFTER — Line-Terminated Records

```conf
[vpn_logs]
SHOULD_LINEMERGE = true
MUST_BREAK_AFTER = (DISCONNECT|CONNECT)
```

**Purpose:** Force event boundaries after specific keywords.  
**How It Was Used:** Ensure each VPN connection or disconnection line became a single Splunk event.  
**Operational Relevance:** Prevents multiple sessions from collapsing into a single record, which would break counting and correlation.

This method is appropriate when each record ends with a consistent marker.

#### ▶ BREAK_ONLY_BEFORE — Header-Based Multi-Line Events

```conf
[auth_logs]
SHOULD_LINEMERGE = true
BREAK_ONLY_BEFORE = \[Authentication\]
```

**Purpose:** Start new events only when a stable header line appears.  
**How It Was Used:** Preserve full authentication sequences as single logical events.  
**Operational Relevance:** Maintains investigative context when logs span multiple lines.

Multi-line preservation is critical for stack traces, authentication traces, and application errors.

---

### Sensitive Data Protection at Ingestion

#### ▶ SEDCMD — Regex-Based Masking

```conf
SEDCMD-cc = s/\d{4}-\d{4}-\d{4}-\d{4}/XXXX-XXXX-XXXX-XXXX/g
```

**Purpose:** Replace sensitive patterns before data is indexed.  
**How It Was Used:** Mask credit card values while preserving surrounding event context.  
**Compliance Relevance:** Supports PCI-style requirements by preventing regulated data from entering the index.

Ingestion-time masking is safer than relying on search-time redaction controls.

---

### Field Extraction and Normalization

Structured fields are required for scalable detection logic and reliable dashboards.

#### ▶ transforms.conf — Reusable Extraction Rules

```conf
[vpn_custom_fields]
REGEX = User:\s(\w+\s\w+),.+Server:\s(.+),.+Action:\s(\w+)
FORMAT = Username::$1 Server::$2 Action::$3
WRITE_META = true
```

**Purpose:** Capture multiple related values from raw text in a single pass.  
**How It Was Used:** Extracted Username, Server, and Action values from VPN events.  
**Operational Relevance:** Enables field-based searches instead of brittle keyword matching.

Reusable transforms support consistent parsing across multiple sourcetypes.

#### ▶ props.conf — Binding Transforms to Sourcetypes

```conf
[vpn_logs]
TRANSFORMS-vpn = vpn_custom_fields
```

**Purpose:** Apply extraction logic only to relevant data.  
**How It Was Used:** Prevented VPN regex rules from running against unrelated datasets.  
**Operational Relevance:** Reduces false extractions and improves parsing performance.

#### ▶ fields.conf — Indexed Field Declaration

```conf
[Username]
INDEXED = true
```

**Purpose:** Promote selected fields to indexed status.  
**How It Was Used:** Demonstrate how performance tradeoffs are managed for high-value fields.  
**Operational Tradeoff:** Indexed fields increase storage usage but accelerate frequent searches.

This decision is typically reserved for fields heavily used in detections and dashboards.

---

### Validation and Troubleshooting Workflow

#### ▶ Splunk Web Searches

```spl
index=main sourcetype=vpn_logs Username=*
```

**Purpose:** Confirm event boundaries and field extraction accuracy.  
**Operational Relevance:** Primary validation mechanism for ingestion pipeline health.

Analysts should validate both event counts and field population.

#### ▶ Service Restarts

```bash
/opt/splunk/bin/splunk restart
```

**Purpose:** Reload parsing configuration files.  
**Operational Reality:** Required for most ingestion-time changes to apply.

Restart discipline is critical during parsing development cycles.

---

### Configuration File Responsibilities

| File | Operational Role |
|--------|------------------|
| inputs.conf | Defines how data enters Splunk |
| props.conf | Controls parsing and rule application |
| transforms.conf | Defines extraction and transformation logic |
| fields.conf | Declares indexed fields |
| indexes.conf | Controls data storage and retention |
| outputs.conf | Controls forwarding behavior |

Knowing which file to modify is essential for efficient troubleshooting.

---

### Operational Safety and Best Practices

- Avoid modifying system default configuration files when testing  
- Validate regex against sample logs before deployment  
- Mask sensitive values before indexing whenever possible  
- Confirm event counts after changing break rules  
- Document parsing assumptions and limitations

These practices support defensible SOC operations and reduce regression risk.

---

### Detection Engineering and Monitoring Relevance

Proper parsing enables:

- Accurate threshold-based detections  
- Reliable behavioral correlation  
- Field-based anomaly detection  
- Consistent dashboard metrics

Most detection failures originate from ingestion and normalization errors rather than search logic.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Linux host running Splunk Enterprise  
- **Configuration Tools:** inputs.conf, props.conf, transforms.conf, fields.conf  
- **Ingestion Sources:** Scripted telemetry generators  
- **Execution Interfaces:** Linux shell and Splunk Web  
- **Data Types:** VPN events, authentication traces, transaction logs  
- **Analysis Method:** Parsing validation, field extraction verification, and compliance protection

These tools collectively support the telemetry onboarding workflows required for effective SOC monitoring and detection engineering.

