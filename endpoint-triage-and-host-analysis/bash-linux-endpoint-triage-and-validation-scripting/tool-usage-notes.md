# Tool Usage Notes — Linux Endpoint Triage and Script-Based Validation Using Bash

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational security tasks such as host triage, system enumeration, and validation of system state.  
> - **Executions** refer to the hands-on performance of those tasks using real commands, scripts, and live host data.  
> - **Writeups** document how tasks were performed, what decisions were made during investigation, and how results were interpreted in an operational context.

This document serves as a technical reference for the Linux commands, shell behavior, and Bash scripting constructs used during the endpoint triage execution. The intent is to preserve not just the syntax of commands, but also the investigative purpose behind each tool and how it fits into real SOC and incident response workflows.

All actions in this execution are performed using native Linux utilities and the Bash shell, without installing additional packages or automation frameworks. This reflects real-world constraints where analysts often work on production servers, jump hosts, or forensic images with limited tooling available.

---

### Execution Platform and Operating Environment

#### ▶ Remote Linux Virtual Machine

**Purpose:** Provide a realistic host environment for practicing live endpoint investigation and shell-based automation.  
**How It Was Used:** All commands and scripts were executed directly on the host through terminal-based SSH sessions. Files were created, edited, and executed locally within the VM filesystem.  
**Operational Relevance:** Mirrors how analysts access and investigate potentially compromised systems during triage and containment.

Environment characteristics observed during execution:

- **Operating System:** Ubuntu Linux (LTS release)  
- **Kernel:** Linux 5.x series  
- **Shell:** Bash (`/bin/bash`)  
- **Access Method:** SSH terminal sessions  
- **Editor:** `nano`  
- **Execution Context:** Ephemeral instance requiring scripts to be recreated between sessions  

Because the environment reset between sessions, scripts and files did not persist. This reinforced realistic operational behavior where analysts must repeatedly validate environment state and rebuild tooling during investigations.

---

### System and Host Context Enumeration

Establishing baseline host context is a critical first step in endpoint triage. These commands were used to confirm system identity, configuration, and resource state before deeper inspection.

#### ▶ Operating System and Kernel

```bash
uname -a
lsb_release -a
```

**Purpose:** Identify kernel version, architecture, and Linux distribution.  
**Investigative Use:** Kernel version affects exploit feasibility, module compatibility, and EDR driver behavior. Distribution determines file paths, logging locations, and package management tooling.  
**Operational Relevance:** Analysts must confirm host type before applying response scripts or remediation steps.


#### ▶ Host Identity

```bash
hostname
hostnamectl
```

**Purpose:** Confirm system hostname and machine identity.  
**Investigative Use:** Prevents accidental investigation of unintended systems when pivoting between hosts.  
**Operational Relevance:** Hostname correlation is critical when matching endpoint activity to SIEM alerts and asset inventories.


#### ▶ Resource Utilization

```bash
df -h /
free -h
```

**Purpose:** Check disk usage and memory consumption.  
**Investigative Use:** Disk exhaustion may indicate log flooding, malware staging, or ransomware encryption activity. Memory pressure may suggest runaway processes or cryptomining.  
**Operational Relevance:** Resource anomalies often precede or accompany security incidents.


#### ▶ Network Configuration

```bash
ip a
ip route
```

**Purpose:** Identify active interfaces, IP addresses, and routing paths.  
**Investigative Use:** Determines which interfaces may be receiving malicious traffic and whether the host is on the expected subnet.  
**Operational Relevance:** Network context supports lateral movement detection and containment decisions.

Interface names varied by cloud provider and system configuration, reinforcing that scripts should not rely on fixed interface naming conventions.

---

### User and Authentication Context

Understanding which accounts are active and how users are accessing the system is central to intrusion detection and incident response.

#### ▶ Current User and Privileges

```bash
whoami
id
```

**Purpose:** Confirm active user and group memberships.  
**Investigative Use:** Determines whether the session has elevated privileges and whether privilege escalation may be required for further analysis.  
**Operational Relevance:** Group memberships (e.g., `sudo`, `docker`) can significantly expand attacker capabilities.

#### ▶ Login History

```bash
last -a | head
```

**Purpose:** Review recent login sessions.  
**Investigative Use:** Identifies unexpected remote logins or service account usage.  
**Operational Relevance:** Provides rapid situational awareness before deeper log analysis.

This command relies on `wtmp` logs and does not capture all authentication attempts, so it is used as a quick triage signal rather than a complete audit trail.

---

### Filesystem Navigation and Inspection

Filesystem awareness supports both normal administration and compromise investigation.

#### ▶ Directory Context and Visibility

```bash
pwd
ls
ls -la
```

**Purpose:** Confirm working directory and enumerate visible and hidden files.  
**Investigative Use:** Identifies suspicious executables, hidden persistence files, or unauthorized scripts.  
**Operational Relevance:** Analysts must always confirm directory context before executing or modifying files.

#### ▶ File Content Review

```bash
cat file.txt
less file.txt
head file.txt
tail file.txt
```

**Purpose:** Inspect file contents efficiently.  
**Investigative Use:** Review configuration files, dropped artifacts, logs, and scripts.  
**Operational Relevance:** Supports IOC discovery and validation of system configuration changes.

`less` was preferred for large files to avoid flooding the terminal and degrading performance.

#### ▶ Pattern-Based Searching

```bash
grep "pattern" file.txt
grep -i "error" logfile.log
```

**Purpose:** Filter large files for indicators or keywords.  
**Investigative Use:** IOC searches, credential traces, and error detection.  
**Operational Relevance:** Mirrors how analysts filter logs prior to ingesting them into SIEM platforms.

---

### File Permissions and Execution Control

Linux enforces execution as a filesystem-level permission, making permission inspection critical for both defense and response.

#### ▶ Permission Inspection

```bash
ls -l
stat script.sh
```

**Purpose:** Review ownership, permissions, and timestamps.  
**Investigative Use:** World-writable or executable files in sensitive locations can indicate persistence or misconfiguration.  
**Operational Relevance:** Supports timeline reconstruction and detection of unauthorized modifications.

#### ▶ Granting Execution Rights

```bash
chmod +x script.sh
```

**Purpose:** Enable direct execution of scripts.  
**Investigative Use:** Required to run response or validation scripts during investigations.  
**Security Context:** Execution permission is a core Linux security boundary and helps prevent unauthorized code execution.

#### ▶ Executing Scripts

```bash
./script.sh
bash script.sh
```

**Purpose:** Run scripts using either direct execution or interpreter invocation.  
**Operational Relevance:** Direct execution honors the shebang and permission model, while invoking `bash script.sh` bypasses execute permissions.  
**Security Note:** Bypassing permissions may violate operational safeguards in hardened environments.

---

### Bash Scripting Constructs Used

#### ▶ Shebang Declaration

```bash
#!/bin/bash
```

**Purpose:** Specifies which interpreter executes the script.  
**Operational Relevance:** Ensures consistent execution regardless of user default shell.

#### ▶ Variable Assignment and Input

```bash
read user_input
```

**Purpose:** Capture interactive user input.  
**Operational Relevance:** Supports confirmation prompts, triage questionnaires, and manual validation workflows.  
**Security Note:** Input is unvalidated and not suitable for secret handling in production systems.

#### ▶ Loops

```bash
for i in {1..3}; do
    ...
done
```

**Purpose:** Repeat structured validation or data collection steps.  
**Operational Relevance:** Enables scalable checks without duplicating code.  
**Portability Note:** Brace expansion is Bash-specific and not POSIX portable.

#### ▶ Conditional Execution

```bash
if [ "$var" = "value" ]; then
    ...
fi
```

**Purpose:** Control execution based on system state or user input.  
**Operational Relevance:** Supports access validation, response gating, and state-dependent automation.  
**Syntax Requirements:** Spaces inside brackets are mandatory; variables should always be quoted.

#### ▶  Compound Conditions

```bash
if [ cond1 ] && [ cond2 ]; then
```

**Purpose:** Require multiple conditions to pass.  
**Operational Relevance:** Mirrors multi-factor style validation and chained response checks.

---

### Output Interpretation and Failure Modes

#### ▶ Common Errors Observed

- `Permission denied` — missing execute bit or insufficient privileges  
- Immediate script exit — syntax error or unmet condition  
- Empty variables — incorrect input capture or quoting issues  

**Troubleshooting Approach:**  
1. Verify file permissions and ownership  
2. Add `echo` statements to trace variable values  
3. Run scripts with `bash -x script.sh` for execution tracing

---

### Performance and Safety Considerations

Most commands used in this execution are low impact and safe for live systems. However:

- Recursive searches such as `find /` can significantly impact performance  
- Reading very large files may degrade system responsiveness  
- Poorly written loops may consume excessive CPU resources  

Scripts should always be tested in isolated environments before use in production response scenarios.

---

### Portability and Environment Caveats

- Network interface names vary across platforms  
- Minimal containers may not include `/bin/bash`  
- Brace expansion and certain conditionals are Bash-specific  
- File permissions may reset across mounts or file transfers  

Enterprise automation must account for heterogeneous host configurations.

---

### Tools That Extend This Workflow into Full IR

Additional tools commonly used during deeper endpoint investigations include:

- `top`, `htop` — real-time process analysis  
- `find` — filesystem-wide artifact discovery  
- `crontab -l` and `/etc/cron*` — persistence detection  
- `journalctl` — systemd event review  
- Endpoint detection and response (EDR) platforms  

These tools would be used after initial triage to support containment and eradication workflows.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Remote Linux virtual machine  
- **Operating System:** Ubuntu Linux  
- **Shell:** Bash  
- **Editor:** nano  
- **Access Method:** SSH  
- **Utilities Used:** ls, cd, pwd, grep, cat, less, head, tail, ps, df, free, ip, whoami, hostname, chmod, uname, last  
- **Data Sources:** Live system state and local filesystem contents  
- **Execution Method:** Interactive shell commands and script execution  
- **Analysis Method:** Host-based enumeration, permission inspection, and behavioral validation

These tools collectively support realistic endpoint triage and lightweight automation workflows commonly performed by SOC analysts and incident responders.

