Cloud-Hosted Active Directory & Identity Management Infrastructure Lab
📌 Project Overview & Objectives
🔍 Business Case & Purpose

Managing users and security policies machine-by-machine doesn't work in an enterprise environment. It is impossible to scale. This lab solves that problem by building a centralized identity management ecosystem from scratch.

Using Microsoft Azure, I set up a corporate Active Directory domain, designed a structured directory topology, used PowerShell to automate repetitive administration, and proved the whole thing worked by authenticating network clients.
🛠️ Core Technology Stack

    Cloud Platform: Microsoft Azure

    Operating Systems: Windows Server 2022 Datacenter, Windows 10/11 Pro Enterprise Client

    Directory Services: Active Directory Domain Services (AD DS), Integrated Domain Name System (DNS)

    Automation & Scripting: PowerShell (CLI Administrative Shell)

    Networking Architecture: Azure Virtual Networks (VNets), Network Security Groups (NSGs), Private Static IP Provisioning

🎓 Engineering Competencies Demonstrated

    Deployed cloud infrastructure while keeping an eye on costs and resource lifecycles.

    Built secure identity boundaries and managed access controls (IAM).

    Designed clean, scalable Organizational Unit (OU) structures using industry naming standards.

    Used command-line automation to spin up accounts fast, cutting out human error.

    Connected client machines to servers to build a secure authentication pipeline.

[PRO TIP: Once you finish the lab, this is a great place to put a screenshot of your finished network topology map or your cloud resource dashboard!]
☁️ Step 1: Infrastructure Provisioning & Network Configuration
⚙️ Implementation Phase

    Virtual Network Deployment: I spun up an isolated Virtual Network (VNet) in Azure with a 10.0.0.0/16 address space. This acts as our secure network boundary.

    Static IP Allocation: I provisioned DC-01, a Windows Server 2022 Datacenter VM, and locked it down with a static internal IP of 10.0.0.4. DNS and directory services need a permanent home to work properly.

    Network Security Subnetting: I carved out a backend subnet (10.0.0.0/24) to keep these corporate resources safe from the public internet.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Head over to your Azure Portal, open the Virtual Machines dashboard, and grab a screenshot showing your DC-01 server running with its Private IP address explicitly set to 10.0.0.4.

🗄️ Step 2: AD DS Installation & Domain Controller Promotion
⚙️ Implementation Phase

    Directory Services Deployment: I used Windows Server Manager to install the Active Directory Domain Services (AD DS) role and integrated DNS.

    Forest Infrastructure Creation: I promoted the server to a primary Domain Controller. This created a brand-new Active Directory forest root domain named mydomain.local.

    Enterprise Identity Anchoring: This process generated the core NTDS.dit database, set up the SYSVOL folder structure, and initialized the schema needed to handle authentication requests across the network.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Open up Server Manager inside your RDP session. Take a quick snippet of the main dashboard showing both the AD DS and DNS roles sitting in the left sidebar with healthy green checkmarks.

👑 Step 3: Domain Controller Promotion & Forest Initialization
⚙️ Implementation Phase

    Active Directory Forest Promotion: I finished the promotion steps to establish DC-01 as the root authority for the mydomain.local forest namespace.

    Directory Services Restore Mode (DSRM): I set up secure administrator backup and recovery credentials. If the database ever goes sideways, I can fix it offline.

    NetBIOS Configuration: I checked that the NetBIOS domain name (MYDOMAIN) broadcasted properly so older protocols can still talk to the network.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Open Active Directory Users and Computers (dsa.msc), expand your domain root, and click on the Domain Controllers folder. Take a screenshot showing DC-01 explicitly listed as a Global Catalog server.

📁 Step 4: Organizational Unit (OU) Architecture & Design
⚙️ Implementation Phase

    Enterprise Structural Design: I mapped out and built a clean Organizational Unit (OU) tree that mirrors what you'd see in a real corporate environment.

    Administrative Segmentation: I created a main root OU called _Production and tucked nested OUs underneath it, specifically _Employees, _Admins, and _Workstations.

    Security & GPO Readiness: Keeping admins separated from regular users makes it much easier to apply targeted Group Policy Objects (GPOs) and delegate rights later.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Open Active Directory Users and Computers (dsa.msc). Expand your domain and click on your custom _Production OU structure in the left panel so the whole nested tree is visible.

👥 Step 5: Automated Bulk User Provisioning via PowerShell
⚙️ Implementation Phase

    Administrative Automation: Creating hundreds of accounts by hand is a waste of time. I used a PowerShell script to handle it automatically, making the deployment instantly scalable.

    CSV Data Ingestion: The script reads a mock employee list from a database file (names.csv), pulling out details like first names, last names, and departments.

    Standardized Identity Generation: The code automatically generated standard User Principal Names (UPNs), set up complex default passwords, and dumped each user into the correct OU.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Open Active Directory Users and Computers, look inside your _Employees OU, and take a snippet of the directory filled with the dozens of new accounts your script just built.

🛡️ Step 6: Security Group Architecture & Role-Based Access Control (RBAC)
⚙️ Implementation Phase

    Security Boundary Management: I set up Role-Based Access Control (RBAC) by building specific Security Groups, like HR_Modified and IT_Admins.

    Access Scope Alignment: I organized these groups to match corporate data boundaries. People only get access to what their department allows.

    Scalable Identity Management: Managing permissions at the group level instead of the user level keeps things clean and stops permission creep over time.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Go to your security groups folder in Active Directory Users and Computers (dsa.msc). Double-click an admin or HR group, open the Members tab, and snap a screenshot showing the users assigned to it.

💻 Step 7: Client Infrastructure & Network Layer Integration
⚙️ Implementation Phase

    Workstation Provisioning: I spun up a separate Windows client VM inside the same Azure VNet to act as our end-user workstation.

    DNS Subnet Alignment: I changed the client's DNS settings to point directly to the static IP of DC-01 (10.0.0.4). Without this, the workstation won't find the domain.

    Domain Integration: I ran through the formal domain join, authenticating the client machine against the mydomain.local forest using domain credentials.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Open Settings > About or the System Properties window on your Windows client machine. Take a clear screenshot showing the computer name and its active membership in your custom domain.

🔐 Step 8: Authentication Validation & Identity Lifecycle Testing
⚙️ Implementation Phase

    End-to-End Login Verification: To prove the setup works, I logged into the client workstation using one of the fake employee accounts generated by my script.

    Profile Provisioning: The workstation successfully talked to the Domain Controller, pulled down the necessary directory configurations, and built a local profile.

    Network Connectivity Audit: I ran whoami /domain in the command line to verify that the workstation completely recognizes the new domain boundary.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: While logged into the Windows client machine as a domain user, open the Command Prompt, type whoami or set user, and press enter. Take a screenshot showing the domain name alongside the active user.

🌟 Step 9: Advanced Operations, GPOs, & Help Desk Lifecycle Management
⚙️ Implementation Phase

    Group Policy Object (GPO) Deployment: I built and linked custom Group Policies to enforce corporate rules. For example, I set up automatic lock screens and standard desktop wallpapers across specific OUs.

    Enterprise Storage Integration: I created a shared network folder on a file server, locked down permissions with NTFS, and used a logon script/GPO to map it as the Z: drive automatically when users log in.

    Help Desk Operational Simulation: I ran through common Tier 1 help desk tasks to simulate daily operations. This included unlocking locked accounts, forcing password resets at next login, and updating security group memberships.

📸 REQUIRED SCREENSHOT PLACEMENT

    What to capture: Open the Group Policy Management Console (gpmc.msc) on your Domain Controller showing your policy linked to an OU, OR open File Explorer on the client machine to show the successfully mapped Z: drive.

🛑 Step 10: Resource Lifecycle & Cloud Cost Optimization
⚙️ Implementation Phase

    Cloud Resource Deletion: I tore down the infrastructure. I deleted the entire Azure Resource Group containing DC-01, the client machine, and the virtual network.

    Cost Mitigation Management: Cleaning up avoids surprise bills. No idle virtual machines or unattached storage disks were left running.

📈 Skills Demonstrated & Portfolio Conclusion

This 10-step project shows I can build cloud-hosted infrastructure from the ground up. I configured a secure enterprise identity setup, cut down manual work with PowerShell automation, and handled the everyday administrative tasks required to keep a corporate environment running smoothly.
