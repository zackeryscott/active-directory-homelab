Step 0 — Prerequisites  

Overview 

Before building the Active Directory home lab, we first need to download a few tools and operating system images. In this lab we’ll be using virtualization to simulate a small enterprise network that includes a domain controller, multiple servers, and a client workstation. 

Virtualization allows us to run multiple operating systems on a single computer. This makes it possible to safely test and configure enterprise infrastructure in a controlled lab environment without needing multiple physical machines. 

 

 

Required Software 

The following software and operating systems will be used throughout this lab: 

Virtualization Platform 

Oracle VM VirtualBox 
VirtualBox will be used to create and manage the virtual machines that make up the lab environment. 

Operating Systems 

Windows Server 2025 

This operating system will be used for the following machines: 

Domain Controller 

Member Server 

Server Core system 

Windows 11 

Windows 11 will be used for the client workstation that will eventually join the Active Directory domain. 

 

  

Downloads 

Download and install the following components before starting the lab. 

VirtualBox Installer 
(https://www.virtualbox.org/wiki/Downloads) 

Windows Server 2025 Evaluation ISO 
(https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2025) 

Windows 11 ISO 
(https://www.microsoft.com/en-us/software-download/windows11) 

 

 

Lab Environment Components 

This home lab will consist of four virtual machines that simulate a simplified corporate network. 

GA-DC-01 
Primary Domain Controller and DNS Server 

GA-SVR1 
Member Server joined to the domain 

GA-Core 
Windows Server Core system used for command-line server administration 

GA-Client 

Windows 11 workstation that will join the domain and authenticate with Active Directory 

 

 

 

Purpose of the Lab 

This environment will be used to practice several core Windows infrastructure skills, including: 

Installing Active Directory Domain Services 

Configuring DNS 

Setting static IP addresses 

Deploying a domain controller 

Domain authentication 

Administering Windows Server Core 

Joining systems to a Windows domain 

These are foundational tasks commonly performed by system administrators and IT support professionals in enterprise environments. 

 

 

 

Lab Architecture 

 

 

 

 

Step 1 – Creating the Virtual Machines 

Overview 

The first stage of building the home lab environment is creating the virtual machines that will simulate the servers and workstation used in a typical enterprise network. 

Using VirtualBox, we will create four virtual machines that represent the core infrastructure of the lab environment: 

• Domain Controller 

• Member Server 

• Server Core system 

• Client workstation 

Each machine will later receive a static IP addresses and be joined to the Active Directory domain. 

 

 

Creating the Virtual Machines 

Open VirtualBox and create each virtual machine using the New option. 

When creating each machine, assign the appropriate name and operating system based on the ISO file being used. 

 

The three server machines will use the Windows Server ISO, while the client machine will use the Windows 11 ISO. 

 

 

Recommended Virtual Machine Resources 

To ensure the machines run smoothly, the following resource allocation is recommended. 

Server Machines 

Processors: 2 CPUs 

Memory: 4 GB RAM 

Storage: 60 GB virtual disk 

Client Machine 

Processors: 2 CPUs 

Memory: 4 GB RAM 

Storage: 50 GB virtual disk 

These settings provide enough resources for the lab while still being manageable on most host systems. 

 

 

Installing the Operating Systems 

After creating the virtual machines, attach the appropriate ISO file to each machine and start the installation process. 

 

For the three server machines, install Windows Server. 

 

 

During installation: 

Select Server with Desktop Experience for GA-DC-01 and GA-SVR1 

Do not select Desktop Experience for GA-Core 

 

For the client machine, I'll be installing Windows 11 using the standard installation process. 

 

Once installation is complete, allow each machine to boot fully before moving on to the next step. 

 

 

 

 

Step 2 – Configuring the Virtual Network 

Overview 

Before configuring IP addresses or installing Active Directory, the virtual machines must be connected to the same network so they can communicate with each other. 

VirtualBox offers several networking options. For this lab we will use an Internal Network, which allows the virtual machines to communicate with each other while remaining isolated from my host computer’s physical network. 

This setup simulates a small internal corporate network. 

 

 

Network Configuration 

All virtual machines in this lab will be connected to the same internal network. 

Network Mode: Internal Network 

Network Name: HomelabNet 

The following machines must be connected to this network: 

GA-DC-01 

GA-SVR1 

GA-Core 

GA-Client 

Using the same network name ensures that all machines are placed on the same virtual network segment. 

 

 

Configuring the Network Adapter 

For each virtual machine: 

Power off the virtual machine if it is currently running. 

Open the VirtualBox Manager. 

Select the virtual machine. 

Click Settings. 

Navigate to the Network section. 

Under Adapter 1, enable the network adapter. 

Set Attached To to Internal Network. 

Enter the network name HomelabNet. 

Click OK to save the configuration. 

Repeat this process for all four virtual machines. 

 

Purpose of the Internal Network 

Using an internal network provides several advantages for a lab environment: 

• Allows all virtual machines to communicate with one another 

• Simulates an isolated corporate network environment 

• Prevents interference with the host computer’s home network 

• Provides a controlled environment for testing Active Directory 

Once the virtual network is configured, we can move on to assigning static IP addresses.  

 

 

 

Step 3 – Configuring Static IP Addressing 

Overview 

In most real-world environments, servers use static IP addresses rather than dynamically assigned addresses from DHCP. This ensures that important services like Active Directory and DNS are always reachable at a known address. 

In this lab, each virtual machine will be assigned a static IP address within the 192.168.1.0/24 network. 

The Domain Controller will also serve as the DNS server for the environment. 

 

 

Network Addressing Plan 

GA-DC-01 

Role: Domain Controller and DNS Server 

IP Address: 192.168.1.250 

Subnet Mask: 255.255.255.0 

Default Gateway: 192.168.1.1 

Preferred DNS: 127.0.0.1 

GA-SVR1 

Role: Member Server 

IP Address: 192.168.1.251 

Preferred DNS: 192.168.1.250 

GA-Core 

Role: Server Core System 

IP Address: 192.168.1.252 

Preferred DNS: 192.168.1.250 

GA-Client 

Role: Workstation 

IP Address: 192.168.1.100 

Preferred DNS: 192.168.1.250 

 

 

Configuring Static IP Addresses (Windows Server) 

For GA-DC-01, GA-SVR1,  I will configure the static IP address using Server Manager. 

Open Server Manager. 

Select Local Server. 

Click the Ethernet link 

Right-click the Ethernet adapter and select Properties. 

Select Internet Protocol Version 4 (TCP/IPv4). 

Click Properties. 

Choose Use the following IP address. 

Here I will enter the IP address configuration(s) based on the network addressing plan provided above. 

Click OK. 

I'll repeat this process for GA-SVR1 using the respective IP address. 

 

 

Configuring Static IP Addresses (Windows 11) 

For the client machine: 

Open Control Panel. 

Select Network and Internet. 

Click Network and Sharing Center. 

Select Change adapter settings. 

Right-click the Ethernet adapter and select Properties. 

Select Internet Protocol Version 4 (TCP/IPv4). 

Click Properties. 

Choose use the following IP address. 

Here I will enter the IP address configuration based on the addressing plan for the lab environment. 

Click OK. 

 

Set the DNS server to the Domain Controller so the client can locate the domain later. 

 

 

Configuring Static IP on Server Core 

Because GA-Core runs Server Core without a graphical interface, networking is configured using the sconfig utility. 

 

Log into GA-Core.. 

From the main menu, select Option 8 – Network Settings. 

Select the network adaptor number. 

Choose Option 1 – Set Network Adapter Address 

Select S for Static 

Here I will enter the network configuration based on the addressing plan for this lab: 

IP Address: 192.168.1.252 

Subnet Mask: 255.255.255.0 

Default Gateway: 192.168.1.1 

Return to the menu. 

Select Option 2 – Set DNS Servers. 

Here I will enter the DNS server address for the Domain Controller: 

192.168.1.250 

 

 

Verifying Network Connectivity 

Once all IP addresses are configured, I will verify that the machines can communicate by running the p. 

I'll do this by opening a command prompt or PowerShell window and run the following commands from each machine. 

ping 192.168.1.250 

ping 192.168.1.251 

ping 192.168.1.252 

ping 192.168.1.100 

 

Successful replies confirm the systems can communicate across the network. 

 

 

Troubleshooting Network Connectivity 

During testing, the servers initially could not ping each other even though the network configuration was correct. 

The issue turned out to be Windows Firewall blocking ICMP traffic. 

To fix this, the inbound rule File and Printer Sharing (Echo Request – ICMPv4-In) was enabled. 

After enabling the rule, the machines were able to successfully respond to ping requests. 

This change was applied across all virtual machines. 

 

Allowing ICMP (Ping) Through the Firewall 

An inbound firewall rule was created to allow ICMP echo requests: 

On GA-SVR1: 

Open Windows Defender Firewall 

Click Advanced Settings 

Select Inbound Rules 

Enable rule: 

File and Printer Sharing (Echo Request - ICMPv4-In) 

After enabling the rule, the machines were able to successfully respond to ping requests. 

 

This change was applied across all virtual machines except Server Core. For Server Core we simply select option 4 then enable remote management. 

 

 

Verification 

Connectivity was confirmed  by successfully pinging: 

GA-DC-01 → 192.168.1.250 

GA-SVR1 → 192.168.1.251 

GA-Core → 192.168.1.252 

GA-Client → 192.168.1.100 

 

 

 

 

Step 4 — Installing Active Directory Domain Services 

Overview 

Now that the virtual machines are communicating and static IP addresses have been configured, the next step is to install Active Directory Domain Services (AD DS) on the server GA-DC-01. 

Active Directory is used in most enterprise Windows environments to manage users, computers, and resources across a network. Once installed, GA-DC-01 will become the Domain Controller for the lab environment and will also function as the DNS server. 

After this step is completed, the server will host the new domain that the rest of the machines will join later in the lab. 

 

 

Installing the Active Directory Role 

On GA-DC-01, open Server Manager. 

In the top right corner of Server Manager, click Manage. 

Select Add Roles and Features. 

The Add Roles and Features Wizard will open. 

Click Next on the Before You Begin page. 

Select Role-based or feature-based installation. 

Click Next. 

Confirm that GA-DC-01 is selected as the destination server. 

Click Next. 

On the Server Roles page, check the box for: 

Active Directory Domain Services 

A window will appear asking to add required features. Click Add Features. 

Click Next. 

Continue through the wizard by clicking Next until you reach the Confirmation page. 

Click Install to begin installing the Active Directory Domain Services role. 

Once the installation completes, the server will need to be promoted to a Domain Controller. 

 

 

Promoting the Server to a Domain Controller 

After the AD DS role is installed, return to Server Manager. 

You will see a notification flag in the top right corner indicating that additional configuration is required. 

Click the notification flag. 

Select Promote this server to a domain controller. 

The Active Directory Domain Services Configuration Wizard will open. 

Choose Add a new forest. 

I will use the following domain name: 

homelab.local 

Click Next. 

On the Domain Controller Options page: 

Leave the default settings selected. 

Enter a password for Directory Services Restore Mode (DSRM). 

Click Next. 

Continue clicking Next through the remaining configuration pages. 

On the Prerequisites Check page, confirm that all checks pass. 

Click Install. 

The server will automatically restart after the installation completes. 

 

 

Verifying Domain Controller Installation 

After the server finished installing Active Directory and restarted, I logged back into GA-DC-01 to verify that the installation completed successfully. 

To confirm that the server had been promoted to a Domain Controller, I opened Server Manager and checked that the Active Directory services were now installed and running. 

Next, I opened the administrative tool Active Directory Users and Computers. 

Within the console, I was able to see the newly created domain: 

homelab.local 

Seeing the domain structure within this tool confirmed that GA-DC-01 had successfully been promoted to a Domain Controller and that the Active Directory environment was now active. 

 

 

 

Step 5 — Creating Organizational Units and Domain Users 

Overview 

With Active Directory now installed and GA-DC-01 functioning as the Domain Controller, the next step is to begin organizing the domain structure. 

In most enterprise environments, administrators use Organizational Units (OUs) to group objects such as users, computers, and servers. This makes it easier to manage permissions, apply group policies, and organize systems within the domain. 

For this lab, I created several Organizational Units to simulate how systems and users might be organized inside a small company environment. 

 

 

Opening Active Directory Users and Computers 

To begin configuring the domain structure, I opened the Active Directory Users and Computers management console on GA-DC-01. 

This tool is used to manage domain objects such as users, groups, computers, and organizational units. 

After opening the console, I expanded the domain homelab.local to view the default Active Directory structure. 

 

 

Creating Organizational Units 

To organize the environment, I created several Organizational Units inside the domain. 

Right-click the domain homelab.local 

Select New 

Click Organizational Unit 

Enter a name for the OU 

Click OK 

Using this process, I created the following Organizational Units: 

Servers 

Employees 

IT Admins 

These OUs will later be used to organize servers and user accounts within the domain. 

 

 

Creating Domain Users 

Next, I created several user accounts to simulate employees within the environment. 

To create a user account: 

Right-click the Employees Organizational Unit 

Select New 

Click User 

Enter the user’s first name, last name, and username 

Click Next 

Assign a password for the account 

Complete the wizard to create the user 

Using this process, I created the following users: 

josh.smith 

sally.doe 

helpdesk.admin 

 

These accounts will be used later when testing domain authentication and logging into domain-joined machines. 

 

 

Verifying the Organizational Structure 

After creating the Organizational Units and user accounts, I reviewed the structure inside Active Directory Users and Computers to verify everything had been created correctly. 

At this point, the domain structure included the newly created OUs as well as the test user accounts inside the Employees organizational unit. 

This confirms that Active Directory is functioning correctly and that domain objects can be created and managed successfully. 

 

 

 

Step 6 — Joining Systems to the Domain 

Overview 

With Active Directory installed and the domain structure in place, the next step is to join the remaining machines to the domain. 

Joining a computer to a domain allows it to authenticate with Active Directory and be managed centrally by the Domain Controller. Once a system joins the domain, users can log in with domain accounts and administrators can manage the system through Active Directory. 

In this step, I joined the following machines to the domain homelab.local: 

GA-SVR1 

GA-Core 

GA-Client 

 

 

Joining GA-SVR1 to the Domain 

To begin, I joined the member server GA-SVR1 to the domain. 

On GA-SVR1, open Server Manager. 

Select Local Server. 

Click the Workgroup link next to the computer name. 

In the System Properties window, click Change. 

Select Domain. 

Enter the domain name: 

homelab.local 

Click OK. 

When prompted, I entered the domain administrator credentials. 

After the credentials were accepted, the server displayed a message confirming that it had successfully joined the domain. 

The system was then restarted to complete the process. 

 

 

Joining GA-Core to the Domain 

Because GA-Core runs Windows Server Core, the domain join process is completed using the sconfig utility. 

Log into GA-Core. 

From the main menu, select Option 1 — Domain/Workgroup. 

Choose D to join a domain. 

Enter the domain name: 

homelab.local 

When prompted, enter the credentials for the domain administrator account. 

Confirm the changes and allow the server to restart. 

After the restart, GA-Core was successfully joined to the domain. 

 

 

Joining GA-Client to the Domain 

Next, I joined the Windows 11 workstation to the domain. 

On GA-Client, open Control Panel. 

Select System and Security. 

Click System. 

Select Advanced system settings. 

In the Computer Name tab, click Change. 

Select Domain. 

Enter: 

homelab.local 

Click OK and enter the domain administrator credentials when prompted. 

After receiving confirmation that the computer joined the domain successfully, I restarted the machine. 

 

Verifying the Domain Join 

After all systems restarted, I verified that the machines had successfully joined the domain. 

From GA-DC-01, I opened Active Directory Users and Computers and confirmed that the newly joined computers appeared in the domain. 

At this point, the lab environment now includes multiple systems joined to the homelab.local domain. 

Organizing Domain Computers 

After joining the machines to the domain, I moved them into the appropriate Organizational Units to keep the domain structure organized. 

Using Active Directory Users and Computers, I moved the servers into the Servers OU and placed the client workstation in the Computers OU. 

This helps keep servers and workstations separated and reflects how systems are typically organized in many Active Directory environments. 

 

 

 

Step 7 — Testing Domain Authentication 

Overview 

With the servers and client workstation successfully joined to the domain, the final step is to verify that domain authentication is working properly. 

One of the main benefits of Active Directory is centralized authentication. This allows users to log into any domain-joined machine using their domain credentials instead of local accounts. 

In this step, I tested the environment by logging into the client workstation using one of the domain user accounts that was created earlier. 

 

 

Signing in with a Domain User 

To test domain authentication, I logged into the Windows 11 client machine using the domain user account josh.smith. 

On GA-Client, restart or sign out of the system. 

At the login screen, select Other user. 

Enter the domain username using the following format: 

homelab\j.smith 

Enter the password that was assigned to the user account. 

Click Sign in. 

After signing in, Windows created a new user profile for the domain account and loaded the desktop environment. 

 

Verifying the Domain Login 

Once logged in, I confirmed that the user account was authenticated by the domain. 

To verify this, I opened Command Prompt and ran the following command: 

whoami 

The result displayed the following: 

Homelab\j.smith 

This confirms that the system successfully authenticated the user through the homelab.local domain. 

 

 

Confirmation of a Working Domain Environment 

At this point, the Active Directory environment is fully operational. 

The Domain Controller is managing authentication, the servers are joined to the domain, and users are able to sign into domain-joined machines using domain credentials. 

This confirms that the core components of the Active Directory lab environment are functioning correctly. 

 

 

 

Step 8 — Lessons Learned 

Overview 

Building this Active Directory home lab provided hands-on experience with several core Windows infrastructure concepts. Throughout the process, I was able to practice configuring servers, managing network settings, and deploying a basic domain environment similar to what might exist in a small organization. 

Working through the lab step-by-step helped reinforce how the different components of an Active Directory environment work together. 

 

 

Key Concepts Practiced 

During this lab I gained practical experience with several important system administration tasks, including: 

• Creating and managing virtual machines using VirtualBox 

• Configuring internal virtual networks 

• Assigning static IP addresses to servers and clients 

• Installing Active Directory Domain Services 

• Promoting a server to a Domain Controller 

• Creating Organizational Units and domain users 

• Joining systems to an Active Directory domain 

• Authenticating users using domain credentials 

These tasks represent many of the foundational skills used by system administrators when managing Windows-based networks. 

 

 

Troubleshooting Experience 

During the lab setup I encountered a few issues that required troubleshooting. 

One example occurred while testing domain authentication on the Windows 11 client machine. Initially the login attempt failed because the Domain Controller was powered off, preventing the client from contacting the domain for authentication. 

After starting the Domain Controller again, the client was able to successfully authenticate the domain user account. 

This reinforced the importance of ensuring that critical infrastructure services, such as Domain Controllers and DNS servers, are available when systems need to authenticate with the domain. 

 

 

Final Result 

By the end of this lab, I successfully built a small Active Directory environment consisting of a Domain Controller, multiple servers, and a client workstation. 

All systems were able to communicate across the network, join the domain, and authenticate using domain user accounts. 

This project helped me better understand how Windows domain environments are structured and managed in real-world IT environments. 

 

 

Future Improvements 

  

• Implement Group Policy Objects (GPO) 

• Create additional domain users and groups 

• Configure shared folders with permissions 

• Add a second domain controller for redundancy 

• Implement DHCP in the lab environment 

 

   

 
