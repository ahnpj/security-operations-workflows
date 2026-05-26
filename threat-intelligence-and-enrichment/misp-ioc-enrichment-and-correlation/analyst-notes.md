# Analyst Notes — MISP-Based Threat Intelligence Investigation and Malware Analysis

This file captures informal observations, investigative reasoning, troubleshooting notes, and operational takeaways recorded during execution of the MISP-based threat intelligence investigation lab.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but were useful for understanding how investigative reasoning, intelligence pivoting, and threat analysis workflows developed throughout the exercise.

> **Workflow vs Investigation vs Intelligence Review (Terminology Used Here)**  
> - **Workflows** refer to structured threat intelligence and malware investigation processes performed throughout the lab.  
> - **Investigations** refer to active analysis of malware campaigns, ransomware activity, infrastructure indicators, vulnerabilities, and external reporting.  
> - **Intelligence Reviews** refer to interpretation of operational indicators, contextual adversary intelligence, and external technical reporting.

---

### Observations During Execution

One of the biggest realizations during this lab was that MISP itself is not acting like a SIEM or live telemetry monitoring platform. Initially, I expected the platform to behave more like Splunk or another monitoring solution where alerts and logs were generated dynamically. Instead, MISP functioned more like a centralized intelligence repository that aggregates malware indicators, external reporting, infrastructure intelligence, threat actor context, and operational research into structured intelligence events.

At first, this was somewhat confusing because many of the investigation steps involved pivoting away from MISP itself into external sources such as:
- VirusTotal,
- Reddit technical malware reporting,
- Arctic Wolf incident response reporting,
- public CVE advisories,
- vulnerability research.

Over time, it became clearer that this mirrors real-world threat intelligence workflows where analysts continuously pivot between multiple intelligence sources rather than relying entirely on a single platform.

The repeated process of:
1. locating an event,
2. reviewing Tags and Attributes,
3. identifying external references,
4. pivoting into external research,
5. validating infrastructure or malware behavior,

helped reinforce how operational threat intelligence investigations are often driven by investigative pivoting rather than isolated indicator review.

---

### Understanding MISP During the Investigation

A major learning point throughout the exercise was understanding the distinction between:
- operational indicators,
- contextual intelligence,
- external research references.

Initially, many of the Attributes inside events seemed disconnected or difficult to interpret because they included:
- hashes,
- filenames,
- Suricata rules,
- Sigma detections,
- URLs,
- IP addresses,
- ATT&CK mappings,
- Galaxy references.

However, after working through several investigations, the structure became much clearer.

The Attributes table primarily represented operational intelligence artifacts that defenders could directly operationalize into:
- SIEM detections,
- IOC matching,
- firewall blocks,
- EDR alerts,
- threat hunting queries.

Meanwhile, the Galaxies section represented broader contextual intelligence about:
- ransomware groups,
- malware families,
- adversary operations,
- attack campaigns,
- operational timelines.

This distinction helped reinforce how MISP combines tactical indicators with higher-level strategic threat intelligence.

---

### Challenges and Confusion Encountered

One of the most difficult parts of the lab was understanding why certain investigation pivots were required.

Several times during execution, the actual answers were not directly visible inside MISP itself. Instead, the workflow required:
- identifying an external link,
- opening the linked reporting source,
- understanding the technical reporting,
- extracting the operationally relevant artifact.

For example:
- the CoalaBot investigation required pivoting into VirusTotal,
- the Rhombus investigation required reviewing a Reddit malware analysis report,
- the MiVoice investigation required external CVE research,
- the Lorenz ransomware persistence investigation required Arctic Wolf reporting.

Initially, this felt confusing because it seemed like the answers were “outside” the platform. Eventually, it became clearer that MISP was intentionally functioning as the intelligence pivoting platform rather than the sole source of information.

Another challenge involved understanding the purpose of different indicator types inside the Attributes table. Early on, indicators such as:
- `ip-dst`,
- `filename`,
- `link`,
- `yara`,
- `sha256`

appeared somewhat abstract until the relationship between indicators and operational detection workflows became more obvious.

---

### Investigative Reasoning and Pivots

The investigation process repeatedly reinforced how analysts pivot between technical indicators and contextual intelligence during real-world threat investigations.

For example, during the DDoS booter investigation, reviewing multiple `ip-dst` indicators helped demonstrate how malicious infrastructure associated with botnet operations can be tracked and shared operationally.

During the Rhombus malware investigation, the Reddit analysis report provided significantly more value than the original event itself because it explained:
- malware installation behavior,
- persistence mechanisms,
- dropped payload activity,
- command-and-control communications,
- DDoS functionality.

This reinforced how external technical reporting often provides the behavioral context necessary to understand how malware actually operates after compromise.

Similarly, the Lorenz ransomware investigation demonstrated how threat intelligence workflows frequently intersect with:
- vulnerability management,
- exploit intelligence,
- incident response,
- persistence analysis,
- ransomware profiling.

The investigation gradually shifted from simple IOC review into broader operational understanding of:
- attacker objectives,
- persistence techniques,
- exploitation behavior,
- ransomware operations,
- infrastructure usage.

---

### ATT&CK Matrix and Threat Mapping Observations

Reviewing ATT&CK mappings inside the Turla-related event helped clarify how ATT&CK techniques are used operationally within intelligence reporting.

Before this investigation, ATT&CK mappings mostly felt theoretical. During the lab, it became clearer that ATT&CK mappings help analysts:
- visualize adversary behavior,
- identify intrusion phases,
- understand attacker objectives,
- correlate observed activity to known tactics and techniques.

The ATT&CK Matrix also helped reinforce how intelligence reporting frequently maps malware campaigns and adversary activity across the full intrusion lifecycle rather than focusing only on malware execution itself.

---

### Persistence and Detection Thinking

The persistence-focused portions of the investigation were some of the most operationally valuable parts of the lab.

Reviewing:
- malicious webshells,
- persistence filenames,
- SHA256 hashes,
- C2 infrastructure,
- cron persistence mechanisms,

helped reinforce how defenders identify and track post-compromise activity.

The Arctic Wolf reporting especially demonstrated how incident response reporting often exposes:
- attacker persistence methods,
- post-exploitation tooling,
- operational tradecraft,
- forensic artifacts,
- threat hunting opportunities.

This reinforced the importance of focusing not only on malware delivery, but also on how attackers maintain access after initial compromise.

---

### Limitations of the Lab Environment

The investigation environment was intentionally intelligence-focused rather than telemetry-focused.

No live:
- packet captures,
- endpoint telemetry,
- SIEM dashboards,
- EDR consoles,
- live alerts,
- forensic acquisitions

were present during the workflow.

Instead, the exercise focused on:
- intelligence correlation,
- malware analysis pivoting,
- infrastructure analysis,
- vulnerability research,
- adversary context analysis.

This reinforced that threat intelligence analysis often occurs independently from active incident response operations and may rely heavily on previously collected reporting and shared intelligence artifacts.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded significantly through:
- SIEM correlation using identified indicators,
- Suricata or Sigma rule deployment,
- IOC enrichment automation,
- malware sandbox analysis,
- automated VirusTotal lookups,
- ATT&CK-based threat hunting queries,
- integration with Splunk or Elastic,
- automated MISP-to-SIEM ingestion workflows.

Additional improvements could also include:
- packet analysis,
- PCAP review,
- malware reverse engineering,
- persistence validation,
- endpoint artifact collection,
- detection engineering validation.

These additions would bridge the gap between intelligence analysis and active detection engineering workflows.

---

### Cross-Workflow Connections

Several concepts from this investigation directly connect to other workflows within the portfolio, including:
- phishing investigations,
- IOC enrichment workflows,
- SIEM detection analysis,
- malware analysis,
- endpoint triage,
- threat hunting,
- vulnerability management,
- incident response reporting.

The workflow especially reinforced how threat intelligence acts as a connective layer between:
- malware analysis,
- vulnerability research,
- detection engineering,
- incident response,
- infrastructure analysis,
- adversary tracking.

This investigation serves as a foundational threat intelligence workflow that supports broader SOC, DFIR, and detection engineering operations throughout the portfolio.
