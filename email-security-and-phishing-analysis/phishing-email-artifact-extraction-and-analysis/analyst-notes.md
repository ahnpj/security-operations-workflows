# Analyst Notes — Manual Phishing Email Artifact Extraction and Triage

This file captures investigative reasoning, interpretation decisions, and workflow-specific observations recorded during manual phishing email analysis using Outlook, raw message review, and supporting enrichment tools.

These notes focus on *how conclusions were formed* from visible and raw email evidence and how the chosen tools supported investigative pivots beyond simple surface-level message review.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as phishing triage, sender validation, and artifact extraction.  
> - **Executions** refer to hands-on performance of those tasks using real email messages, local analysis tools, and enrichment utilities.  
> - **Writeups** document extraction decisions, validation steps, and investigative conclusions.

---

### Observations During Execution

Starting with the visible Outlook message reinforced that phishing analysis should begin with what the user actually saw, not with raw headers alone. The display name, subject, message theme, and visible sender address all contribute to the social engineering angle, even if the raw data later reveals a different technical reality.

Opening the downloaded message in a text editor made it much easier to separate the visible sender from the reply behavior and underlying sending context. Seeing raw fields directly helped reinforce that the sender address, reply-to address, return-path-related values, and the originating sending server do not all represent the same thing.

Reviewing suspicious URLs alongside their root domains proved especially helpful. Full URLs showed exactly where the user would have been sent, while the root domain made it easier to assess whether the destination aligned with the brand or service the email was impersonating.

WHOIS was useful as a contextual enrichment step rather than as a standalone answer. It helped add background around suspicious domains and sending infrastructure, but it only became meaningful when compared against the email’s claimed identity and other extracted artifacts.

PhishTool was most useful after manual extraction had already been performed. Using it at the end of the workflow made it easier to validate my interpretation of fields such as reply-to, originating IP, sender domain, and SPF-related results rather than relying on the platform to do all the interpretation for me.

The attachment hashing step reinforced the value of using stable identifiers first. Hashing the file before deeper handling created a repeatable pivot for VirusTotal, IOC documentation, and later verification without requiring risky interaction with the file itself.

---

### Challenges and Troubleshooting

One of the main challenges was making sure that sender-related fields were not blended together conceptually. It is easy to confuse the visible sender, reply-to, return-path-related data, and the actual sending server when first learning email analysis. Keeping those concepts distinct was necessary to avoid drawing incorrect conclusions from raw headers or PhishTool panels.

Another challenge was resisting the urge to treat authentication results as proof of legitimacy. SPF results can look reassuring at first glance, but a phishing email can still pass SPF if the attacker controls the sending domain or has authorized a third-party delivery service to send on its behalf.

Extracting URLs required careful attention because the full URL and the root domain serve different investigative purposes. Focusing only on the root domain would have missed suspicious paths or parameters, while focusing only on the full URL would have made it harder to compare destinations across multiple messages or cases.

Attachment analysis also required caution. The goal was not to interact with the file casually, but to preserve it as an artifact, compute its hash safely, and use that hash as the primary enrichment pivot before deciding on any further analysis steps.

---

### Investigative Reasoning and Workflow Design

The workflow followed an intentional analytical progression:

1. Review the visible email as the target would see it  
2. Extract raw email fields that clarify sender and reply behavior  
3. Identify actual sending infrastructure and delivery context  
4. Extract and validate web destinations  
5. Enrich suspicious infrastructure and message structure  
6. Hash and validate suspicious attachments  
7. Correlate all artifact types before drawing a conclusion  

This progression mirrors incident response processes where analysts first establish the claimed story of the email, then validate the technical story, and only later draw a phishing conclusion based on correlated evidence.

Manual extraction was performed before relying heavily on enrichment tools so that the reasoning remained grounded in the message itself rather than in platform interpretation. This also made it easier to understand what each field meant when reviewing PhishTool results later.

Infrastructure and file enrichment were used to support the conclusion, not replace it. A suspicious sender mismatch, unrelated destination domain, and poor file reputation together are much more useful than any one isolated indicator taken alone.

---

### Limitations of This Workflow

This walkthrough does not include:

- mail gateway log correlation  
- mailbox-wide recipient scoping  
- sandbox execution of suspicious attachments  
- DMARC/DKIM deep-dive validation across multiple message hops  
- automated phishing case enrichment pipelines  

As a result, the workflow focuses on manual visibility, field interpretation, and artifact validation rather than enterprise-scale automation or advanced malware detonation.

The workflow also does not simulate fully targeted business email compromise scenarios where malicious intent may depend more on conversation context than on obviously suspicious URLs or attachments.

---

### Ideas for Expansion or Automation

Future enhancements could include:

- correlating suspicious sender domains across multiple reported phishing emails  
- integrating sandboxing for suspicious attachments  
- building a phishing triage checklist mapped to common sender and URL anomalies  
- correlating extracted domains and hashes with SIEM or EDR telemetry  
- creating automation notes for parsing reported emails into a standardized case template  

These additions would connect manual artifact extraction to broader SOC detection and response workflows.

---

### Cross-Workflow Connections

This workflow directly supports:

- business email compromise triage workflows  
- malicious attachment handling workflows  
- URL investigation and reputation analysis workflows  
- SIEM alert validation exercises involving phishing-related indicators  

Understanding how phishing artifacts are extracted and interpreted improves confidence when validating email alerts generated by other security tools.
