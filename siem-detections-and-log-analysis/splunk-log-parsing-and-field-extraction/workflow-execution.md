# Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk

### Overview

This workflow documents practical log parsing and field extraction using Splunk to transform raw or inconsistently formatted log data into structured, searchable, and detection-ready telemetry. The workflow focuses on extracting meaningful attributes from unstructured log events, validating parsing accuracy, and preparing datasets that support monitoring, detection engineering, and investigative analysis.

The analysis progresses through structured parsing phases that begin with raw log exploration, followed by field extraction using search processing language (SPL), regular expressions, and transformation logic. The workflow demonstrates how log normalization improves data visibility, query reliability, and investigative efficiency within security operations environments.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshots**, see the **[Step-by-Step Execution Walkthrough](#step-by-step-execution)** section below.

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to perform structured log parsing and field extraction using Splunk to convert raw log telemetry into normalized and searchable datasets. The workflow focuses on identifying key security-relevant attributes such as timestamps, usernames, event actions, authentication outcomes, and source network indicators.

The workflow demonstrates how log parsing supports detection preparation by improving field consistency, enabling reliable query development, and reducing noise within monitoring pipelines. It emphasizes how structured telemetry allows analysts to pivot across events, correlate activity across datasets, and build detection logic that relies on consistent data formatting.

#### ▶ Analyst Focus

The analyst focus is on using Splunk to analyze raw log datasets, apply field extraction techniques, validate parsing accuracy, and confirm that extracted telemetry supports detection and investigative workflows. The workflow reflects responsibilities commonly performed by SOC analysts and detection engineers when onboarding new log sources or improving telemetry quality.

The workflow reinforces how analysts evaluate parsing logic, validate extracted attributes against original log content, and ensure that normalized telemetry supports alerting, correlation, and threat-hunting activities. It highlights the importance of understanding log structure, extraction techniques, and data quality validation when preparing telemetry for operational security monitoring.

This workflow is framed as a realistic SOC parsing and onboarding task for a fictional organization (“CyberT”) that is ingesting machine-generated logs from a custom source. The logs are produced by local scripted generators and intentionally exhibit common ingestion problems that analysts and detection engineers routinely have to correct before detections can be trusted.

The scope of work is intentionally “pipeline-first” rather than “search-first.” Each objective below describes the operational outcome that must be achieved in Splunk so that downstream searches and detections behave consistently.

- **Ingest scripted outputs as first-class data sources (`inputs.conf`).** The scripted generators are treated as log-producing sources whose output must be collected on a recurring interval. In practice, this is the baseline requirement for onboarding new telemetry into a SIEM: data must land in the intended index, carry the intended `source` and `sourcetype`, and be queryable immediately after ingestion.
- **Establish correct event boundaries (`props.conf`).** When Splunk fails to break events properly, multiple real-world actions can collapse into a single event (or a single action can fragment into multiple events). This workflow uses stanza-scoped parsing controls (for example, `MUST_BREAK_AFTER`) to ensure that each logical action becomes one searchable Splunk event.
- **Preserve multi-line log entries as single logical events.** Many authentication and application logs span multiple lines. The workflow applies merge + break-before logic (`SHOULD_LINEMERGE` with `BREAK_ONLY_BEFORE`) so Splunk stores the complete entry as one event, preserving investigative context that would otherwise be split across unrelated records.
- **Mask sensitive values during parsing (`SEDCMD`).** Some log sources contain values that should not be retained in cleartext (for example, payment card data). This workflow applies ingestion-time redaction using `SEDCMD` (sed-style regex replacement) so the data remains useful for analytics without creating unnecessary compliance exposure.
- **Extract custom fields for detection-ready filtering (`transforms.conf` + `props.conf`).** Raw text searches are fragile and hard to tune. The workflow uses regex-based extraction rules in `transforms.conf` and applies them via `props.conf` `TRANSFORMS-*` bindings so important values (for example, Username / Server / Action) become first-class fields. Where appropriate, fields are optionally declared as indexed via `fields.conf` to support fast, field-based searches.

<blockquote>
I started by reviewing the role of configuration files such as "inputs.conf", "props.conf", and "transforms.conf", and how to use these files to extract and filter fields. I paid attention to the context of why parsing is so critical for a SOC analyst: poorly parsed logs can lead to incomplete data, false negatives, or gaps in investigations.
</blockquote>

---

### Environment and Execution Context

This section documents the tools, evidence sources, and constraints used to perform the workflow so that results can be interpreted consistently and reproduced by another analyst.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on software, tools, environment, data sources, and more.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

The workflow is executed in a controlled Linux environment where the analyst has local access to a Splunk Enterprise instance and a set of scripted log generators. The scenario assumes a SOC analyst is onboarding and normalizing a new custom log source for a fictional company (“CyberT”), with the goal of producing detection-ready events that can be queried reliably.

Scripts are staged under `/Downloads/scripts/`, and the workflow assumes root-level command execution (for example, using `nano` for file edits and running Splunk lifecycle commands). Splunk is accessed through Splunk Web, and parsing changes are applied by editing `.conf` files and restarting Splunk so the updated stanzas take effect.

</details>

<details>
<summary><strong>▶ Tooling and Constraints</strong><br>
</summary><br>

- Splunk Enterprise (local instance, accessed via Splunk Web)
- Splunk configuration files: `inputs.conf`, `props.conf`, `transforms.conf`, `fields.conf` (and additional config files reviewed for context: `indexes.conf`, `outputs.conf`, `authentication.conf`)
- Linux (Ubuntu-based) command line (root execution context), including `cd`, `ls`, and `nano`
- Browser (FireFox) to access Splunk Web

</details>

<details>
<summary><strong>▶ Data Sources Analyzed</strong><br>
</summary><br>

The workflow uses three scripted log generators (located in the scripts directory) to produce different parsing challenges:

- `vpnlogs` — connection/disconnection lines that require explicit event boundaries.
- `authentication_logs` — multi-line authentication entries that must be merged into single events.
- `purchase-details` — purchase logs containing credit card values that must be masked prior to indexing.

</details>


<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

1. Validate the local scripts directory and identify available log generators.
2. Review Splunk’s parsing pipeline concepts: format → sourcetype → parsing rules → field extraction → verification.
3. Review key Splunk configuration files and the stanza model that controls parsing behavior.
4. Create a simple Splunk app (`DataApp`) to hold scripts and inputs.
5. Ingest scripted data sources and confirm indexing.
6. Fix event boundaries for VPN logs using `props.conf` breaking settings.
7. Fix multi-line authentication events using merge + break-before logic.
8. Mask credit card values using `SEDCMD` to reduce compliance risk.
9. Extract custom fields using `transforms.conf` and apply them via `props.conf`, optionally indexing fields via `fields.conf`.
10. Validate the resulting data model using searches and field-based filtering.

</details>

---

### Step-by-Step Execution

This section documents the workflow in the same order an analyst would realistically perform log parsing and telemetry normalization using Splunk. 

The process begins with exploring raw log datasets to understand event structure, field patterns, and available data sources. Analysis then progresses into applying search filters, building field extraction logic, and validating extracted attributes against original log entries.

Each step captures both the technical actions performed using SPL and the analytical reasoning behind those actions. The workflow intentionally progresses from raw data inspection into structured field extraction and dataset validation, mirroring how analysts transform unstructured telemetry into detection-ready data during log onboarding and detection engineering workflows.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 1 — Scenario Orientation and Script Inventory</strong><br>
→ establishing the working directory, confirming where scripts live, and identifying the scripted data sources that will be ingested
</summary><br>

**Goal:** Establish the working directory, confirm where scripts live, and identify the scripted data sources that will be ingested into Splunk.

##### 🔷 Phase 1.1 — Navigate to the scripts directory  
Scripts are provided in `/Downloads/scripts/`, and commands are executed as a root user. The shell prompt indicates an Ubuntu-based system (for example, `ubuntu@tryhackme`), which matters because paths and service management commands follow Linux conventions.

Navigation was performed using:

```bash
cd Downloads/scripts
```

<p align="left">
  <img src="images/lab10-splunk-data-manipulation-figure01.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="500"><br>
  <em>Figure 1</em>
</p>

##### 🔷 Phase 1.2 — Enumerate directory contents and confirm the available scripted generators  

The directory contents were listed using `ls`, and the output showed three items: `authentication_logs`, `purchase-details`, and `vpnlogs`. These items do not use the typical `.py` extension that often identifies Python scripts. However, scripts can exist without a `.py` extension (for example, executable scripts using a shebang such as `#!/usr/bin/python3`), and training environments frequently name executables without extensions.

<p align="left">
  <img src="images/lab10-splunk-data-manipulation-figure01.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="500"><br>
  <em>Figure 2</em>
</p>

**Analyst validation check:**

**Prompt:** Confirm how many script-based generators are present under `/Downloads/scripts/` so the ingestion scope is explicit.  
**Conclusion:** Three generator scripts are present: `authentication_logs`, `purchase-details`, and `vpnlogs`.

**What this establishes:** Before any ingestion configuration is changed, an inventory of available sources is confirmed. This reduces the likelihood of configuring inputs against the wrong path or misidentifying which script corresponds to which parsing problem.

<blockquote>
This reinforced how to quickly navigate through the Linux file system and inspect directories using commands like `cd` and `ls`. It also reminded me that while most Python scripts are saved with a `.py` extension, technically any file could contain Python code if it starts with a proper line (e.g., #!/usr/bin/python3). However, in practical scenarios and exams, the `.py` extension is the standard indicator.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 2 — Splunk Parsing Pipeline Concepts</strong><br>
→ understanding how Splunk converts raw input into searchable events and which configuration objects influence each stage
</summary><br>

**Goal:** Understand how Splunk converts raw input into searchable events and which configuration objects influence each stage.

<blockquote>
I walked through the high-level process Splunk uses to parse data. First, I studied how Splunk needs to understand the format of incoming data, whether it’s JSON, XML, syslog, or CSV. Next, I saw how every dataset is assigned a sourcetype, which tells Splunk what parsing rules to apply. I then worked with examples of how to configure the "props.conf" file to bind a "sourcetype" to a source path, and how to define regular expressions for extracting fields. The provided configuration snippets showed me how to write to specific sections (or stanzas) in "props.conf" and attach field extractions using "EXTRACT-field = regex". Finally, I looked at the importance of saving the file, restarting Splunk, and validating by running searches to confirm whether the extracted fields were working as intended.
</blockquote>

##### 🔷 Phase 2.1 — Understand the data format and expected fields  

Splunk supports multiple formats (CSV, JSON, XML, syslog-style text). In this workflow, the scripts emit text logs with recognizable markers (authentication header lines, VPN connect/disconnect keywords, purchase line patterns). Knowing these markers is necessary to design breaking and extraction regex safely.

##### 🔷 Phase 2.2 — Identify the sourcetype  

A sourcetype tells Splunk how to parse and interpret incoming data. Operationally, sourcetype is the anchor for most parsing stanzas in `props.conf`, which is why it becomes the primary control point for event breaking, merging, and extraction application.

<blockquote>
The "sourcetype" field is essential for parsing, as it tells Splunk how to handle a specific dataset.
</blockquote>

##### 🔷 Phase 2.3 — Bind a source path to a sourcetype (source stanza template)

```conf
[source::/path/to/your/data]  
sourcetype = your_sourcetype  
```

This command template binds the path to a sourcetype.

```
- `/path/to/your/data` is basically telling Splunk where the data source lives.
- `sourcetype` tells Splunk the format of the data being indexed and subsequently how to parse and interpret the data.

<blockquote>
You can use the configuration file (example: "props.conf") to define data parsing settings for specific source types and data sources. You can also assign a "sourcetype" to a data source by first defining the path of the data source, and then defining its "sourcetype".
</blockquote>

**Step 4:** Define field extractions (direct `EXTRACT-*` template)

You can define regular expressions (regex) to parse (or "filter") to extract specific fields from the data, like usernames or server names.

This command template defines the fields you want to extract from your data source:

```conf
[your_sourcetype]  
EXTRACT-field1 = regular_expression1  
EXTRACT-field2 = regular_expression2  
```

- `your_sourcetype` is the sourcetype (as described earlier)
- `EXTRACT-field1 = regular_expression1` and `EXTRACT-field2 = regular_expression2` is basically how you would extract a single field from your data source.
  - `field1` and `field2` are the names of a fields you want to extract, and `regular_expression1` and `regular_expression2` are the regex used to match, filter, and extract the values.

##### 🔷 Phase 2.4 — Field extraction mental model Example (kept from original content)

I wanted to try out my own simple example to check my understanding of how field extractions work in Splunk. 

A small log line (from an imaginary data source) with two users and their actions were created:  

```
user=john action=login
user=alice action=logout
```

To extract these values, the following in `props.conf` file was written:  

```
[mysourcetype]
EXTRACT-user = user=(\w+)
EXTRACT-action = action=(\w+)
```

The regex `user=(\w+)` will match both `john` and `alice`, and the regex `action=(\w+)` will match both `login` and `logout`.

- `user=(\w+)`
  - `user=` literally matches the users in the log.
  - `(\w+)` is a capturing group.
      - `\w matches` any word character (letters a-z, A-Z, digits 0-9, and underscore _).
      - `+` means basically means "one or more."
      - Together, `\w+` captures names like `john` or `alice`.
- `action=(\w+)`
  - `action=` literally matches the text action=.
  - `(\w+)` works the same as above, capturing words like `login` and `logout`.

<blockquote>
I wanted to try out another example, but slightly more complex, so I imagined (lol, yes just imagining) the data source contained many different users and I wanted to capture everyone whose name starts with “j”, but only when their action was specifically `login`, I could adjust the regex like this:
</blockquote>

```
[mysourcetype]
EXTRACT-j_users_login = user=(j\w+)\s+action=login
```

- `user=(j\w+)` captures any username starting with the letter “j” (e.g., john, james, jill, jacob).
- `s+` would match the space or spaces after the username, so like any whitespace. The `+` is one or more of however many whitespace there is.
- `action=login` would only match entries where the action is exactly `login`.

The result would create a field listing all users whose names start with a "j" and who performed a `login` action.

##### 🔷 Phase 2.5 — Apply changes and validate  

After editing configuration files, Splunk must be restarted so parsing changes apply. Validation is performed by searching the ingested data and confirming that events break correctly and fields appear as expected.

<blockquote>
I learned that parsing in Splunk is a structured pipeline that begins with ingestion and continues through sourcetype assignment, regex extraction, and validation. The `props.conf` file (example configuration file) in this task  is central to this process, acting as the instruction manual for Splunk on how to handle each dataset. I also learned that without proper configuration, Splunk would ingest data as raw text, making searches much less useful.
</blockquote>

</details>


<details>
<summary><strong>▶ Phase 3 — Exploring Splunk Configuration Files and Stanzas</strong><br>
→ establishing which configuration file controls ingestion vs parsing vs transformation and understanding the stanza model Splunk uses to scope behavior
</summary><br>

**Goal:** Establish which configuration file controls ingestion vs parsing vs transformation, and understand the stanza model Splunk uses to scope behavior.

**Overview of actions taken:**

- Explored multiple configuration files to understand their roles.
- Practiced writing examples for `inputs.conf` to ingest log files, `props.conf` to define field extractions, and `transforms.conf` to enrich data by creating new fields
- Looked at `indexes.conf`, which determines where the data is stored, and `outputs.conf`, which controls how data is sent to other Splunk instances. 
- Learned about `authentication.conf`, which enables features like LDAP authentication. 
- Also examined the different stanza types in Splunk, such as `[sourcetype]`, `REPORT`, `EXTRACT`, and `TIME_PREFIX`, which define how events are processed and indexed. 

##### 🔷 Phase 3.1 — Review key configuration files

I learned about several important configuration files in Splunk. Examples tested:

**(1) `inputs.conf` — ingestion definition**  
Defines data inputs, where the data lives, and how Splunk collects it.

Below is an example `inputs.conf` file.

```conf
[monitor:///path/to/logfile.log]  
sourcetype = my_sourcetype  
```

**(2) `props.conf` — parsing and extraction controls**  
Defines parsing rules, line breaking, line merging, and many field extractions and parsing.

Below is a configuration file worked with in Phase 2. 

```conf
[my_sourcetype]  
EXTRACT-field1 = regex1  
EXTRACT-field2 = regex2  
```

**(3) `transforms.conf` — transformation rules**  
Defines how Splunk changes or processes data using regex capture and formatting. In practical terms, `transforms.conf` defines the reusable “rule,” and `props.conf` defines when and where to apply that rule.

<blockquote>
I was confused what "transformation" actually meant, but learned that it's simply a rule that tells Splunk how to change or process data. It is similar to "props.conf" except "transforms.conf" is the actual rule for transformations, whereas "props.conf" defines when and where to apply a transformation. 
</blockquote>

The following is an example block of a `transform.conf` file. As a simple example comparison, `transforms.conf` says "here's the rule on how to extract users" and `props.conf` says "now apply those rules to these logs".

```conf
[add_new_field]  
REGEX = existing_field=(.*)  
FORMAT = new_field::$1  
```

**(3-A) Expanded example context (from original write-up, retained)**  

<blockquote>
I thought about this configuration file a little more and imagined a data source that contains a log entry
</blockquote>

Consider a web access log with a custom user fragment (`user=john action=login`) which in practice, could look something like this:

`192.168.1.45 - - [04/Oct/2025:09:34:56 -0400] "GET /index.html HTTP/1.1" 200 1024 user=john`

- `192.168.1.45` is the client IP address
- The first `-` is a placeholder that shows the name of the remote user

<blockquote>
I learned is called "identd". I also learned that in practice, almost nobody uses this, so it's just a single `-`. The second instance of `-` would show the username if the web server `john` is connected to required login or authentication.
</blockquote>

- The second `-` can represent an authenticated username depending on configuration, but is often unused.
- `[04/Oct/2025:09:34:56 -0400` is the timestamp. 
  - The `-0400` represents time zone, which is the offset from UTC.
- `GET /index.html HTTP/1.1" indicates the HTTP method, path, and protocol version.

<blockquote>
In this example, the "GET" HTTP method for data retrieval from the web server. It's also requesting resources from "/index.html". "HTTP/1.1" is the web protocol and version number.
</blockquote>

- `200` is an HTTP success status code returned by the web server (common comparisons: `404` Not Found, `500` Server Error).
- `1024` is the response body size in bytes (not including headers). This means 1024 bytes were retrieved and sent back to the client, `john`.
- `user=john` is a custom field and provides a value. In this case, it would be the client's name making the request.

**(3-B) Example transform rule to extract the username**

If someone wanted to pull the username as a searchable field, they would modify the `transforms.conf` file by defining an `extract_user` rule:

```conf
[extract_user]
REGEX = user=(\w+)
FORMAT = user::$1
```

- `[extract_user]` is the name of the rule/transformation.
- `REGEX = user=(\w+)` is the regular expression Splunk will look for inside the log.
  - `user=` is the text in the log,
  - `(\w+)` matches the word that comes after `user=`.
- `FORMAT = user::$1` tells Splunk how to store the captured value as a field.
  - `user::` means the field name will be `user`.
  - `$1` means “take the first capture group from the regex” (e.g., `john`).

**(3-C) Example of applying the transform via `props.conf`**

To apply these rules to a log file:

```conf
[source_type]
REPORT-extract_user = extract_user
```

- `[source_type]` is the format of the data being indexed, which also tells Splunk how to parse and interpret the data.

<blockquote>
This is what I discussed in Phase 2.
</blockquote>

- `REPORT-extract_user = extract_user` pulls and runs the `extract_user` transform rule from the `transforms.conf` file.

**(4) `indexes.conf` — index management / storage**  

Controls where data is stored and how it rolls from hot/warm to cold. Index management manages index storage. 

<blockquote>
I learned that Splunk stores data in indexes, which are like separate folders of events from a data source. It basically tells Splunk where to store the data and how to do it.
</blockquote>

Below is an example `indexes.conf` file:

```conf
[my_index]  
homePath = $SPLUNK_DB/my_index/db  
coldPath = $SPLUNK_DB/my_index/colddb
```

- `homePath` communicates where recent data should be stored.
- `coldPath` communicates where to move aged data (past certain thresholds).


**(5) `outputs.conf` — forwarding events to indexers**  

Defines where data is forwarded, commonly via TCP (and optionally TLS), and supports grouping for load distribution. It tells Splunk where to sent/forward data in instances where Splunk is not the final destination. It controls aspects like which indexers to send events to (good for load balancing, sending events to multiple targets) and which protocol to use (TCP, SSL). 

Example `outputs.conf` file below:

```conf
[tcpout]
defaultGroup = my_indexers
[tcpout: myindexers]
server = remote_indexer:9997
```

- `[tcpout]` defines a TCP output. Communicates to Splunk about setting up a TCP output (forwarding events via TCP]
- `defaultGroup = my_indexers` selects a logical group of indexers. Communicates to Splunk that user is sending their data to the `my_indexers` group. 

<blockquote>
Side note, I learned that a group is just a nickname for a group of Splunk indexers.
</blockquote>

- `[tcpout: myindexers]` defines the group name. In this case, it's `myindexers`.
- `server = remote_indexer:9997` defines which indexer host(s) and port(s) receive the data. Communicates to Splunk which specific index servers within the group to send the data.

**(6) `authentication.conf` — authentication settings (LDAP example)**  

Defines Splunk authentication methods and security settings such as TLS for directory connections. Manages and configures authentication settings. 

Below is an example `authentication.conf` file:

```conf
[authentication]  
authSettings = LDAP
[authenticationLDAP]
SSLEnabled = true 
```

- The `[authentication]` communicates to Splunk which authentication method to use. In this example, it's `LDAP`.
- The `[authenticationLDAP]` defines how LDAP is configured. 
- The `SSLEnabled = true` defines encruption method. SSL/TLS encryption when connecting to an LDAP server. 

<blockquote>
This is crucial because without SSL, login credentials would be sent in cleartext.
</blockquote>

<blockquote>
Although these are not all of the configuration files Splunk provides, these are the ones that I primarily focused on. I learned about "inputs.conf", "props.conf", "transforms.conf", "indexes.conf", "outputs.conf", and a small piece of "authentication.conf" to understand how parsing, routing, storage, forwarding, and authentication work together. Splunk also has many other configuration files that handle tasks such as server settings ("server.conf"), limits ("limits.conf"), deployment ("deploymentclient.conf"), and more. I concentrated on the files most relevant to the data pipeline and authentication basics.
</blockquote>

##### 🔷 Phase 3.2 — Understand stanzas and how they control parsing  

A stanza is a named section within a `.conf` file that scopes the settings beneath it. Stanzas such as `TIME_PREFIX` and `TIME_FORMAT` help Splunk identify timestamps. Stanzas like `LINE_BREAKER`, `BREAK_ONLY_BEFORE`, `MUST_BREAK_AFTER`, and `SHOULD_LINEMERGE` determine how raw data is split into events or individual lines. Other stanzas such as `REPORT-*`, `TRANSFORMS-*`, and `EXTRACT-*` use regex to define how fields are extracted. `KV_MODE` can automatically extract key/value pairs.

<blockquote>
Understanding these gave me a clearer picture of how Splunk’s parsing pipeline works and how flexible it is when dealing with different log formats.
</blockquote?

A quick reference table created to loosely define some common stanzas and provides examples of how it might appear in configuration files:

| Stanza | Explanation | Example |
|--------|-------------|---------|
| sourcetype | Defines data source format | [apache:access] |
| TRANSFORMS | Field transformations | TRANSFORMS-example = myfield |
| REPORT | Extraction rules | REPORT-field1 = regex_pattern |
| EXTRACT | Regex field extraction | EXTRACT-field = regex |
| TIME_PREFIX | Defines timestamp prefix | TIME_PREFIX = \[timestamp\] |
| LINE_BREAKER | Defines event breaks | LINE_BREAKER = ([\r\n]+) |
| KV_MODE | Key-value parsing | KV_MODE = json |

**Challenge / Question Set (knowledge check)**

<p align="left">
  <img src="images/lab10-splunk-data-manipulation-figure03.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="900"><br>
  <em>Figure 3</em>
</p>

**Q1:** Which stanza breaks events after a provided pattern?  
**A:** `BREAK_ONLY_AFTER` 

**Q2:** Which stanza specifies the pattern for line breaks?  
**A:** `LINE_BREAKER` 

**Q3:** Which config defines transformations?  
**A:** `transforms.conf`  

**Q4:** Which config defines inputs?  
**A:** `inputs.conf`  

**Operational interpretation:** This phase builds the “division of responsibilities” mental model: `inputs.conf` ingests, `props.conf` parses and applies rules, `transforms.conf` defines transformations, `indexes.conf` stores, and `outputs.conf` forwards. During real troubleshooting, this model is often the fastest way to decide where a fix belongs.

<blockquote>
I learned that each configuration file has a unique responsibility, and together they create the entire ingestion and parsing pipeline. Knowing which file to modify is critical to solving problems quickly. I also learned that stanza-based configuration is extremely powerful, allowing very granular control over parsing behavior with just a few lines of configuration. I learned the **"division of responsibilities"**: "inputs.conf" ingests, "props.conf" parses, "transforms.conf" manipulates, "indexes.conf" stores, and "outputs.conf" forwards. I also learned about how "stanzas" in Splunk configuration files is essentially a section within a ".conf" file that defines specific behavior or rules.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 4 — Creating a Simple Splunk App (`DataApp`) for Scripted Ingestion</strong><br>
→ creating a Splunk app container and configuring a scripted input so ingestion and parsing work can be isolated and managed through the app framework
</summary><br>

**Goal:** Create a minimal Splunk app container and configure a scripted input so the ingestion and parsing work can be isolated and managed through the app framework.

I created my own Splunk app called `DataApp` to better understand how Splunk organizes and extends functionality through apps. An app in Splunk is essentially a container that holds configurations, inputs, and supporting files such as scripts or dashboards. By building a very simple sample app that outputs a test log, I was able to see how Splunk apps are structured and where they are stored in the file system. The purpose of this workflow execution exercise is not to build a production-ready application, but to practice the process of creating, saving, and placing files into Splunk’s app framework. This helps demonstrate how custom data sources or logic can be added into Splunk through apps, making it easier to manage specific use cases in an organized way.

The Splunk service was started from `/opt/splunk` using the `bin/splunk start` command and logged in with the provided credentials. Once inside the Splunk web interface > the Apps section, a new app was created with fields like name, folder path, author, and description. To simulate log ingestion, simple Python script called `samplelogs.py` was created to print a single log line. This script was placed in the `bin` directory of the app. An `inputs.conf` file was then created to instruct Splunk to execute the script every five seconds, sending its output to the `main` index with a sourcetype of `testing`. Splunk was restarted to apply the changes.  


##### 🔷 Phase 4.1 — Start Splunk

```bash
cd /opt/splunk
bin/splunk start
```
Starting Splunk from `/opt/splunk` launches the Splunk server processes (including Splunk Web). In this environment, the service required elevated rights to start successfully. The console output provides the Splunk Web interface URL: `http://tryhackme:8000`.

<blockquote>
I changed into the Splunk installation directory with "cd /opt/splunk" (where third-party apps are commonly placed on Linux). From there I started Splunk by running "sudo bin/splunk start". The bin folder contained the Splunk executable, so running Splunk launches the Splunk server processes ("splunkd" web server).
</blockquote>

<blockquote>
I hit permission errors when trying to start it without elevated rights, so I prefixed the command with "sudo" to run it as superuser. The console showed the web server coming up and printed the access URL — “The Splunk web interface is at http://tryhackme:8000”, which is the address to open in a browser to reach the running Splunk instance.
</blockquote>

##### 🔷 Phase 4.2 — Log in to Splunk Web

I logged into the Splunk web app.

##### 🔷 Phase 4.3 — Create a new app  

(Phase 4.3, Step 1): Select the gear icon next to **Apps** to open the Apps management page.

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure04.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 4</em>
</p>

(Phase 4.3, Step 2): Select **[Create app]**.

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure05.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 5</em>
</p>

(Phase 4.3, Step 3): Provide app metadata and create the app:

  - Name and folder: **DataApp**
  - Path location: `$SPLUNK_HOME/etc/apps/`
  - Version: **1.0.0**
  - Author and description set per the original workflow notes

<blockquote>
I was redirected to a page where I can add details for my Splunk app. I named the app and folder name "DataApp", located at "$SPLUNK_HOME/etc/apps/". I gave it version **1.0.0** as is the first version of this sample app. Lastly, I filled in the remaining details like my name for the "Author", and a quick "Description".
</blockquote>

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure06.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 6</em>
</p>

(Phase 4.3, Step 4): Confirm the app appears in the Apps table.

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure07.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 7</em>
</p>

(Phase 4.3, Step 5):  Launch the app to confirm it loads. At this point, no data appears yet because ingestion is not configured.

<blockquote>
I clicked [Launch App] under the "Actions" column, which evidently showed that no activity has been logged. I went ahead and wrote a Python Script for sample logs in previous steps.
</blockquote>
    
<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure08.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 8</em>
</p>

(Phase 4.3, Step 6):  Return to the Linux terminal to locate the newly created app on disk.

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure09.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 9</em>
</p>

##### 🔷 Phase 4.4 — Create a simple script that generates log output  

Within the app’s `bin` directory, a script named `samplelogs.py` was created/edited. The script prints a single line:

<blockquote>
As learned earlier in this phase, the "bin" directory contains the scripts required by the app I have just created.
</blockquote>

Switched directories to the `/bin` folder and entered `ls` to see a list of available scripts. 

<blockquote>
I withheld the screenshots because it required multiple screenshots to capture the entire list of scripts.
</blockquote>

The sample script was named `samplelogs.py`. The command: `nano samplelogs.py` was used to open the script file, then entered the following:

```python
print("This is a sample log...")
```

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure10.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 10</em>
</p>

The script was executed to confirm the output is produced as expected.

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure11.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 11</em>
</p>

##### 🔷 Phase 4.5 — Configure `inputs.conf` for scripted ingestion  

To ingest the script output, `inputs.conf` was edited in `/opt/splunk/etc/system/default`. This location was used in the original workflow notes, and the file was opened with `nano`.

An `inputs.conf` file was created because in Splunk, `inputs.conf` is the configuration file that tells Splunk what data to collect and how to collect it. By defining settings inside `inputs.conf`, specific details such as file paths, scripts, or network ports that Splunk should monitor for incoming events can be defined. 

The goal was to simulate a real-world scenario where Splunk needs to ingest data from a custom source — in this case, the sample Python script created earlier that generates simple log messages. By setting up `inputs.conf`,  Splunk treats the output of that script as input data and begin indexing it.

The purpose of this step is not just to collect fake data, but to understand how Splunk apps bundle configurations that control data ingestion. In production, different apps use their own `inputs.conf` files to define how logs from servers, applications, or security tools are pulled into Splunk. This exercise helps reinforce that idea by walking through the process in a simplified example.

<p align="center">
  <img src="images/lab10-splunk-data-manipulation-figure12.png?raw=true&v=2" 
       alt="SIEM alert" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="600"><br>
  <em>Figure 12</em>
</p>

To make changes to the sample `inputs.conf` file located in Splunk’s default directory. the main Splunk directory was opened to have clean starting point. From there, `cd /opt/splunk/etc/system/default` was ran to move into the `default` configuration folder, `ls` was used to confirm that the inputs.conf file was there. `nano inputs.conf` was entered to begin editing.

The stanza used:

```
[script:///opt/splunk/etc/apps/DataApp/bin/samplelogs.py]
index = main
source = test_log
sourcetype = testing
interval = 5
```

This configuration instructs Splunk to execute the script every five seconds, treat stdout as events, and assign consistent metadata (index, source, sourcetype).

##### 🔷 Phase 4.6 —  Restart Splunk to apply changes

```bash
/opt/splunk/bin/splunk restart
```
**Analyst validation check:**

**Analyst validation check:** Confirm the on-disk path convention Splunk uses for apps created under `$SPLUNK_HOME/etc/apps/`.

Splunk stores apps under `/opt/splunk/etc/apps/<AppName>`. Therefore, an app named `THM` would be located at: `/opt/splunk/etc/apps/THM`.

<blockquote>
I learned how Splunk apps organize configurations and that "inputs.conf" scripts can simulate live log ingestion.
I also learned how Splunk apps provide a modular way to manage configuration and how they can be used to simulate log ingestion for testing. Writing even a simple script and configuring "inputs.conf" gave me a clear picture of how Splunk consumes and indexes events in real time. There is significant importance to restarting Splunk to make new configurations effective. It was cool to see how Splunk apps organize configurations and that "inputs.conf" scripts can simulate live log ingestion.
</blockquote>

##### 🔷 Phase 4.7 — Event Boundaries for VPN Logs (`vpnlogs`)

**Objective:** Correct event breaking so each connect/disconnect line becomes a separate Splunk event.

<blockquote>
I worked with the "vpnlogs" script, which generated VPN connection and disconnection events. After placing the script into the app’s "bin" directory, I configured an "inputs.conf" entry to ingest it into Splunk. When I searched the ingested data, I noticed that Splunk did not break the events correctly, treating multiple log lines as one. To fix this, I created a regular expression that matched the words "DISCONNECT" or "CONNECT" at the end of each line. I updated "props.conf" to include "MUST_BREAK_AFTER = (DISCONNECT|CONNECT)" and enabled line merging with "SHOULD_LINEMERGE = true". After restarting Splunk, I confirmed that each event was being properly broken at the correct boundary. 
</blockquote>

(Phase 4.7, Step 1): Configure ingestion for `vpnlogs`

```
[script:///opt/splunk/etc/apps/DataApp/bin/vpnlogs]
index = main
source = vpn
sourcetype = vpn_logs
interval = 5
```

- Observed that Splunk failed to break events correctly.
- Created regex for breaking events:  
   ```regex
   (DISCONNECT|CONNECT)
   ```
<blockquote>
I encountered a problem where Splunk was grouping multiple events together incorrectly.
</blockquote>

(Phase 4.7, Step 2): Validate ingestion and observe incorrect event breaking  

In Splunk (search):  

```spl
index=main sourcetype=vpn_logs  
```
The issue observed is that Splunk grouped multiple logical events together, which prevents accurate counting and correlation.

**Step 3:** The Fix: Define and apply the break regex  

Updated `props.conf`:  

```conf
[vpn_logs]  
SHOULD_LINEMERGE = true  
MUST_BREAK_AFTER = (DISCONNECT|CONNECT)  
```

**Analyst validation check:**

**Prompt 1:** Which file defines parsing rules? — Conclusion: `props.conf`  
**Prompt 2:** Regex used? — Conclusion: `(DISCONNECT|CONNECT)`  
**Prompt 3:** Stanza to break events? — Conclusion: `MUST_BREAK_AFTER`  
**Prompt 4:** Disable line merging? — Conclusion: `SHOULD_LINEMERGE=false`

<blockquote>
I learned how important it is to configure event boundaries so Splunk can distinguish between separate events. Regex-based rules in `props.conf` give me precise control over where events start and end. This is essential because improper event breaking can cause searches and dashboards to misinterpret the data. 
</blockquote>

</details>


<details>
<summary><strong>▶ Phase 5 — Multi-line Authentication Events (`authentication_logs`)</strong><br>
→ preserving multi-line entries as single events.
</summary><br>

**Goal:** Preserve multi-line entries as single events.

There was a problem where `authentication_logs` produced multi-line events split incorrectly. Worked with `authentication_logs` script.

<blockquote>
Next, I worked with the "authentication_logs" script, which generates multi-line log entries. After ingesting the logs using "inputs.conf", I noticed that Splunk incorrectly treated the logs as multiple events.
<blockquote> 

##### 🔷 Phase 5.1 — Configure ingestion for `authentication_logs`

To fix this, the "props.conf" file was configured to merge lines and only break events when a line started with "[Authentication]". This was done using "BREAK_ONLY_BEFORE = \[Authentication\]" along with "SHOULD_LINEMERGE = true". Restarting Splunk and re-running the search showed me that the multi-line logs were now captured correctly as single events.  
<blockquote>

Configured inputs.conf:

```conf
[script:///opt/splunk/etc/apps/DataApp/bin/authentication_logs]  
index = main  
sourcetype = auth_logs  
host = auth_server  
interval = 5  
```

Observed multi-line logs breaking incorrectly.  

##### 🔷 Phase 5.2 — Apply merge + break-before logic in `props.conf`

 ```
[auth_logs]
SHOULD_LINEMERGE = true
BREAK_ONLY_BEFORE = \[Authentication\]
```

**Analyst validation check:**
**Prompt 1:** Which stanza breaks events before a pattern? — Conclusion: `BREAK_ONLY_BEFORE`  
**Prompt 2:** Which regex pattern was used? — Conclusion: `\[Authentication\]`

<blockquote>
I learned that multi-line events are a common challenge in Splunk, especially for logs like authentication or application errors that span several lines. Using the right regex and stanza settings in `props.conf` ensures these logs remain intact, preventing data fragmentation.  
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 6 — Masking Sensitive Data (`purchase-details`)</strong><br>
→ masking credit card numbers before indexing while keeping event context searchable
</summary><br>

**Goal:** Mask credit card numbers before indexing while keeping event context searchable.

The `purchase-details` script, which generated logs containing credit card numbers, was modified. These logs were ingested into Splunk using an `inputs.conf` configuration. 

To fix event boundaries, I wrote a regex that broke events after a 4-digit sequence. After confirming the events were structured properly, I used the `SEDCMD` setting in `props.conf` to mask the credit card numbers. My regex replaced the full numbers with `XXXX-XXXX-XXXX-XXXX`. Restarting Splunk showed that the sensitive information was masked in the search results.

Summary of my actions:
- Used `purchase-details` script to simulate credit card logs.
- Configured `inputs.conf`.
- Created regex for event boundaries.


##### 🔷 Phase 6.1 — Define event boundary pattern for purchase logs

```regex
\d{4}\.
```

Apply in `props.conf`:

```conf
[purchase_logs]
SHOULD_LINEMERGE = true
MUST_BREAK_AFTER = \d{4}\.
```

##### 🔷 Phase 6.2 — Apply masking with `SEDCMD`

Introduced **SEDCMD** for masking:  

```
SEDCMD-cc = s/\d{4}-\d{4}-\d{4}-\d{4}/XXXX-XXXX-XXXX-XXXX/g
```

**Analyst validation check:**

**Prompt 1:** Which stanza breaks events after regex? — Conclusion: `MUST_BREAK_AFTER`  
**Prompt 2:** What is the SEDCMD regex? — Conclusion: `s/\d{4}-\d{4}-\d{4}-\d{4}/XXXX-XXXX-XXXX-XXXX/g`

<blockquote>
I learned that Splunk provides built-in mechanisms to anonymize sensitive data at ingestion time. The `SEDCMD` setting works like the Unix `sed` command, applying regex replacements before indexing the data. This ensures compliance with standards like PCI DSS and HIPAA while still preserving logs for analysis.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 7 — Extracting Custom Fields (`vpn_logs`)</strong><br>
→ exracting fields so detections can filter on structured values instead of raw text matching
</summary><br>

**Goal:** Extract fields so detections can filter on structured values instead of raw text matching.

Summary of actions taken:

- I returned to the `vpnlogs` dataset to practice extracting custom fields. Using regex, I captured the username with `User:\s(\w+\s\w+)` and created a stanza in `transforms.conf` that formatted the result into a `Username` field. 
- I then updated `props.conf` to apply this transform and created a `fields.conf` entry to mark the new field as indexed. 
- After restarting Splunk, I validated that usernames were extracted. I then extended the regex to capture not only the username but also the server and action fields, using `User:\s(\w+\s\w+),.+Server:\s(.+),.+Action:\s(\w+)`.
- I updated the transform and fields configuration to include these fields and confirmed that Splunk extracted them successfully. 

##### 🔷 Phase 7.1 — Extract `Username`

VPN logs don’t auto-extract fields (`username`, `server`, `action`). Worked with `vpn_logs`. 

Regex:

```regex
User:\s(\w+\s\w+)
```

`transforms.conf`:

Created `transforms.conf` and added:  

```
[vpn_custom_fields]
REGEX = User:\s(\w+\s\w+)
FORMAT = Username::$1
WRITE_META = true
```

Updated `props.conf`:  

```
[vpn_logs]
TRANSFORMS-vpn = vpn_custom_fields
```

Created `fields.conf`:

```
[Username]
INDEXED = true
```

Restarted Splunk and validated. 

##### 🔷 Phase 7.2 — Extended regex to capture username, server, and action
 
- Extended regex to capture username, server, and action:  

```regex
User:\s(\w+\s\w+),.+Server:\s(.+),.+Action:\s(\w+)
```

##### 🔷 Phase 7.3 — Analyst validation check (captured values):**

**Prompt 1:** Regex for three fields? — Conclusion: `User:\s(\w+\s\w+),.*Server:\s(\w+),.*Action:\s(\w+)`  
**Prompt 2:** How many usernames extracted from purchase_logs? — Conclusion: Example: 5  
**Prompt 3:** How many unique CC values? — Conclusion: Example: 4  

<blockquote>
I learned that custom field extraction is one of the most powerful aspects of Splunk. Using regex in `transforms.conf` and `props.conf` allows me to create new, meaningful fields from raw log text. This makes searches much more efficient, since I can query structured fields rather than relying on free-text search.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 8 — Recap and Closure</strong><br>
→ consolidating the parsing pipeline sequence and confirm the workflow produces detection-ready events
</summary><br>

**Goal:** Consolidate the parsing pipeline sequence and confirm the workflow produces detection-ready events.

The workflow executed a chain that can be summarized as:
- Defining event boundaries  
- Masking sensitive information  
- Updating configuration files (`inputs.conf`, `props.conf`, `transforms.conf`)  
- Extracting custom fields
- Ingestion (`inputs.conf`)
- Parsing and event boundary control (`props.conf`)
- Transform rules (`transforms.conf`)
- Masking (`SEDCMD`)
- Field indexing decisions (`fields.conf`)

These skills are critical for SOC Analysts to properly analyze and secure log data.

##### 🔷 Phase 8.1 —  Workflow execution reached a stable end state with parsing and extraction rules applied and verified through search results.

</details>

---

### Results & Interpretation

The workflow produced Splunk data that is more reliable for detection engineering and investigation.

- VPN connect/disconnect lines were broken into discrete events, enabling accurate aggregation and time-series analysis.
- Multi-line authentication entries were merged into single logical events, preserving context that would otherwise be fragmented.
- Credit card values were masked during parsing using `SEDCMD`, reducing risk while preserving event structure.
- Custom fields were extracted using `transforms.conf` and applied via `props.conf`, enabling structured queries instead of free-text matching.

---

### Operational & Defensive Takeaways

- **Parsing quality is detection quality.** Even well-written SPL detections underperform if Splunk’s event model is wrong or if key values remain trapped inside `_raw`.
- **Choose the right breaking strategy for the log type.** Single-line logs often work well with `MUST_BREAK_AFTER`, while multi-line logs commonly require `SHOULD_LINEMERGE` plus `BREAK_ONLY_BEFORE` so events start at a stable header pattern.
- **Mask sensitive values early.** `SEDCMD` provides a practical ingestion-time control that supports compliance (for example, PCI DSS) while keeping logs usable.
- **Prefer field-based detections.** Field extraction via `transforms.conf` and `props.conf` enables detections like `index=main sourcetype=vpn_logs Username="First Last"` rather than keyword searches that are more error-prone.
- **App-based configuration supports maintainability.** Packaging scripts and configs into an app makes it easier to manage parsing logic and reduces the temptation to modify system defaults in a way that is hard to track.

---

### Reuse Pack (Quick Reference)

#### ▶ Scripted input template (`inputs.conf`)

```
[script:///path/to/script]
index = main
source = <source_name>
sourcetype = <sourcetype_name>
interval = 5
```

#### ▶ Event breaking for single-line records (`props.conf`)

```conf
[<sourcetype>]
SHOULD_LINEMERGE = true
MUST_BREAK_AFTER = <regex>
```

#### ▶ Multi-line preservation (`props.conf`)

```conf
[<sourcetype>]
SHOULD_LINEMERGE = true
BREAK_ONLY_BEFORE = <regex>
```

#### ▶ Masking with `SEDCMD` (`props.conf`)

```
SEDCMD-<name> = s/<regex_to_match>/<replacement>/g
```

(Example used in this workflow)

```
SEDCMD-cc = s/\d{4}-\d{4}-\d{4}-\d{4}/XXXX-XXXX-XXXX-XXXX/g
```

#### ▶ Custom field extraction (`transforms.conf` + `props.conf`)

`transforms.conf`:

```
[<transform_name>]
REGEX = <regex_with_capture_groups>
FORMAT = FieldName::$1
WRITE_META = true
```

`props.conf`:

```
[<sourcetype>]
TRANSFORMS-<label> = <transform_name>
```

Optional `fields.conf`:

```
[FieldName]
INDEXED = true
```

---

### What I Learned (Skills Demonstrated)

This workflow strengthened practical understanding of how Splunk transforms raw text into detection-ready events through a configuration-driven pipeline. It reinforced the division of responsibilities between `inputs.conf` (ingestion), `props.conf` (parsing and rule application), and `transforms.conf` (transformation logic), and demonstrated how stanza-scoped regex rules can correct event boundaries and preserve multi-line structures. It also demonstrated ingestion-time data protection using `SEDCMD` and showed how custom field extraction enables more reliable, field-based searches that are better suited to SOC monitoring and detection engineering.

---

### Reflection

This workflow execution exercise gave me practical experience with Splunk configuration files, regex-based parsing, masking sensitive data, and extracting custom fields. These skills are directly relevant to real-world SOC analyst responsibilities, including compliance, incident investigation, and monitoring.  

I reviewed everything I had completed in the lab, from defining event boundaries and parsing multi-line logs to masking sensitive information and extracting custom fields.

I also built a simple Splunk app to simulate log ingestion, which tied together all the configurations I had practiced. Each phase built upon the last, giving me a realistic view of the types of parsing and ingestion issues a SOC analyst faces. 
