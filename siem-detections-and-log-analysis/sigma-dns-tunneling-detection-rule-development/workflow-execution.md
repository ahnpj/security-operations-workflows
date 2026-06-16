# Detection Engineering and Sigma Rule Development for DNS Tunneling Activity

### Overview

This workflow documents practical Sigma rule analysis and modification using threat intelligence to improve detection coverage for DNS-based command-and-control activity.

The workflow focuses on reviewing an existing Sigma rule, understanding its detection logic, identifying detection gaps through intelligence analysis, modifying detection criteria, and validating that the final detection aligns with observed adversary behavior.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**
> - **Workflows** refer to operational detection engineering tasks such as rule development and validation.
> - **Executions** refer to hands-on modification and review of Sigma detection content.
> - **Writeups** document detection decisions, validation activities, and operational reasoning.

> 👉 For a **detailed, step-by-step walkthrough of how this workflow was executed — complete with screenshots**, see the **Step-by-Step Execution** section below.

---

### Purpose and Analyst Focus

#### ▶ Purpose

The purpose of this workflow is to perform threat-informed Sigma rule development by modifying an existing DNS tunneling detection rule using intelligence associated with the fictional TRANSPORTER malware family.

#### ▶ Analyst Focus

The analyst focus is on reviewing detection content, identifying detection gaps, incorporating threat intelligence into detection logic, validating detection thresholds, and ensuring that detection content remains operationally useful within a SIEM environment.

---

### Environment and Execution Context

<details>
<summary><strong>▶ Environment & Platform</strong><br></summary><br>

The workflow was performed using the Sigma rule repository and a text editor to review and modify YAML-based detection content.

</details>

<details>
<summary><strong>▶ Tooling and Constraints</strong><br></summary><br>

- Sigma Rules Repository
- YAML Detection Rules
- Sublime Text
- Threat Intelligence Briefing

</details>

<details>
<summary><strong>▶ Data Sources Analyzed</strong><br></summary><br>

- Existing DNS tunneling Sigma rule
- Threat intelligence briefing
- DNS telemetry requirements
- MITRE ATT&CK mappings

</details>

<details>
<summary><strong>▶ Workflow Map (High-Level)</strong><br></summary><br>

1. Download Sigma rule repository.
2. Review existing DNS tunneling detection rule.
3. Analyze threat intelligence requirements.
4. Identify detection gaps.
5. Modify Sigma rule metadata and detection logic.
6. Validate thresholds and indicators.
7. Review ATT&CK mappings and false positive considerations.
8. Finalize updated detection rule.

</details>

---

### Step-by-Step Execution

This section documents the workflow in the same order an analyst would realistically perform detection engineering activities when updating a Sigma rule using new threat intelligence.

**Note:** Each section is collapsible. Click the ▶ arrow to expand and view the detailed steps.

<details>
<summary><strong>▶ Phase 1 — Scenario Orientation and Sigma Repository Review</strong><br>
→ establishing the working dataset and identifying an existing DNS tunneling detection
</summary><br>

**Goal:** Locate a Sigma rule related to DNS tunneling activity that can be modified using threat intelligence.

I began by downloading the Sigma master ZIP archive and extracting the contents locally. After reviewing the available rule categories, I navigated through the repository to locate DNS-related detections.

To keep the activity organized, I created a dedicated project folder named `peter-ahn-sigma-rule-dns-tunneling` and extracted the Sigma repository into that location. This allowed me to keep screenshots, notes, modified rules, and supporting files together in a single workspace.

While reviewing the repository structure, I observed that Sigma rules are organized by detection category rather than by SIEM vendor. This reinforces one of Sigma's primary goals: allowing analysts to focus on detection logic without being tied to a specific platform.

The objective was not to immediately modify a rule, but to first identify an existing detection that already focused on DNS command-and-control behavior.

<p align="center">
<img src="images/sigma-rule-development-figure01.png?raw=true" width="700"><br>
<em>Figure 1: Downloading and reviewing the Sigma rule repository.</em>
</p>

I observed that the Sigma rule referenced in the original training material (net_dns_c2_detection.yml) was not present within the current version of the Sigma repository downloaded during execution.

The training material referenced a rule located under an unsupported directory, which historically contained community-contributed, experimental, deprecated, or less-maintained detection content. Over time, the Sigma project has undergone significant restructuring, with rules being renamed, relocated, merged, improved, or removed as the project evolved.

As a result, some rule paths and filenames shown in older training material may no longer exist in current Sigma releases. This appears to be a repository version difference rather than an issue with the workflow itself.

In operational environments, detection content is regularly updated to reflect new threats, improved detection methodologies, and changes in project standards. Because of this, analysts frequently encounter discrepancies between historical documentation and current detection repositories.

For the purposes of this workflow, the focus remained on understanding Sigma rule structure, reviewing DNS-focused detection content, identifying detection gaps through threat intelligence, and modifying detection logic using intelligence-derived indicators. The specific filename used is less important than the underlying detection engineering concepts being demonstrated.

After reviewing the available folders, I located the file:

```text
rules/unsupported/network/net_dns_c2_detection.yml
```

<p align="center">
<img src="images/sigma-rule-development-figure02.png?raw=true" width="700"><br>
<em>Figure 2: Locating the appropriate .yml file.</em>
</p>

This rule was selected because it already targeted DNS tunneling behavior.

<blockquote>
Before modifying any detection content, I wanted to understand what the existing rule was designed to detect and whether it could be adapted rather than creating a completely new rule.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 2 — Reviewing Existing Detection Logic</strong><br>
→ understanding how the baseline rule functions before making changes
</summary><br>

**Goal:** Understand how the original detection identifies suspicious DNS activity.

I opened the rule and reviewed its major sections, including metadata, log source requirements, detection logic, false positives, severity level, and ATT&CK mappings.

Before modifying the rule, I wanted to understand how the existing detection worked and what information each section contained. I reviewed the metadata, log source requirements, detection logic, false positive considerations, and ATT&CK mappings to understand both the purpose of the rule and the telemetry required for it to function.

The `logsource` section was particularly important because it defines which data source is required for detection. In this case, the rule relies on DNS logs, meaning organizations that do not collect DNS telemetry would be unable to use this detection regardless of how effective the logic is.

The rule required DNS logs and monitored parent domains for unusually high query volumes.

<blockquote>
The Sigma rule was opened and reviewed using Notepad to examine its YAML structure, metadata, log source requirements, and detection logic before any modifications were made. Since Sigma rules are stored as plain-text YAML files, a simple text editor was sufficient for reviewing and editing the detection content.
</blockquote>

<p align="center">
<img src="images/sigma-rule-development-figure03.png?raw=true" width="700"><br>
<em>Figure 3: Reviewing the original Sigma rule structure and detection logic.</em>
</p>

The original detection logic counted DNS requests and generated an alert when activity exceeded 1000 requests.

**Analyst validation check:**

**Prompt:** What behavior is the rule attempting to identify?

**Conclusion:** Potential DNS tunneling activity through excessive DNS requests to a domain.

<blockquote>
Reviewing the original rule helped establish a baseline understanding before making any modifications. This reduced the risk of changing detection logic without understanding its original purpose.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 3 — Intelligence Analysis and Detection Requirement Identification</strong><br>
→ extracting actionable indicators from the threat intelligence briefing
</summary><br>

**Goal:** Determine which elements of the intelligence briefing should be incorporated into the detection.

The intelligence briefing described a malware family called TRANSPORTER that used DNS tunneling for command-and-control communications.

The briefing identified:

| Intelligence Element | Value |
|---|---|
| Malware Family | TRANSPORTER |
| Domain | redhunt.net |
| Average DNS Requests | 500 |
| ATT&CK Technique | T1071.004 |

After reviewing the intelligence briefing, I compared the reported malware behavior against the existing detection logic. The briefing indicated that TRANSPORTER generated approximately 500 DNS requests on average, while the existing rule only alerted when activity exceeded 1000 requests.

This comparison immediately highlighted a potential detection gap. Although the original rule was designed to detect DNS tunneling activity, the threshold was high enough that it could potentially miss the specific malware behavior described in the intelligence report.

During review, I noticed the original detection threshold of 1000 requests exceeded the observed average activity described in the briefing.

This created a potential detection gap.

**Analyst validation check:**
**Prompt:** What changes are likely required?
**Conclusion:** Monitor the known malicious domain and reduce the detection threshold below the observed average request volume.

<blockquote>
This phase reinforced how threat intelligence becomes useful only when it can be translated into practical detection requirements.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 4 — Modifying Detection Logic</strong><br>
→ converting intelligence into actionable Sigma rule criteria
</summary><br>

**Goal:** Update the existing rule to detect known TRANSPORTER activity.

I modified the Sigma rule to better align with the intelligence briefing.

Rather than creating a completely new rule, I chose to modify the existing detection because it already monitored DNS tunneling behavior. This approach allowed me to retain the original detection objective while incorporating threat-specific indicators identified during intelligence analysis.

My goal was to transform a generic behavioral detection into a more focused, threat-informed detection capable of identifying known TRANSPORTER infrastructure.

The rule was converted from a generic DNS tunneling detection that monitored all domains into a threat-informed detection that specifically monitored known TRANSPORTER infrastructure (redhunt.net) while lowering the detection threshold from 1000 to 400 requests to improve visibility into the malware's observed behavior.

<p align="center">
<img src="images/sigma-rule-development-figure04.png?raw=true" width="700"><br>
<em>Figure 4: Modifying Sigma detection logic using threat intelligence indicators.</em>
</p>

The original Sigma rule was designed as a generic DNS tunneling detection that monitored all parent domains and generated an alert when DNS query activity exceeded 1000 requests. To align the rule with the TRANSPORTER intelligence briefing, I modified the detection to focus on a known malicious domain (redhunt.net) rather than all domains, transforming it from a broad behavioral detection into a threat-informed detection. I updated the title and description to reflect the malware family and command-and-control activity being monitored, added ATT&CK mappings relevant to DNS-based communications and data exfiltration, retained the original author while documenting my modifications, and updated the modification date to reflect the changes. The detection threshold was reduced from 1000 to 400 requests because the intelligence briefing reported that TRANSPORTER generated an average of approximately 500 DNS requests, meaning the original threshold could potentially miss the activity being described. Finally, the false positive section was updated to reflect the much narrower detection scope now that the rule specifically monitors known malicious infrastructure rather than all DNS traffic.

<blockquote>
The threshold was intentionally set below the observed average of 500 requests in order to improve the likelihood of detecting infections that generate slightly lower volumes of traffic.
</blockquote>

<blockquote>
Detection thresholds should reflect observed adversary behavior whenever possible rather than relying on arbitrary values.
</blockquote>

</details>

<details>
<summary><strong>▶ Phase 5 — Detection Validation and Final Review</strong><br>
→ validating that the updated rule aligns with intelligence requirements
</summary><br>

**Goal:** Confirm that the completed rule accurately reflects the threat intelligence.

Before considering the rule complete, I performed a final review of both the metadata and detection logic. The objective was to ensure that all intelligence-derived indicators had been incorporated correctly and that the modified rule still functioned as intended.

This validation process is important because even small changes to detection logic can unintentionally reduce coverage or create inconsistencies between the rule description and its actual behavior.

The completed rule was reviewed to verify:

- DNS logs remain the required data source.
- The malicious domain matches intelligence reporting.
- Thresholds align with observed activity.
- ATT&CK mappings remain accurate.
- Rule descriptions accurately describe detection intent.

The final rule provided targeted coverage for known TRANSPORTER infrastructure while maintaining the original DNS tunneling detection objective.

<blockquote>
Rule validation is a critical step because a technically correct rule may still fail to address the threat it was intended to detect.
</blockquote>

</details>

---

### Results & Interpretation

The workflow resulted in a modified Sigma rule capable of identifying DNS tunneling activity associated with known TRANSPORTER infrastructure.

The updated detection incorporated intelligence-driven indicators and improved visibility compared to the original generic DNS tunneling rule.

---

### Operational & Defensive Takeaways

- Threat intelligence should be translated into actionable detection content whenever possible.
- Detection thresholds should be based on observed adversary behavior.
- Sigma provides a vendor-neutral approach to detection engineering.
- Detection validation is as important as detection creation.
- Threat-specific detections can complement broader behavioral detections.

---

### What I Learned (Skills Demonstrated)

This workflow strengthened my understanding of Sigma rule structure, threat-informed detection engineering, and the relationship between threat intelligence and security monitoring. It demonstrated how existing detections can be adapted using intelligence-derived indicators to improve detection coverage and operational relevance.

---

### Reflection

This workflow provided practical experience reviewing and modifying Sigma detection content. Rather than creating a rule from scratch, I learned how existing detections can be tuned using new intelligence and how detection engineering involves balancing detection coverage, operational requirements, and observed adversary behavior.
