# Analyst Notes — Windows Artifact Analysis Using WFA, PECmd, and JumpList Explorer

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the Windows artifact analysis workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding how Windows artifacts work, what evidence they preserve, and how multiple artifact sources can be correlated during an investigation.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**
> - **Workflows** refer to structured forensic tasks such as shortcut analysis, Prefetch analysis, and Jump List analysis.
> - **Examinations** refer to the hands-on review of Windows artifacts using forensic tools.
> - **Conceptual Reviews** refer to the interpretation of Windows artifact behavior, execution evidence, user activity reconstruction, and evidence correlation.

---

### Observations During Execution

One of the biggest realizations during this workflow was that Windows continuously creates artifacts that preserve evidence of user and application activity, often without the user realizing it.

Before completing this workflow, it was easy to think of user activity only in terms of visible files, folders, and applications. However, the investigation demonstrated that Windows itself preserves many traces of activity through operating system artifacts.

The investigation never opened the actual suspicious PowerShell script. Instead, evidence was reconstructed using artifacts that Windows created automatically as normal system operations occurred.

This reinforced an important forensic concept:

Investigators are often reconstructing activity indirectly.

Rather than observing an event directly, investigators analyze traces left behind by the operating system and applications.

---

### Understanding Shortcut Files (.LNK)

The investigation began with shortcut analysis.

Initially, shortcut files were somewhat confusing because they appear to be simple links created for convenience. A desktop shortcut, for example, simply opens another file or application.

However, forensic analysis treats shortcut files differently.

A shortcut file can preserve information such as:

- file names,
- file paths,
- timestamps,
- volume information,
- historical locations,
- archive relationships.

This means a shortcut can sometimes reveal information about a file even when the original file no longer exists.

During the investigation, the shortcut artifact:

```text
PlagueRat.ps1.lnk
```

became the first lead.

The file itself was not the suspicious PowerShell script. Instead, it was a shortcut pointing to the script.

This distinction became important because the artifact was providing information about another file rather than being the file itself.

---

### Why Archive Relationships Matter

One of the more interesting findings was discovering that the suspicious file was associated with:

```text
2020_Summer_Photos.zip
```

and that another document:

```text
Invoice537-PR-06-05-2020.docx
```

was associated with the same archive.

At first this seemed like a minor detail.

However, understanding where a file originated is often just as important as understanding what the file does.

A suspicious file found in isolation provides limited context.

A suspicious file discovered inside an archive containing additional files provides more investigative value because it helps establish relationships between artifacts.

This is why investigators frequently attempt to answer questions such as:

- Where did the file come from?
- Was it delivered alongside other files?
- Was it part of a compressed archive?
- Was it disguised among legitimate-looking documents?

The shortcut artifacts helped answer some of those questions.

---

### Understanding Prefetch Files

The most confusing part of the workflow initially was understanding Prefetch files.

Windows creates Prefetch artifacts primarily to improve application startup performance.

The operating system records information about executed applications so that future launches can occur more efficiently.

From a forensic perspective, however, these artifacts provide a record of application activity.

Prefetch artifacts may reveal:

- application names,
- execution counts,
- execution timestamps,
- referenced files,
- referenced directories.

This makes them useful during investigations because they can provide evidence that an application executed on the system.

---

### Connecting Shortcut Analysis to Prefetch Analysis

One of the biggest confusion points during the workflow was understanding why the investigation moved from shortcut analysis into Prefetch analysis.

The reason became clearer after understanding what each artifact type can and cannot prove.

Shortcut artifacts helped identify:

```text
PlagueRat.ps1.lnk
```

and helped establish where the file originated.

However, shortcut artifacts do not necessarily prove execution.

A file may exist on a system without ever being executed.

To move closer to answering questions about execution-related activity, the investigation transitioned into Prefetch analysis.

This is an important investigative pattern:

```text
Shortcut Analysis
        ↓
File Existence and Origin

Prefetch Analysis
        ↓
Execution-Related Evidence
```

The second artifact type was selected because it could potentially answer questions the first artifact type could not.

---

### Understanding CMD.EXE

Another confusion point involved:

```text
CMD.EXE
```

and why it was being investigated.

Before the workflow, it was easy to think of Command Prompt as simply a black terminal window.

However, Command Prompt is actually:

```text
CMD.EXE
```

the executable responsible for processing command-line instructions.

Examples include:

```text
dir
cd
copy
move
```

and many others.

Command Prompt can also execute batch files.

Because the investigation identified:

```text
PlagueRat.bat
```

inside the CMD.EXE Prefetch artifact, it suggested that command-line activity was associated with a file using the same naming convention as the suspicious PowerShell script.

This did not necessarily prove exactly what happened, but it provided additional context.

---

### Understanding PowerShell

PowerShell was another major concept introduced during the workflow.

PowerShell scripts use the extension:

```text
.ps1
```

which immediately explains part of the suspicious filename:

```text
PlagueRat.ps1
```

PowerShell is much more powerful than traditional Command Prompt.

It can:

- automate administrative tasks,
- interact with Windows APIs,
- manipulate files,
- query system information,
- execute complex scripts.

Because of these capabilities, PowerShell is frequently encountered during:

- system administration,
- automation,
- penetration testing,
- malware execution,
- attacker post-exploitation activity.

This explains why finding:

```text
POWERSHELL.EXE
```

associated with the suspicious script was significant.

---

### Understanding NOTEPAD.EXE Findings

Another interesting finding involved:

```text
NOTEPAD.EXE
```

At first this seemed less important than PowerShell.

However, the discovery helped reinforce an important investigative principle:

Not every application associated with a file is necessarily responsible for executing it.

PowerShell may execute a script.

Notepad typically does not.

Instead, Notepad is commonly used to:

- view scripts,
- edit scripts,
- inspect file contents.

The presence of:

```text
NOTEPAD.EXE
```

suggested that the script may have been opened or reviewed as a text file.

This demonstrated how different artifacts provide different forms of context.

---

### Understanding Keyword Searching Across Prefetch Artifacts

Another major learning point involved the directory-wide Prefetch search.

Initially, the large amount of PECmd output was overwhelming.

Many lines appeared in red, making it difficult to determine which results were actually relevant.

Eventually it became clear that the goal was not to review every highlighted line.

Instead, the investigation focused specifically on references to:

```text
plaguerat.ps1
```

Once a relevant match was located, the next step was to identify which Prefetch artifact contained the reference.

This process helped identify:

```text
POWERSHELL.EXE
```

and

```text
NOTEPAD.EXE
```

as associated applications.

This reinforced another investigative lesson:

Large forensic outputs often require targeted searching rather than line-by-line review.

---

### Understanding Jump Lists

The Jump List portion of the workflow was arguably the most confusing section.

At first it seemed disconnected from the rest of the investigation.

The earlier phases focused on:

```text
PlagueRat
```

while the Jump List phase focused on:

```text
Browser Activity
```

Initially it was difficult to understand why the investigation suddenly shifted focus.

The explanation became clearer after understanding what Jump Lists actually are.

A Jump List is a Windows artifact that records resources accessed through a specific application.

Examples include:

- recently opened documents,
- recently accessed folders,
- recently visited websites.

Unlike shortcuts or Prefetch files, Jump Lists often focus more on user activity than execution activity.

---

### Why Browser Activity Was Examined

The Jump List phase introduced an important investigative concept:

Different artifact types answer different questions.

Shortcut artifacts helped answer:

```text
What files existed?
```

Prefetch artifacts helped answer:

```text
What applications interacted with those files?
```

Jump Lists helped answer:

```text
What resources did the user access?
```

The browser investigation was not necessarily proving a connection between:

```text
PlagueRat
```

and

```text
discordapp.com
```

Instead, it demonstrated another category of evidence that Windows artifacts can provide.

This distinction was important because it explained why the investigation suddenly shifted from file activity to browser activity.

---

### Understanding Evidence Correlation

One of the biggest lessons from the workflow was understanding evidence correlation.

No single artifact provided a complete picture.

Instead:

- shortcut artifacts identified suspicious files,
- Prefetch artifacts identified application interactions,
- Jump Lists identified browser activity.

Each artifact provided a different perspective.

This is similar to interviewing multiple witnesses during an investigation.

Each witness may know only part of the story.

The investigator combines all available information to produce a more complete understanding of events.

Digital forensic artifacts often work the same way.

---

### Challenges and Confusion Encountered

The most confusing portions of the workflow included:

- understanding the difference between a shortcut file and the file it references,
- understanding why Prefetch analysis followed shortcut analysis,
- understanding what CMD.EXE actually was,
- understanding why a PowerShell script appeared alongside a batch file,
- identifying relevant results within large PECmd outputs,
- understanding why Jump List analysis appeared disconnected from the earlier phases,
- understanding that browser activity was a separate investigative objective.

These confusion points became useful learning opportunities because they forced a deeper understanding of what each artifact type contributes to an investigation.

---

### Cross-Workflow Connections

This workflow connects to several other digital forensic and security operations workflows.

It connects to file system investigations because Windows artifacts ultimately exist on a file system and rely on file system structures.

It connects to malware investigations because suspicious scripts and execution evidence are frequently encountered during malware analysis.

It connects to incident response because analysts regularly use Windows artifacts to reconstruct activity after an incident occurs.

It connects to threat hunting because artifact analysis may reveal suspicious user behavior, application usage, or execution patterns.

It also connects to timeline analysis because artifacts often preserve timestamps that can later be used to build a chronological reconstruction of activity.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

- examining additional Windows artifact types,
- performing timeline reconstruction,
- correlating artifact timestamps,
- analyzing browser history databases,
- reviewing registry artifacts,
- examining event logs,
- analyzing PowerShell logging,
- comparing multiple user profiles,
- correlating artifacts across multiple systems,
- investigating persistence mechanisms.

These additions would expand the workflow from artifact identification into deeper incident response and forensic reconstruction activities.

---

### Summary

This workflow reinforced that forensic evidence often exists in multiple layers.

Shortcut artifacts helped identify suspicious files and establish origin relationships.

Prefetch artifacts provided execution-related context and identified applications associated with the suspicious script.

Jump Lists provided insight into browser-based user activity.

Together, these artifacts demonstrated how investigators use multiple independent evidence sources to reconstruct activity and develop a more complete understanding of events occurring on a Windows system.

Perhaps the most important lesson was that different artifacts answer different questions. Effective investigations require understanding what each artifact can reveal, what it cannot reveal, and how findings from multiple sources can be combined into a coherent investigative narrative.
