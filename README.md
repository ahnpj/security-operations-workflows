## Security Operations Workflows

This repository contains hands-on security operations projects focused on digital forensics, threat intelligence, email security, SIEM analysis, endpoint investigations, network traffic analysis, identity and access management, detection engineering, and security automation.

Projects are organized into category folders representing major security domains. Within each category, individual project folders contain complete, self-contained exercises documenting a specific task, investigation, analysis, or operational scenario from start to finish.

The emphasis is not simply on using tools, but on understanding how evidence is collected, validated, analyzed, and transformed into defensible findings. Each project documents the technical execution, supporting evidence, analytical reasoning, and conclusions behind the work performed.

> 👉 **[Category Folders and Current Projects](#category-folders-and-current-projects)**</br>
> **Browse all projects organized by security domain**

> 👉 **[How This Repository Is Organized](#how-this-repository-is-organized)**</br>
> **Understand how categories, projects, and supporting documentation are structured**

> 👉 **[Project Structure and Documentation](#project-structure-and-documentation)**</br>
> **Review the standard files and documentation approach used throughout the repository**

---

### Start Here: How to Navigate This Repository

Projects are grouped by security domain and organized into self-contained project folders. Each folder documents a complete technical exercise, investigation, analysis, or operational scenario and includes the supporting documentation needed to understand both the execution process and resulting findings.

If you're new to the repository, start with **[Category Folders and Current Projects](#category-folders-and-current-projects)** to quickly see the types of security work represented throughout the portfolio.

<details>
<summary><strong>▶️ Project Documentation Structure</strong></summary></br>

Most project folders include the following supporting files:

| File | Purpose |
|---|---|
| `README.md` | Project overview, scope, assumptions, and objectives |
| `workflow-execution.md` | Step-by-step execution and analysis |
| `analyst-notes.md` | Reasoning, interpretation, and investigative thought process |
| `tool-usage-notes.md` | Tool-specific observations, syntax, and references |
| `images/` | Screenshots and supporting evidence |
| `automation-design-notes.md` | Automation concepts and implementation notes (when applicable) |
| `supporting-files/` | Supporting artifacts such as Sigma rules, sample logs, scripts, configuration files, exports, datasets, reference material, or other project-specific resources used throughout the workflow (when applicable) |

The documentation is intentionally separated so execution, analysis, and reference material can be reviewed independently while remaining tied to the same project.

</details>

---

### How This Repository Is Organized
This repository is organized into category folders, which represent major security operations domains. Projects are placed into categories based on the primary security function being performed rather than solely by the tools used during analysis.

Each project lives in its own folder and contains the documentation, notes, screenshots, and supporting evidence required to understand and repeat the work.

<details>
<summary><strong>▶️ Category Folders</strong></summary></br>

| Category Folder | Security Domain | Focus Area | What You Will Find Inside |
|---|---|---|---|
| **endpoint-triage-and-host-analysis/** | Security Operations | Endpoint Triage & Host Analysis | Windows and Linux host triage, process and service inspection, filesystem artifact checks, network exposure validation, Windows event log triage, and quick health/persistence checks. |
| **siem-detections-and-log-analysis/** | Security Operations | SIEM Detection & Log Analysis | SPL-driven investigations, field extraction and normalization, ingestion corrections, and detection-ready data shaping, including VPN remote-access anomaly analysis. |
| **network-monitoring-and-traffic-analysis/** | Security Operations | Network Monitoring & Traffic Analysis | Bounded packet capture with tcpdump and deeper PCAP analysis in Wireshark, including filtering, session reconstruction, and evidence extraction. |
| **identity-and-access-management/** | Identity & Access Security | Identity & Access Management | Active Directory administration, domain controller operations, cloud identity provisioning, user and group lifecycle management, license assignment, delegation, OU management, access control validation, and directory security hardening using Active Directory and Microsoft Entra ID. |
| **detection-automation-and-log-processing/** | Detection Engineering | Detection Automation & Log Processing | Python-based parsing and analysis, transforming logs into structured signals, and prototyping behavior-based detections. |
| **digital-forensics-and-evidence-analysis/** | Digital Forensics & Incident Response | Digital Forensics & Evidence Analysis | Evidence acquisition, forensic imaging, disk image analysis, memory forensics, browser artifact analysis, Windows artifact analysis, deleted-file investigations, Recycle Bin analysis, filesystem examination, metadata analysis, file carving, hash validation, process analysis, user activity reconstruction, timeline development, artifact correlation, and host-based forensic investigations using FTK Imager, KAPE, ProcDump, ExifTool, Scalpel, Browser History Viewer, WFA, PECmd, JumpList Explorer, RBCmd, Volatility, Volatility Workbench, and Autopsy. |
| **email-security-and-phishing-analysis/** | Email Security | Email Security & Phishing Analysis | Manual email header analysis, sender spoofing detection, URL investigation, attachment analysis, and phishing artifact enrichment. |
| **threat-intelligence-and-enrichment/** | Threat Intelligence | Threat Intelligence & Enrichment | IOC analysis, ATT&CK mapping, threat actor profiling, malware research, intelligence enrichment, and operational threat intelligence workflows. |
| **vulnerability-management-and-exposure-analysis/** | Vulnerability Management | Vulnerability Management & Exposure Analysis | Vulnerability prioritization, exposure analysis, remediation validation, and risk-based vulnerability management activities. |
| **firewall-and-network-security/** | Network Security | Firewall Administration & Network Security | Firewall deployment, traffic filtering, routing and gateway management, network segmentation fundamentals, policy enforcement, traffic flow analysis, firewall rule administration, centralized security controls, and network security validation using pfSense and future firewall platforms. |

</details>



<!--
#### Category Folders (Top-Level Directories)
| Category Folder | Workflow Focus | What You Will Find Inside |
|---|---|---|
| **endpoint-triage-and-host-analysis/** | Endpoint triage and host investigation workflows focused on validating host state and suspicious behavior using native tools. | Workflows for Windows and Linux host triage: process/service inspection, filesystem artifact checks, network exposure validation, and quick health/persistence checks. |
| **siem-detections-and-log-analysis/** | SIEM-centered workflows for parsing, normalizing, and analyzing telemetry for investigations and detections. | Workflows for SPL-driven investigations, field extraction and normalization, ingestion corrections, and detection-ready data shaping (including VPN remote-access anomaly analysis). |
| **network-monitoring-and-traffic-analysis/** | Network monitoring workflows focused on packet capture, filtering, and protocol-level analysis. | Workflows for bounded packet capture with tcpdump and deeper PCAP analysis in Wireshark (filtering, session reconstruction, evidence extraction). |
| **identity-and-access-management/** | Identity and access management workflows focused on directory operations and permission validation. | Workflows for Active Directory user/group administration, delegation, OU management, and access control validation using standard enterprise tooling. |
| **detection-automation-and-log-processing/** | Automation and log-processing workflows focused on turning raw telemetry into repeatable detection logic. | Workflows for Python-based parsing/analysis, transforming logs into structured signals, and prototyping behavior-based detections. |
| **vulnerability-management-and-exposure-analysis/** | Vulnerability management workflows focused on exposure analysis, prioritization, and remediation tracking. | Category scaffold for future workflows (currently no workflow folders in this category). | **threat-intelligence-and-enrichment** | Threat intelligence workflows focused on collecting, validating, enriching, and operationalizing indicators and adversary intelligence. | Workflows for MISP-based intelligence analysis, ATT&CK mapping, IOC investigation, threat actor profiling, malware and campaign research, indicator enrichment, intelligence sharing concepts (ISACs, TAXII, TLP, PAP), and operational threat intelligence workflows. |
| **email-security-and-phising-analysis/** | Phishing email analysis workflows focused on extracting, validating, and analyzing email-based attack artifacts. | Workflows for manual email header analysis, sender spoofing detection, reply-to validation, URL and domain investigation, attachment hashing, and artifact enrichment using tools like PhishTool, WHOIS, and VirusTotal. |
| - **digital-forensics-and-evidence-analysis** | Digital forensics and evidence analysis workflows focused on acquiring, validating, examining, interpreting, and correlating digital evidence from storage media, memory, files, operating systems, browser artifacts, and forensic artifacts. Workflows include forensic disk image analysis, forensic imaging, file system identification, partition analysis, metadata examination, file carving, hash validation, browser artifact collection, browser history analysis, cached content analysis, download artifact investigation, operating system identification, hostname attribution, Recent Documents analysis, Windows shortcut (.LNK) analysis, Prefetch analysis, Jump List analysis, Recycle Bin analysis, deleted-file metadata examination, SID attribution analysis, deletion timestamp analysis, execution evidence review, filesystem navigation, account activity analysis, user activity reconstruction, timeline development, artifact correlation, memory acquisition, memory image analysis, Volatility-based memory forensics, process enumeration, process tree analysis, parent-child process analysis, privilege analysis, command-line artifact recovery, PID-based investigation, and volatile evidence examination using Volatility Workbench and the Volatility Framework. Workflows cover identifying deleted files, recovering deleted-file metadata, attributing deleted activity to specific users, analyzing Security Identifiers (SIDs), reviewing deletion timestamps, correlating original file paths, validating deleted-file ownership, reconstructing deleted-file activity using RBCmd, CSVQuickViewer, and Windows Recycle Bin artifacts, as well as analyzing forensic disk images using Autopsy to recover operating system information, browser download artifacts, source URLs, Recent Documents artifacts, filesystem metadata, local account activity, and host-based evidence. |
-->

---

### Project Structure and Documentation

Projects are organized using a two-level hierarchy:

| Level | Purpose |
|---|---|
| **Category Folders** | Group related projects by primary security domain |
| **Project Folders** | Contain one complete technical exercise, investigation, analysis, or operational scenario |

Project folders use descriptive naming conventions that typically combine the primary tool, platform, or artifact type with the security task being performed.

<details>
<summary><strong>▶️ How These Projects Are Designed</strong></summary></br>

This repository is task-first rather than tool-documentation-first.

Projects focus on demonstrating how practical security work is performed, validated, documented, and interpreted. The emphasis is on investigative methodology, evidence validation, analytical reasoning, and operational decision-making.

Common project components include:

- Step-by-step execution documentation
- Analyst reasoning and interpretation
- Tool-specific reference material
- Supporting screenshots and evidence
- Automation concepts where applicable

The goal is to capture what a capable SOC, DFIR, threat intelligence, or blue-team analyst would document while performing the work.

</details>

<details>
<summary><strong>▶️ Standard Files in Each Project Folder</strong></summary></br>
 
| File / Folder | Purpose | Contents and Focus |
|---|---|---|
| **Project README** (`README.md`) | Quick orientation for the project. | Defines the operational purpose, scope, assumptions, tooling, and what the project is meant to demonstrate. |
| **Workflow Execution** (`workflow-execution.md`) | Step-by-step hands-on execution. | The actual command flow and analyst pivots, including what to run, what to look for, and how to validate results. |
| **Analyst Notes** (`analyst-notes.md`) | Reasoning, interpretation, and edge cases. | Why specific checks are performed, how to avoid common mistakes, and how to interpret ambiguous outputs. |
| **Tool Usage Notes** (`tool-usage-notes.md`) | Reusable tool reference. | Flags, syntax patterns, parsing tips, and operational “gotchas” you can apply across multiple scenarios. |
| **Supporting Files** (`supporting-files/`) | Project-specific artifacts and resources. | Sigma rules, sample logs, scripts, configuration files, datasets, exports, reference material, generated outputs, and other supporting project artifacts. (when applicable) |
| **Automation Design Notes** (`automation-design-notes.md`) | Turning execution into repeatable automation (when present). | Notes on parsing logic, data structures, detection logic shaping, and how to operationalize the project with scripts or scheduled jobs. (when applicable) |
| **Screenshots and Supporting Evidence** (`images/`) | Visual validation artifacts. | Screenshots referenced throughout the project execution and notes to support the documented conclusions. |

Together, these files separate execution, reasoning, and reference material into clear, reviewable components while keeping everything tied to the same operational task.

</details>


<details>
 
<summary><strong>▶️ Category Overlap and Repository Scope</strong></summary></br>

A single project may touch endpoint artifacts, identity context, network indicators, SIEM analysis, threat intelligence, forensic artifacts, or automation logic.

Projects are grouped according to the primary operational objective rather than duplicated across multiple categories.

This repository focuses on reusable technical projects and operational analysis patterns. Full incident narratives, case reports, timelines, and investigation deliverables are maintained separately within the incident-response-and-investigations repository.

</details>


---

### Category Folders and Current Projects

<details>
<summary><strong>▶️ Endpoint Triage and Host Analysis</strong></summary></br>
 
`endpoint-triage-and-host-analysis/`

Projects focused on validating host state, identifying suspicious activity, and assessing system exposure through direct examination of Windows and Linux endpoints. Work commonly includes process analysis, service inspection, filesystem review, network validation, and host-based investigation using native operating system tools.

- **Linux Endpoint Triage and Script-Based Validation Logic Using Bash**</br>
  - **Folder:** `endpoint-triage-and-host-analysis` **➜** `bash-linux-endpoint-triage-and-validation-scripting`
  - **Summary:** Live host enumeration, filesystem inspection, shell environment awareness, and basic automation using Bash.
  - **Tools:** Linux (Ubuntu), Bash shell, native command-line utilities, SSH-based access.

- **Windows Endpoint Triage and Network Exposure Validation Using CMD and PowerShell**</br>
  - **Folder:** `endpoint-triage-and-host-analysis` **➜** `cmd-windows-process-and-network-triage`</br>
  - **Summary:** Filesystem inspection, process enumeration, network correlation, and targeted containment using native Windows CLI tools.</br>
  - **Tools:** Windows CMD, PowerShell, native Windows system utilities.

- **Windows Endpoint Triage and System Inspection Using PowerShell**</br>
  - **Folder:** `endpoint-triage-and-host-analysis` **➜** `powershell-windows-endpoint-triage-and-system-inspection`</br>
  - **Summary:** Process inspection, filesystem validation, service analysis, network inspection, integrity validation, and remote execution using PowerShell.</br>
  - **Tools:** Windows PowerShell, native Windows telemetry and services.

- **Windows Event Log Triage Using DeepBlueCLI**</br>
  - **Folder:** `endpoint-triage-and-host-analysis` **➜** `deepbluecli-windows-event-log-triage`</br>
  - **Summary:** Password spray detection and MITRE ATT&CK technique attribution, unauthorized local account creation and privileged group assignment detection, PowerShell `Net.WebClient` download cradle identification, and bulk `.evtx` log processing with keyword search to identify and attribute a downloaded offensive tool.</br>
  - **Tools:** DeepBlueCLI, Windows PowerShell, MITRE ATT&CK (research reference).

</details>

<details>
<summary><strong>▶️ SIEM Detections and Log Analysis</strong></summary></br>
 
`siem-detections-and-log-analysis/`

Projects focused on analyzing, normalizing, enriching, and investigating security telemetry within SIEM platforms. Work includes log parsing, field extraction, detection development, authentication analysis, anomaly identification, and transforming raw events into actionable security insights.

- **Log Parsing, Event Normalization, and Field Extraction for Detection Queries Using Splunk**</br>
  - **Folder:** `siem-detections-and-log-analysis` **➜** `splunk-log-parsing-and-field-extraction`</br>
  - **Summary:** Ingestion pipeline design, event boundary correction, sensitive data masking, and structured field extraction for detection-ready telemetry.</br>
  - **Tools:** Splunk Enterprise, Linux command line, Splunk configuration files (`inputs.conf`, `props.conf`, `transforms.conf`, `fields.conf`).

- **VPN Authentication and Remote Access Anomaly Analysis Using Splunk**</br>
  - **Folder:** `siem-detections-and-log-analysis` **➜** `splunk-vpn-remote-access-anomaly-detection`</br>
  - **Summary:** Authentication analysis, geographic anomaly detection, session outcome analysis, and baseline development using VPN telemetry.</br>
  - **Tools:** Splunk Enterprise, SPL (Search Processing Language), JSON field extraction using `spath`.

</details>

<details>
<summary><strong>▶️ Network Monitoring and Traffic Analysis</strong></summary></br>  
 
`network-monitoring-and-traffic-analysis/`

Projects focused on collecting, filtering, inspecting, and interpreting network traffic to identify communication patterns, protocol behavior, and evidence of suspicious activity. Work includes packet capture, protocol analysis, session reconstruction, traffic filtering, and network-based investigation techniques.

- **Targeted Packet Capture and Traffic Filtering Using tcpdump**</br>
  - **Folder:** `network-monitoring-and-traffic-analysis` **➜** `tcpdump-targeted-packet-capture-and-filtering`</br>
  - **Summary:** Interface validation, bounded packet capture, protocol filtering, and offline analysis preparation.</br>
  - **Tools:** Linux, tcpdump, native networking utilities.

- **Traffic Filtering, Protocol Dissection, and Session Reconstruction Using Wireshark**</br>
  - **Folder:** `network-monitoring-and-traffic-analysis` **➜** `wireshark-traffic-analysis-and-session-reconstruction`</br>
  - **Summary:** Protocol inspection, OSI-layer dissection, session reconstruction, and evidence extraction from packet captures.</br>
  - **Tools:** Wireshark, stored PCAP and PCAPNG capture files.

</details>


<details>
<summary><strong>▶️ Identity and Access Management</strong></summary></br>
 
`identity-and-access-management/`

Projects focused on managing identities, validating permissions, and administering directory-based access controls within enterprise and cloud environments. Work includes Active Directory domain controller operations, cloud identity provisioning in Microsoft Entra ID, user and group lifecycle management, license assignment, delegation, organizational unit management, policy application, and access validation using Active Directory and Microsoft Entra ID.

- **Identity and Access Management Operations Using Active Directory**</br>

  - **Folder:** `identity-and-access-management` **➜** `active-directory-iam-operations-and-permission-validation`</br>
  - **Summary:** User and group administration, organizational unit management, delegation, and access control validation in an enterprise directory.</br>
  - **Tools:** Windows Server, Active Directory Domain Services (AD DS), Active Directory Users and Computers (ADUC), Group Policy Management Console (GPMC).

- **Active Directory Domain Services Administration — Domain Controller Operations, User Management, Password Policies, and Security Configuration**</br>

  - **Folder:** `identity-and-access-management` **➜** `active-directory-domain-services-administration`</br>
  - **Summary:** Hyper-V lab environment setup, domain controller promotion and FSMO role transfer, Active Directory site/subnet configuration, organizational unit and user account management, Protected Users and delegation of control, domain and fine-grained password policy configuration, AD Recycle Bin enablement, NTLM restriction, advanced audit policy configuration, and user rights assignment.</br>
  - **Tools:** Hyper-V, Windows Server 2022, Server Manager, Active Directory Domain Services Configuration Wizard, Active Directory Users and Computers (ADUC), Active Directory Administrative Center (ADAC), Active Directory Sites and Services, Group Policy Management Console (GPMC).

- **Active Directory Branch Office Deployment and Security Hardening**</br>

  - **Folder:** `identity-and-access-management` **➜** `active-directory-branch-office-deployment-and-security-hardening`</br>
  - **Summary:** Branch office domain extension including second domain controller deployment, AD site and subnet topology configuration, FSMO role transfer, Paris OU structure creation, scoped delegation of administrative rights, bulk PowerShell user lifecycle management, domain and fine-grained password policy configuration, AD Recycle Bin enablement, NTLM restriction, failed-logon auditing, and user rights assignment via Group Policy.</br>
  - **Tools:** Windows Server, Server Manager, Active Directory Domain Services Configuration Wizard, Active Directory Users and Computers (ADUC), Active Directory Administrative Center (ADAC), Active Directory Sites and Services, Group Policy Management Console (GPMC), Windows PowerShell, repadmin, netdom.

- **Microsoft Entra ID — User and Group Management, License Assignment, and User Lifecycle Operations**</br>

  - **Folder:** `identity-and-access-management` **➜** `entra-id-user-group-license-management`</br>
  - **Summary:** Cloud identity provisioning in Microsoft Entra ID, Security and Microsoft 365 group creation and administration, group-based and individual license assignment, and user lifecycle operations including deletion and restoration within the 30-day recovery window.</br>
  - **Tools:** Microsoft Entra admin center (`entra.microsoft.com`), Microsoft 365 admin center (`admin.microsoft.com`).

- **Microsoft Entra ID — Identity Administration, Role-Assignable Groups, Security Configuration Documentation, and Conditional Access Analysis**</br>

  - **Folder:** `identity-and-access-management` **➜** `entra-id-identity-administration-role-groups-and-conditional-access`</br>
  - **Summary:** Cloud identity provisioning with disabled-account creation, individual license assignment, directory role assignment, business-to-business guest invitation, role-assignable security group creation and group-based directory role assignment, group owner and member management, password protection and self-service password reset documentation, Conditional Access named location review, and Conditional Access What If analysis and policy scope documentation.</br>
  - **Tools:** Microsoft Entra admin center (`entra.microsoft.com`), Microsoft 365 admin center (`admin.microsoft.com`), Configuration Data Editor (lab-provided desktop application).

</details>

<details>
<summary><strong>▶️ Detection Automation and Log Processing</strong></summary></br>
 
`detection-automation-and-log-processing/`

Projects focused on transforming raw security telemetry into repeatable detection logic through automation, parsing, normalization, and enrichment. Work includes Python-based log analysis, structured data extraction, behavioral detection development, and automation of common security operations tasks.

- **Log Parsing and Threat Detection Automation Using Python**</br>
  - **Folder:** `detection-automation-and-log-processing` **➜** `python-log-parsing-and-threat-detection`</br>
  - **Summary:** Behavior-based detection logic prototyping from raw security telemetry across multiple environments using Python automation.</br>
  - **Tools:** Python (standard library only), Linux shell environment (Google Cloud Shell), multi-format log datasets.

</details>

<details>
<summary><strong>▶️ Email Security and Phishing Analysis</strong></summary></br>
 
`email-security-and-phishing-analysis/`

Projects focused on acquiring, validating, examining, and correlating digital evidence from storage media, memory, operating systems, browser artifacts, and forensic artifacts. Work includes forensic imaging, disk and memory analysis, browser forensics, Windows artifact analysis, deleted-file investigations, evidence validation, user activity reconstruction, timeline development, and host-based forensic investigations using industry-standard DFIR tools and methodologies.

- **File System Identification and Evidence Structure Analysis Using FTK Imager**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `filesystem-identification-and-evidence-structure-analysis-using-ftk-imager`</br>
  - **Summary:** Forensic image examination, partition analysis, file system identification, and evidence structure interpretation using acquired disk images. Focuses on identifying NTFS (NT File System), FAT32 (File Allocation Table 32), and EXT3 (Third Extended Filesystem) volumes while establishing investigative context for future artifact analysis, metadata examination, deleted file recovery, and timeline reconstruction. Reinforces foundational digital forensic concepts including partitions, clusters, file allocation structures, Logical Block Addresses (LBAs), Flash Translation Layers (FTLs), SSD storage behavior, TRIM operations, garbage collection, and wear leveling.</br>
  - **Tools:** FTK Imager, forensic disk image files (`carve1.img`, `carve2.img`, `disk1.img`).

- **Metadata Analysis and File Carving Using ExifTool and Scalpel**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `metadata-analysis-and-file-carving-using-exiftool-and-scalpel`</br>
  - **Summary:** Metadata extraction and deleted file recovery workflow focused on identifying embedded document and image metadata, recovering deleted image artifacts from a forensic disk image, and validating recovered evidence using cryptographic hashing. Demonstrates PDF author identification, EXIF camera metadata analysis, file signature-based carving, deleted file recovery methodology, and evidence validation through MD5 hashing.</br>
  - **Tools:** ExifTool, Scalpel, Linux Terminal, nano, grep, md5sum.

- **Hash Analysis and File Integrity Validation**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `hash-analysis-and-file-integrity-validation`</br>
  - **Summary:** Cryptographic hashing workflow focused on generating, comparing, and validating MD5 (Message Digest Algorithm 5), SHA1 (Secure Hash Algorithm 1), and SHA256 (Secure Hash Algorithm 256-bit) hash values from both text strings and file-based artifacts. Demonstrates evidence integrity verification, file fingerprinting, algorithm comparison, artifact identification, and hash lookup methodologies used throughout digital forensic investigations and incident response operations. Reinforces foundational concepts including one-way hashing functions, evidence validation, chain-of-custody support, file-content hashing versus filename hashing, and the distinction between hashing and encryption. Covers practical use of Linux hashing utilities, Windows PowerShell hashing, and public hash databases while highlighting the role of hashes in forensic acquisitions, malware identification, artifact correlation, and threat intelligence sharing.</br>
  - **Tools:** md5sum, sha1sum, sha256sum, echo, PowerShell Get-FileHash, Linux Terminal, Public Hash Lookup Databases.

- **Data Acquisition Using FTK Imager, ProcDump, and KAPE**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `data-acquisition-using-ftk-imager-procdump-and-kape`</br>
  - **Summary:** Evidence acquisition workflow focused on preserving volatile memory, process memory, forensic disk images, and browser-related artifacts from local and remote Windows systems. Demonstrates practical acquisition methodologies used throughout digital forensic and incident response investigations, including physical memory capture, process-specific memory dumping, forensic disk imaging, evidence integrity validation through hash generation, remote evidence collection, and targeted artifact acquisition. Reinforces foundational DFIR concepts including volatile versus persistent data, evidence preservation, process identification, forensic imaging workflows, browser artifact collection, remote acquisition operations, and the relationship between acquisition and later forensic analysis. Covers practical use of FTK Imager, ProcDump, PowerShell, Remote Desktop Protocol (RDP), and KAPE while highlighting how different acquisition methods support memory forensics, malware investigations, browser forensics, incident response activities, and digital evidence preservation.</br>
  - **Tools:** FTK Imager, ProcDump, PowerShell, RDP, KAPE, gkape, Windows File Explorer, forensic disk image files (`.E01`), memory dump files (`.mem`), process dump files (`.dmp`).

- **Windows Artifact Analysis Using WFA, PECmd, and JumpList Explorer**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `windows-artifact-analysis-using-wfa-pecmd-and-jumplistexplorer`</br>
  - **Summary:** Windows artifact analysis workflow focused on identifying suspicious files, reconstructing user activity, and reviewing execution-related evidence through Windows forensic artifacts. Demonstrates practical analysis of Shortcut (.LNK) files, Prefetch (.pf) files, and Jump List artifacts to identify file origins, archive relationships, application interactions, execution traces, browser activity, and user behavior. Reinforces foundational DFIR concepts including artifact correlation, execution evidence review, application attribution, user activity reconstruction, and the distinction between file existence, file access, and execution-related activity. Covers practical use of Windows File Analyzer (WFA), PECmd, JumpList Explorer, Command Prompt, and Windows artifact analysis methodologies while highlighting how multiple independent artifact sources can be combined to reconstruct investigative timelines and support forensic conclusions.</br>
  - **Tools:** Windows File Analyzer (WFA), PECmd, JumpList Explorer, Command Prompt, Windows Shortcut Artifacts (.LNK), Windows Prefetch Artifacts (.pf), Windows Jump Lists.

- **Browser History Analysis Using Browser History Viewer**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `browser-history-analysis-with-browser-history-viewer`</br>
  - **Summary:** Browser artifact analysis workflow focused on reconstructing user activity, examining cached browser content, identifying downloaded files, and correlating browser-based evidence. Demonstrates practical analysis of Website History records, Cached Images, browser profiles, keyword filtering, download artifact identification, timeline reconstruction, and source attribution using Browser History Viewer (BHV). Reinforces foundational DFIR concepts including browser artifact analysis, user activity reconstruction, evidence correlation, cached content examination, download investigation, delivery source versus hosting source attribution, timeline analysis, and browser-based forensic methodologies while highlighting how multiple browser artifacts can be combined to reconstruct investigative narratives and support forensic conclusions.</br>
  - **Tools:** Browser History Viewer (BHV), Website History, Cached Images, Browser Profiles, Download Records, Local File References, URL Records.

- **Recycle Bin Analysis Using RBCmd and CSVQuickViewer**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `recycle-bin-analysis-using-rbcmd-and-csvquickviewer`</br>
  - **Summary:** Recycle Bin analysis workflow focused on identifying deleted files, reconstructing deleted-file activity, attributing deleted artifacts to specific users, and reviewing deletion-related evidence through Windows Recycle Bin artifacts. Demonstrates practical examination of Recycle Bin metadata, Security Identifier (SID) attribution, deletion timestamps, original file path analysis, file size correlation, and deleted-file investigation using RBCmd and CSVQuickViewer. Reinforces foundational DFIR concepts including deleted-file recovery methodologies, metadata interpretation, user attribution, evidence correlation, artifact persistence, and the distinction between file deletion and evidence removal. Covers practical use of RBCmd, CSVQuickViewer, Command Prompt, and Windows Recycle Bin artifacts while highlighting how multiple metadata fields can be combined to support investigative findings and reconstruct deleted-file activity.</br>
  - **Tools:** RBCmd, CSVQuickViewer, Command Prompt, Windows Recycle Bin Artifacts, SID-Based Recycle Bin Folders, Deleted-File Metadata Records.

- **Memory Analysis Using Volatility**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `memory-analysis-using-volatility`</br>
  - **Summary:** Memory analysis workflow focused on identifying operating system profiles, analyzing running processes, investigating process relationships, reviewing command-line execution artifacts, identifying suspicious network communications, and extracting executable processes from memory. Demonstrates practical examination of Windows memory images using Volatility Framework 2.6.1 and Linux command-line utilities. Covers image identification, profile selection, process enumeration, process tree analysis, command-line recovery, network connection analysis, process dumping, executable hashing, and volatile evidence correlation. Reinforces foundational DFIR concepts including memory forensics methodology, volatile versus persistent evidence, process attribution, parent-child process analysis, command execution investigation, network artifact interpretation, evidence preservation, and multi-artifact correlation used to reconstruct activity occurring at the time of memory acquisition.</br>
  - **Tools:** Volatility Framework 2.6.1, Bash, grep, wc, md5sum, Linux Terminal, Process Lists, Process Trees, Command-Line Arguments, Network Connections, Process Memory Dumps, Memory Image Metadata.

- **Memory Forensics and Process Analysis Using Volatility 3 Workbench**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `memory-forensics-and-process-analysis-using-volatility`</br>
  - **Summary:** Memory forensics workflow focused on analyzing Windows memory images, identifying operating system metadata, enumerating active processes, investigating process relationships, reviewing process privileges, and examining command-line execution artifacts using Volatility Workbench and the Volatility Framework. Demonstrates practical examination of volatile memory evidence through process enumeration, operating system identification, process tree analysis, privilege analysis, PID-based investigation, and command-line artifact recovery. Reinforces foundational DFIR concepts including memory artifact interpretation, process lineage analysis, parent-child process relationships, privilege attribution, execution context analysis, evidence correlation, and the distinction between process presence and process behavior. Covers practical use of Volatility Workbench, Volatility Framework plugins, Process ID filtering, and memory-based host investigation methodologies.</br>
  - **Tools:** Volatility Workbench, Volatility Framework 3, Windows Memory Images, Process Lists, Operating System Metadata, Process Trees, Process Privileges, Command-Line Arguments.

- **Disk Image Forensics and Artifact Analysis Using Autopsy**</br>
  - **Folder:** `digital-forensics-and-evidence-analysis` **➜** `disk-image-forensics-and-artifact-analysis-using-autopsy`</br>
  - **Summary:** Disk image forensic workflow focused on examining a forensic disk image to identify operating system information, hostname metadata, browser download artifacts, source URLs, Recent Documents evidence, virtual filesystem metadata, and local operating system account activity. Demonstrates practical use of Autopsy for case creation, evidence ingestion, ingest module configuration, artifact review, filesystem navigation, download investigation, shortcut artifact analysis, account analysis, and evidence correlation. Covers operating system identification, hostname attribution, browser download reconstruction, URL recovery, Recent Documents analysis, Windows shortcut (.LNK) interpretation, filesystem examination, directory metadata analysis, and local account activity review. Reinforces foundational DFIR concepts including disk image examination, persistent evidence analysis, artifact interpretation, filesystem navigation, user activity reconstruction, account attribution, browser artifact analysis, host-based evidence correlation, and forensic workflow methodology.</br>
  - **Tools:** Autopsy, E01 Disk Images, Operating System Information, Hostname Metadata, Web Downloads, Source URLs, Recent Documents, Windows Shortcut (.LNK) Artifacts, Virtual Filesystem Metadata, Directory Metadata, OS Accounts.


</details>

<details>
<summary><strong>▶️ Threat Intelligence Enrichment and Correlation</strong></summary></br>
 
`threat-intelligence-enrichment-and-correlation/`

Projects focused on collecting, validating, enriching, and operationalizing threat intelligence to better understand adversaries, infrastructure, malware activity, and attack techniques. Work includes IOC analysis, ATT&CK mapping, threat actor research, malware infrastructure investigation, intelligence enrichment, campaign analysis, and contextual correlation using structured threat intelligence platforms and external research sources.

- **Threat Intelligence Research and Analysis Using MISP**</br>

  - **Folder:** `threat-intelligence-enrichment-and-correlation` **➜** `misp-ioc-enrichment-and-correlation`</br>
  - **Summary:** Investigation and enrichment of ransomware campaigns, malware infrastructure, DDoS botnet activity, persistence mechanisms, and vulnerability exploitation using MISP (Malware Information Sharing Platform). Focuses on operational IOC analysis, ATT&CK mapping interpretation, Galaxy intelligence review, malware infrastructure correlation, external threat reporting pivoting, and contextual adversary analysis using both structured threat intelligence platforms and external research sources.</br>
  - **Tools:** MISP, VirusTotal, Arctic Wolf Reporting, Reddit Technical Malware Analysis, CVE/Vulnerability Intelligence Research.

</details>

<details>
<summary><strong>▶️ Vulnerability Management and Exposure Analysis</strong></summary></br>
 
`vulnerability-management-and-exposure-analysis/`

Projects focused on identifying, prioritizing, validating, and reducing security exposures across systems and applications. Work includes vulnerability assessment, risk prioritization, remediation validation, exposure analysis, and supporting activities designed to reduce attack surface and improve defensive posture.

- Ongoing development

</details>

<details>
<summary><strong>▶️ Firewall and Network Security</strong></summary></br>

`firewall-and-network-security/`

Projects focused on deploying, configuring, validating, and administering network security controls that regulate traffic flow between systems and networks. Work commonly includes firewall deployment, routing and gateway management, traffic filtering, policy enforcement, network segmentation fundamentals, and centralized security control validation.

- **pfSense Firewall Rule Management and Traffic Filtering**</br>
  - **Folder:** `firewall-and-network-security` ➜ `pfsense-firewall-rule-management`</br>
  - **Summary:** Deploy a pfSense firewall within a virtualized environment, configure interfaces and gateways, create aliases and firewall policies, redirect workstation traffic through the firewall, and validate traffic filtering behavior through controlled testing. Demonstrates firewall administration, routing analysis, rule evaluation order, policy enforcement, and centralized network security controls.</br>
  - **Tools:** pfSense, VirtualBox, Bridged Networking, Firewall Rules, Aliases, Gateway Configuration.

</details>

---

<!--
### Overlap Between Categories

Overlap between operational domains is expected and intentional.

A single workflow may touch:

- Endpoint artifacts
- Identity context
- Network indicators
- SIEM queries and field extraction

Rather than duplicating workflows across multiple categories, each workflow is grouped by the **primary operational job-to-be-done**, while the workflow documentation can include supporting context from other domains where it is necessary to validate results.

-->

<!--
### Relationship to Investigations and Case Work

This repository documents **how common SOC tasks are executed**.

Case-based incident writeups (full incident narratives, timelines, and findings) are maintained separately in the investigations repository so that this repo can remain focused on reusable operational execution patterns.

Where relevant, workflows can be used as building blocks inside investigations to demonstrate how repeatable operational procedures translate into real investigative decisions.
-->

### Ongoing Development

Workflows may be expanded over time as additional tools, validation steps, and automation approaches are added. Updates are intended to reflect iterative improvement, similar to how real SOC runbooks mature through repeated use and post-incident learning.
