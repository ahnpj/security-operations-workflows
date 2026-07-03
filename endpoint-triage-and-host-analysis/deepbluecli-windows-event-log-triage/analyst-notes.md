# Analyst Notes — Windows Event Log Triage Using DeepBlueCLI

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the DeepBlueCLI workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding how DeepBlueCLI interprets Windows event logs, what evidence can be recovered from Security and PowerShell Operational logs, and how multiple detection categories can be correlated during a single triage session.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**
>
> * **Workflows** refer to structured log triage tasks such as password spray detection, account creation review, and download cradle identification.
> * **Examinations** refer to the hands-on review of DeepBlueCLI output and `.evtx` evidence using PowerShell.
> * **Conceptual Reviews** refer to the interpretation of Windows event log structures, detection logic, and evidence correlation.

---

### Observations During Execution

One of the biggest realizations during this workflow was how much faster event log triage becomes once a purpose-built detection tool is involved, compared to opening Event Viewer and manually filtering by Event ID.

In previous log-review exercises, most findings came from manually applying filters, sorting by timestamp, and reading through individual events one at a time.

Examples included:

* filtering Security logs by Event ID 4625 (failed logon),
* manually reviewing Event ID 4720 (account creation) entries,
* scrolling through PowerShell Operational logs looking for script block content.

DeepBlueCLI shifts the focus away from manual filtering and toward reviewing pre-summarized findings that the tool has already flagged as noteworthy.

This was an important mindset shift.

Rather than asking:

```text
Which Event IDs should I filter for?
```

DeepBlueCLI-driven triage often asks:

```text
What did the tool already flag, and does it make sense?
```

The workflow reinforced that automated triage tools don't replace analyst judgment — they just move the analyst's effort from *searching* for something suspicious to *validating* something the tool already found.

---

### Understanding What DeepBlueCLI Actually Does

Before this workflow, it wasn't entirely clear what separated a tool like DeepBlueCLI from just running `Get-WinEvent` with a filter.

The investigation helped clarify that DeepBlueCLI isn't just a generic log reader — it applies specific, pre-built detection logic tuned to known attack patterns.

Examples include:

* recognizing clusters of failed authentication attempts across many distinct accounts (password spray),
* recognizing account creation events followed by privileged group membership changes,
* recognizing PowerShell script block content referencing `Net.WebClient` download activity.

This means DeepBlueCLI is doing the equivalent of several manual, multi-step Event Viewer searches in a single command, and then presenting only the results that matched its detection logic.

The phrase:

```text
detection heuristics
```

became much easier to understand after seeing how differently each `.evtx` file's output was structured depending on what type of activity was present in that log.

---

### Understanding Why the Password Spray Log Reported a Count

One of the first things that stood out was that DeepBlueCLI didn't just say "password spray detected" — it reported a specific number of accounts targeted.

Initially it felt like a small detail, but the reasoning became clearer after thinking about **why** account count matters.

A single failed login doesn't tell you much. A handful of failed logins against one account could just be a user who forgot their password. But dozens of accounts each showing a small number of failed attempts, clustered closely together in time, is a very different signal — that pattern is what distinguishes password spraying from an ordinary failed-login event.

This explains why the "41 accounts targeted" finding was the headline result rather than a raw failed-logon count. The technique isn't really about volume against one account; it's about breadth across many accounts, so the account count is the number that actually tells the story.

---

### Understanding Password Spraying vs Traditional Brute Force

Researching the MITRE ATT&CK sub-technique page helped clarify a distinction that hadn't been fully obvious before this workflow.

Traditional brute force tends to hammer one account with many password guesses. Password spraying flips that approach — try a small number of common or likely passwords, but spread those attempts across many accounts.

The reasoning behind this technique makes a lot more sense once account lockout policies are factored in. Most environments lock an account after a handful of failed attempts. An attacker who tries many passwords against one account will likely trigger a lockout quickly. An attacker who tries one or two passwords across dozens of accounts is far less likely to trip any single account's lockout threshold, while still having a reasonable chance that at least one targeted account is using a weak or common password.

This was one of those findings where understanding the "why" behind the technique made the DeepBlueCLI output feel less like an arbitrary number and more like a meaningful behavioral signal.

---

### Understanding Account Creation and Group Assignment as a Pair

The second `.evtx` file introduced a pattern that initially seemed like two separate findings — a new account being created, and that account being added to a security group — but the workflow reinforced that these two events are usually meaningful **together**, not individually.

Account creation by itself isn't necessarily suspicious. Administrators create new accounts constantly.

Group membership changes by themselves aren't necessarily suspicious either. Adding an existing account to a new group happens all the time as part of normal access management.

What made this pairing worth flagging was the sequence: a brand-new account, immediately followed by placement into a privileged group. That specific combination is a much stronger indicator of backdoor account creation than either event would be in isolation.

This reinforced a lesson that came up repeatedly throughout the workflow:

Individual events rarely tell the full story. Relationships between events do.

---

### Understanding Why Two Download Detections Needed to Be Compared

The `powersploit-system.evtx` file was the first point in the workflow where DeepBlueCLI's output alone wasn't enough to answer the question directly.

There were two separate `Net.WebClient` download detections in the output, and the question specifically asked for the **most recent** one.

At first glance it would have been easy to just grab whichever detection appeared first in the console output. But that's not necessarily the same as which one happened most recently in time. This meant actually comparing the timestamps associated with each detection rather than assuming output order reflected chronological order.

This was a useful reminder that automated tool output should still be read carefully rather than skimmed — the tool did the hard part of finding both events, but interpreting *which* one answered the specific question asked was still up to the analyst.

---

### Understanding Net.WebClient as a Download Technique

Before this workflow, `Net.WebClient` wasn't something that stood out as particularly significant.

The investigation helped clarify why it shows up so often in offensive PowerShell tooling and living-off-the-land techniques specifically.

Rather than using a separate download utility, `Net.WebClient` lets an attacker download a file directly through PowerShell, which is already a trusted, signed Windows binary present on essentially every Windows host. This avoids introducing a separate, potentially more suspicious binary into the process tree just to retrieve a file.

This tied back into the earlier lesson from the account creation phase: the technique itself uses something completely normal (PowerShell) in a way that becomes suspicious only in context — in this case, a script block referencing a direct download from a code-hosting domain rather than typical application traffic.

---

### Understanding the Value of Bulk Export and Search

The bulk export phase was one of the more practically useful parts of the workflow.

Running DeepBlueCLI against a single `.evtx` file at a time is fine when you already know which file you need to look at. But real evidence sets are rarely that convenient — attackers' activity is often scattered across multiple logs, and an analyst doesn't always know in advance which specific file contains the artifact they're looking for.

Pointing DeepBlueCLI at the entire `evtx` folder and redirecting all of its output into a single `output.txt` file meant that a single Ctrl+F search could sweep across the combined findings from every log at once. Searching for `githubusercontent` didn't require knowing in advance which `.evtx` file the download activity lived in — the consolidated export made that distinction irrelevant for the purposes of the search.

This felt like a genuinely useful, repeatable pattern rather than just a lab exercise step: when you have a known indicator (a domain, a filename fragment, a hosting pattern), search the consolidated export first before deciding whether you need to go back and re-run more targeted analysis against a specific file.

---

### Understanding Why the Same Download Showed Up Multiple Times

Seeing three matches for `githubusercontent` in `output.txt`, all referencing the same file, was slightly confusing at first — it looked like it might indicate three separate download events.

After reviewing the matches more closely, it became clear that DeepBlueCLI was reporting different aspects of the same underlying activity across different parts of its output (for example, different detection modules referencing the same script block content, or the same event surfaced through more than one detection path within the combined export).

This was a good reminder that when a single artifact appears multiple times in an aggregated export, the first instinct shouldn't be "there were multiple events" — it's worth checking whether the tool is just reporting the same event from more than one angle.

---

### Understanding MITRE ATT&CK Software IDs

The final phase of the workflow — researching Mimikatz and finding its Software ID — highlighted a distinction that hadn't been obvious in earlier labs: MITRE ATT&CK doesn't just catalog *techniques*, it also catalogs *specific known tools* under a separate Software reference, each with its own ID (like `S0002` for Mimikatz).

This means a single investigative finding can potentially be tagged two different ways: the **technique** the activity maps to (in this case, something like OS Credential Dumping, T1003) and the **specific tool** used to carry it out (Mimikatz, S0002).

This distinction matters for reporting. Saying "this matches T1003" tells you what category of activity occurred. Saying "this matches S0002 (Mimikatz)" tells you specifically which known tool was involved, which is often far more actionable for threat intelligence correlation, since defenders can then cross-reference other known indicators, detection signatures, or campaigns historically associated with that specific tool.

---

### Challenges and Confusion Encountered

The most confusing portions of the workflow included:

* understanding why account count (rather than raw failed-logon count) was the meaningful password spray metric,
* understanding why password spraying is considered different from traditional brute-force behavior,
* recognizing that account creation and group assignment needed to be interpreted together rather than separately,
* realizing that DeepBlueCLI's two download detections required timestamp comparison rather than just reading the first result,
* understanding why `Net.WebClient` specifically is significant as a living-off-the-land download technique,
* understanding why the same download appeared to show up three times in the bulk export,
* understanding the distinction between a MITRE ATT&CK Technique ID and a MITRE ATT&CK Software ID.

These confusion points ultimately became some of the most valuable learning opportunities in the workflow.

---

### Cross-Workflow Connections

This workflow connects naturally to several other security and forensic workflows.

It connects to identity and access investigations because password spray detection and account creation review both center on authentication and account management activity.

It connects to malware investigations because the download cradle and Mimikatz findings represent the very early stage of a broader credential-theft or post-exploitation toolchain.

It connects to incident response because rapid log triage tools like DeepBlueCLI are exactly the kind of thing an analyst would reach for early in an active incident, before deeper forensic tooling gets involved.

It connects to threat hunting because bulk export and keyword search across consolidated logs is a pattern that extends naturally beyond a single lab exercise into proactive searches for known indicators across much larger log sets.

It also connects to MITRE ATT&CK-aligned detection engineering, since every finding in this workflow was ultimately tied back to a formal technique or software identifier.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

* reviewing the full contents of the downloaded `.ps1` script to understand exactly what Mimikatz functionality was being invoked,
* correlating the password spray source IP (if present in the underlying `.evtx`) with the account creation and download activity to determine whether all three logs represent a single continuous attacker session,
* reviewing additional DeepBlueCLI detection modules against other Windows Security Event IDs not covered in this lab,
* comparing DeepBlueCLI's automated findings against a manual Event Viewer review of the same `.evtx` files to reinforce understanding of the underlying Event IDs,
* researching the full MITRE ATT&CK technique associated with Mimikatz (OS Credential Dumping, T1003) in more depth,
* building a small script to automate the MITRE ATT&CK research step by querying the ATT&CK API instead of manual web search.

These additions would move the workflow beyond introductory event log triage and into more advanced DFIR and threat hunting activities.

---

### Summary

This workflow demonstrated how a single purpose-built PowerShell tool can be used to rapidly triage multiple, unrelated Windows event logs.

Password spray detection established scope and technique attribution. Account creation and group assignment detection surfaced a likely persistence mechanism. Download cradle detection revealed living-off-the-land tool acquisition. Bulk export and keyword search extended that triage across the entire evidence folder at once, and MITRE ATT&CK research formally classified the identified tool.

Together, these findings demonstrated that Windows event log triage is not about running one command and copying an answer.

It is about combining automated detection output with analyst judgment — comparing timestamps, recognizing when two events need to be interpreted together, and independently validating tool findings against authoritative references like MITRE ATT&CK — to reconstruct a coherent picture of what occurred on the host.

Perhaps the most important lesson was that DeepBlueCLI's real value isn't that it eliminates the need for analyst reasoning — it's that it points the analyst toward the right events fast enough that the reasoning can happen sooner.