# Active Directory Homelab — Full Lab Guide

This document provides a full walkthrough of building a small Active Directory homelab using VirtualBox, Windows Server 2025, and Windows 11.

The goal of this lab was to simulate a basic enterprise network environment and gain hands-on experience with:

- Virtual machine deployment
- Internal network configuration
- Static IP addressing
- Active Directory Domain Services
- DNS
- Domain joining
- Domain authentication
- Basic troubleshooting

---

## Lab Architecture

![Active Directory Lab Network](../images/network-diagram.png)

---

# Lab Environment

The lab consists of four virtual machines that simulate a small corporate network.

| Machine | Role | IP Address |
|-------|------|-----------|
| GA-DC-01 | Domain Controller / DNS | 192.168.1.250 |
| GA-SVR1 | Member Server | 192.168.1.251 |
| GA-Core | Windows Server Core | 192.168.1.252 |
| GA-Client | Windows 11 Workstation | 192.168.1.100 |

---

# Table of Contents

- [Step 0 — Prerequisites](#step-0--prerequisites)
- [Step 1 — Creating the Virtual Machines](#step-1--creating-the-virtual-machines)
- [Step 2 — Configuring the Virtual Network](#step-2--configuring-the-virtual-network)
- [Step 3 — Configuring Static IP Addressing](#step-3--configuring-static-ip-addressing)
- [Step 4 — Installing Active Directory Domain Services](#step-4--installing-active-directory-domain-services)
- [Step 5 — Creating Organizational Units and Domain Users](#step-5--creating-organizational-units-and-domain-users)
- [Step 6 — Joining Systems to the Domain](#step-6--joining-systems-to-the-domain)
- [Step 7 — Testing Domain Authentication](#step-7--testing-domain-authentication)
- [Step 8 — Lessons Learned](#step-8--lessons-learned)

---

# Step 0 — Prerequisites

## Overview

Before building the Active Directory home lab, I first needed to download a few tools and operating system images.

In this lab, virtualization is used to simulate a small enterprise network that includes a domain controller, multiple servers, and a client workstation.

Virtualization allows multiple operating systems to run on a single computer, making it possible to safely test and configure enterprise infrastructure in a controlled lab environment without needing multiple physical machines.

---

## Required Software

### Virtualization Platform

**Oracle VM VirtualBox**

VirtualBox is used to create and manage the virtual machines that make up the lab environment.

![VirtualBox Dashboard](../screenshots/step0-virtualbox-dashboard.png)

### Operating Systems

**Windows Server 2025**

This operating system is used for the following machines:

- Domain Controller
- Member Server
- Server Core system

**Windows 11**

Windows 11 is used for the client workstation that will eventually join the Active Directory domain.

---

## Downloads

Download and install the following components before starting the lab.

### VirtualBox Installer

https://www.virtualbox.org/wiki/Downloads

![VirtualBox Download](../screenshots/step0-virtualbox-download.png)

### Windows Server 2025 Evaluation ISO

https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2025

![Windows Server 2025 ISO](../screenshots/step0-windows-server2025.png)

### Windows 11 ISO

https://www.microsoft.com/en-us/software-download/windows11

![Windows 11 ISO](../screenshots/step0-windows11.png)

---

## Lab Environment Components

This homelab consists of four virtual machines that simulate a simplified corporate network.

- **GA-DC-01** — Primary Domain Controller and DNS Server
- **GA-SVR1** — Member Server joined to the domain
- **GA-Core** — Windows Server Core system used for command-line server administration
- **GA-Client** — Windows 11 workstation that will join the domain and authenticate with Active Directory

---

## Purpose of the Lab

This environment is used to practice several core Windows infrastructure skills, including:

- Installing Active Directory Domain Services
- Configuring DNS
- Setting static IP addresses
- Deploying a domain controller
- Domain authentication
- Administering Windows Server Core
- Joining systems to a Windows domain

These are foundational tasks commonly performed by system administrators and IT support professionals in enterprise environments.

---

# Step 1 — Creating the Virtual Machines

## Overview

The first stage of building the homelab environment is creating the virtual machines that will simulate the servers and workstation used in a typical enterprise network.

Using VirtualBox, I created four virtual machines that represent the core infrastructure of the lab environment:

- Domain Controller
- Member Server
- Server Core system
- Client workstation

Each machine will later receive a static IP address and be joined to the Active Directory domain.

---

## Creating the Virtual Machines

Open VirtualBox and create each virtual machine using the **New** option.

When creating each machine, assign the appropriate name and operating system based on the ISO file being used.

![VM Creation 1](../screenshots/step1-vm-creation1.png)

![VM Creation 2](../screenshots/step1-vm-creation2.png)

The three server machines use the Windows Server ISO, while the client machine uses the Windows 11 ISO.

---

## Virtual Machine List

After creating the systems, the VirtualBox manager should show all four machines.

![VM List](../screenshots/step1-vm-list.png)

---

## Recommended Virtual Machine Resources

To ensure the machines run smoothly, the following resource allocation is recommended.

### Server Machines

- Processors: 2 CPUs
- Memory: 4 GB RAM
- Storage: 60 GB virtual disk

### Client Machine

- Processors: 2 CPUs
- Memory: 4 GB RAM
- Storage: 50 GB virtual disk

These settings provide enough resources for the lab while still being manageable on most host systems.

![VM Resources 1](../screenshots/step1-vm-resources1.png)

![VM Resources 2](../screenshots/step1-vm-resources2.png)

---

## Installing the Operating Systems

After creating the virtual machines, attach the appropriate ISO file to each machine and start the installation process.

For the three server machines, install Windows Server.

During installation:

- Select **Server with Desktop Experience** for **GA-DC-01** and **GA-SVR1**
- Do **not** select Desktop Experience for **GA-Core**

### Windows Server Installation

![Windows Server Setup 1](../screenshots/step1-win-server1.png)

![Windows Server Setup 2](../screenshots/step1-win-server2.png)

![Windows Server Setup 3](../screenshots/step1-win-server3.png)

![Windows Server Setup 4](../screenshots/step1-win-server4.png)

![Windows Server Boot](../screenshots/step1-win-server-boot.png)

### Windows 11 Installation

For the client machine, install Windows 11 using the standard installation process.

![Windows 11 Install](../screenshots/step1-win11-install.png)

![Windows 11 Boot](../screenshots/step1-win11-boot.png)

### Server Core Boot

GA-Core is installed without Desktop Experience.

![Server Core Boot](../screenshots/step1-core-server-boot.png)

Once installation is complete, allow each machine to boot fully before moving on to the next step.

---

# Step 2 — Configuring the Virtual Network

## Overview

Before configuring IP addresses or installing Active Directory, the virtual machines must be connected to the same network so they can communicate with each other.

VirtualBox offers several networking options. For this lab, I used an **Internal Network**, which allows the virtual machines to communicate with each other while remaining isolated from my host computer’s physical network.

This setup simulates a small internal corporate network.

---

## Network Configuration

All virtual machines in this lab are connected to the same internal network.

- **Network Mode:** Internal Network
- **Network Name:** HomelabNet

The following machines must be connected to this network:

- GA-DC-01
- GA-SVR1
- GA-Core
- GA-Client

Using the same network name ensures that all machines are placed on the same virtual network segment.

---

## Configuring the Network Adapter

For each virtual machine:

1. Power off the virtual machine if it is currently running.
2. Open the VirtualBox Manager.
3. Select the virtual machine.
4. Click **Settings**.
5. Navigate to the **Network** section.
6. Under **Adapter 1**, enable the network adapter.
7. Set **Attached To** to **Internal Network**.
8. Enter the network name **HomelabNet**.
9. Click **OK** to save the configuration.

Repeat this process for all four virtual machines.

### Screenshots

![VirtualBox Manager](../screenshots/step2-vm-manager.png)

![VM Settings](../screenshots/step2-settings.png)

![Internal Network Configuration](../screenshots/step2-network-config.png)

---

## Purpose of the Internal Network

Using an internal network provides several advantages for a lab environment:

- Allows all virtual machines to communicate with one another
- Simulates an isolated corporate network environment
- Prevents interference with the host computer’s home network
- Provides a controlled environment for testing Active Directory

Once the virtual network is configured, I can move on to assigning static IP addresses.

---

# Step 3 — Configuring Static IP Addressing

## Overview

In most real-world environments, servers use static IP addresses rather than dynamically assigned addresses from DHCP. This ensures that important services like Active Directory and DNS are always reachable at a known address.

In this lab, each virtual machine is assigned a static IP address within the `192.168.1.0/24` network.

The Domain Controller also serves as the DNS server for the environment.

---

## Network Addressing Plan

### GA-DC-01

- **Role:** Domain Controller and DNS Server
- **IP Address:** 192.168.1.250
- **Subnet Mask:** 255.255.255.0
- **Default Gateway:** 192.168.1.1
- **Preferred DNS:** 127.0.0.1

### GA-SVR1

- **Role:** Member Server
- **IP Address:** 192.168.1.251
- **Preferred DNS:** 192.168.1.250

### GA-Core

- **Role:** Server Core System
- **IP Address:** 192.168.1.252
- **Preferred DNS:** 192.168.1.250

### GA-Client

- **Role:** Workstation
- **IP Address:** 192.168.1.100
- **Preferred DNS:** 192.168.1.250

---

## Configuring Static IP Addresses (Windows Server)

For **GA-DC-01** and **GA-SVR1**, I configured the static IP address using **Server Manager**.

1. Open **Server Manager**
2. Select **Local Server**
3. Click the **Ethernet** link
4. Right-click the Ethernet adapter and select **Properties**
5. Select **Internet Protocol Version 4 (TCP/IPv4)**
6. Click **Properties**
7. Choose **Use the following IP address**
8. Enter the IP address configuration based on the network addressing plan
9. Click **OK**

I repeated this process for **GA-SVR1** using its respective IP address.

### Windows Server Screenshots

![Windows Server Static IP 1](../screenshots/step3-win-server-staticip1.png)

![Windows Server Static IP 2](../screenshots/step3-win-server-staticip2.png)

![Windows Server Static IP 3](../screenshots/step3-win-server-staticip3.png)

![Windows Server Static IP 4](../screenshots/step3-win-server-staticip4.png)

---

## Configuring Static IP Addresses (Windows 11)

For the client machine:

1. Open **Control Panel**
2. Select **Network and Internet**
3. Click **Network and Sharing Center**
4. Select **Change adapter settings**
5. Right-click the Ethernet adapter and select **Properties**
6. Select **Internet Protocol Version 4 (TCP/IPv4)**
7. Click **Properties**
8. Choose **Use the following IP address**
9. Enter the IP address configuration based on the addressing plan
10. Click **OK**

Set the DNS server to the Domain Controller so the client can locate the domain later.

### Windows 11 Screenshots

![Windows 11 Static IP 1](../screenshots/step3-win11-staticip1.png)

![Windows 11 Static IP 2](../screenshots/step3-win11-staticip2.png)

![Windows 11 Static IP 3](../screenshots/step3-win11-static3.png)

![Windows 11 Static IP 4](../screenshots/step3-win11-static4.png)

![Windows 11 Static IP 5](../screenshots/step3-win11-static5.png)

![Windows 11 Static IP 6](../screenshots/step3-win11-static6.png)

![Windows 11 Static IP 7](../screenshots/step3-win11-static7.png)

---

## Configuring Static IP on Server Core

Because **GA-Core** runs Server Core without a graphical interface, networking is configured using the **sconfig** utility.

1. Log into **GA-Core**
2. From the main menu, select **Option 8 — Network Settings**
3. Select the network adapter number
4. Choose **Option 1 — Set Network Adapter Address**
5. Select **S** for Static
6. Enter the following:
   - IP Address: 192.168.1.252
   - Subnet Mask: 255.255.255.0
   - Default Gateway: 192.168.1.1
7. Return to the menu
8. Select **Option 2 — Set DNS Servers**
9. Enter the DNS server address for the Domain Controller:
   - 192.168.1.250

### Server Core Screenshots

![Server Core Static IP 1](../screenshots/step3-core-staticip1.png)

![Server Core Static IP 2](../screenshots/step3-core-staticip2.png)

![Server Core Static IP 3](../screenshots/step3-core-staticip3.png)

![Server Core Static IP 4](../screenshots/step3-core-staticip4.png)

![Server Core DNS](../screenshots/step3-core-dns.png)

---

## Verifying Network Connectivity

Once all IP addresses are configured, I verified that the machines could communicate by running the following commands from each machine:

```powershell
ping 192.168.1.250
ping 192.168.1.251
ping 192.168.1.252
ping 192.168.1.100
````

Successful replies confirm the systems can communicate across the network.

---

## Troubleshooting Network Connectivity

During testing, the servers initially could not ping each other even though the network configuration was correct.

The issue turned out to be **Windows Firewall blocking ICMP traffic**.

To fix this, the inbound rule **File and Printer Sharing (Echo Request - ICMPv4-In)** was enabled.

After enabling the rule, the machines were able to successfully respond to ping requests.

This change was applied across all virtual machines.

### Troubleshooting Screenshots

![Troubleshooting 1](../screenshots/step3-troubleshooting.png)

![Troubleshooting 2](../screenshots/step3-troubleshooting2.png)

![Troubleshooting 3](../screenshots/step3-troubleshooting3.png)

![Troubleshooting 4](../screenshots/step3-troubleshooting4.png)


### Verification Screenshots

![Verification 1](../screenshots/step3-verification.png)

![Verification 2](../screenshots/step3-verification2.png)

![Verification 3](../screenshots/step3-verificaton3.png)

![Verification 4](../screenshots/step3-verification4.png)

---

# Step 4 — Installing Active Directory Domain Services

## Overview

Now that the virtual machines are communicating and static IP addresses have been configured, the next step is to install **Active Directory Domain Services (AD DS)** on the server **GA-DC-01**.

Active Directory is used in most enterprise Windows environments to manage users, computers, and resources across a network. Once installed, **GA-DC-01** becomes the Domain Controller for the lab environment and also functions as the DNS server.

After this step is completed, the server hosts the new domain that the rest of the machines will join later in the lab.

---

## Installing the Active Directory Role

On **GA-DC-01**:

1. Open **Server Manager**
2. In the top right corner, click **Manage**
3. Select **Add Roles and Features**
4. Click **Next** on the **Before You Begin** page
5. Select **Role-based or feature-based installation**
6. Click **Next**
7. Confirm **GA-DC-01** is selected as the destination server
8. Click **Next**
9. On the **Server Roles** page, check:

   * **Active Directory Domain Services**
10. When prompted, click **Add Features**
11. Continue through the wizard until the **Confirmation** page
12. Click **Install**

Once the installation completes, the server must be promoted to a Domain Controller.

### AD DS Installation Screenshots

![Install AD DS 1](../screenshots/step4-install-adds.png)

![Install AD DS 2](../screenshots/step4-install-adds2.png)

![Install AD DS 3](../screenshots/step4-install-adds3.png)

![Install AD DS 4](../screenshots/step4-install-adds4.png)

---

## Promoting the Server to a Domain Controller

After the AD DS role is installed:

1. Return to **Server Manager**
2. Click the **notification flag**
3. Select **Promote this server to a domain controller**
4. Choose **Add a new forest**
5. Use the domain name:

   * `homelab.local`
6. Click **Next**
7. On the **Domain Controller Options** page, leave the default settings selected
8. Enter a password for **Directory Services Restore Mode (DSRM)**
9. Continue through the remaining configuration pages
10. On the **Prerequisites Check** page, confirm all checks pass
11. Click **Install**

The server automatically restarts after the installation completes.

### Domain Controller Promotion Screenshots

![Promote DC 1](../screenshots/step4-promote-dc.png)

![Promote DC 2](../screenshots/step4-promote-dc2.png)

![Promote DC 3](../screenshots/step4-promote-dc3.png)

![Promote DC 4](../screenshots/step4-promote-dc4.png)

---

## Verifying Domain Controller Installation

After the server finished installing Active Directory and restarted, I logged back into **GA-DC-01** to verify that the installation completed successfully.

To confirm that the server had been promoted to a Domain Controller:

1. Open **Server Manager**
2. Confirm that Active Directory services are installed and running
3. Open **Active Directory Users and Computers**
4. Confirm the domain **homelab.local** is visible

Seeing the domain structure confirms that **GA-DC-01** had successfully been promoted to a Domain Controller and that the Active Directory environment was now active.

### Verification Screenshots

![AD Verification 1](../screenshots/step4-verification.png)

![AD Verification 2](../screenshots/step4-verification2.png)

---

# Step 5 — Creating Organizational Units and Domain Users

## Overview

With Active Directory now installed and **GA-DC-01** functioning as the Domain Controller, the next step is to begin organizing the domain structure.

In most enterprise environments, administrators use **Organizational Units (OUs)** to group objects such as users, computers, and servers. This makes it easier to manage permissions, apply group policies, and organize systems within the domain.

For this lab, I created several Organizational Units to simulate how systems and users might be organized inside a small company environment.

---

## Opening Active Directory Users and Computers

To begin configuring the domain structure:

1. Open **Active Directory Users and Computers** on **GA-DC-01**
2. Expand the domain **homelab.local**
3. View the default Active Directory structure

### ADUC Screenshot

![Creating OUs and Users 1](../screenshots/step5-creating-ous.png)

---

## Creating Organizational Units

To organize the environment, I created several Organizational Units inside the domain.

1. Right-click the domain **homelab.local**
2. Select **New**
3. Click **Organizational Unit**
4. Enter a name for the OU
5. Click **OK**

Using this process, I created the following Organizational Units:

* Servers
* Employees
* IT Admins

These OUs will later be used to organize servers and user accounts within the domain.

### OU Screenshots

![Creating OUs 2](../screenshots/step5-creating-ous2.png)

![Creating OUs 3](../screenshots/step5-creating-ous3.png)

---

## Creating Domain Users

Next, I created several user accounts to simulate employees within the environment.

To create a user account:

1. Right-click the **Employees** Organizational Unit
2. Select **New**
3. Click **User**
4. Enter the user’s first name, last name, and logon name
5. Click **Next**
6. Assign a password for the account
7. Complete the wizard to create the user

Using this process, I created the following sample users:

* **Josh Smith** — logon name: **j.smith**
* **Sally Doe** — logon name: **s.doe**
* **Helpdesk Admin** — logon name: **helpdesk.admin**

These accounts will be used later when testing domain authentication and logging into domain-joined machines.

### User Creation Screenshots

![Creating Users 1](../screenshots/step5-creating-dus.png)

![Creating Users 1](../screenshots/step5-creating-dus2.png)

![Creating Users 2](../screenshots/step5-creating-dus3.png)

---

## Verifying the Organizational Structure

After creating the Organizational Units and user accounts, I reviewed the structure inside **Active Directory Users and Computers** to verify everything had been created correctly.

At this point, the domain structure included the newly created OUs as well as the test user accounts inside the **Employees** Organizational Unit.

This confirms that Active Directory is functioning correctly and that domain objects can be created and managed successfully.

---

# Step 6 — Joining Systems to the Domain

## Overview

With Active Directory installed and the domain structure in place, the next step is to join the remaining machines to the domain.

Joining a computer to a domain allows it to authenticate with Active Directory and be managed centrally by the Domain Controller. Once a system joins the domain, users can log in with domain accounts and administrators can manage the system through Active Directory.

In this step, I joined the following machines to the domain `homelab.local`:

* GA-SVR1
* GA-Core
* GA-Client

---

## Joining GA-SVR1 to the Domain

To begin, I joined the member server **GA-SVR1** to the domain.

1. Open **Server Manager**
2. Select **Local Server**
3. Click the **Workgroup** link next to the computer name
4. In the **System Properties** window, click **Change**
5. Select **Domain**
6. Enter the domain name:

   * `homelab.local`
7. Click **OK**
8. When prompted, enter the domain administrator credentials
9. Restart the server to complete the process

### GA-SVR1 Screenshots

![Join Server 1](../screenshots/step6-joinsvr.png)

![Join Server 2](../screenshots/step6-joinsvr2.png)

![Join Server 3](../screenshots/step6-joinsvr3.png)

---

## Joining GA-Core to the Domain

Because **GA-Core** runs Windows Server Core, the domain join process is completed using the **sconfig** utility.

1. Log into **GA-Core**
2. From the main menu, select **Option 1 — Domain/Workgroup**
3. Choose **D** to join a domain
4. Enter the domain name:

   * `homelab.local`
5. When prompted, enter the credentials for the domain administrator account
6. Confirm the changes and allow the server to restart

After the restart, **GA-Core** was successfully joined to the domain.

### GA-Core Screenshots

![Join Core 1](../screenshots/step6-joincore.png)

![Join Core 2](../screenshots/step6-joincore2.png)

---

## Joining GA-Client to the Domain

Next, I joined the Windows 11 workstation to the domain.

1. On **GA-Client**, open **Control Panel**
2. Select **System and Security**
3. Click **System**
4. Select **Advanced system settings**
5. In the **Computer Name** tab, click **Change**
6. Select **Domain**
7. Enter:

   * `homelab.local`
8. Click **OK** and enter the domain administrator credentials when prompted
9. After receiving confirmation, restart the machine

### GA-Client Screenshots

![Join Client 1](../screenshots/step6-joinclient.png)

![Join Client 2](../screenshots/step6-joinclient2.png)

![Join Client 3](../screenshots/step6-joinclient3.png)

![Join Client 4](../screenshots/step6-joinclient4.png)

---

## Verifying the Domain Join

After all systems restarted, I verified that the machines had successfully joined the domain.

From **GA-DC-01**, I opened **Active Directory Users and Computers** and confirmed that the newly joined computers appeared in the domain.

At this point, the lab environment now includes multiple systems joined to the `homelab.local` domain.

### Domain Join Verification

![Verify Domain Join](../screenshots/step6-verify-domain-join.png)

---

## Organizing Domain Computers

After joining the machines to the domain, I moved them into the appropriate Organizational Units to keep the domain structure organized.

Using **Active Directory Users and Computers**:

* I moved the servers into the **Servers** OU
* I placed the client workstation in the default **Computers** container

This helps keep servers and workstations separated and reflects how systems are typically organized in many Active Directory environments.

### Organizing Domain Computers

![Organizing Domain Computers](../screenshots/step6-organizing-domain-comptuers.png)

---
# Step 7 — Testing Domain Authentication

## Overview

With the servers and client workstation successfully joined to the domain, the final step is to verify that domain authentication is working properly.

One of the main benefits of Active Directory is centralized authentication. This allows users to log into domain-joined machines using their domain credentials instead of local accounts.

In this step, I tested the environment by logging into the client workstation using one of the domain user accounts that was created earlier.

---

## Signing in with a Domain User

To test domain authentication, I logged into the Windows 11 client machine using the domain user account for **Josh Smith**.

Although the user's full name is **Josh Smith**, the account was configured with the logon name **j.smith**, so the correct domain sign-in format is:

* `homelab\j.smith`

At the login screen:

1. Restart or sign out of **GA-Client**
2. Select **Other user**
3. Enter the username using the following format:

   * `homelab\j.smith`
4. Enter the password that was assigned to the user account
5. Click **Sign in**

After signing in, Windows created a new user profile for the domain account and loaded the desktop environment.

### Sign-In Screenshots

![Sign In 1](../screenshots/step7-sign-in-du.png)

![Sign In 2](../screenshots/step7-sign-in-du2.png)

---

## Verifying the Domain Login

Once logged in, I confirmed that the user account was authenticated by the domain.

To verify this, I opened **Command Prompt** and ran the following command:

```powershell
whoami
```
The result displayed:

```powershell
homelab\j.smith
```

This confirms the user authenticated successfully with the domain account homelab\j.smith, which is the configured logon name for Josh Smith.

### Verification Screenshot

![Domain Login Verification](../screenshots/step7-verify.png)

---

## Confirmation of a Working Domain Environment

At this point, the Active Directory environment is fully operational.

* The Domain Controller is managing authentication
* The servers are joined to the domain
* Users are able to sign into domain-joined machines using domain credentials

This confirms that the core components of the Active Directory lab environment are functioning correctly.

---

# Step 8 — Lessons Learned

## Overview

Building this Active Directory homelab provided hands-on experience with several core Windows infrastructure concepts. Throughout the process, I was able to practice configuring servers, managing network settings, and deploying a basic domain environment similar to what might exist in a small organization.

Working through the lab step-by-step helped reinforce how the different components of an Active Directory environment work together.

---

## Key Concepts Practiced

During this lab, I gained practical experience with several important system administration tasks, including:

* Creating and managing virtual machines using VirtualBox
* Configuring internal virtual networks
* Assigning static IP addresses to servers and clients
* Installing Active Directory Domain Services
* Promoting a server to a Domain Controller
* Creating Organizational Units and domain users
* Joining systems to an Active Directory domain
* Authenticating users using domain credentials

These tasks represent many of the foundational skills used by system administrators when managing Windows-based networks.

---

## Troubleshooting Experience

During the lab setup, I encountered a few issues that required troubleshooting.

One example occurred while testing domain authentication on the Windows 11 client machine. Initially, the login attempt failed because the Domain Controller was powered off, preventing the client from contacting the domain for authentication.

After starting the Domain Controller again, the client was able to successfully authenticate the domain user account.

This reinforced the importance of ensuring that critical infrastructure services, such as Domain Controllers and DNS servers, are available when systems need to authenticate with the domain.

---

## Final Result

By the end of this lab, I successfully built a small Active Directory environment consisting of:

* A Domain Controller
* Multiple servers
* A client workstation

All systems were able to communicate across the network, join the domain, and authenticate using domain user accounts.

This project helped me better understand how Windows domain environments are structured and managed in real-world IT environments.

### Final Result Screenshot

![Final Result](../screenshots/step8-final-result.png)

---

# Future Improvements

Possible improvements to expand this lab include:

* Implement Group Policy Objects (GPO)
* Create additional domain users and groups
* Configure shared folders with permissions
* Add a second domain controller for redundancy
* Implement DHCP in the lab environment


[1]: https://github.com/zackeryscott/active-directory-homelab/tree/main/screenshots "active-directory-homelab/screenshots at main · zackeryscott/active-directory-homelab · GitHub"


 

   

 
