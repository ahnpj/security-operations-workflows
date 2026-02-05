# Log Parsing and Threat Detection Automation Using Python

### Overview

The investigative purpose of this workflow execution is to programmatically parse security telemetry using Python in a Linux environment to surface behavioral indicators that would normally drive alert validation and investigative triage in SOC operations. This writeup includes the analytical techniques being exercised, and how Python scripting is used to support security monitoring and incident response tasks.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshots**, see the **[Step-by-Step Execution Walkthrough](#step-by-step-execution)** section below.

---

### Purpose and Analyst Focus

This workflow execution focuses on programmatically parsing heterogeneous security telemetry using Python in a Linux environment in order to surface behavioral indicators that would normally drive alert validation and investigative triage in SOC operations.

Rather than relying on a SIEM or prebuilt detection rules, the analysis intentionally operates on raw log files to simulate early-stage detection logic development and investigative validation. This mirrors how analysts and detection engineers often begin by validating hypotheses against raw telemetry before translating detection logic into production monitoring platforms.

> **Personal Learning Context:**  
> Before starting this workflow, I independently reviewed how Python handles text processing and structured data parsing, including reading files line-by-line, working with regular expressions, parsing CSV files, and handling nested JSON structures. I also refreshed core Python concepts such as loops, conditional logic, and organizing small detection functions in a clear and readable way.  
>
> Spending time on these fundamentals ensured that each parser was intentionally written and understood, rather than copied or executed mechanically. This helped reinforce confidence in reading unfamiliar log formats and designing detection logic from first principles.

---

### What This Workflow Demonstrates

This workflow documents how multiple log formats are ingested, parsed, and analyzed using Python to identify suspicious behavioral patterns commonly reviewed by SOC analysts.

The process demonstrates how raw telemetry can be transformed into structured indicators that support:

- Alert validation
- Behavioral analysis
- Investigative triage
- Detection logic development

Instead of treating each dataset as an isolated task, the workflow repeatedly applies the same analytical methodology across multiple telemetry types. This reinforces the idea that detection logic is portable across environments when analysts focus on behaviors rather than specific log formats.

---

### Investigation and Detection Relevance

The techniques demonstrated in this workflow directly map to common SOC and detection engineering tasks, including:

- Identifying repeated authentication failures from the same source
- Detecting reconnaissance patterns in web traffic
- Tracking risky IAM activity in cloud audit logs
- Grouping events by user, IP address, or action to identify anomalies

By building lightweight parsers using only Python’s standard library, the workflow reflects how analysts may prototype detection logic before integrating it into SIEM pipelines, SOAR playbooks, or cloud-native monitoring systems.

---

### Environment and Execution Context

This section describes the execution platform, available tooling, and dataset constraints that shaped how scripts were written, tested, and validated during the investigation workflow.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view details on software, tools, environment, data sources, and more.

<details>
<summary><strong>▶ Environment & Platform</strong><br>
</summary><br>

All analysis is performed in a Linux virtual machine accessed through Google Cloud Shell, providing a browser-based environment for file creation, scripting, and execution without requiring local tooling.

This setup simulates environments where analysts may need to quickly analyze data in cloud-hosted terminals or restricted systems without installing additional software.

- **Execution Platform:** Linux virtual machine accessed via Google Cloud Shell (browser-based shell environment)
- **Language:** Python (standard library only)
- **Local Tooling Required:** None (all execution performed in Cloud Shell)
- **File Handling:** Local files created directly in the VM filesystem
- **Output Artifacts:** Terminal output, parsed indicators, and screenshots documenting results

</details>

<details>
<summary><strong>▶ Tooling and Constraints</strong><br>
</summary><br>

Python is used as the primary analysis language, relying exclusively on the standard library to ensure portability across environments and avoid dependency on third-party packages.

This constraint reinforces realistic scenarios where:

- Analysts may not be able to install external libraries
- Detection logic must run in constrained or sandboxed environments
- Scripts must remain compatible across multiple systems

- **Language:** Python (standard library only)  
- **Modules Used:** `re`, `csv`, `json`, `collections`  
- **Execution Method:** Command-line script execution via `python3`

</details>


<details>
<summary><strong>▶ Data Sources Analyzed</strong><br>
</summary><br>

Multiple representative log formats commonly reviewed in SOC and detection engineering workflows are analyzed:

- **Apache web server access logs**  
  Containing both normal requests and reconnaissance-style probing of administrative paths.

- **Linux authentication logs (`auth.log`)**  
  Simulating repeated failed SSH login attempts followed by a successful authentication event.

- **Windows authentication events exported as CSV**  
  Representing failed and successful logons using standard Event IDs.

- **AWS CloudTrail audit logs in JSON format**  
  Including routine activity and high-risk IAM operations such as policy attachment and log deletion.

These datasets are intentionally heterogeneous to demonstrate that consistent detection methodology can be applied across:

- Unstructured text logs
- Structured CSV records
- Nested JSON telemetry

This reinforces the idea that analysts must adapt parsing logic to data format while keeping investigative logic consistent.

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br>
</summary><br>

Rather than treating each dataset as an isolated task, the workflow follows the same analytical rhythm across all telemetry sources:

1. Prepare representative datasets
2. Parse and normalize events using Python
3. Aggregate behavior by IP, user, or action
4. Identify anomalous or risky patterns
5. Validate findings against expected behavior
6. Summarize defensive and detection implications

Each execution phase may involve multiple concrete actions, which are grouped within the same step when they serve the same analytical purpose.

</details>

---

### Step-by-Step Execution

This section includes step-by-step automation and parsing workflow showing scripts, command execution, transformation stages, and validation checkpoints.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Step 1 — Apache Access Logs: Detecting Web Enumeration Activity</strong><br>
→ simulating web attack enumeration and writing a Python parset to detect suspicious events
</summary><br>

**Objective:** Simulate web attack enumeration (404 spikes, admin path scans) and write a Python parser to detect and identify reconnaissance-style behavior such as repeated requests to administrative paths and elevated volumes of 404 responses.

This phase includes both dataset preparation and parsing logic development, since controlled input data is required to validate that detection logic behaves as expected.

- Log filename: `apache_access.log`
- Parser filename: `parser.apache.py`

##### 🔷 Step 1A — Construct Representative Apache Access Logs

To simulate how attackers probe web servers for weak points, a synthetic access log was created containing a mix of normal browsing behavior and repeated requests to common administrative paths.

<blockquote>
I started with a small Apache web server log. My goal was to see how attackers probe web servers for weak points by hitting URLs like `/wp-login.php` or `/phpmyadmin`. To simulate this, I created my own short log file containing a mix of normal and suspicious requests.
</blockquote>

```bash
cat << 'EOF' > apache_access.log
127.0.0.1 - - [20/Nov/2025:10:00:00 +0000] "GET /index.html HTTP/1.1" 200 512 "-" "Mozilla/5.0"
203.0.113.10 - - [20/Nov/2025:10:01:10 +0000] "GET /wp-login.php HTTP/1.1" 404 256 "-" "Mozilla/5.0"
203.0.113.10 - - [20/Nov/2025:10:01:12 +0000] "GET /admin HTTP/1.1" 404 256 "-" "Mozilla/5.0"
203.0.113.10 - - [20/Nov/2025:10:01:14 +0000] "GET /phpmyadmin HTTP/1.1" 404 256 "-" "Mozilla/5.0"
198.51.100.5 - - [20/Nov/2025:10:02:00 +0000] "GET /login HTTP/1.1" 200 512 "-" "Mozilla/5.0"
EOF
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-01.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 1</em>
</p>

Creating known-good and known-bad patterns makes it easier to validate that parsing logic is detecting the behaviors it is intended to flag.

##### 🔷 Step 1B — Write Script: Parse Requests and Suspicious IP Behavior

With test data in place, the next action was to build a parser that could extract IP addresses, count request volume, and track repeated 404 responses that often indicate scanning activity.

- Extracts all IP addresses using regex
- Counts how many total requests each IP made
- Tracks which IPs triggered 404 errors
- Highlights IPs that repeatedly attempted invalid paths

<blockquote>
This gave me quick insight into how enumeration attempts look in logs. Even in a tiny dataset, the patterns stood out clearly—one attacker IP triggered multiple 404s across different admin paths, which is exactly the kind of behavior a SOC analyst flags for deeper review.
</blockquote>

```python
import re
from collections import Counter

log_file = "apache_access.log"
ip_pattern = re.compile(r"(\d{1,3}\.){3}\d{1,3}")

ips = []
errors_404 = {}

with open(log_file) as f:
    for line in f:
        m = ip_pattern.search(line)
        if not m:
            continue

        ip = m.group()
        ips.append(ip)

        if " 404 " in line:
            errors_404[ip] = errors_404.get(ip, 0) + 1

print("Top IPs by request count:")
for ip, count in Counter(ips).most_common():
    print(f"{ip}: {count} requests")

print("\nIPs with 404s (possible scanning):")
for ip, count in errors_404.items():
    print(f"{ip}: {count} x 404")
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-02.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 2</em>
</p>

**Thought Process Behind the Apache Log Parser**:

- Importing modules (`import re`, `from collections import Counter`): I began by importing the re module because I knew I’d need a regular expression to reliably extract IP addresses from log lines. I also imported `Counter` since counting IP occurrences manually would be more work, and Counter provides a clean, built-in way to tally request frequency.
- Defining the log file (`log_file = "apache_access.log"`): I explicitly set the file name up front so the script is easy to modify later and all file handling points to a single variable.
- Creating the regex pattern (`ip_pattern = re.compile(r"(\d{1,3}\.){3}\d{1,3}")`): My next step was writing a regex to capture standard IPv4 addresses. I used a repeated group `(\d{1,3}\.){3}` followed by `\d{1,3}` to match the common format of four numerical octets separated by dots.
- Preparing storage structures (`ips = [], errors_404 = {}`): I created an ips list to store every IP address I extract so I can count total requests later. I also created `errors_404` as a dictionary so I can associate each IP with how many 404 errors they caused—useful for spotting scanning behavior.
- Reading the file and iterating line-by-line (`with open(log_file) as f:`): I opened the log file in a context manager to ensure safe and clean reading. Looping through the file line by line keeps memory usage low, which is ideal for large log files.
- Finding and extracting an IP (`m = ip_pattern.search(line)`) - For each line, I used the compiled regex to search for an IP. If no IP was found, I skipped that line using continue. If found, I extracted the value using `m.group()` and appended it to the ips list.
- Identifying 404 responses (`if " 404 " in line:`) - After grabbing the IP, I checked for `404` in the current log line. My reasoning was that repeated `404` responses from the same IP could indicate reconnaissance or path brute-forcing. When a `404` was found, I incremented the count for that IP in the `errors_404` dictionary.
- Printing request counts (`Counter(ips).most_common()`): After processing the log, I used `Counter` to rank IPs by the volume of requests. Using `.most_common()` automatically sorts them in descending order, giving a clear picture of the most active clients.
- Printing 404 summaries (`for ip, count in errors_404.items():`)
Finally, I displayed which IPs generated the most 404s. This creates a simple but effective way to highlight potentially suspicious activity.

##### 🔷 Step 1C - Run the Python Parser Script (parser_apache.py)

Used the command: `python3 parser.apache.py`.

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-03.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 3</em>
</p>

**Interpretation of Results:**

- IP **203.0.113.10** repeatedly accessed invalid administrative paths
- Multiple 404 responses strongly indicate enumeration behavior

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-04.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 4</em>
</p>

<blockquote>
During execution, the terminal initially reported that the file did not exist. After checking the directory, I realized the script was named `parser.apache.py` while I attempted to run `parser_apache.py`. Updating the command resolved the issue and reinforced the importance of verifying filenames during troubleshooting.
</blockquote>

##### 🔷 Step 1 Findings

Even simple regex-based parsing is enough to reveal attacker behavior. 404 spikes almost always indicate scanning or automated tools looking for known vulnerabilities.

- IP **203.0.113.10** repeatedly attempted invalid admin paths.
- Multiple 404 errors indicate enumeration/scanning behavior.
- Regex-based extraction made parsing extremely simple.

</details>


<details>
<summary><strong>▶ Step 2 — Linux Authentication Logs: Identifying SSH Brute-Force Attempts</strong><br>
→ determining whether repeated authentication failutres originate from a common external source
</summary><br>

**Objective:** Determine whether repeated authentication failures originate from a common external source.

This phase again combines dataset creation with focused parsing logic to validate detection techniques.

- Log filename: `auth.log`
- Parser filename: `parser_auth.py`

##### 🔷 Step 2A - Generate Authentication Log Events

A synthetic `auth.log` file was created containing multiple failed login attempts followed by a successful authentication event. To simulate this, I created my own short log file containing SSH login events, which are commonly brute-forced. I generated a few lines manually—some failed attempts against fake users (admin, root), and one legitimate login.

```bash
cat << 'EOF' > auth.log
Nov 20 10:10:01 server sshd[1001]: Failed password for invalid user admin from 203.0.113.50 port 54321 ssh2
Nov 20 10:10:03 server sshd[1001]: Failed password for invalid user admin from 203.0.113.50 port 54322 ssh2
Nov 20 10:10:05 server sshd[1001]: Failed password for invalid user root from 203.0.113.50 port 54323 ssh2
Nov 20 10:11:00 server sshd[1002]: Accepted password for peter from 198.51.100.77 port 50000 ssh2
EOF
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-05.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 5</em>
</p>

##### 🔷 Step 2B - Write Script: Extract and Count Failed Login Sources

The parser (`parser_auth.py`) focuses only on failed authentication messages and aggregates attempts by source IP.

```python
import re
from collections import Counter

log_file = "auth.log"
fail_pattern = re.compile(r"Failed password .* from (\d{1,3}(?:\.\d{1,3}){3})")

fail_ips = []

with open(log_file) as f:
    for line in f:
        m = fail_pattern.search(line)
        if m:
            fail_ips.append(m.group(1))

print("Failed SSH logins by IP:")
for ip, count in Counter(fail_ips).most_common():
    print(f"{ip}: {count} failed attempts")
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-06.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 6</em>
</p>

**Thought Process Behind the Auth Log Parser**:

- Importing tools (`import re`, `from collections import Counter`): I started by importing two tools Python provides. One helps me find patterns in text (like IP addresses), and the other helps me count how many times something appears.
  
- Choosing the log file (`log_file = "auth.log"`): I set the script to read from the auth.log file, which is where Linux stores information about login attempts.
  
- Creating the pattern to detect failed logins: I wrote a pattern (`fail_pattern = re.compile(...)`) that looks for the phrase “Failed password” followed by an IP address. This lets the script automatically detect when someone tried and failed to log in.
  
- Preparing a list to store IPs (`fail_ips = []`): I made an empty list where I could collect all the IP addresses that failed to log in.

- Reading the log file line by line: I opened the log file and looked at each line individually. This makes it easy to scan for failed logins without loading the whole file at once.
  
- Checking each line for a failed login attempt: For every line, I asked Python to look for the pattern. If it found one (meaning the line contained a failed login), I extracted the IP address and added it to the list.
  
- Counting failures (`Counter(fail_ips)`): After collecting all the IPs, I used the counting tool to figure out how many times each IP tried and failed to log in.
  
- Showing the results: Finally, I printed the results in order from most failures to least. This helps quickly spot which IP addresses might be attacking or probing the system.

#### Step 2C - Run the Python Parser Script (parser_auth.py)

Used the command: `python3 parser_auth.py`.

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-07.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 7</em>
</p>

**Interpretation of Results:**

- IP **203.0.113.50** generated three failed attempts across two invalid accounts

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-08.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 8</em>
</p>


##### 🔷 Step 2 Findings

This quickly highlighted one IP making multiple failed attempts. Even though the dataset was small, the logic mirrors real brute-force detection: consistent login failures from the same IP over a short time window.

- The attacker IP **203.0.113.50** attempted 3 logins across 2 fake users (admin & root).
- Perfect small-scale example of SSH brute force.

Auth logs are verbose but uniform. Regex makes parsing attacker IPs straightforward. SSH failures follow a very predictable format. Once you extract the IP addresses, it becomes trivial to identify malicious login patterns.

</details>


<details>
<summary><strong>▶ Step 3 — Windows Event Logs: Failed Logon Analysis via CSV</strong><br>
→ identifying repeated authentication failures used Windows Event ID 4625
</summary><br>

**Objective:** Identify repeated authentication failures using Windows Event ID **4625** grouped by account and source IP.

- Log filename: `windows_events.csv`
- Parser filename: `python3 parser_windows.py`

##### 🔷 Step 3A — Simulate Exported Windows Event Data

To simulate a typical SOC workflow where Windows Event Logs are exported into CSV for easier analysis, a small CSV file containing Event IDs, usernames, IP addresses, and statuses was created.

```bash
cat << 'EOF' > windows_events.csv
EventID,AccountName,IpAddress,Status
4625,admin,203.0.113.80,FAILURE
4625,admin,203.0.113.80,FAILURE
4625,testuser,198.51.100.10,FAILURE
4624,peter,10.0.0.5,SUCCESS
EOF
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-09.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 9</em>
</p>

##### 🔷 Step 3B —  Write Script: Filter and Aggregate Failed Authentication Events

The parser was designed to perform the following tasks:
- Loads the CSV
- Filters only Event ID 4625 (failed logons)
- Counts failures by username and source IP

```python
import csv
from collections import Counter

fail_counts = Counter()

with open("windows_events.csv") as f:
    reader = csv.DictReader(f)
    for row in reader:
        if row["EventID"] == "4625":
            key = (row["AccountName"], row["IpAddress"])
            fail_counts[key] += 1

print("Windows 4625 failed logons:")
for (user, ip), count in fail_counts.most_common():
    print(f"{user} from {ip}: {count} failures")
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-10.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 10</em>
</p>

**Thought Process Behind the Windows 4625 Failed Logon Parser**

- Importing tools (`import csv`, `from collections import Counter`): I started by importing `csv` so the script can read rows from a CSV file easily, and `Counter` so I could count repeated items without writing extra code.

- Setting up the counter (`fail_counts = Counter()`): I created a Counter object to keep track of how many failed logons each user/IP combination had.

- Opening the CSV (`with open("windows_events.csv") as f:`): I opened the Windows event log CSV file. This file contains rows of Windows Event IDs, usernames, IP addresses, and status codes.

- Using `DictReader` to read each row: I used `csv.DictReader(f)` so each row is read as a dictionary. This makes it easy to reference columns by name (like `row["EventID"]`).

- Checking for failed logins (`if row["EventID"] == "4625":`): Event ID **4625** represents a failed Windows logon attempt. I filtered the rows so the script only analyzes failed logins and ignores successful ones.

- Grouping failures by user and IP: I created a key made of the username and IP address: `(row["AccountName"], row["IpAddress"])`. This let me track how many times each user/IP pair failed to log in.

- Updating the count (`fail_counts[key] += 1`): Every time the same user from the same IP failed again, the counter increased.

- Printing the results: After processing the file, I printed each user/IP pair and the number of failed attempts. This creates a simple way to see which accounts or IPs might be suspicious or repeatedly failing authentication.

#### Step 3C - Run the Python Parser Script (parser_windows.py)

Used the command: `python3 parser_windows.py`.

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-11.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 11</em>
</p>

**Interpretation of Results:**

When parsing the Windows event CSV, the script correctly filtered for failed authentication attempts by looking specifically for `Event ID 4625`, which represents failed logon events. As a result, only the accounts associated with failed logons appeared in the output: `admin` (with two failures from `203.0.113.80`) and `testuser` (one failure from `198.51.100.10`). The account `peter` did not appear because his entry corresponded to `Event ID 4624`, which indicates a successful authentication and therefore did not meet the fail-only filter. This confirmed that the script was accurately identifying and counting failed logon attempts while excluding normal or successful logons.

- 1 admin account from **203.0.113.80** had multiple failures.
- 1 testuser account from **198.51.100.10** had 2 failure.

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-12.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 12</em>
</p>

<blockquote>
When I first ran the Windows 4625 parser, the script threw a KeyError for "EventID", which made me stop and check the CSV more closely. I realized the file still contained the heredoc wrapper lines (cat << 'EOF' and EOF) at the top and bottom, which caused Python’s DictReader to treat the heredoc text as the actual header instead of the real CSV column names. Because of that, the "EventID" field didn’t exist from the script’s perspective, and the parser failed. 
</blockquote>

<blockquote>
After removing those extra heredoc lines so that the file began directly with the correct header row (EventID,AccountName,IpAddress,Status), the script parsed the data correctly and produced the expected 4625 failed-logon results.
</blockquote>

##### 🔷 Step 3 Findings

This showed me which accounts were repeatedly targeted. Even with synthetic data, the exercise helped reinforce how valuable Event ID filtering is. Windows logs are noisy, so focusing on specific events is crucial.

- admin from **203.0.113.80** had multiple failures.
- CSV-based logs are extremely easy to parse compared to raw EVTX.

Windows logs become manageable when exported to CSV and filtered by Event ID. CSV parsing is extremely simple in Python, and Windows log analysis becomes much easier when I focus on specific event types like 4625 or 4688.

</details>

<details>
<summary><strong>▶ Step 4 — CloudTrail Identity Logs (JSON): IAM Abuse Detection via CloudTrail Logs</strong><br>
→ surfacing IAM actions that might indicate privilege escalation or tampering
</summary><br>
  
**Objective:** Surface IAM actions that may indicate privilege escalation or tampering with logging visibility.

- Log filename: `cloudtrail.json`
- Parser filename: `parser_windows.py`

##### 🔷 Step 4A — Create CloudTrail Event Records

Instead of using real logs, a small JSON array with actions like `AttachUserPolicy` and `DeleteTrail` was created, both of which could indicate risky or unauthorized changes.

```bash
[
  {
    "eventName": "AttachUserPolicy",
    "userIdentity": { "userName": "alice" },
    "sourceIPAddress": "203.0.113.90"
  },
  {
    "eventName": "ListBuckets",
    "userIdentity": { "userName": "alice" },
    "sourceIPAddress": "203.0.113.90"
  },
  {
    "eventName": "DeleteTrail",
    "userIdentity": { "userName": "bob" },
    "sourceIPAddress": "198.51.100.200"
  }
]
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-13.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 13</em>
</p>

##### 🔷 Step 4B — Write Script: Group Activity by User and Flag Risky Actions

The parser was designed to perform the following tasks:
- Loaded the JSON file
- Grouped actions by user
- Flagged high-risk IAM actions

```python
import json
from collections import defaultdict

with open("cloudtrail.json") as f:
    events = json.load(f)

actions_by_user = defaultdict(list)
risky = {"AttachUserPolicy", "DeleteTrail", "PutUserPolicy"}

for e in events:
    user = e.get("userIdentity", {}).get("userName", "UNKNOWN")
    actions_by_user[user].append(e["eventName"])

print("CloudTrail actions by user:")
for user, actions in actions_by_user.items():
    print(f"{user}: {', '.join(actions)}")

print("\nPotentially risky actions:")
for e in events:
    if e["eventName"] in risky:
        user = e.get("userIdentity", {}).get("userName", "UNKNOWN")
        print(f"{user} did {e['eventName']} from {e['sourceIPAddress']}")
```

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-14.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 14</em>
</p>

**Thought Process Behind the CloudTrail Log Parser**

- Importing tools (`import json`, `from collections import defaultdict`): I imported `json` so the script can read CloudTrail logs stored in JSON format, and `defaultdict` so I can easily group actions by user without checking if keys already exist.

- Loading the CloudTrail log file: I opened `cloudtrail.json` and used `json.load(f)` to turn the JSON file into a Python list of event records that I can loop through.

- Preparing a structure to group actions by user (`actions_by_user = defaultdict(list)`): I created a dictionary where each username automatically starts with an empty list. This allows me to store every action each user performed.

- Defining what I consider "risky" (`risky = {...}`): I created a small set of actions—like `AttachUserPolicy`, `DeleteTrail`, and `PutUserPolicy`—that could represent privilege escalation or tampering with logging.

- Looping through every event: For each event in the CloudTrail data, I pulled the username from `userIdentity.userName`. If the field was missing, I used `"UNKNOWN"` as a safe fallback. Then I added the event’s action name (`eventName`) to that user’s list.

- Printing normal user activity: After grouping everything, I printed each user along with a list of all the actions they performed. This gives a simple overview of account behavior.

- Detecting risky actions: I looped through the events again and checked if the event’s action name was in my “risky” set. If it was, I printed which user performed the action and from which IP address. This highlights potentially suspicious activity without needing complex analysis.


##### 🔷 Step 4C - Run the Python Parser Script (parser_cloudtrail.py)

Used the command: `python3 parser_cloudtrail.py`.

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-15.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 15</em>
</p>

**Interpretation of Results:**

- alice performed a privileged action: **AttachUserPolicy**
- bob executed **DeleteTrail**, which is highly suspicious.

<p align="left">
  <img src="images/python-log-parsing-and-security-analysis-16.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 16</em>
</p>

<blockquote>
When I tried running the script, the terminal indicated the file didn’t exist. After checking the directory, I realized the issue was a filename mismatch, the actual file was named "parser.cloudtrail.py", but I was trying to run "parser_cloudtrail.py". Updating the command to use the correct filename resolved the error. This was a quick reminder to always verify paths and filenames when troubleshooting.
</blockquote>

##### 🔷 Step 4 Findings

The CloudTrail log analysis revealed a clear breakdown of user activity across the environment. The user alice performed two actions, `AttachUserPolicy` and `ListBuckets`, both originating from the same IP address (`203.0.113.90`), while bob executed a `DeleteTrail` action from a different external IP (`198.51.100.200`). When flagging potentially risky behavior, the parser highlighted that alice attaching a user policy and bob deleting a trail were notable events because both actions can impact security visibility and permissions. These findings show that the script successfully identified user-level activity and surfaced actions that could represent privilege changes or attempts to modify logging, making them important for further review.

- alice performed a privileged action: **AttachUserPolicy**
- bob executed **DeleteTrail**, which is highly suspicious.

CloudTrail JSON parsing is simple in Python and quickly reveals dangerous IAM behavior. Even tiny CloudTrail datasets reflect security patterns. IAM actions like attaching new policies or deleting trails are high-signal indicators worth alerting on.

</details>

---

### Results & Interpretation

Across all telemetry sources, consistent behavior-based indicators were successfully identified:

- Web server logs revealed reconnaissance via repeated 404 responses and probing of administrative endpoints
- Linux authentication logs showed repeated SSH failures from a single source IP
- Windows authentication logs highlighted accounts receiving multiple failed login attempts
- CloudTrail logs exposed potentially dangerous IAM actions affecting permissions and logging visibility

Running these scripts inside Google Cloud Shell gave me a clean and controlled Linux environment without needing to install anything locally. Writing small, focused parsers helped me understand common log formats and build confidence handling raw data. These exercises represent foundational SOC skills—reading logs, spotting anomalies, and automating analysis.

Even with small synthetic datasets, the workflow mirrors how analysts validate detection logic before deploying production alert rules.

---

### Operational & Defensive Takeaways

- Repeated 404 responses targeting administrative paths are strong indicators of reconnaissance
- SSH brute-force attempts are reliably detected by grouping failed authentication events by IP
- Windows Event ID filtering significantly reduces noise and accelerates triage
- IAM actions modifying policies or deleting trails are high-signal behaviors requiring immediate investigation

Python-based parsing logic can later be translated into SIEM queries or cloud-native detection rules.

---

### Reuse Pack (Quick Reference)

#### ▶ Command and Script Patterns

- Regex-based IP extraction from text logs
- CSV-based grouping by user and IP
- JSON parsing with dictionary traversal
- Event filtering by ID or action name

#### ▶ Common Analysis Pivots

- Group by source IP
- Group by account name
- Count event frequency
- Filter for high-risk action types

---

### What I Learned (Skills Demonstrated)

- Parsing raw telemetry across multiple formats (text, CSV, JSON)
- Building lightweight detection logic using Python
- Applying consistent analytical methodology across different platforms
- Identifying behavior-based indicators instead of single-event alerts
- Translating raw event data into investigative signals

---







