# Analyst Notes — Active Directory Branch Office Deployment and Security Hardening

This document captures administrative reasoning, decisions made during the workflow, observations about tool behavior, and the security relevance of each major configuration area.

---

## Phase 1 — Domain Controller and Site Topology

### Site before subnet
The Paris AD site was created before the subnet was associated with it. This is the correct order — you cannot map a subnet to a site that doesn't exist yet. The site is the location object; the subnet is the rule that maps device addresses to that location.

### Role installation vs. promotion
These are two separate steps and cannot be combined. Installing the AD DS role places the required software on the server. Promotion is what actually turns it into a domain controller by replicating the directory database and registering DNS service records. A server with the AD DS role installed but not promoted is still a member server, not a domain controller.

### Writable vs. read-only domain controller
`DC2` was promoted as a writable domain controller, meaning it holds a full read-write copy of the directory. In branch-office scenarios, a read-only domain controller (RODC) is sometimes preferred for locations with weaker physical security. This workflow used a writable DC consistent with the lab requirements.

### FSMO transfer failure on graphical interface
The initial attempt to transfer the RID Master role through ADUC's Operations Masters dialog failed with a communication error. Rather than seizing the role, I confirmed that `DC1` was reachable via ping and that replication was functional, then performed the transfer through PowerShell. Seizure is an emergency procedure for when the current holder is permanently unavailable — using it when the holder is online can leave conflicting metadata. The correct approach to a temporary error is to resolve the communication issue and perform a normal transfer.

### Replication validation before FSMO transfer
`repadmin /replsummary` was run before the FSMO transfer to confirm that `DC2` had received current directory data. Transferring a FSMO role to a controller that hasn't fully replicated can cause operational issues — the RID Master specifically needs to have accurate knowledge of existing RID pool allocations before it starts issuing new blocks.

---

## Phase 2 — Directory Objects and Delegation

### Group vs. direct assignment
The delegated password-management right was assigned to `Paris Admins` as a group, not directly to `ParisContractor`. This is the correct approach — if a second contractor or permanent administrator needs the same capability later, they receive it by joining the group rather than requiring a new delegation entry on the OU. The group is the administration layer; the OU is the scope.

### Account is sensitive and cannot be delegated
This flag was applied to `ParisContractor` because the account has a real delegated capability. The flag prevents Kerberos constrained or unconstrained delegation from forwarding the contractor's credentials to another service. It protects the account without granting it additional permissions.

This is different from adding a user to the Protected Users group, which applies a broader set of authentication restrictions including no NTLM authentication, no DES or RC4 encryption in Kerberos, and limited credential caching. The lab used the account-level flag appropriate for this specific requirement.

### Account expiration vs. disablement
Account expiration was configured at creation time (December 31, 2027). This is preferable to relying on a manual disable action when the engagement ends, because manual processes can be forgotten. Expiration provides an automatic access cutoff without requiring any follow-up action.

### Delegation scope
The Delegation of Control Wizard writes access-control entries (ACEs) directly onto the Paris OU object. The delegation applies only to user objects inside that OU. It does not extend to child OUs unless the wizard is run again targeting those containers, and it does not apply to objects in other parts of the domain.

---

## Phase 3 — Bulk User Administration with PowerShell

### Preview before bulk modification
`Get-ADUser` with the City filter was run as a read-only preview before any changes were made. This is a critical step in any bulk operation — confirming the exact population that will be affected reduces the risk of an incorrect filter modifying the wrong accounts. The preview also confirmed that the City attribute was populated correctly on the target accounts.

### Variable storage
Storing `$targetOU` and `$users` in variables before the loop improves readability and reduces the chance of a typo in a repeated distinguished name. It also means the filter runs once and returns a stable collection rather than being re-evaluated inside the loop.

### Disable rather than delete
Paris users were disabled, not deleted. Disabling is reversible and preserves group membership, attribute values, and audit history. Deletion removes the object entirely and, without the Recycle Bin enabled, makes recovery significantly more complex. In identity lifecycle management, disabling accounts during transitions is the standard first step before eventual deletion after a defined retention period.

### Verification using calculated properties
The verification query used a calculated property (`@{Name="ChangeAtNextLogon";Expression={$_.pwdLastSet -eq 0}}`) rather than relying on a built-in property name. `pwdLastSet` equal to zero is the directory's representation of "must change password at next logon." Using a pipeline and `Select-Object` to inspect the resulting state — rather than assuming the commands succeeded — is the correct verification approach for bulk operations.

---

## Phase 4 — Password Policy and Recovery

### Default Domain Policy vs. Fine-Grained Password Policy
These are fundamentally different objects. The Default Domain Policy is a GPO linked at the domain root — it applies broadly to all domain accounts through the standard Group Policy processing chain. A Fine-Grained Password Policy is a Password Settings Object (PSO) stored directly in the directory under `System\Password Settings Container`. PSOs target specific users or global security groups and are not GPO-based, which is why they are configured in ADAC rather than the Group Policy Management console.

The precedence value on a PSO determines which policy wins when multiple PSOs could apply to the same user. Lower numbers win. A precedence of 1 means this policy takes priority over any other PSO that might apply to a Domain Admins member.

### Why Domain Admins gets a stricter policy
Domain Admins have unrestricted control over the entire domain. A single compromised Domain Admins credential is a domain-wide incident. Applying a stricter minimum password length (16 vs. 12) is a risk-based control that acknowledges the higher impact of compromise for privileged accounts. In production, additional controls such as Protected Users group membership, just-in-time privileged access, and PAM solutions would typically supplement or replace persistent Domain Admins membership.

### AD Recycle Bin timing
The Recycle Bin must be enabled before a deletion occurs to provide full restoration capability. Once enabled, it is effectively a forest-wide and irreversible change — the console warns about this before confirming. Objects deleted after enablement are moved to a deleted objects container and can be restored with most attributes and group memberships preserved. Objects deleted before enablement cannot benefit from this feature retroactively.

---

## Phase 5 — Group Policy Security Configuration

### Default Domain Policy vs. Default Domain Controllers Policy
These are two different GPOs with different links and scopes:
- **Default Domain Policy** is linked at the `contoso.com` domain root. It applies to all computers and users in the domain (subject to inheritance and filtering). Used here for the domain-wide minimum password length.
- **Default Domain Controllers Policy** is linked to the **Domain Controllers** OU. It applies to domain controller computer objects. Used here for the NTLM restriction because domain controllers are where domain authentication decisions are processed.

Modifying built-in GPOs is generally not recommended in production without careful change control, but these are the standard GPOs for domain password policy and DC security policy in a lab environment.

### NTLM restriction considerations
Setting NTLM authentication to `Deny all` in the Default Domain Controllers Policy removes the legacy authentication fallback. In production, this would need to be preceded by an NTLM dependency audit — applications, scripts, and devices that rely on NTLM would stop working. The lab environment had no such dependencies, making `Deny all` appropriate for the stated security requirement.

### GPO link vs. security filtering vs. named group inside a setting
These are three distinct concepts that are easy to confuse when working with `GPO1` and `Paris Admins`:

1. **GPO link** — `GPO1` is linked to the Paris OU. This defines where the GPO's settings can be applied (computers in the Paris OU scope).
2. **Security filtering** — `GPO1` retains its default Authenticated Users filter, meaning all eligible computer objects in the linked scope can process it.
3. **Paris Admins named inside Deny log on as a service** — this is a setting inside the GPO that tells computers processing the GPO to apply a local user-right rule that denies service logon to members of `Paris Admins`. `Paris Admins` is the subject of the restriction, not a filter on who processes the GPO.

The group does not need to be located in the Paris OU for this to work. The computers process the GPO and receive a local rule that references the group by its Security Identifier.

### Audit logon failure as detective control
Enabling failure auditing for logon events does not prevent failed logons — it records them in the Security event log on each affected computer. This is a detective rather than preventive control. Its value is in investigation: failed logon events support analysis of mistyped credentials, brute-force attempts, password spraying, and access attempts by disabled or expired accounts. In a production environment with a SIEM, these events would be forwarded centrally for correlation and alerting.

### Why Computer Configuration was used for all GPO1 settings
Both settings in `GPO1` (audit policy and user-right assignment) are under **Computer Configuration → Policies**. This means the computer object's OU placement determines whether the setting is processed — not the user's OU. A user who is a member of `Paris Admins` and is even stored in the Paris OU does not personally process these settings. The computer objects in the Paris OU receive the policy and apply the rules locally, including the rule that names `Paris Admins` in the service logon denial.