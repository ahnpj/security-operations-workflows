# Tool Usage Notes — MISP-Based Threat Intelligence Investigation and Malware Analysis

### Overview

This document summarizes the tools, platforms, workflows, and intelligence sources used throughout the MISP-based threat intelligence investigation lab.

The investigation focused on:
- malware analysis,
- ransomware intelligence,
- DDoS botnet infrastructure,
- persistence mechanisms,
- CVE research,
- external threat intelligence enrichment,
- adversary context analysis.

---

### Primary Investigation Platform — MISP

#### Purpose
MISP (Malware Information Sharing Platform) was used as the primary threat intelligence platform for reviewing:
- malware campaigns,
- ransomware activity,
- IP infrastructure,
- hashes,
- filenames,
- persistence artifacts,
- external reporting references,
- contextual adversary intelligence.

#### How It Was Used
MISP Event Info searches were performed using terms such as:
- `booter`
- `coalabot`
- `rhombus`
- `mivoice`

Individual events were reviewed through:
- Tags,
- Attributes,
- ATT&CK mappings,
- Galaxy entries,
- linked intelligence references.

#### Operational Relevance
MISP functions as a centralized intelligence repository commonly used by:
- SOC teams,
- incident responders,
- CERT teams,
- threat intelligence analysts.

It supports:
- IOC sharing,
- malware correlation,
- threat hunting,
- adversary tracking,
- detection engineering,
- incident response enrichment.

---

### Attributes Table Review

#### Purpose
Review operational indicators associated with malware campaigns and attacker infrastructure.

#### Indicators Reviewed
- IP addresses
- SHA256 hashes
- malware filenames
- persistence artifacts
- webshell filenames
- external analysis references
- command-and-control infrastructure
- Suricata rules
- Sigma detections

#### Operational Relevance
Indicators reviewed within the Attributes table can be operationalized into:
- SIEM detections,
- firewall blocks,
- EDR IOC matching,
- network monitoring,
- threat hunting workflows.

---

### VirusTotal Investigation

#### Purpose
Validate malware samples and identify uploaded malware metadata.

#### How It Was Used
A VirusTotal permalink stored inside the CoalaBot event was reviewed externally to identify:
- malware filenames,
- associated hashes,
- malware detections,
- uploaded sample information.

The investigation identified:
- `cla.exe`

#### Operational Relevance
VirusTotal is commonly used during malware investigations to:
- validate suspicious files,
- correlate malware families,
- pivot across related malware samples,
- review antivirus detections.

---

### Reddit Malware Analysis Reporting

#### Purpose
Review external malware analysis reporting associated with the Rhombus Linux DDoS botnet malware.

#### How It Was Used
A Reddit technical malware analysis report linked inside the Rhombus MISP event was reviewed to identify:
- persistence behavior,
- dropped payloads,
- DDoS functionality,
- command-and-control communications,
- attacker infrastructure.

The investigation identified:
- `209.126.69.167:2020`

as callback infrastructure used by infected systems.

### Operational Relevance
Behavioral malware analysis reporting provides operational visibility into:
- malware execution flow,
- botnet behavior,
- persistence mechanisms,
- attacker communications,
- detection opportunities.

---

### Arctic Wolf Threat Reporting

#### Purpose
Review incident response intelligence associated with Lorenz ransomware and MiVoice exploitation activity.

#### How It Was Used
The linked Arctic Wolf report was reviewed to identify:
- persistence artifacts,
- malicious webshells,
- attacker operational behavior,
- SHA256 hashes,
- post-exploitation activity.

The investigation identified:
- `pdf_import_export.php`

and its associated SHA256 hash.

#### Operational Relevance
Persistence-related reporting provides defenders with:
- forensic artifacts,
- IOC matching opportunities,
- malware detection opportunities,
- incident response intelligence,
- threat hunting indicators.

---

### Vulnerability Research

#### Purpose
Investigate exploited vulnerabilities associated with MiVoice ransomware activity.

#### How It Was Used
External CVE research was performed for:
- `CVE-2022-29499`

The investigation identified vulnerable MiVoice versions including:
- `R19.2 SP3 and earlier`
- `R14.x and earlier`

### Operational Relevance
Threat intelligence investigations frequently extend into:
- vulnerability management,
- exposure analysis,
- patch prioritization,
- attack surface reduction.

---

### MISP Galaxy Intelligence

#### Purpose
Review broader contextual intelligence associated with ransomware groups.

#### How It Was Used
The Lorenz Ransomware Galaxy entry was reviewed to identify:
- historical activity,
- operational timelines,
- ransomware group context,
- first observed activity.

The investigation identified:
- `February 2021`

as the first observed operational activity timeframe.

### Operational Relevance
MISP Galaxies provide structured contextual intelligence used for:
- adversary tracking,
- ransomware profiling,
- campaign attribution,
- strategic intelligence analysis.

---

### Investigation Workflow Summary

The investigation repeatedly followed a structured workflow:

1. Search MISP for relevant malware or ransomware events  
2. Review Tags, Attributes, and ATT&CK mappings  
3. Analyze indicators and infrastructure artifacts  
4. Pivot into external intelligence references  
5. Validate malware behavior and persistence activity  
6. Correlate findings with vulnerability intelligence  
7. Review broader adversary context through Galaxy intelligence

---

### Summary

This investigation demonstrated how modern threat intelligence workflows extend beyond reviewing isolated indicators of compromise. Throughout the lab, MISP was used as a centralized intelligence platform for correlating malware infrastructure, ransomware campaigns, persistence artifacts, external malware analysis, vulnerability reporting, and contextual adversary intelligence.

The workflow reinforced how analysts continuously pivot between:
- operational indicators,
- malware analysis,
- incident response reporting,
- vulnerability research,
- infrastructure intelligence,
- adversary context

in order to improve threat visibility, support incident response operations, strengthen detection capabilities, and better understand malicious activity across enterprise environments.
