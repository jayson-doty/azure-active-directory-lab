Cloud-Hosted Active Directory & Identity Management Infrastructure Lab
📌 Project Overview & Objectives
🔍 Business Case & Purpose
In an enterprise environment, managing user accounts, permissions, and security policies on a machine-by-machine basis is impossible. This project replicates a real-world enterprise infrastructure deployment by establishing a centralized identity management ecosystem.
Using Microsoft Azure as the infrastructure platform, this lab builds a corporate Active Directory domain from scratch, establishes structured directory topologies, automates administrative overhead via scripting, and validates secure network authentication.
---
🛠️ Core Technology Stack
Cloud Platform: Microsoft Azure
Operating Systems: Windows Server 2022 Datacenter, Windows 10/11 Pro Enterprise Client
Directory Services: Active Directory Domain Services (AD DS), Integrated Domain Name System (DNS)
Automation & Scripting: PowerShell (CLI Administrative Shell)
Networking Architecture: Azure Virtual Networks (VNets), Network Security Groups (NSGs), Private Static IP Provisioning
---
🎓 Engineering Competencies Demonstrated
Deployed cloud-hosted infrastructure with strict cost-mitigation and resource life-cycle management.
Configured enterprise-grade identity boundaries and access control vectors (IAM).
Designed scalable, industry-standard Organizational Unit (OU) naming conventions and structures.
Leveraged command-line automation to handle high-volume administrative tasks, minimizing manual operational errors.
Engineered secure, end-to-end client-to-server directory authentication pipelines.
---
[PRO TIP: Once you finish the lab, this is a great place to put a screenshot of your finished network topology map or your cloud resource dashboard!]

☁️ Step 1: Infrastructure Provisioning & Network Configuration
⚙️ Implementation Phase
Virtual Network Deployment: Created an isolated Virtual Network (VNet) in Azure with a dedicated `10.0.0.0/16` address space to serve as the corporate network perimeter.
Static IP Allocation: Provisioned the primary Virtual Machine (`DC-01`) running Windows Server 2022 Datacenter and explicitly assigned it a static internal IP address of `10.0.0.4`. This guarantees that directory and DNS services remain anchored to a permanent network location.
Network Security Subnetting: Configured an explicit backend subnet (`10.0.0.0/24`) to separate enterprise resources from the public-facing internet interface.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open your \\\*\\\*Azure Portal\\\*\\\*, navigate to your Virtual Machines dashboard, and take a snippet showing your `DC-01` server running with its Private IP address explicitly showing as `10.0.0.4`.
![Azure Virtual Machine Dashboard](images/azure_vm_provision.png)
(Note: Replace "images/azure_vm_provision.png" with your actual screenshot link later!)
🗄️ Step 2: AD DS Installation & Domain Controller Promotion
⚙️ Implementation Phase
Directory Services Deployment: Utilized Windows Server Manager to install the Active Directory Domain Services (AD DS) role alongside integrated Domain Name System (DNS) features.
Forest Infrastructure Creation: Promoted the server to a primary Domain Controller, establishing a completely new Active Directory forest root domain named `mydomain.local` (or your custom domain name).
Enterprise Identity Anchoring: Initiated the core database creation (`NTDS.dit`), SYSVOL folder structure, and schema initialization required to manage secure enterprise-wide authentication requests.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open your \\\*\\\*Server Manager\\\*\\\* inside your Remote Desktop session. Take a tight snippet of the main dashboard showing the \\\*\\\*AD DS\\\*\\\* and \\\*\\\*DNS\\\*\\\* roles listed in the left-hand sidebar with green status indicators.
![Active Directory Role Installation](images/ad_ds_installed.png)
👑 Step 3: Domain Controller Promotion & Forest Initialization
⚙️ Implementation Phase
Active Directory Forest Promotion: Executed the promotion phase to establish `DC-01` as the root domain controller for the `mydomain.local` forest namespace.
Directory Services Restore Mode (DSRM): Configured secure administrative backup and recovery credentials to ensure database integrity in the event of offline directory maintenance.
NetBIOS Configuration: Validated NetBIOS domain name propagation (`MYDOMAIN`) to ensure legacy protocol compatibility across the network architecture.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open your \\\*\\\*Active Directory Users and Computers\\\*\\\* administrative tool (`dsa.msc`), expand your domain root name, click on the \\\*\\\*Domain Controllers\\\*\\\* organizational folder, and snap a screenshot showing `DC-01` explicitly listed as a Global Catalog server.
![Domain Controller Folder Verification](images/dc_promotion_verify.png)
📁 Step 4: Organizational Unit (OU) Architecture & Design
⚙️ Implementation Phase
Enterprise Structural Design: Designed and deployed a customized, logical Organizational Unit (OU) hierarchy to replicate a standard corporate infrastructure.
Administrative Segmentation: Created a master production root OU (`\\\_Production`) and built nested departmental OUs, including `\\\_Employees`, `\\\_Admins`, and `\\\_Workstations`.
Security & GPO Readiness: Strategically isolated administrative assets from standard end-user accounts to lay the groundwork for targeted Group Policy Object (GPO) application and delegated administrative rights.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open \\\*\\\*Active Directory Users and Computers\\\*\\\* (`dsa.msc`). Expand your domain and highlight your custom `\\\_Production` OU folder structure in the left-hand tree panel so your entire nested directory hierarchy is cleanly visible.
![Active Directory OU Structural Design](images/ou_structure_design.png)
👥 Step 5: Automated Bulk User Provisioning via PowerShell
⚙️ Implementation Phase
Administrative Automation: Leveraged PowerShell scripting to eliminate manual account creation overhead, optimizing deployment scaling for large-scale enterprise environments.
CSV Data Ingestion: Developed/executed a script to parse an external mock employee identity database (`names.csv`), extracting attributes such as first name, last name, and department.
Standardized Identity Generation: Programmatically generated standardized User Principal Names (UPNs), secure default password complexities, and automatically sorted accounts into their respective destination OUs.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open your \\\*\\\*Active Directory Users and Computers\\\*\\\* tool, navigate inside your `\\\_Employees` OU, and take a snippet showing the directory populated with dozens of newly script-generated user accounts.
![Bulk Scripted User Creation Validation](images/bulk_user_creation.png)
🛡️ Step 6: Security Group Architecture & Role-Based Access Control (RBAC)
⚙️ Implementation Phase
Security Boundary Management: Implemented Role-Based Access Control (RBAC) by provisioning explicit Security Groups (e.g., `HR\\\_Modified`, `IT\\\_Admins`) within the directory structure.
Access Scope Alignment: Structured group objects to match organizational security boundaries, ensuring network resources conform strictly to departmental data isolation mandates.
Scalable Identity Management: Standardized account permission management by assigning access rights solely to group containers rather than individual user nodes, minimizing permission creep.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open \\\*\\\*Active Directory Users and Computers\\\*\\\* (`dsa.msc`), navigate to your custom security groups container, double-click one of your groups (like an admin or HR group), and take a screenshot of the \\\*\\\*Members\\\*\\\* tab showing users assigned to it.
![Security Group Membership Verification](images/security_groups_rbac.png)
💻 Step 7: Client Infrastructure & Network Layer Integration
⚙️ Implementation Phase
Workstation Provisioning: Deployed an isolated Windows client virtual machine within the managed Azure Virtual Network topology to simulate an end-user workstation environment.
DNS Subnet Alignment: Manually configured the client workstation's primary DNS address to target the static private IP of `DC-01` (`10.0.0.4`). This network-layer configuration ensures the client can accurately resolve the enterprise domain forest namespace.
Domain Integration: Executed the formal domain join process, securely authenticating client hardware to the `mydomain.local` forest via central Active Directory credentials.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open the \\\*\\\*Settings > About\\\*\\\* or \\\*\\\*System Properties\\\*\\\* panel on your newly joined Windows client machine. Take a crisp screenshot showing the computer's name alongside its active membership inside your custom domain forest.
![Workstation Domain Join Verification](images/client_domain_join.png)
🔐 Step 8: Authentication Validation & Identity Lifecycle Testing
⚙️ Implementation Phase
End-to-End Login Verification: Validated directory authentication stability by executing an initial interactive logon to the client workstation using a script-generated end-user account.
Profile Provisioning: Supervised the initialization of local user profile parameters, verifying that the client workstation successfully requested and cached directory settings from the Domain Controller.
Network Connectivity Audit: Conducted standard post-logon diagnostic checks (`whoami /domain`) to verify the security identifier context and confirm the system recognizes the managed account boundary.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Logged into your Windows client machine, open the \\\*\\\*Command Prompt\\\*\\\*, type `whoami` or `set user`, and hit enter. Take a screenshot showing your custom domain name and the username of the script-generated account you logged in with.
![Client Authentication Validation](images/client_login_validation.png)
🌟 Step 9: Advanced Operations, GPOs, & Help Desk Lifecycle Management
⚙️ Implementation Phase
Group Policy Object (GPO) Deployment: Designed and linked custom Group Policies across specific Organizational Units to enforce corporate security compliance (e.g., configuring automatic lock screen timeouts and deploying standard desktop backgrounds).
Enterprise Storage Integration: Provisioned a centralized file server directory share, configured explicit NTFS share permissions, and engineered a logon script/GPO policy to automatically map a shared network drive (`Z:`) on client workstations.
Help Desk Operational Simulation: Simulated real-world Tier 1 help desk ticketing lifecycles by executing administrative interventions: manually locking/unlocking user profiles, forcing next-logon password resets, and modifying security group scopes.
---
📸 REQUIRED SCREENSHOT PLACEMENT
> \\\*\\\*What to capture:\\\*\\\* Open your \\\*\\\*Group Policy Management Console\\\*\\\* (`gpmc.msc`) on the Domain Controller showing your custom policy linked to an OU, \\\*\\\*OR\\\*\\\* open "File Explorer" on your Windows client machine showing your successfully mapped network folder share.
![Group Policy and Storage Verification](images/gpo_and_share_validation.png)
🛑 Step 10: Resource Lifecycle & Cloud Cost Optimization
⚙️ Implementation Phase
Cloud Resource Deletion: Executed proper decommissioning protocols for all active cloud assets, safely shutting down and purging the Azure Resource Group containing `DC-01`, the client workstation, and the isolated virtual network.
Cost Mitigation Management: Implemented best-practice cloud financial hygiene by ensuring no idle compute resources or unattached storage volumes remained active, eliminating ongoing subscription expenditures.
---
📈 Skills Demonstrated & Portfolio Conclusion
Through this 11-stage project, I have successfully demonstrated the ability to provision infrastructure within modern cloud architectures, engineer secure enterprise identity topologies, leverage PowerShell automation to minimize manual configuration drift, and implement standard administrative operations essential to supporting a corporate environment.
