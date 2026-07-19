# Analyst Notes — Microsoft Entra ID User and Group Management, License Assignment, and User Lifecycle Operations

This document captures administrative reasoning, conceptual explanations, decisions made during the workflow, and observations about how each operation fits into real-world identity and access management practices.


### Phase 1 — User and Group Creation, Group License Assignment

#### The three types of user identities in Microsoft Entra ID

Before creating any user, it's worth understanding that Entra ID recognizes three distinct identity types — and knowing which type a user account is has implications for how it's managed and what happens to it if the source it came from changes.

**Cloud identities** exist only in Entra ID. They are created and managed entirely within the tenant — there is no external system they're synchronized from. The accounts created in this workflow (`Chris Green` and `Dominique Koch`) are cloud identities. When a cloud identity is deleted from Entra, it's gone from the directory — there's no upstream system that would re-create it.

**Directory-synchronized identities** originate in an on-premises Active Directory and are synchronized into Entra ID using Microsoft Entra Cloud Sync (the current recommended tool for most organizations) or Microsoft Entra Connect Sync (used for more complex scenarios). For these accounts, the on-premises AD is the system of record — attributes are managed there and pushed to Entra, not the other way around. If you delete a synced account in Entra, it may reappear the next time synchronization runs from the source AD.

**Guest users** are external identities — accounts that belong to another organization (another Entra tenant, Google, or another identity provider) that have been invited into your tenant for collaboration purposes. They're useful for contractors, partners, or external vendors who need access to specific resources without being given a full internal account. Guest accounts can be removed when the external relationship ends.

This distinction matters because the *source* of an identity determines where you have to go to manage it. You can't change a synced user's attributes directly in Entra — you have to change them in on-premises AD and let synchronization propagate the change. Knowing which type of account you're dealing with before attempting to make changes prevents confusion and wasted effort.

#### Why usage location is required before license assignment

The usage location field might seem like optional metadata, but it's actually a prerequisite for license assignment. Microsoft 365 services are subject to data sovereignty and availability regulations that vary by country — certain features and service plans are available in some regions but not others. Microsoft requires the usage location to be set on a user account before assigning any license, because the location determines which service plan components are legally permitted to be provisioned for that account.

If you attempt to assign a license to a user without a usage location set, the assignment will fail. This is a common source of confusion when administrators provision accounts quickly without setting all required attributes — the license assignment appears to work in the interface but doesn't complete, or it produces an error. Setting usage location at account creation time (rather than treating it as something to configure later) avoids this failure mode.

#### Security groups vs. Microsoft 365 groups

Both group types can contain users and both can be used to manage access to resources, but they serve different primary purposes and provision different things when created.

A **Security group** is a membership container whose primary function is access control and policy assignment. It can be used to grant permissions to shared resources (SharePoint sites, Azure resources), assign licenses (as in Phase 1.3), or apply policies (like Conditional Access or Intune). It does not create any shared collaboration infrastructure when provisioned. Security groups can contain users, devices, and service principals as members, making them versatile across identity and endpoint management scenarios. Only administrators can create Security groups by default.

A **Microsoft 365 group** is a collaboration container. When created, it automatically provisions a shared mailbox, a shared calendar, a SharePoint document library, a OneNote notebook, and optionally a Microsoft Teams workspace. These resources are tied to the group and shared among its members automatically — no additional configuration is required to activate them. Microsoft 365 groups are designed around the idea of a team that works together and needs shared, persistent collaboration infrastructure. Both administrators and regular users can create Microsoft 365 groups (subject to tenant configuration).

The practical guidance is: use a Security group when the primary purpose is access control or license assignment; use a Microsoft 365 group when the primary purpose is enabling a team to collaborate with shared tools.

#### Membership type: Assigned vs. Dynamic

**Assigned** membership is the simplest and most common type: an administrator manually adds and removes members. Every member in the group is there because someone explicitly added them.

**Dynamic User** membership uses rules that evaluate user attributes to automatically determine membership. For example, a rule could be written to automatically include every user whose Department attribute equals "Sales." When a new user is created with Department = "Sales," they are added to the group automatically. When a user's Department changes away from "Sales," they are automatically removed. This keeps group membership current without requiring constant manual maintenance.

Dynamic membership requires a Microsoft Entra ID P1 license and is most valuable for large, frequently changing groups where membership can be reliably defined by an attribute-based rule. It's less appropriate when membership is small, irregular, or can't be cleanly expressed as a rule against existing user attributes.

**Dynamic Device** membership is similar but evaluates device attributes instead of user attributes, and is only available for Security groups (not Microsoft 365 groups).

#### Why group-based licensing is preferred over individual assignment

Individual license assignment is straightforward for a handful of users but doesn't scale. An organization with 500 users who all need the same license would have to perform 500 separate assignment operations individually — and then repeat a similar process any time someone joins, leaves, or changes role.

Group-based licensing delegates this to group membership. The license is assigned to a group once; every current and future member of that group receives the license automatically. When a user leaves the organization and their account is removed from the group (or their account is deleted entirely), the license is automatically reclaimed. This approach also reduces the risk of license over-provisioning — a common compliance and cost issue in real environments where licenses accumulate on inactive accounts because no one manually revoked them.

The administrative model shifts from "manage individual license assignments" to "manage group memberships," which is significantly lower overhead and more auditable.

---

### Phase 2 — User Lifecycle Operations (Deletion and Restoration)

#### The 30-day suspended state

When a user account is deleted from Microsoft Entra ID, it does not immediately and permanently disappear. Instead, it enters a **suspended state** where it remains for up to 30 days. During this period:

- The account cannot be used to authenticate.
- The account retains all of its original attributes, group memberships, and directory properties.
- The account is visible in the **Deleted Users** list and can be restored to full active status in a single operation.
- After the 30-day window expires, the account is permanently deleted automatically.

This suspended state exists specifically to allow for recovery from accidental deletions — a mistake that happens regularly in real environments, whether from a misidentified bulk operation, a helpdesk error, or an automation script with an incorrect filter.

#### The hard limit on permanent deletion

Once the 30-day window expires and the account is permanently deleted, **restoration is not possible**. Not through the portal, not through PowerShell, not through a Microsoft support request. The identity data — the SID, the attribute history, the group membership history — is gone.

This is worth internalizing for two reasons. First, it means the Deleted Users list needs to be monitored with some regularity in environments where accidental deletions are a real risk — if a deletion goes unnoticed for more than 30 days, the recovery window is gone. Second, it means that re-creating an account with the same username is possible (from a technical standpoint) but will produce a brand-new identity object with a new SID — it will not inherit any of the original account's permissions, group memberships, or history. From an access control perspective, a new account with the same username is a completely different identity.

#### Who can restore deleted users

The ability to restore or permanently delete user accounts from the Deleted Users list requires one of the following Entra ID roles: Global Administrator, User Administrator, Partner Tier-1 Support, or Partner Tier-2 Support. Users without one of these roles can view the Deleted Users list but cannot take action on it.

#### Deletion vs. disablement

Deleting an account and disabling an account are different operations with different operational implications.

**Disabling** an account blocks it from authenticating but leaves it fully intact in the directory — all attributes, group memberships, licenses, and history are preserved. The account can be re-enabled at any time with no data loss. Disablement is typically the first step when an employee leaves, because it immediately revokes access while allowing time for investigation, account archival, or litigation hold compliance before permanent removal.

**Deleting** an account removes it from the active directory and starts the 30-day suspended-state clock. It's the step taken after disablement is no longer sufficient — for example, when a former employee's account has been reviewed, any needed data has been archived, and there's no further reason to retain the identity object.

In practice, the standard lifecycle for an employee departure in a well-run environment is: disable immediately → review and archive → delete after a defined retention period → permanent deletion after 30 days. This workflow demonstrates the deletion and restoration steps specifically.

---

### Phase 3 — Microsoft 365 Group Creation

#### What a Microsoft 365 group actually creates

Creating a Microsoft 365 group does more than create a membership list. Behind the scenes, Microsoft automatically provisions a set of collaboration resources tied to the group:

- A **shared Exchange mailbox** with a group email address, allowing all members to send and receive messages as the group.
- A **shared calendar** where meeting invites and events are visible to all members.
- A **SharePoint document library** for shared file storage.
- A **OneNote notebook** shared among members.
- Optionally, a **Microsoft Teams workspace** if Teams is enabled in the tenant.

These resources are created automatically at group creation time and require no additional configuration to activate. They persist for as long as the group exists and are deleted when the group is deleted.

This is fundamentally different from a Security group, which creates only the group object itself with no associated collaboration infrastructure.

#### The Owner role in Microsoft 365 groups

Assigning an owner to a Microsoft 365 group is a delegation decision. Group owners can manage membership, change group settings, and in some cases manage the associated SharePoint site and mailbox — without needing Global Administrator or User Administrator rights in the tenant. This allows business unit leaders or team leads to manage their own group membership without involving IT for every change.

A group without an owner is still functional, but it becomes IT-dependent for all membership changes. Best practice is to assign at least one owner (and often two, to avoid single points of failure) so that routine group management can be self-service.

#### Why the All Groups list may require a refresh

Newly created Microsoft 365 groups can take slightly longer to appear in the All Groups list than Security groups, because the backend provisioning of shared resources (the mailbox, SharePoint site, etc.) adds processing time. This is normal behavior — refreshing the list one or more times will eventually surface the group once provisioning completes. It is not an error.

---

### Phase 4 — Individual License Assignment

#### When individual assignment is still appropriate

While group-based licensing is the preferred approach at scale, there are scenarios where individual license assignment is the right choice:

- **Small environments** where the overhead of managing groups doesn't provide meaningful benefit over individual assignments.
- **Exception accounts** — a specific executive, a shared service account, or a temporary contractor who doesn't fit into an existing group and doesn't warrant a new group being created just for them.
- **Testing or validation** — assigning a license to a single test account to verify functionality before rolling out to a group.
- **Time-limited assignments** — in some cases, individual assignment makes it easier to track and reclaim licenses for temporary access scenarios.

The key operational consideration is that individual license assignments require individual revocation. If the goal is scalable, low-maintenance license administration, group-based assignment is almost always the better answer.

#### Why cross-portal verification matters

After assigning a license in the Microsoft 365 admin center, the final verification step returns to the Microsoft Entra admin center to confirm the license appears on the user's profile. This cross-portal verification reflects a real operational practice: confirming that an action completed in one system has propagated correctly to the connected system.

In a production environment, licensing operations can occasionally fail silently — an assignment appears to succeed in the M365 admin center but doesn't fully propagate to the Entra directory object due to a transient error or a conflict (like a missing usage location). Verifying the outcome in a second portal before considering the task complete is how administrators catch these edge cases rather than assuming success.

#### The relationship between the two portals

A point of confusion for administrators new to Microsoft cloud environments is understanding which portal is responsible for which operations.

The **Microsoft Entra admin center** (`entra.microsoft.com`) is the authoritative source for identity objects: users, groups, applications, service principals, and directory roles. Creating, modifying, or deleting these objects happens here.

The **Microsoft 365 admin center** (`admin.microsoft.com`) handles subscription and licensing management: purchasing and managing subscriptions, assigning licenses to users or groups, and viewing license utilization. The M365 admin center also provides simplified management views for common tasks (like managing Exchange mailboxes or Teams settings), but identity object management always traces back to Entra.

The reason license assignment lives in the M365 admin center rather than the Entra admin center is partly historical (they were separate products that were later integrated) and partly functional (licensing is a billing and subscription management function, not an identity management function per se). The practical implication is that an administrator working in this environment needs to be comfortable navigating both portals and knowing which one to use for each type of task.
