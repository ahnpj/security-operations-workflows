# Tool Usage Notes — Browser History Analysis Using Browser History Viewer (BHV)

## Overview

This document summarizes the tools, workflows, and operational relevance of the utilities used during the browser artifact analysis workflow.

The workflow focused on:

- loading browser history captures,
- reviewing browser profile activity,
- analyzing website history artifacts,
- examining cached image artifacts,
- filtering browser activity,
- identifying downloaded files,
- correlating local file references with remote URLs,
- reconstructing browser-based user activity.

---

## Primary Browser Analysis Tool — Browser History Viewer (BHV)

### Purpose

Browser History Viewer (BHV) was used to analyze browser artifacts collected from multiple web browsers.

Browser artifacts can contain evidence of visited websites, page titles, visit timestamps, browser profiles, cached images, downloaded files, local file references, and web-based user activity.

### How It Was Used

Browser History Viewer was launched and configured to load a browser history capture from the provided Capture directory. The workflow primarily utilized the Website History view, Cached Images view, keyword filtering, date filtering, and browser profile information.

### Operational Relevance

Browser artifacts are often among the most valuable sources of user activity evidence because they can reveal what websites were visited, what content was viewed, what files were downloaded, and what actions occurred immediately before or after a suspicious event.

During this workflow, BHV was used to identify browser usage, social media activity, cached web content, downloaded files, and the likely delivery path of a suspicious download.

---

## Website History Analysis

### Purpose

The Website History view provides records of browser activity collected from supported web browsers.

### How It Was Used

The Website History tab was reviewed to identify browsers used on the system, social media activity, YouTube activity, download-related artifacts, and browser activity surrounding a suspicious download.

### Operational Relevance

Website history records can help investigators reconstruct user browsing behavior and establish timelines of activity.

---

## Cached Images Analysis

### Purpose

The Cached Images view provides access to image artifacts stored by the browser.

### How It Was Used

Cached image records were reviewed using filename information, timestamps, browser profile metadata, and image previews. Keyword filtering was used to locate a specific cached image artifact.

### Operational Relevance

Cached images can provide information not immediately visible within website history records and may preserve visual evidence of webpages viewed by a user.

---

## Keyword Filtering

### Purpose

Keyword filtering allows investigators to quickly reduce the number of displayed records and focus on specific activity.

### How It Was Used

Keyword filtering was used to locate YouTube activity and specific cached image artifacts.

### Operational Relevance

Filtering allows analysts to isolate activity associated with specific websites, filenames, services, and browser artifacts.

---

## Download Artifact Analysis

### Purpose

Browser history records frequently preserve evidence of downloaded files.

### How It Was Used

The Website History view was filtered for local file references to identify downloaded files. Timeline review and surrounding browser activity were then used to identify the corresponding download source.

### Operational Relevance

Download-related artifacts can help investigators connect local files to external infrastructure and reconstruct how a file reached a system.

---

## Common Mistakes to Avoid

- Assuming a visited URL proves malicious activity.
- Confusing a local file reference with a remote download URL.
- Assuming cached images only contain pictures and not forensic evidence.
- Relying solely on filenames without reviewing surrounding browser activity.

---

## Summary

Browser History Viewer was used to analyze browser artifacts collected from multiple browser profiles. Website history records, cached image artifacts, filtering capabilities, download records, and timeline analysis were used to reconstruct user activity and investigate a suspicious downloaded file.
