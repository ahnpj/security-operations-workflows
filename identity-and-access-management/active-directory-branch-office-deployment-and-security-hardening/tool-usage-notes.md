# Tool Usage Notes — Active Directory Branch Office Deployment and Security Hardening

This document covers how each tool was used during this workflow, what function it served, and why it was the appropriate tool for that task.

---

## Server Manager

**Used for:** Installing the Active Directory Domain Services server role on `DC2` and launching the domain-controller promotion wizard.

**How it was used:**
- Navigated to **Manage → Add Roles and Features**
- Selected role-based installation targeting `DC2`
- Selected **Active Directory Domain Services** and accepted required management features
- After role installation, selected the notification flag to launch the AD DS Configuration Wizard

**Why this tool:** Server Manager is the Windows Server entry point for role and feature management. It is the correct starting point for role installation before promotion — installing the role and promoting the server are two separate steps, and Server Manager handles the first.

---

## Active Directory Domain Services Configuration Wizard

**Used for:** Promoting `DC2` as an additional writable domain controller in the existing `contoso.com` domain.

**How it was used:**
- Selected **Add a domain controller to an existing domain**
- Confirmed domain: `contoso.com`
- Configured `DC2` as a writable domain controller assigned to the `Paris` site
- Set the Directory Services Restore Mode (DSRM) password
- Completed promotion; server restarted automatically

**Why this tool:** The wizard is the standard graphical interface for the promotion process. It handles replication setup, DNS registration, and database initialization as part of the promotion sequence.

---

## Active Directory Sites and Services

**Used for:** Creating the Paris AD site, associating the `172.16.1.0/24` subnet, and validating `DC2`'s site assignment.

**How it was used:**
- Right-clicked **Sites → New Site** → created `Paris` site
- Right-clicked **Subnets → New Subnet** → entered `172.16.1.0/24`, associated with `Paris`
- Confirmed `DC2` appeared under the Paris site after promotion

**Why this tool:** Active Directory Sites and Services is the dedicated console for network topology objects — sites, subnets, and site links. It is separate from ADUC because it manages the physical network model, not the directory object tree.

---

## Active Directory Users and Computers (ADUC)

**Used for:** Creating the Paris OU, `Paris Admins` group, and `ParisContractor` account; managing group membership; configuring account expiration and non-delegation flag; delegating control; and accessing the Operations Masters dialog for the initial FSMO transfer attempt.

**How it was used:**
- Right-clicked `contoso.com` → **New → Organizational Unit** → `Paris`
- Inside `Paris` OU: **New → Group** → `Paris Admins` (Universal, Security)
- Inside `Paris` OU: **New → User** → `ParisContractor`
- `ParisContractor` properties → **Account** tab → set expiration to December 31, 2027
- `ParisContractor` properties → **Account** tab → enabled **Account is sensitive and cannot be delegated**
- `ParisContractor` properties → **Member Of** → added `Paris Admins`
- Right-clicked `Paris` OU → **Delegate Control** → added `Paris Admins` → selected password-reset and force-change rights

**Why this tool:** ADUC is the primary console for directory object management — users, groups, OUs, and access-control delegation. It is distinct from ADAC: ADUC handles day-to-day object administration; ADAC handles advanced features like FGPP and Recycle Bin.

---

## Active Directory Administrative Center (ADAC)

**Used for:** Creating the Fine-Grained Password Policy for Domain Admins and enabling the Active Directory Recycle Bin.

**How it was used:**
- Navigated to `contoso (local) → System → Password Settings Container`
- **New → Password Settings** → configured `Domain Admins 16 Character Policy` with precedence 1, minimum length 16, applied to Domain Admins group
- Selected `contoso (local)` → **Enable Recycle Bin** → confirmed warning

**Why this tool:** Fine-Grained Password Policies are stored as Password Settings Objects in the directory, not as GPO settings — so they are configured in ADAC rather than the Group Policy console. The AD Recycle Bin is also enabled through ADAC as a forest-wide optional feature.

---

## Group Policy Management Console (GPMC)

**Used for:** Editing the Default Domain Policy (password length), editing the Default Domain Controllers Policy (NTLM restriction), and creating and linking `GPO1` to the Paris OU.

**How it was used:**
- Expanded `Forest: contoso.com → Domains → contoso.com`
- Right-clicked **Default Domain Policy → Edit** → navigated to Password Policy → set minimum password length to 12
- Expanded **Domain Controllers** OU → right-clicked **Default Domain Controllers Policy → Edit** → navigated to Security Options → set NTLM restriction to Deny all
- Right-clicked `Paris` OU → **Create a GPO in this domain, and Link it here** → named `GPO1`

**Why this tool:** GPMC is the central interface for managing, creating, linking, and editing Group Policy Objects. It provides the top-level view of GPO structure across the domain and is where linking decisions are made.

---

## Group Policy Management Editor

**Used for:** Configuring the audit policy and user-right assignment inside `GPO1`.

**How it was used:**
- Edited `GPO1` from within GPMC
- Navigated to **Computer Configuration → Policies → Windows Settings → Security Settings → Advanced Audit Policy Configuration → Audit Policies → Logon/Logoff**
- Opened **Audit Logon** → enabled **Failure**
- Navigated to **Local Policies → User Rights Assignment**
- Opened **Deny log on as a service** → defined policy → added `Paris Admins`

**Why this tool:** The Group Policy Management Editor is the settings editor that opens from within GPMC when editing a specific GPO. GPMC manages structure and links; the Editor configures the actual policy settings inside a selected GPO.

---

## Windows PowerShell

**Used for:** Bulk Paris user operations (query, move, disable, flag for password change, verify), FSMO role transfer, and replication and domain-controller validation.

**Key commands used:**

```powershell
# Discover all domain controllers
Get-ADDomainController -Filter *

# Preview Paris users before bulk change
Get-ADUser -Filter "City -eq 'Paris'" -Properties City | Select-Object Name, SamAccountName, City

# Store OU path and user collection
$targetOU = "OU=Paris,DC=contoso,DC=com"
$users = Get-ADUser -Filter "City -eq 'Paris'" -Properties City

# Bulk move, flag, and disable
foreach ($user in $users) {
    Move-ADObject -Identity $user.DistinguishedName -TargetPath $targetOU
    Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true
    Disable-ADAccount -Identity $user.SamAccountName
}

# Verify final state
Get-ADUser -Filter "City -eq 'Paris'" -Properties City, Enabled, pwdLastSet |
    Select-Object Name, DistinguishedName, Enabled,
    @{Name="ChangeAtNextLogon";Expression={$_.pwdLastSet -eq 0}}

# Transfer RID Master
Move-ADDirectoryServerOperationMasterRole -Identity "DC2" -OperationMasterRole RIDMaster

# Verify FSMO roles
netdom query fsmo
Get-ADDomain | Select-Object RIDMaster

# Validate password policies
Get-ADDefaultDomainPasswordPolicy | Select-Object MinPasswordLength
Get-ADFineGrainedPasswordPolicy "Domain Admins 16 Character Policy" | Select-Object Name, Precedence, MinPasswordLength
Get-ADFineGrainedPasswordPolicySubject "Domain Admins 16 Character Policy"
Get-ADOptionalFeature "Recycle Bin Feature" | Select-Object -ExpandProperty EnabledScopes
```

**Why this tool:** PowerShell was used for bulk operations because manual console work is slow and error-prone at scale. It was also used for FSMO transfer when the graphical transfer failed, and for all verification steps — a command completing without errors does not confirm the change succeeded; querying the directory state does.

---

## repadmin

**Used for:** Checking replication health between `DC1` and `DC2` after promotion, and forcing synchronization when needed.

**Key commands:**

```powershell
repadmin /replsummary    # summarize replication status across DCs
repadmin /syncall /AdeP  # push synchronization across all naming contexts and sites
```

**Why this tool:** Replication health must be confirmed before transferring FSMO roles. `repadmin` is the standard command-line tool for inspecting and managing AD replication.

---

## netdom

**Used for:** Verifying FSMO role holders after the RID Master transfer.

**Key command:**

```powershell
netdom query fsmo
```

**Why this tool:** `netdom query fsmo` provides a quick readable summary of all five FSMO role holders across the domain and schema. Used as a confirmation step after the PowerShell transfer command completed.