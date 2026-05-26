# Threat Intelligence Enrichment & Correlation

(`threat-intelligence-enrichment-and-correlation/README.md`)

This folder contains workflow executions where the **primary focus is investigating threat intelligence events, correlating operational indicators, enriching malware intelligence, and analyzing adversary activity using structured intelligence platforms and external reporting sources**. These workflows simulate how SOC analysts, threat intelligence teams, and incident responders investigate malware campaigns, ransomware activity, malicious infrastructure, and attacker tradecraft using operational threat intelligence methodologies.

Rather than presenting abstract intelligence concepts, each folder documents practical execution of specific threat intelligence workflows, including how indicators were identified, how intelligence pivots were performed, how external reporting was correlated, and how operational conclusions were reached during investigation. The emphasis is placed on investigative reasoning, operational enrichment workflows, and intelligence correlation processes similar to what would be documented in internal threat intelligence notes, SOC investigative runbooks, or malware analysis enrichment workflows.

Each folder uses a task-first naming convention followed by the primary operational focus (for example, `misp-ioc-enrichment-and-correlation`). This allows quick identification of the investigative platform or workflow type while documentation itself emphasizes operational analysis, intelligence correlation, and investigative methodology rather than platform features alone.

> 👉 **Each folder represents one complete workflow execution**  
Every subfolder here is a **fully self-contained operational threat intelligence execution scenario** documenting how malware intelligence, infrastructure indicators, ransomware activity, or adversary operations were investigated from initial event review through enrichment and contextual analysis.

> 👉 **Follow the workflow execution first**  
Begin with `workflow-execution.md` inside a workflow folder to see how intelligence events, indicators, and external reporting were investigated step by step using operational threat intelligence workflows.

> 👉 **See what files exist in each workflow folder**  
For a complete breakdown of every standard file included in a workflow execution — including what each document contains, how it supports intelligence analysis workflows, and how the documentation is structured — see the **[Workflow Documentation Structure](#workflow-documentation-structure)** section below.

> 👉 **See which workflow executions currently exist in this category**  
For a list of all threat intelligence and IOC enrichment executions currently included in this folder, along with a description of what each workflow demonstrates and which operational skills it focuses on, see the **[What’s in This Folder](#whats-in-this-folder)** section below.

> **Terminology used:**  
> **Workflows** refer to operational threat intelligence tasks such as IOC enrichment, malware infrastructure analysis, or adversary tracking.  
> **Executions** refer to the hands-on investigation and analysis performed using intelligence platforms, indicators, and external reporting sources.  
> **Writeups** document how investigative workflows were performed, how indicators were interpreted, and how operational findings were validated and contextualized.

---

### How Workflows Are Categorized

Workflows are grouped here when **threat intelligence analysis, IOC enrichment, malware infrastructure investigation, and contextual adversary analysis are the primary operational objectives**, rather than endpoint triage or full incident reconstruction.

Although these activities frequently support investigations and incident response operations, they are categorized separately because their primary purpose is intelligence correlation, infrastructure analysis, operational enrichment, and adversary understanding.

Workflows in this category typically focus on:

- **IOC enrichment and operational indicator analysis**, reviewing IP addresses, hashes, filenames, domains, persistence artifacts, and command-and-control infrastructure associated with malicious activity.

- **Threat intelligence pivoting**, moving between intelligence platforms, malware repositories, external reporting, vulnerability intelligence, and technical analysis sources to enrich investigations and validate findings.

- **Malware and ransomware intelligence analysis**, investigating malware families, ransomware campaigns, persistence mechanisms, and operational attacker behavior documented within intelligence reporting.

- **Adversary and campaign contextualization**, using ATT&CK mappings, Galaxy intelligence, threat actor reporting, and historical campaign analysis to understand broader operational context surrounding malicious activity.

Although these workflows support investigations, they are organized here when the **core work involves intelligence enrichment, operational analysis, and adversary correlation rather than complete incident lifecycle reconstruction.**

---

### Operational Context and Purpose

Threat intelligence enrichment and correlation workflows typically occur during:
- alert triage,
- malware investigations,
- ransomware response preparation,
- IOC validation,
- threat hunting preparation,
- vulnerability exposure analysis,
- infrastructure correlation efforts.

At this stage of security operations, analysts are attempting to determine:
- what a threat is,
- how it operates,
- what infrastructure it uses,
- what persistence mechanisms are involved,
- what systems may be vulnerable,
- how the activity maps to known adversary behavior.

The workflow executions in this category reflect real SOC and cyber threat intelligence tasks where analysts continuously pivot between operational indicators, malware reporting, infrastructure intelligence, vulnerability research, and contextual adversary analysis to build actionable threat understanding.

---

### What’s in This Folder

Each workflow execution is contained in its **own dedicated folder** and represents **one complete operational threat intelligence investigation scenario**, including execution walkthroughs, analytical reasoning, intelligence enrichment workflows, and tooling references.

Current workflow executions include:

- **Threat Intelligence Research and Analysis Using MISP**  
  (`misp-ioc-enrichment-and-correlation`)  
  Focuses on using MISP (Malware Information Sharing Platform) to investigate ransomware activity, malware infrastructure, DDoS botnet operations, persistence mechanisms, command-and-control communications, and vulnerability exploitation activity. Demonstrates operational IOC enrichment workflows, ATT&CK mapping review, Galaxy intelligence analysis, malware reporting pivoting, and external intelligence correlation using sources such as VirusTotal, Arctic Wolf reporting, Reddit technical malware analysis, and CVE intelligence.

Additional workflow executions will be added as threat intelligence methodologies, malware analysis techniques, infrastructure enrichment workflows, and adversary correlation techniques expand across different intelligence platforms and operational use cases.

---

### Workflow Documentation Structure

Each workflow execution is fully self-contained and uses documentation aligned with how operational threat intelligence investigations and malware analysis workflows are documented within SOC, DFIR, and cyber threat intelligence environments.

| File / Folder | Purpose | Contents and Focus |
|-------------|-------------|--------------------|
| **`workflow-execution.md`** | Operational threat intelligence walkthrough | Step-by-step intelligence investigation workflow showing event searches, IOC analysis, intelligence pivoting, ATT&CK review, malware infrastructure analysis, and external reporting correlation |
| **`README.md`** | Workflow context and operational objective | Describes the intelligence investigation objective, workflow scope, malware analysis context, and how the execution supports SOC and threat intelligence operations |
| **`analyst-notes.md`** | Analytical reasoning and investigative observations | Documents investigative pivots, operational observations, indicator interpretation, ATT&CK analysis thinking, persistence analysis reasoning, and lessons learned during execution |
| **`tool-usage-notes.md`** | Intelligence platform and operational tooling reference | Explains MISP usage, IOC analysis workflows, VirusTotal usage, external reporting review, malware intelligence enrichment, and vulnerability research techniques |
| **`images/` or `screenshots/`** | Validation and evidence artifacts | Contains screenshots of MISP events, ATT&CK mappings, Galaxy intelligence, malware analysis results, persistence artifacts, CVE research, infrastructure indicators, and investigative findings |

Together, these files separate **operational execution**, **analytical reasoning**, **tool reference**, and **contextual intelligence analysis** into clearly reviewable components while remaining tied to the same operational investigation workflow.

---

### How These Workflow Executions Are Designed

This category is **execution-focused**, not theoretical.

You will find:

- Realistic threat intelligence investigation walkthroughs
- IOC enrichment and operational infrastructure analysis
- ATT&CK mapping interpretation and adversary contextualization
- Malware analysis pivoting and external intelligence correlation
- Persistence artifact investigation and vulnerability research
- Operational reasoning behind investigative pivots and intelligence analysis decisions

Each workflow demonstrates not just how indicators are reviewed, but why intelligence enrichment, adversary context, and operational correlation are critical for detection improvement, incident response readiness, and long-term threat visibility.

Documentation is intentionally structured to resemble internal SOC threat intelligence procedures and operational analyst working notes rather than tutorial-style instruction.

---

### Relationship to Incident Response and Detection Engineering

Threat intelligence enrichment workflows directly support:
- incident response,
- threat hunting,
- malware analysis,
- detection engineering,
- vulnerability management,
- infrastructure monitoring,
- IOC correlation efforts.

In operational environments, intelligence analysis workflows documented here are frequently used to:
- validate indicators,
- enrich alerts,
- improve SIEM detections,
- prioritize investigations,
- identify emerging attacker infrastructure,
- support ransomware response preparation.

Full incident lifecycle investigations — including timeline reconstruction, containment planning, forensic analysis, remediation tracking, and case management — are documented separately in the:

`incident-response-and-investigations` repository

This separation reflects how professional security teams distinguish between intelligence enrichment workflows and complete incident response case documentation.

---

### Ongoing Development

Workflows in this category are continuously expanded as new threat intelligence methodologies, malware analysis techniques, IOC enrichment approaches, and adversary tracking workflows are explored.

Some workflow directories may appear iterative or incomplete. This reflects active development and refinement rather than unfinished work. In operational security environments, threat intelligence workflows evolve constantly as:
- malware ecosystems change,
- ransomware operations evolve,
- attacker infrastructure shifts,
- vulnerability exploitation activity emerges,
- intelligence sources expand.

The focus is on building a growing library of reusable operational threat intelligence workflows rather than static one-time demonstrations.