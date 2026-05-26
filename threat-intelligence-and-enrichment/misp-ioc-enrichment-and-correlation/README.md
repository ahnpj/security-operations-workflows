# MISP-Based Threat Intelligence Investigation and Malware Analysis

- **Category:** Threat Intelligence and IOC Enrichment  
- **Primary Operational Focus:** Threat intelligence analysis, malware investigation, infrastructure correlation, and adversary context enrichment  
- **Operational Objectives Demonstrated:** IOC Analysis, Threat Intelligence Pivoting, Malware Infrastructure Investigation, Persistence Analysis, Vulnerability Research, ATT&CK Mapping, Contextual Adversary Intelligence Review  
- **Primary Data Sources:** MISP threat intelligence events, malware indicators, external technical reporting, VirusTotal analysis, CVE intelligence, ransomware reporting, and infrastructure intelligence artifacts

> **Terminology used:**  
> **Workflows** refer to operational threat intelligence tasks such as malware investigation, infrastructure analysis, or adversary tracking.  
> **Executions** refer to the hands-on investigation and analysis performed using MISP events, intelligence pivots, and external reporting sources.  
> **Writeups** document how investigative workflows were performed, how indicators were interpreted, and how operational conclusions were validated.

---

### Overview

This execution documents the practical use of MISP (Malware Information Sharing Platform) to investigate ransomware activity, DDoS botnet infrastructure, malware campaigns, persistence mechanisms, command-and-control communications, and vulnerability exploitation activity.

The execution focuses on how analysts pivot between operational indicators, malware intelligence, external reporting, vulnerability research, and contextual adversary intelligence during real-world threat intelligence investigations.

Throughout the workflow, MISP was used as a centralized intelligence aggregation platform to review:
- malware indicators,
- hashes,
- IP infrastructure,
- ransomware campaigns,
- persistence artifacts,
- ATT&CK mappings,
- Galaxy intelligence,
- external threat reporting references.

The investigation also demonstrates how operational intelligence analysis frequently extends beyond the threat intelligence platform itself and into:
- VirusTotal analysis,
- Reddit technical malware reporting,
- Arctic Wolf incident response reporting,
- CVE and vulnerability intelligence research,
- external malware and ransomware reporting.

> 👉 **Follow the execution walkthrough first**  
Begin with `workflow-execution.md` inside this folder to see how threat intelligence events were investigated, how indicators were analyzed, and how external intelligence pivots were performed step by step using MISP.

> 👉 **Review analytical reasoning and investigative decision-making**  
Move to `analyst-notes.md` to understand how investigative pivots were selected, how operational indicators were interpreted, and how contextual intelligence influenced analytical reasoning throughout the investigation.

> 👉 **Review tooling and intelligence platform usage details**  
See `tool-usage-notes.md` to understand how MISP, VirusTotal, external reporting sources, and vulnerability intelligence resources were used operationally during execution.

> 👉 **See what each execution file contains in full detail**  
For a complete breakdown of every standard file in this folder, explaining the contents, intent, and role of each document in the overall execution, see the **[Repository Structure & Supporting Documents](#repository-structure--supporting-documents)** section below.

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how threat intelligence investigations and malware analysis workflows are documented within SOC, DFIR, and cyber threat intelligence operational environments.

If you want to follow the execution step by step, start with:

**`workflow-execution.md`**

---

### Repository Structure & Supporting Documents

All execution outputs are separated into focused documents to reflect operational threat intelligence and malware investigation documentation practices.

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured threat intelligence investigation walkthrough documenting how MISP events, operational indicators, and external intelligence sources were investigated and correlated. | Documents event searches, IOC analysis, ATT&CK mapping review, malware infrastructure analysis, persistence investigation, CVE research, ransomware intelligence analysis, and external intelligence pivoting workflows. Emphasizes operational investigation methodology and repeatable intelligence analysis processes. |
| `images/` | Visual evidence supporting threat intelligence findings and investigative pivots. | Contains screenshots of MISP events, ATT&CK mappings, Galaxy intelligence, operational indicators, VirusTotal analysis, malware reporting, CVE research, persistence artifacts, and infrastructure intelligence findings supporting investigative conclusions. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of threat intelligence objectives, malware investigation context, and operational relevance aligned with SOC and threat intelligence documentation practices. |
| `analyst-notes.md` | Analytical reasoning and investigative observations recorded during execution. | Documents investigative confusion points, intelligence pivot reasoning, operational observations, ATT&CK interpretation, persistence analysis thinking, and lessons learned while correlating malware indicators and contextual intelligence. |
| `tool-usage-notes.md` | Technical reference for intelligence platforms and external analysis resources used throughout the workflow. | Covers MISP usage, VirusTotal analysis, external reporting review, malware infrastructure investigation, CVE research, Galaxy intelligence usage, and operational indicator interpretation workflows. |

---

### Environment, Data Sources, and Tools

The execution focuses on operational threat intelligence analysis using MISP and external intelligence reporting to investigate malware campaigns, ransomware activity, infrastructure indicators, and persistence mechanisms.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Browser-based threat intelligence and malware investigation environment |
| **Processed Assets** | Threat intelligence events, malware indicators, infrastructure artifacts, hashes, filenames, external intelligence reporting, and contextual adversary intelligence |
| **Execution Platform** | MISP (Malware Information Sharing Platform) |
| **Primary Platforms / Services** | MISP, VirusTotal, Arctic Wolf reporting, Reddit technical malware analysis, CVE and vulnerability intelligence sources |
| **Operational Focus** | Investigate malware campaigns, ransomware activity, persistence mechanisms, command-and-control infrastructure, and vulnerability exploitation activity using operational threat intelligence workflows |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Primary Intelligence Sources** | MISP threat intelligence events, malware infrastructure indicators, ransomware reporting, external malware analysis references, and contextual adversary intelligence |
| **IOC Analysis Techniques** | Review and interpretation of hashes, IP addresses, filenames, command-and-control infrastructure, persistence artifacts, and malware indicators associated with malicious activity |
| **Threat Intelligence Pivoting** | Pivoting from MISP events into external research sources such as VirusTotal, Arctic Wolf reporting, Reddit malware analysis, and public CVE intelligence |
| **ATT&CK Analysis Techniques** | Review of ATT&CK mappings associated with malware campaigns and ransomware operations to understand adversary tactics, techniques, and operational behavior |
| **Galaxy Intelligence Review** | Contextual analysis of ransomware groups and adversary operations using MISP Galaxy intelligence entries |
| **Persistence Analysis Techniques** | Review of malicious webshells, persistence filenames, cron persistence mechanisms, and attacker post-exploitation artifacts |
| **Infrastructure Analysis Techniques** | Identification and interpretation of command-and-control infrastructure, DDoS botnet IP indicators, malware communication endpoints, and malicious external infrastructure |
| **Vulnerability Research Techniques** | Correlation of threat intelligence with CVE reporting, vendor advisories, exploit intelligence, and affected software version research |
| **Operational Workflow Context** | Demonstrates how SOC analysts and threat intelligence teams pivot between operational indicators, malware analysis, external reporting, and contextual adversary intelligence during real-world investigations |

The execution demonstrates how modern threat intelligence investigations combine tactical indicator analysis with broader contextual intelligence and external malware research.

---

### Intended Use

The documented execution demonstrates practical threat intelligence investigation methodology using MISP-based workflows, malware analysis pivoting, operational indicator review, and contextual adversary intelligence correlation.

The workflow reflects how security operations teams, incident responders, and threat intelligence analysts investigate:
- ransomware campaigns,
- malware infrastructure,
- persistence mechanisms,
- command-and-control activity,
- vulnerability exploitation,
- attacker operational behavior.

---

### Relevance to Security Operations

Threat intelligence analysis remains a critical component of modern security operations.

The execution demonstrates how operational threat intelligence workflows help analysts:

- Correlate malware infrastructure and operational indicators  
- Investigate ransomware campaigns and persistence activity  
- Identify command-and-control communications and malicious infrastructure  
- Validate vulnerability exploitation and affected technologies  
- Support threat hunting, incident response, and detection engineering workflows  
- Build broader adversary awareness using contextual intelligence and ATT&CK mappings  

Even mature SOC environments rely heavily on external intelligence reporting and IOC enrichment workflows to improve detection accuracy and accelerate investigative response efforts.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate practical threat intelligence methodology, malware investigation workflows, IOC enrichment processes, ransomware analysis, and professional investigative documentation aligned with real operational SOC and cyber threat intelligence environments.
