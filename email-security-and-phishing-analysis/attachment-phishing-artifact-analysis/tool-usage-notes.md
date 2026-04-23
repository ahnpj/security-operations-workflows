# Tool Usage Notes — Attachment-Based Phishing Artifact Analysis

This document explains how common analyst tools were used together to move from a suspicious HTML attachment to validated evidence of credential harvesting behavior. The emphasis is not on relying on one tool to “label” the file as malicious, but on understanding what each utility reveals and how those pieces of evidence fit together into a defensible phishing conclusion.

The tools used here complement each other. Windows file properties provide the first layer of file identity and context, PowerShell provides a stable file-level identifier through hashing, the browser provides the victim-facing experience of the attachment, “View Source” exposes the raw implementation of the page, CyberChef makes encoded content more readable, and Developer Tools reveal what happens when credentials are submitted. Together, these tools support a practical and repeatable workflow for validating HTML-based phishing attachments.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as phishing triage, attachment validation, and credential theft analysis.  
> - **Executions** refer to the hands-on performance of those tasks using real artifacts, analyst tools, and supporting investigation utilities.  
> - **Writeups** document how artifacts were interpreted, how validation steps were applied, and how investigative conclusions were formed.

---

### Execution Platform and Operating Environment

#### ▶ Analyst Workstation Performing Manual Attachment Analysis

**Purpose:** Provide a controlled environment for reviewing a suspicious attachment, validating what it renders as, and confirming whether it transmits captured credentials externally.  
**How It Was Used:** All analysis was performed on a local analyst workstation with access to the suspicious file, Windows file properties, PowerShell, a web browser, CyberChef, and browser Developer Tools.  
**Operational Relevance:** Reflects common SOC and IR workflows where analysts receive suspicious attachments from reported phishing cases and must validate them manually before escalating, blocking, or initiating credential containment actions.

Environment characteristics:

- **Operating System:** Desktop operating system capable of local file inspection, PowerShell execution, and browser-based analysis  
- **Analysis Mode:** Manual attachment review and behavioral validation  
- **Primary Tool Set:** Windows properties, PowerShell, browser source inspection, CyberChef, Chrome Developer Tools  
- **Interface Dependency:** Local file access plus browser-based analysis tooling  
- **User Context:** Standard analyst workstation session  

This mode ensures that the attachment can be documented, rendered, decoded, and behaviorally validated using tools that are widely available to analysts.

---

### File Identity and Safe Early Handling

Before behavioral analysis begins, the attachment should be treated as a file artifact first.

#### ▶ Windows File Properties

**Purpose:** Capture the basic identity of the suspicious attachment before deeper interaction.  
**How It Was Used:** Review the file name, extension, and file size through the properties panel before opening the attachment in a browser.  
**Operational Relevance:** File properties help the analyst quickly determine whether the artifact is likely to behave like a document, script, or rendered webpage and establish the first layer of case documentation.

In this workflow, the `.html` extension was especially important because it signaled that the attachment was not a standard document, but a locally rendered web artifact that could capture user input.

#### ▶ File Size Review

**Purpose:** Document a simple physical characteristic of the attachment that helps frame expectations about what the file contains.  
**How It Was Used:** Record the size of the HTML attachment as part of the file artifact set.  
**Operational Relevance:** Small HTML attachments are often consistent with login-themed phishing pages or lightweight credential harvesting interfaces rather than with full document content.

File size does not prove maliciousness, but it supports cleaner early documentation and helps distinguish the artifact type conceptually.

---

### File Hashing and Stable Identification

File hashing provides a safe early pivot that does not depend on visually trusting the content.

#### ▶ PowerShell

**Purpose:** Generate a stable cryptographic identifier for the suspicious file without relying only on its visible name or content.  
**How It Was Used:** Open PowerShell in the directory containing the attachment and use `Get-FileHash` to compute the SHA256 hash.  
**Operational Relevance:** File hashes support IOC documentation, case correlation, reputation lookup, and later verification that the same artifact is being discussed across tools or workflows.

Example command pattern:

```powershell
Get-FileHash <attachment-filename> -Algorithm SHA256
```

Hashing is one of the safest early pivots in attachment analysis because it identifies the file precisely without requiring the analyst to trust the rendered page or execute arbitrary logic beyond normal file reading.

---

### Rendered Page Review and Victim-Facing Analysis

Once the file is documented, the analyst must understand what the victim would actually experience.

#### ▶ Web Browser (Rendered Page Review)

**Purpose:** Show how the suspicious attachment appears when opened and determine what service or workflow it is trying to imitate.  
**How It Was Used:** Open the local HTML file in the browser and review the page layout, title, form fields, and general login experience.  
**Operational Relevance:** Many phishing attachments are designed to be believable to humans rather than sophisticated at the code level. Reviewing the rendered page helps identify impersonated brands, authentication flows, and social engineering cues.

In this workflow, the rendered page was determined to be imitating Microsoft Outlook / Office365. Even with missing assets caused by limited internet connectivity, the page still conveyed the intended trust relationship clearly enough to support the impersonation conclusion.

#### ▶ Victim Field Review

**Purpose:** Determine whether the rendered page is generic or targeted toward a specific recipient.  
**How It Was Used:** Inspect the login form for pre-filled values such as an email address.  
**Operational Relevance:** Pre-populated target data can indicate a more deliberate credential theft attempt and often increases the credibility of the page from the victim’s perspective.

The pre-filled email field in this workflow served as an important targeted-phishing indicator because it showed the page was already configured with a specific recipient value.

---

### Source Code Review and Content Decoding

Rendered page inspection is not enough by itself. Analysts also need visibility into how the page is built.

#### ▶ Browser “View Source”

**Purpose:** Expose the raw source code behind the rendered phishing page.  
**How It Was Used:** Open the page source in the browser, copy the content, and prepare it for offline analysis.  
**Operational Relevance:** Source review provides access to implementation details that are not always obvious in the rendered browser view, including encoded values, asset references, hidden fields, and suspicious request paths.

This step is especially useful when the page is visually simple but structurally more revealing once reviewed as source.

#### ▶ CyberChef

**Purpose:** Decode the copied source content so it can be reviewed more clearly and searched more efficiently.  
**How It Was Used:** Paste the copied source into CyberChef and apply the **URL Decode** operation.  
**Operational Relevance:** CyberChef helps analysts normalize or decode cluttered content so that strings, references, and hidden page elements can be inspected without manually untangling encoded data.

In this workflow, decoding made it easier to search for the term `logo` and identify the Microsoft branding resource filename used by the page. That mattered because it supported the conclusion that the page was intentionally designed to imitate Microsoft rather than merely looking like a generic login form.

CyberChef is particularly valuable here because many phishing pages use lightweight encoding or formatting tricks that are not meant to withstand deep reverse engineering, but are still annoying enough to slow down manual review if not normalized first.

---

### Credential Submission and Network Behavior Validation

The most important question in an attachment-based phishing workflow is not only what the page looks like, but what it does with entered credentials.

#### ▶ Chrome Developer Tools — Network Tab

**Purpose:** Reveal the outbound request behavior triggered when the victim interacts with the phishing page.  
**How It Was Used:** Open Developer Tools, move to the Network tab, enter test credentials, submit the form, and inspect the resulting request entries.  
**Operational Relevance:** Network inspection provides direct evidence of credential exfiltration behavior and helps distinguish a suspicious-looking page from one that actually steals data.

This step matters because credential harvesting pages often only become fully conclusive when the analyst observes where the submitted data is sent.

#### ▶ Suspicious Request Path Review (`off.php`)

**Purpose:** Identify the specific request responsible for transmitting the entered credentials.  
**How It Was Used:** Inspect the suspicious request entry generated after submission and record the request URL used to send the email and password to the web server.  
**Operational Relevance:** The request path and destination infrastructure form the strongest behavioral evidence in the workflow because they show the attachment operating as an active credential theft system rather than merely a fake interface.

The browser request review confirmed that the entered credentials were included in the request sent outward to attacker-controlled infrastructure. This is the point where the investigation moves from “strong phishing indicators” to “validated credential harvesting.”

---

### Performance and Evidence Handling Considerations

- Document the file name, extension, and size before relying on rendered content.  
- Use hashing early so the attachment has a stable identifier before deeper interaction.  
- Treat HTML attachments as active content rather than as passive files.  
- Review both the rendered page and the raw source because they answer different investigative questions.  
- Use decoding utilities to normalize cluttered or encoded source content before drawing conclusions.  
- Validate outbound request behavior directly whenever possible rather than relying on appearance alone.  
- Correlate file, page, source, and network artifacts together before making a final phishing determination.

These practices support defensible attachment analysis workflows and reduce the likelihood of over-interpreting any one artifact in isolation.

---

### Detection and Monitoring Relevance

Manual attachment analysis supports detection engineering and alert validation by helping analysts validate:

- suspicious HTML attachment detections  
- email controls that flag attachment-based credential theft attempts  
- browser or proxy telemetry involving suspicious form submission destinations  
- Microsoft brand impersonation themes in phishing cases  
- indicators used for downstream credential containment or user-notification decisions  

Manual review often serves as the ground-truth reference when determining whether a suspicious attachment is merely unusual or actively malicious.

---

### Summary of Tools, Platforms, and Data Sources

- **Platform:** Analyst workstation  
- **Primary Artifact:** Suspicious HTML attachment  
- **Data Sources:** File properties, file hash output, rendered browser page, source code, decoded content, and browser request data  
- **Analysis Mode:** Manual attachment inspection and behavior-driven validation  
- **Supporting Tools:** Windows properties, PowerShell, browser source inspection, CyberChef, Chrome Developer Tools  
- **Execution Method:** File review, hash generation, rendered page inspection, source decoding, and network request validation  
- **Analysis Method:** Attachment identification, impersonation analysis, targeted victim review, decoding, and behavioral confirmation of credential exfiltration
