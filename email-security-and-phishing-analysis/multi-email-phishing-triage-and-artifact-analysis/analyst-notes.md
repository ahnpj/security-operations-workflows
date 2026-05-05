# Analyst Notes — Multi-Email Phishing Triage and Artifact Analysis

This file captures investigative reasoning, interpretation decisions, and workflow-specific observations recorded during triage of five suspicious emails. The analysis focused on identifying which messages were malicious, which were spam or scam-themed, and which artifacts supported escalation.

These notes focus on *how conclusions were formed* from visible message content, raw header fields, sender infrastructure, embedded URLs, attachment replacement evidence, and classification judgment — not just on which answers were submitted.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as suspicious email triage, phishing validation, and artifact extraction.  
> - **Executions** refer to the hands-on performance of those tasks using real emails, analyst tools, and supporting investigation utilities.  
> - **Writeups** document extraction decisions, classification reasoning, and investigative conclusions.

---

### Observations During Execution

One of the most important observations during this workflow was that the five emails were not equally malicious, even though several were suspicious or unwanted. That forced a more realistic triage mindset. In a real SOC queue, analysts cannot assume that every strange message is phishing. They need to separate confirmed malicious activity from spam, scam, newsletter, and suspicious-but-not-actionable content.

Email One stood out because the visible presentation attempted to use an Amazon-themed identity, but the actual sending address did not belong to Amazon. The message also used generic addressing, inconsistent formatting, poor grammar, and a call-to-action button that appeared designed to drive the user toward a refund or help-page flow. Individually, any one of those indicators might not be enough to finalize a phishing conclusion. Together, they formed a clear impersonation and likely credential-harvesting pattern.

Email Two was useful because it looked deceptive but did not rise to the same malicious threshold. It used a lottery/scam theme and encouraged the recipient to contact a Gmail address, but the referenced news URL was not hyperlinked and appeared to relate to a legitimate news site. That made it suspicious and unwanted, but better classified as spam/scam rather than malicious phishing based on the evidence available.

Email Three showed a different malicious pattern. Instead of relying primarily on a link, it pushed the recipient to open an attachment. The message claimed to represent the NHS but used a Gmail sender, generic imagery, generic addressing, and urgency. The strongest artifact was the attachment replacement notice, which indicated that the original malicious file had been removed by the email gateway. The original attachment used a `.pdf.exe` double extension, which is a strong sign that the attacker attempted to disguise an executable as a document.

Email Four and Email Five reinforced that suspiciousness does not always equal maliciousness. Both contained elements that could make users uncomfortable or cautious, but neither contained enough evidence during this triage to classify them as malicious. This was an important part of the exercise because it tested classification discipline instead of just artifact collection.

---

### Challenges and Troubleshooting

One challenge in this workflow was avoiding the temptation to over-classify messages. Email Two and Email Five especially show why phishing triage requires judgment. A message can be misleading, scam-themed, or risky without containing a confirmed malicious URL, malicious attachment, or clear credential-harvesting path. That distinction matters because over-escalating everything creates noise and weakens SOC prioritization.

Another challenge was separating visible sender presentation from the actual sending address. In Email One, the friendly sender presentation was not the same as the real address inside the raw header. That distinction is easy to miss if the analyst only reviews the email client view. Raw header review matters because the display name can be controlled by the sender and may be crafted to look like a known brand.

The reverse DNS review also required careful interpretation. For Email One, the live lookup context did not return the same useful hostname information that was preserved in the original email headers. This reinforced that current WHOIS or reverse DNS lookups may not always reflect the state of infrastructure at the time the email was sent. The preserved message headers can be more reliable for historical delivery context.

The malicious attachment in Email Three also required a different mindset. Since the original file had been removed by an email gateway, the correct action was not to attempt to execute or recover it. The safe approach was to review the replacement text file, document the original filename and provided SHA256 hash, and treat those as the relevant file artifacts.

---

### Investigative Reasoning and Workflow Design

The workflow followed an intentional analytical progression:

1. Review all five emails visually before committing to deep analysis  
2. Classify each email based on evidence and likely intent  
3. Select the malicious emails for deeper raw-header extraction  
4. Extract sender, subject, recipient, Reply-To, date/time, sending IP, and reverse DNS context  
5. Extract full URL evidence from the link-based phishing email  
6. Extract attachment name and SHA256 hash evidence from the attachment-based phishing email  
7. Correlate all evidence into final classification and response recommendations  

This progression mirrors how a real triage queue should be handled. The early steps prevent wasted effort by identifying which messages deserve deeper investigation. The later steps build the evidence needed to support containment, reporting, and detection actions.

The order of operations mattered. Visible message review came first because it established the social engineering story and recipient-facing context. Raw message review came next because it clarified the real sender and delivery details. URL and attachment extraction followed because those artifacts represented the actual mechanisms used to affect the user. The final classification was based on correlation rather than a single indicator.

A key part of the workflow was resisting the urge to let any one clue “win” the conclusion too early. Poor grammar alone is not proof of phishing. A Gmail sender alone is not always malicious. A URL alone does not prove maliciousness. An attachment alone is not necessarily harmful. The strongest conclusions came from patterns: sender mismatch plus brand impersonation plus call-to-action behavior, or public-service impersonation plus urgency plus gateway-replaced malicious attachment.

---

### Limitations of This Workflow

This walkthrough does not include:

- full sandbox detonation of malicious attachments  
- live browsing or detonation of suspicious URLs  
- full email gateway log correlation across the organization  
- SIEM correlation to determine whether users clicked links or opened attachments  
- reputation lookups for all domains and IPs involved  
- full DMARC/SPF/DKIM authentication interpretation beyond the artifacts provided in the lab  
- enterprise-wide recipient scoping beyond the identified recipients  

As a result, the workflow focuses on manual triage and artifact extraction rather than full incident response, campaign attribution, or enterprise containment.

The workflow also depends on values extracted from provided lab screenshots and raw message examples. Any placeholder values in the related documents should be replaced with the exact extracted values after completing the hands-on lab.

---

### Ideas for Expansion or Automation

Future enhancements could include:

- building an IOC table that standardizes sender, subject, recipient, URL, IP, hostname, attachment name, and hash values  
- adding screenshots of each malicious indicator and raw header extraction step  
- validating URLs with URL2PNG, urlscan.io, VirusTotal, or similar enrichment tools  
- checking email authentication results such as SPF, DKIM, and DMARC where available  
- correlating sender IP and URL activity against SIEM, proxy, DNS, or EDR telemetry  
- writing a phishing triage decision matrix to separate spam, scam, phishing, malware delivery, and newsletter categories  
- converting repeated raw-header extraction steps into a reusable checklist  

These additions would connect manual phishing triage to broader SOC detection, enrichment, and response workflows.

---

### Cross-Workflow Connections

This workflow directly supports:

- manual phishing email artifact extraction workflows  
- suspicious attachment handling workflows  
- URL and infrastructure investigation workflows  
- email gateway alert validation workflows  
- credential theft and malware delivery triage workflows  
- SIEM or proxy-based alert validation exercises involving phishing URLs  

Understanding how to classify multiple emails improves confidence when reviewing reported phishing queues, avoiding false positives, and prioritizing messages that require containment.
