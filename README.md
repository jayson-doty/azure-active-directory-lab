# Hybrid Cloud Active Directory Domain Services (AD DS) Lab

This repository documents the end-to-end planning, deployment, and administration of a secure, production-grade **Active Directory Domain Services (AD DS)** environment. Hosted on **Azure virtual infrastructure**, this project simulates a real-world multi-site enterprise network, implementing industry best practices for identity and access management (IAM), logical organizational unit (OU) design, group policy enforcement, and administrative delegation.

---

## 🛠️ Project Architecture & Design

### 1. Topology & Components
The environment is composed of two primary virtual machines deployed on the same Azure Virtual Network (VNet) to ensure seamless local routing and DNS resolution:

*   **Domain Controller (DC01):** Windows Server 2022 Datacenter promoted to the forest root domain controller for `lab.local`. Configured with a static private IP to maintain DNS and authentication stability.
*   **Domain Client (CLIENT01):** Windows Server 2022 Datacenter configured to behave as a standard domain-joined workstation, routed through the Domain Controller for primary DNS.

```
                  ┌──────────────────────────────────────────┐
                  │            Azure Virtual VNet            │
                  └────────────────────┬─────────────────────┘
                                       │
                  ┌────────────────────┴─────────────────────┐
                  ▼                                          ▼
       ┌────────────────────┐                     ┌────────────────────┐
       │   DC01 (WS 2022)   │                     │ CLIENT01 (WS 2022) │
       │  Domain Controller │                     │    Domain Client   │
       │    [lab.local]     │                     │                    │
       │                    │                     │   DNS pointing     │
       │ Static Private IP  │◄────────────────────┤   to DC01 IP       │
       └────────────────────┘  Domain Join & Auth └────────────────────┘
```

### 2. Logical Organizational Unit (OU) & Security Group Structure
The logical design mirrors enterprise deployments by organizing objects around **policy and delegation boundaries** (location-based) rather than flat lists:

*   **`_Branches` (Top-Level OU):** Houses branch-specific locations.
    *   **`Houston` (Branch OU):** Represens a physical site.
        *   **`Users` (Sub-OU):** Isolates branch-specific user accounts.
        *   **`Workstations` (Sub-OU):** Isolates branch desktop assets.
        *   **`Laptops` (Sub-OU):** Isolates branch mobile assets.
*   **`_Groups` (Centralized Top-Level OU):** Centralizes all global security groups to maintain an efficient, scalable, and auditable role-based access control (RBAC) structure.
    *   **`Helpdesk` (Global Security Group):** For tier-1 helpdesk personnel.
    *   **`Accounting` (Global Security Group):** For financial and accounting personnel.
    *   **`ITSupport` (Global Security Group):** For systems administration and escalation teams.

---

## 🚀 Step-by-Step Implementation

### Phase 1: Virtual Infrastructure Provisioning (Azure)
1. Deployed **DC01** as a Windows Server 2022 Datacenter VM using a cost-efficient size (e.g., Standard B2s) and standard SSD OS storage.
2. In the **Networking Tab**, established a new virtual network and subnets, allowing inbound **RDP (3389)** on the network security group (NSG) for management access.
3. **Critical Networking Step:** Navigated to the network interface (NIC) properties of **DC01** in the Azure Portal and changed the **Private IP assignment** from **Dynamic** to **Static** to prevent local DNS and authentication breaks.

### Phase 2: Installing AD DS & Domain Promotion
1. RDP'ed into the local administrator session of **DC01** and launched **Server Manager**.
2. Ran the **Add Roles and Features** wizard to install the **Active Directory Domain Services (AD DS)** role and management binaries.
3. Initiated the **Promotion Wizard** from the Server Manager notification banner:
    *   Created a brand new Active Directory forest with the root domain name: `lab.local`.
    *   Enabled **DNS Server** and **Global Catalog (GC)** roles.
    *   Configured a secure **Directory Services Restore Mode (DSRM)** password for emergency offline recovery.
4. Rebooted the server and logged in using the newly promoted domain administrator account (`LAB\Administrator`).

### Phase 3: Directory Structure Creation & User Provisioning
To demonstrate mastery of administrative tools, the directory structure was built utilizing both the Active Directory Users and Computers (ADUC) GUI and automated PowerShell scripts.

#### 1. Directory Structure Execution
*   Created top-level OUs: `_Branches` and `_Groups`.
*   Nested the location OU (`Houston`) and branch sub-OUs (`Users`, `Workstations`, `Laptops`) with **Protect container from accidental deletion** enabled to prevent administrative mishaps.

#### 2. User & Group Provisioning Script (PowerShell)
Automated the creation of departments, security groups, and user accounts. The following PowerShell script was executed on **DC01** to provision the directory schema:

```powershell
# Define Target Paths
$ouUsers = "OU=Users,OU=Houston,OU=_Branches,DC=lab,DC=local"
$ouGroups = "OU=_Groups,DC=lab,DC=local"

# 1. Create Global Security Groups
New-ADGroup -Name "Helpdesk" -GroupScope Global -GroupCategory Security -Path $ouGroups
New-ADGroup -Name "Accounting" -GroupScope Global -GroupCategory Security -Path $ouGroups
New-ADGroup -Name "ITSupport" -GroupScope Global -GroupCategory Security -Path $ouGroups

# 2. Provision Users within Location-Specific OUs
New-ADUser -Name "Alice Johnson" -GivenName Alice -Surname Johnson -SamAccountName ajohnson -Path $ouUsers -AccountPassword (Read-Host -AsSecureString) -Enabled $true
New-ADUser -Name "Bob Martinez" -GivenName Bob -Surname Martinez -SamAccountName bmartinez -Path $ouUsers -AccountPassword (Read-Host -AsSecureString) -Enabled $true
New-ADUser -Name "Chris Walker" -GivenName Chris -Surname Walker -SamAccountName cwalker -Path $ouUsers -AccountPassword (Read-Host -AsSecureString) -Enabled $true

# 3. Establish Role-Based Group Membership
Add-ADGroupMember -Identity "Helpdesk" -Members ajohnson
Add-ADGroupMember -Identity "Accounting" -Members bmartinez
Add-ADGroupMember -Identity "ITSupport" -Members cwalker
```

---

## 🔒 Client VM Integration & Authentication Testing

### 1. DNS Configuration
Before joining the domain, the network interface of **CLIENT01** was configured to point directly to **DC01**'s static private IP as its **Preferred DNS Server**. Without this step, the client would be unable to locate the domain controller's SRV records.

### 2. Domain Joining
*   Navigated to **Settings → System → About** on **CLIENT01** and selected **Join a domain**.
*   Specified `lab.local` as the target domain, authenticated using domain administrator credentials, and completed the required system reboot.
*   Once joined, verified that **CLIENT01**'s computer object automatically populated in the default Active Directory **Computers** container.

### 3. Remote Desktop Access Delegation
To test client-side authentication via Remote Desktop Protocol (RDP) using standard non-administrative domain credentials, the AD groups were added to the client machine's local security group:

```powershell
# Grant RDP rights to Domain Security Groups on CLIENT01
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "LAB\Helpdesk"
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "LAB\Accounting"
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "LAB\ITSupport"
```

### 4. Authentication and Token Verification
Logged into **CLIENT01** via Remote Desktop as `LAB\ajohnson`. To prove successful authentication and group policy/token evaluation, a command prompt was opened and the following diagnostic command was run:

```cmd
whoami /groups
```

**Verification Proof:** The command successfully outputted active security identifiers (SIDs) showing membership in `LAB\Helpdesk` and `LAB\Domain Users`. This confirms that Kerberos ticket issuance, AD authentication, and security token builds are fully operational across the network.

---

## 🛡️ Enterprise Hardening & Administration (Bonus Work)

To demonstrate advanced sysadmin competencies, several infrastructure enhancements were performed:

### 1. Password Policy Enforcement (GPMC)
Modified the **Default Domain Policy** via the **Group Policy Management Console (GPMC)** to enforce baseline organization security standards:
*   **Path:** `Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`
*   Configured key variables: **Maximum password age** and **Minimum password length** to secure domain identities against brute force attacks.

### 2. SYSVOL logon Scripting
Configured a legacy/hybrid-style automation script to prove understanding of directory-wide file replication:
*   Created a standard `logon.bat` script containing:
    ```cmd
    @echo off
    echo Welcome to the domain
    ```
*   Placed the file inside the replicated domain folder: `C:\Windows\SYSVOL\sysvol\lab.local\scripts`.
*   Assigned `logon.bat` to the **Logon script** attribute on user account profiles in ADUC, enabling automatic policy retrieval from the SYSVOL share during session launch.

### 3. Helpdesk Administrative Delegation
Enforced the **Principle of Least Privilege (PoLP)** by delegating specific domain controls without granting full Domain Admin permissions:
*   Right-clicked the branch `Users` OU under `_Branches → Houston`.
*   Launched the **Delegation of Control Wizard** and targeted the **Helpdesk** global security group.
*   Delegated permissions exclusively to:
    *   **Reset user passwords**
    *   **Force password change at next logon**
*   This ensures the Helpdesk group can handle user management tickets for Houston users without full administrator privileges.

### 4. Active Directory Asset Lifecycle Management
Moved **CLIENT01**'s computer account out of the generic, unmanageable default **Computers** container and placed it within the structured branch-specific workspace OU: `_Branches → Houston → Workstations`. This guarantees that location-targeted Group Policies apply correctly to the device.

---

## 🎓 Core Competencies Demonstrated
*   **Directory Services Management:** Active Directory Domain Services (AD DS) design and schema management.
*   **Cloud Infrastructure Administration:** Deployed and linked multiple cloud assets on Azure virtual networks using static IP schemes and NSG controls.
*   **Scripting & Automation:** Formulated and deployed PowerShell cmdlets for directory manipulation and account generation.
*   **Identity & Access Management (IAM):** Established Role-Based Access Control (RBAC), Global Security Group structures, and user assignments.
*   **Security & Hardening:** Designed Group Policies, password rules, administrative delegation (least privilege), and logon triggers.
*   **Enterprise Troubleshooting:** Root-caused DNS resolution issues, Remote Desktop user permissions, and AD authentication flows.
