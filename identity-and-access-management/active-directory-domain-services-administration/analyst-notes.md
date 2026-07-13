# Analyst Notes — Active Directory Domain Services Administration

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the Active Directory Domain Services administration workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding why domain controller operations, identity lifecycle management, password policy layering, and security hardening settings are structured the way they are in Active Directory.

> **Workflow vs Execution vs Conceptual Review (Terminology Used Here)**
>
> * **Workflows** refer to structured AD DS administration tasks such as domain controller promotion, user lifecycle management, and policy configuration.
> * **Executions** refer to the hands-on administration performed using Server Manager, ADUC, ADAC, Active Directory Sites and Services, and the Group Policy Management Console.
> * **Conceptual Reviews** refer to the interpretation of Active Directory structures, policy precedence, and security configuration reasoning.

---

### Observations During Execution

One of the biggest realizations during this workflow was how much of Active Directory administration is really about **structure and scope** rather than any single powerful command.

Almost none of the individual tasks in this workflow were technically difficult on their own — creating an OU, creating a user, changing a password length setting. What made the workflow meaningful was seeing how these small, individually simple actions combine to create a directory that's organized, appropriately delegated, and progressively hardened.

This was an important mindset shift.

Rather than asking:

```text
What's the one setting that secures Active Directory?
```

the workflow reinforced that the better question is:

```text
How do many individually reasonable configuration decisions add up to a well-structured, defensible directory?
```

---

### Understanding Why a Second Domain Controller Matters

Before this workflow, the idea of "promoting a domain controller" felt like a somewhat abstract infrastructure task.

The reasoning became clearer after thinking through what happens if an environment only has one domain controller: if that single server goes down, authentication, Group Policy processing, and directory lookups across the entire domain stop working. A second domain controller isn't just about "having a backup" — it's about distributing load and providing redundancy for one of the most critical services in the environment.

This also made the RID FSMO role transfer make more sense. Certain Active Directory operations (like relative identifier allocation, which is what the RID Master handles) can only be performed by one domain controller at a time. If that specific domain controller becomes unavailable and no other DC can step in for that role, certain directory operations can stall even though the rest of the domain still appears to be functioning.

---

### Understanding FSMO Roles More Generally

The RID Master transfer was the only FSMO role explicitly moved in this workflow, but it opened up a broader concept worth understanding: Active Directory has five FSMO roles total (Schema Master, Domain Naming Master, RID Master, PDC Emulator, and Infrastructure Master), and each one handles a specific type of operation that can't be safely handled by multiple domain controllers simultaneously.

This reinforced why FSMO role placement is something administrators actively plan around, rather than just leaving on whichever domain controller happened to hold the role by default (typically the first DC installed in the domain).

---

### Understanding AD Sites and Subnets

The site and subnet configuration task initially felt like a small, almost cosmetic step — creating a site called "Sydney" and mapping a subnet to it.

The reasoning became clearer when thinking about what AD sites actually control: they tell Active Directory which domain controllers are "close" to which physical network locations. This directly affects two things — which domain controller a client authenticates against (clients prefer authenticating to a domain controller in their own site rather than one across a slower WAN link), and how replication traffic is scheduled between sites (intra-site replication is more frequent/aggressive than inter-site replication, which can be scheduled to avoid consuming WAN bandwidth during business hours).

Without a site and subnet mapped correctly, a client machine at a "Sydney" office could end up authenticating against a domain controller much farther away, adding latency to something as routine as a user login.

---

### Understanding Why Organizational Units Matter Beyond Organization

Before this workflow, OUs felt like little more than folders for tidiness — a way to keep user accounts visually grouped by office location.

The delegation task changed that understanding. OUs aren't just a visual organizing tool — they're the actual **scope boundary** for both Group Policy application and administrative delegation. Delegating "reset passwords" specifically to the Sydney OU means Sydney Administrators can reset passwords for Sydney accounts, but not Melbourne or Brisbane accounts, without needing three separate, more complicated permission structures.

This reframed OU design as a security decision, not just an organizational one. A poorly structured OU layout can make it much harder to delegate access narrowly, which tends to push administrators toward over-granting broader rights just because it's easier.

---

### Understanding the Delegation of Control Wizard vs. Just Adding Someone to Domain Admins

This was one of the more important lessons in the workflow.

It would have been technically simpler to just add `Sydney Administrators` to the Domain Admins group and call it done — that group would then be able to reset passwords (and do essentially anything else) anywhere in the domain.

Using the Delegation of Control Wizard instead to grant one specific, narrow capability (reset user passwords and force password change) over one specific OU (Sydney) reinforced the principle of least privilege in a very concrete way. If the Sydney Administrators group's credentials were ever compromised, the blast radius is "password reset capability over Sydney accounts," not "full administrative control over the entire domain."

---

### Understanding the Protected Users Group

The Protected Users group was one of the more conceptually interesting parts of the workflow, mostly because its effects aren't something you can directly see just by looking at group membership in ADUC.

Adding `SydneyContractor` to Protected Users doesn't change what the account can do — it changes **how the account is allowed to authenticate**. Accounts in Protected Users can't authenticate using older, weaker mechanisms (like NTLM or DES/RC4 Kerberos encryption), can't have their credentials cached for offline logon, and can't have their TGT renewed beyond a set lifetime, forcing more frequent re-authentication.

This made it clear that Protected Users is specifically designed to reduce the usefulness of stolen credentials for a specific account, even if group membership alone wouldn't suggest anything special was happening.

---

### Understanding Domain Password Policy vs. Fine-Grained Password Policy

Before this workflow, it wasn't fully clear why an organization would need more than one password policy at all — doesn't the domain password policy just apply to everyone?

The Fine-Grained Password Policy task clarified this well. A domain password policy is a single, blanket setting that applies to every account in the domain (short of using FGPPs). But not every account carries the same risk — a compromised standard user account is bad, but a compromised Domain Admins account is far more damaging. FGPPs let specific groups (like Domain Admins) be held to a stricter standard (in this case, a longer minimum password length) without forcing that same stricter requirement onto every account in the domain, which could otherwise create unnecessary friction for lower-risk accounts.

The precedence value was also worth understanding — when multiple FGPPs could apply to the same account, the one with the **lowest** precedence number wins. Setting precedence to `1` in this workflow made sense once framed as "this is the most important/highest-priority policy," since lower numbers taking priority is the opposite of how "precedence" might intuitively be assumed to work.

---

### Understanding Why the AD Recycle Bin Isn't Enabled by Default

Before this workflow, it wasn't obvious why something as clearly useful as an "undo" button for deleted directory objects wouldn't just be turned on automatically.

The reasoning became clearer after learning that enabling the Recycle Bin is a one-way, irreversible forest-wide change, and it does have a modest storage/replication cost (deleted objects are retained with more of their original attributes for a longer period rather than being immediately tombstoned). Because it can't be casually toggled on and off, Microsoft leaves it as an explicit administrative decision rather than a default — which is exactly why enabling it proactively, before an accidental deletion actually happens, is the recommended approach rather than waiting until it's needed.

---

### Understanding Why NTLM Restriction Matters

The NTLM restriction task was one of the more security-focused pieces of the workflow, and it connected back to concepts that come up frequently in real-world Active Directory attack techniques.

NTLM is an older authentication protocol that, unlike Kerberos, doesn't mutually authenticate client and server in the same way, which makes it more susceptible to relay attacks (where an attacker intercepts and forwards NTLM authentication attempts to a different target) and downgrade-style attacks (where an attacker forces a fallback to NTLM specifically because it's weaker than Kerberos). Setting the domain-wide NTLM restriction to "Deny all" forces authentication to rely on Kerberos instead, closing off an entire category of attack technique that specifically depends on NTLM being available.

---

### Understanding Why Advanced Audit Policy Matters More Than Basic Auditing

The advanced audit policy task made it clear why "basic" auditing (the older, broader audit categories) is generally considered insufficient compared to Advanced Audit Policy Configuration.

Basic auditing tends to log broad categories with limited granularity, generating either too much noise or missing specific event subtypes that matter most. Enabling **Audit User Account Management** specifically, scoped to the Sydney OU, and logging both **Success and Failure**, means account creation, modification, and deletion events within that OU are captured in enough detail to actually support an investigation later — rather than just knowing "something account-related happened" without enough context to act on it.

---

### Understanding "Deny Log On As a Service"

This was one of the less intuitive settings in the workflow at first glance — why would a group of administrators need to be explicitly denied the right to log on as a service?

The reasoning became clearer when thinking about it from a least-privilege and attack-surface perspective rather than a "what does this group need to do" perspective. Sydney Administrators is a group meant for interactive administrative tasks (like resetting passwords), not for running as a service account. Explicitly denying that logon type closes off a potential avenue of abuse — if credentials for that group were ever compromised, they couldn't be used to install or run a malicious service under that group's context, even if some other misconfiguration might have otherwise allowed it.

---

### Challenges and Confusion Encountered

The most confusing portions of the workflow included:

* understanding why a single domain controller isn't considered sufficient in most environments,
* understanding what a FSMO role actually controls and why it can't be split across multiple domain controllers,
* understanding how AD sites and subnets affect authentication and replication behavior rather than being purely organizational,
* understanding why OU structure is a security/delegation decision and not just a visual organizing tool,
* understanding what the Protected Users group actually changes, since its effects aren't visible through group membership alone,
* understanding why Fine-Grained Password Policy precedence works in reverse (lower number = higher priority),
* understanding why the AD Recycle Bin isn't enabled by default despite being clearly useful,
* understanding why NTLM restriction is considered a meaningful hardening step rather than just a compatibility setting,
* understanding why "Deny log on as a service" would apply to an administrative group rather than only to service accounts.

These confusion points ultimately became some of the most valuable learning opportunities in the workflow.

---

### Cross-Workflow Connections

This workflow connects naturally to several other security and identity-focused workflows.

It connects to identity and access management broadly, since organizational unit design, group scoping, and delegation are core IAM concepts applicable well beyond this specific lab.

It connects to Windows event log triage and threat detection workflows (such as DeepBlueCLI-based analysis), since the advanced audit policy configured here (Audit User Account Management) is exactly the kind of setting that determines whether account creation and group membership changes are even captured in the Security event log for later detection.

It connects to network security and segmentation concepts, since AD site/subnet design reflects the same physical-topology-awareness that shows up in network security zoning and traffic flow analysis.

It connects to credential and authentication security more broadly, since Protected Users, NTLM restriction, and Fine-Grained Password Policies all address different angles of the same underlying goal: making stolen or guessed credentials less useful to an attacker.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

* scripting the account creation and OU structure tasks using PowerShell instead of ADUC, to compare the two approaches,
* configuring a second AD site with its own subnet and reviewing how replication scheduling could be configured between sites,
* testing authentication behavior for the Protected User account for both Kerberos and NTLM to directly observe the enforced restrictions,
* reviewing Security event log entries generated by the advanced audit policy configured in this workflow (tying directly into a DeepBlueCLI or Event Viewer-based triage exercise),
* transferring and documenting the remaining FSMO roles (Schema Master, Domain Naming Master, PDC Emulator, Infrastructure Master) in a similar structured format,
* exploring AD DS security assessment tooling (such as PingCastle or similar) against this lab environment to compare tool-identified findings against the hardening steps performed manually here.

These additions would move the workflow beyond introductory AD DS administration and into more advanced identity security assessment and detection-focused activities.

---

### Summary

This workflow demonstrated that Active Directory administration operates across multiple layers simultaneously: infrastructure (domain controllers and FSMO roles), topology (sites and subnets), structure (OUs), identity lifecycle (users, groups, attributes), delegation (scoped administrative rights), policy (domain and fine-grained password requirements), recoverability (the Recycle Bin), and security hardening (NTLM restriction, advanced auditing, and user rights assignment).

No single task in this workflow, taken alone, would meaningfully secure or organize an Active Directory environment. Structuring OUs sensibly, delegating narrowly instead of broadly, applying stricter password requirements to higher-risk groups, and closing off legacy authentication paths and unnecessary logon rights are all individually modest steps.

Together, though, they represent the layered, defense-in-depth thinking that distinguishes a deliberately administered Active Directory environment from one that simply happens to work.