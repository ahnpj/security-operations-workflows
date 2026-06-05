# Browser History Analysis Using Browser History Viewer

### Overview

This execution documents the practical analysis of browser artifacts using Browser History Viewer (BHV) to reconstruct user activity and investigate a suspicious file download. The workflow focuses on three primary browser artifact sources: Website History records, Cached Image artifacts, and Download-related browser activity. This execution is designed to demonstrate how different browser artifacts answer different investigative questions. No single artifact provides a complete picture of activity. Instead, investigators correlate browser history, cached content, download records, and timeline context to reconstruct user behavior and develop a more complete understanding of browser-based activity.

The investigation begins by identifying the web browsers represented in the evidence and reviewing browsing activity recorded across multiple browser profiles. It then moves into website history analysis to identify social media activity, review user browsing behavior, and examine web-based activity recorded by the browser. Next, cached image artifacts are analyzed to recover visual content preserved by the browser cache and demonstrate how browser artifacts can reveal information not immediately visible within URL history alone. Finally, download-related browser artifacts are examined to identify a suspicious downloaded file, trace the file to its remote hosting location, and reconstruct the likely sequence of events that led to the download.

**Note:** Click the ▶ arrow to expand and view details on software, evidence sources, workflow scope, and the high-level execution map.

<details>
<summary><strong>▶ Recommended Reading Order</strong><br>
</summary><br>
  
> 👉 **Follow the execution walkthrough first**</br>
> Begin with `workflow-execution.md` to see how browser history, cached images, download artifacts, and timeline analysis were used throughout the investigation.

> 👉 **Review analytical reasoning and conceptual notes**</br>
> Move to `analyst-notes.md` to understand why browser artifacts exist, what they reveal, how they differ from one another, and how they can be correlated during an investigation.

> 👉 **Review tooling and feature usage details**</br>
> See `tool-usage-notes.md` for detailed explanations of Browser History Viewer, browser artifacts, filtering techniques, timeline reconstruction, and common analysis considerations.

> 👉 **See what each execution file contains in full detail**</br>
> Review the **Repository Structure & Supporting Documents** section below.

</details>

<details>
<summary><strong>▶ Workflow Scope & Terminology</strong><br>
</summary><br>

- **Category:** Digital Forensics and Evidence Analysis
- **Primary Operational Focus:** Browser artifact analysis, web activity reconstruction, cached content analysis, download investigation, and browser-based evidence correlation
- **Operational Objectives Demonstrated:** Browser History Analysis, Browser Profile Identification, Cached Image Analysis, Keyword Filtering, Download Artifact Analysis, Timeline Correlation, Source Attribution
- **Primary Data Sources:** Website History records, Cached Images, Browser Profiles, Download Records, Local File References, and URL Records

> **Terminology used:**
> **Workflows** refer to repeatable forensic processes used to analyze browser artifacts and reconstruct user activity.
> **Executions** refer to the hands-on analysis performed using Browser History Viewer (BHV) against collected browser artifacts.
> **Writeups** document how browser artifacts were analyzed, how evidence was interpreted, and how findings were correlated to support investigative conclusions.

</details>

---

### How to Navigate This Execution

Documentation is separated into focused components to reflect how digital forensic investigations are commonly documented.

If you want to follow the investigation step by step, start with:

**`workflow-execution.md`**

This file contains the structured walkthrough showing how browser history artifacts were reviewed, how cached images were analyzed, how downloaded files were identified, and how browser records were correlated to reconstruct user activity.

If you want to understand the reasoning behind the process, review:

**`analyst-notes.md`**

This file explains the major learning points behind browser history artifacts, cached images, browser profiles, download records, timeline correlation, and browser-based evidence reconstruction.

If you want to understand tool usage, review:

**`tool-usage-notes.md`**

This file explains how Browser History Viewer was used, why specific features were selected, and what evidence each analysis technique helped uncover.

---

### Repository Structure & Supporting Documents

| File / Folder | Purpose | Contents and Focus |
|-------------|--------|--------------------|
| `workflow-execution.md` | Structured forensic walkthrough documenting browser artifact analysis. | Documents browser identification, website history analysis, cached image review, download artifact analysis, timeline reconstruction, and source attribution. |
| `images/` | Visual evidence supporting investigative findings. | Contains screenshots of Browser History Viewer, browser profiles, website history records, cached images, filtering operations, download records, and timeline analysis. |
| `README.md` | Execution overview and operational summary. | Provides structured explanation of workflow objectives, forensic relevance, repository navigation, and intended use. |
| `analyst-notes.md` | Analyst observations, conceptual explanations, and learning notes. | Documents browser artifact behavior, investigative reasoning, evidence interpretation, and artifact correlation concepts. |
| `tool-usage-notes.md` | Technical reference for tools and artifacts used throughout the workflow. | Covers Browser History Viewer, browser artifacts, filtering capabilities, timeline analysis, operational relevance, and common mistakes. |

---

### Environment, Data Sources, and Tools

The execution focuses on browser artifact analysis using Browser History Viewer to examine web activity and reconstruct user behavior.

#### Environment and Execution Scope (At a Glance)

| Area | Details |
|--------|---------|
| **Environment Type** | Windows forensic training environment |
| **Working Directory** | Windows Investigation Two |
| **Processed Assets** | Browser history artifacts, cached images, browser profiles, download records |
| **Execution Platform** | Windows |
| **Primary Tool** | Browser History Viewer (BHV) |
| **Operational Focus** | Browser activity analysis, download investigation, and user activity reconstruction |

#### Data Sources, Evidence, and Analysis Techniques

| Area | Details |
|--------|---------|
| **Browser Artifact Source** | Browser history capture |
| **Website Activity Source** | Website History records |
| **Cached Content Source** | Cached Images records |
| **Browser Identification Technique** | Browser Profile analysis |
| **Filtering Technique** | Keyword filtering and date filtering |
| **Download Analysis Technique** | Local file reference correlation |
| **Timeline Analysis Technique** | Browser history reconstruction |
| **Suspicious File Identified** | `fThwMIR.zip` |
| **Likely Delivery Source** | `outlook.live.com` |
| **Hosting Domain Identified** | `secretbeauty.ge` |

---

### Intended Use

This execution is intended to demonstrate foundational browser forensic methodology involving browser artifact analysis and activity reconstruction.

The workflow reflects how analysts may answer questions such as:

- What browsers were used on the system?
- What websites were visited?
- Was there social media activity?
- What content was preserved within the browser cache?
- What files were downloaded?
- Where did the downloaded file originate?
- What activity occurred immediately before the download?
- How can multiple browser artifacts be correlated to reconstruct activity?

This process supports later forensic work involving user activity reconstruction, malware investigations, browser-based evidence analysis, incident response, evidence triage, and forensic reporting.

---

### Relevance to Security Operations and Digital Forensics

Modern web browsers generate a large volume of artifacts that record evidence of user activity.

Website history records can reveal visited websites, page titles, timestamps, and browsing behavior.

Cached image artifacts can preserve visual content viewed by the user and may provide evidence that is not immediately visible through URL history alone.

Download records can reveal files obtained from the internet and may help identify how suspicious content reached a system.

Together, these artifacts support:

- user activity reconstruction,
- malware investigations,
- evidence triage,
- browser forensics,
- incident response,
- forensic reporting,
- artifact correlation,
- timeline development.

This workflow demonstrates how multiple browser artifact sources can be combined to produce a more complete understanding of activity occurring on a system.

---

If you are reviewing this as part of my cybersecurity portfolio: this execution is intended to demonstrate foundational browser forensic methodology, browser artifact analysis, cached content review, download investigation, user activity reconstruction, investigative reasoning, and professional documentation aligned with entry-level DFIR, SOC, and incident response workflows.
