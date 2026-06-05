# Analyst Notes — Recycle Bin Analysis Using RBCmd and CSVQuickViewer

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the Recycle Bin analysis workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding how Windows Recycle Bin artifacts work, what deleted-file metadata reveals, and how multiple metadata fields can be correlated during an investigation.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**
>
> * **Workflows** refer to structured forensic tasks such as Recycle Bin analysis, deleted-file review, and user attribution.
> * **Examinations** refer to the hands-on review of Recycle Bin artifacts using forensic tools.
> * **Conceptual Reviews** refer to the interpretation of deleted-file metadata, user ownership, SID attribution, and evidence correlation.

---

### Observations During Execution

One of the biggest realizations during this workflow was that deleting a file does not necessarily remove all evidence of that file.

Before completing this investigation, it was easy to think of deletion as a simple action:

```text
Delete File
      ↓
File Disappears
```

However, the Recycle Bin demonstrated that Windows may preserve useful metadata even after a file is deleted.

The deleted files themselves were not the primary focus of this investigation.

Instead, the investigation focused on metadata about the deleted files.

This reinforced an important forensic concept:

Investigators are often analyzing traces left behind by user actions rather than the user actions themselves.

The deleted file may no longer be visible in its original location, but metadata can continue to provide evidence about what happened.

---

### Understanding the Recycle Bin

Before this workflow, the Recycle Bin seemed relatively simple.

Most users understand it as:

```text
Trash Can
```

for deleted files.

However, forensic analysis treats the Recycle Bin differently.

The Recycle Bin is not simply a storage location for deleted files.

It is also a source of metadata that can reveal:

* original filenames,
* original file paths,
* file sizes,
* deletion timestamps,
* user-specific ownership information.

This means that even if a file is deleted from its original location, evidence about that file may still exist.

This immediately increases the investigative value of the Recycle Bin.

---

### Understanding Why the Investigation Started in C:$Recycle.Bin

One of the first questions that came up was:

```text
Why start inside C:\$Recycle.Bin?
```

The answer became clearer after understanding where the evidence actually resides.

The Recycle Bin artifacts are stored in:

```text
C:\$Recycle.Bin
```

rather than inside user profile folders.

This means the investigation must begin where the artifacts exist.

Before any metadata can be reviewed, the artifact source itself must be identified.

This follows a common forensic pattern:

```text
Identify Artifact Source
          ↓
Parse Artifact
          ↓
Review Results
          ↓
Interpret Evidence
```

The Recycle Bin directory represented the starting point for that process.

---

### Understanding SID Folders

The SID folders were initially confusing.

Instead of seeing:

```text
Rick.Sanchez
Simon.Leeves
Sarah.Jones
```

the Recycle Bin contained folders named:

```text
S-1-5-21-...
```

At first these values looked random.

However, they are actually Security Identifiers, commonly called SIDs.

A SID is the unique identifier Windows assigns to a user account.

Windows internally tracks users using SIDs rather than usernames.

This explained why the Recycle Bin is organized around SID folders.

The operating system cares about the SID.

Humans care about the username.

Understanding this distinction helped explain later phases involving user attribution.

---

### Understanding RBCmd

One of the biggest lessons from the workflow was understanding why a parsing tool was needed.

Initially, it seemed possible that the Recycle Bin files could simply be opened and reviewed manually.

However, the metadata is not stored in a human-friendly format.

RBCmd exists because it translates Recycle Bin metadata into structured records.

Instead of manually interpreting raw artifact data, RBCmd provides fields such as:

```text
FileName
FileSize
DeletedOn
```

This dramatically simplifies analysis.

The workflow reinforced a common forensic reality:

Many artifacts are not useful until they are parsed.

The parser is often what converts raw data into actionable evidence.

---

### Understanding CSV Output

At first, generating CSV output seemed like a convenience feature.

However, it became clear that structured output is critical for investigations.

Once the Recycle Bin metadata was converted into a table, it became possible to:

* sort records,
* group records,
* count records,
* search records,
* compare records.

Without structured output, answering even simple questions would have been significantly more difficult.

The CSV became the primary evidence source throughout the investigation.

---

### Understanding Why FileSize Matters

Initially, file size appeared to be one of the less important fields.

However, the first investigation question focused entirely on:

```text
FileSize
```

This demonstrated that metadata itself can become the investigative pivot.

Instead of asking:

```text
What file am I looking for?
```

the investigation asked:

```text
What file matches this size?
```

This reinforced an important lesson:

Investigators do not always begin with filenames.

Sometimes they begin with metadata and work backwards.

---

### Understanding Why Original File Paths Matter

Another important realization involved the:

```text
FileName
```

column.

Initially it seemed to contain only filenames.

However, it actually preserved original file paths.

For example:

```text
C:\Users\Simon.Leeves\Downloads\
```

immediately revealed:

* the user account,
* the original location,
* possible context surrounding the file.

The path itself became evidence.

This was especially important when evaluating the allegation involving confidential files.

The Downloads folder helped support the theory that the files were downloaded by the user.

---

### Understanding the Confidential File Allegation

One of the more interesting parts of the workflow involved:

```text
IT Passwords.docx
```

and:

```text
CI Financials 2021.xlsx
```

At first it was tempting to conclude:

```text
These files prove wrongdoing.
```

However, the investigation highlighted an important limitation.

The filenames suggested sensitive information.

They did not prove the actual contents.

This distinction is important in digital forensics.

Evidence should support conclusions, but conclusions should not exceed what the evidence actually proves.

The filenames supported the allegation.

They did not independently prove it.

---

### Understanding Deletion Timestamps

Another useful lesson involved:

```text
DeletedOn
```

timestamps.

At first it seemed obvious that a deletion timestamp tells us exactly what happened.

However, timestamps answer a very specific question:

```text
When did the file enter the Recycle Bin?
```

That is not necessarily the same thing as:

```text
When was the file created?
When was it modified?
When was it first accessed?
```

Understanding what a timestamp actually represents is critical.

A timestamp is only useful if the investigator understands what event generated it.

---

### Understanding Why User Attribution Matters

Several phases focused on identifying which user account was associated with a file.

Initially this seemed repetitive.

However, attribution is one of the most important objectives in forensic investigations.

Investigators frequently need to answer:

```text
Who was associated with this activity?
```

The workflow demonstrated several ways that attribution can occur:

* user profile paths,
* SID folders,
* deleted-file metadata,
* original file locations.

Each source contributed additional confidence to the attribution process.

---

### Understanding Why Rick Sanchez's SID Was Relevant

The final question involving Rick Sanchez's SID initially felt disconnected from the rest of the investigation.

Most earlier phases focused on filenames and deleted files.

The SID question focused on a Windows account identifier.

However, this phase helped connect the user-facing world and the operating-system world.

Humans see:

```text
Rick.Sanchez
```

Windows sees:

```text
S-1-5-21-...
```

Understanding that relationship helps investigators correlate artifact locations back to real user accounts.

---

### Understanding Evidence Correlation

Perhaps the biggest lesson from the workflow involved evidence correlation.

No single field provided the entire story.

Instead:

* FileName identified original locations,
* FileSize identified specific files,
* DeletedOn identified deletion activity,
* SID folders identified ownership,
* record counts identified scope.

Each field answered a different question.

When combined, they provided a much stronger understanding of deleted-file activity than any single field alone.

This reinforced an important forensic principle:

Evidence becomes more valuable when multiple independent artifacts support the same conclusion.

---

### Challenges and Confusion Encountered

The most confusing portions of the workflow included:

* understanding why the Recycle Bin uses SID folders instead of usernames,
* understanding what RBCmd was actually parsing,
* understanding why CSV output was necessary,
* understanding how file size could be used as an investigative pivot,
* understanding what deletion timestamps actually represent,
* understanding the difference between evidence that supports a theory and evidence that proves a theory,
* understanding how usernames and SID values relate to each other.

These confusion points ultimately became useful learning opportunities because they encouraged deeper understanding of Windows artifact behavior.

---

### Cross-Workflow Connections

This workflow connects to several other digital forensic workflows.

It connects to file system investigations because deleted files originate from the file system.

It connects to timeline analysis because deletion timestamps can contribute to event reconstruction.

It connects to user activity reconstruction because deleted files often reveal user behavior.

It connects to incident response because investigators frequently review deleted-file activity during investigations.

It connects to insider threat investigations because deleted files may indicate attempted cleanup or removal of sensitive information.

It also connects to artifact correlation because Recycle Bin findings are often combined with browser artifacts, Prefetch artifacts, registry artifacts, and event logs.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

* recovering deleted file contents,
* examining Recycle Bin artifact internals,
* correlating deletion timestamps with other artifact sources,
* building a timeline of deleted-file activity,
* analyzing additional Windows user accounts,
* comparing deleted-file activity across systems,
* integrating registry artifact review,
* integrating event log review,
* correlating Recycle Bin activity with browser downloads,
* performing full user activity reconstruction.

These additions would expand the workflow from deleted-file analysis into broader forensic reconstruction and incident response investigations.

---

### Summary

This workflow reinforced that deleting a file does not necessarily eliminate forensic evidence.

Recycle Bin artifacts preserved metadata about deleted files, including original locations, deletion timestamps, file sizes, and user ownership information.

RBCmd transformed raw Recycle Bin metadata into structured records that could be analyzed efficiently.

CSVQuickViewer made it possible to sort, group, search, and correlate those records.

Together, these artifacts demonstrated how investigators reconstruct deleted-file activity and associate deleted content with specific users.

Perhaps the most important lesson was that metadata itself is evidence. Original file paths, timestamps, file sizes, and SID information may reveal just as much about user activity as the deleted files themselves.
