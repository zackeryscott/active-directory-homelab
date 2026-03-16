# Active Directory Homelab

This project documents the process of building an Active Directory home lab using VirtualBox and Windows Server 2025. The goal of this lab was to simulate a small enterprise environment and gain hands-on experience with Active Directory, DNS, networking, and domain authentication.

---

# Network Architecture

![Network Diagram](images/network-diagram.png)

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

## Lab Build Highlights

Below are some key steps from the lab build process.

### Creating Virtual Machines

![VM Creation](screenshots/step1-vm-creation1.png)

---

### Configuring the Virtual Network

![Network Configuration](screenshots/step2-network-config.png)

---

### Configuring Static IP Addresses

![Server Core Static IP](screenshots/step3-core-staticip4.png)

---

### Active Directory Domain Services Installed

![Active Directory Installation](screenshots/step4-verification.png)

---

### Domain Authentication Test

![Domain Login](screenshots/step7-verify.png)


---

# Step 0 — Prerequisites

Before building the environment, the required software and installation media were downloaded.

### Required Software

- Oracle VirtualBox
- Windows Server 2025 Evaluation ISO
- Windows 11 ISO

### VirtualBox Dashboard

![VirtualBox Dashboard](screenshots/step0-virtualbox-dashboard.png)

### Download VirtualBox

![VirtualBox Download](screenshots/step0-virtualbox-download.png)

### Windows Server 2025 ISO

![Windows Server 2025 ISO](screenshots/step0-windows-server2025.png)

### Windows 11 ISO

![Windows 11 ISO](screenshots/step0-windows11.png)

---

# Step 1 — Creating the Virtual Machines

Four virtual machines were created in VirtualBox to simulate the servers and client workstation used in the lab.

### Creating the Virtual Machines

![VM Creation](screenshots/step1-vm-creation1.png)

![VM Creation](screenshots/step1-vm-creation2.png)

### Virtual Machine List

![VM List](screenshots/step1-vm-list.png)

### VM Resource Configuration

![VM Resources](screenshots/step1-vm-resources1.png)

![VM Resources](screenshots/step1-vm-resources2.png)

### Installing Windows Server

![Windows Server Setup](screenshots/step1-win-server1.png)

![Windows Server Setup](screenshots/step1-win-server2.png)

![Windows Server Setup](screenshots/step1-win-server3.png)

![Windows Server Setup](screenshots/step1-win-server4.png)

![Windows Server Boot](screenshots/step1-win-server-boot.png)


### Installing Windows 11

![Windows 11 Install](screenshots/step1-win11-install.png)

![Windows 11 Boot](screenshots/step1-win11-boot.png)

### Server Core Boot

![Server Core Boot](screenshots/step1-core-server-boot.png)

---

# Step 2 — Configuring the Virtual Network

All machines were connected to an internal VirtualBox network so they could communicate with each other while remaining isolated from the host network.

Network Settings:

- Network Mode: Internal Network  
- Network Name: **HomelabNet**

### VirtualBox Network Configuration

![VM Manager](screenshots/step2-vm-manager.png)

![Network Settings](screenshots/step2-settings.png)

![Network Configuration](screenshots/step2-network-config.png)

---

# Step 3 — Configuring Static IP Addresses

Each machine in the lab was assigned a static IP address to ensure reliable communication between systems and to support domain services.

### Server Core Network Configuration

![Static IP Configuration](screenshots/step3-core-staticip1.png)

![Static IP Configuration](screenshots/step3-core-staticip2.png)

![Static IP Configuration](screenshots/step3-core-staticip3.png)

![Static IP Configuration](screenshots/step3-core-staticip4.png)

![Server Core DNS](screenshots/step3-core-dns.png)

---

# Step 4 — Installing Active Directory

Active Directory Domain Services (AD DS) was installed on **GA-DC-01**.

The server was then promoted to a **Domain Controller** for the domain:
homelab.local

This allows the server to manage authentication, users, computers, and other domain resources.

---

# Step 5 — Creating Organizational Units and Users

To simulate a corporate environment, several Organizational Units were created:

- Servers
- Computers
- Employees
- IT Admins

User accounts were created inside the domain to represent employees who would log into domain-joined systems.

---

# Step 6 — Joining Systems to the Domain

The remaining systems were joined to the domain:

- GA-SVR1
- GA-Core
- GA-Client

Once joined, the machines appeared in **Active Directory Users and Computers** and could be managed centrally.

---

# Step 7 — Testing Domain Authentication

To confirm that the environment was functioning correctly, the Windows 11 client was used to log in using a domain account.

Example login:
homelab\josh.smith

Successful authentication confirmed that:

- Active Directory was functioning
- DNS was resolving correctly
- Domain connectivity was working across the virtual network

---

# Lessons Learned

Building this homelab provided hands-on experience with several important system administration tasks:

- Deploying Active Directory Domain Services
- Configuring DNS
- Assigning static IP addresses
- Creating Organizational Units and users
- Joining systems to a Windows domain
- Troubleshooting networking and authentication issues

During testing, several issues were encountered such as firewall rules blocking ping requests and domain authentication failing when the Domain Controller was offline. Resolving these issues helped reinforce how the different components of a Windows domain environment work together.

---

# Future Improvements

Possible improvements to expand this lab include:

- Implement Group Policy Objects (GPO)
- Configure shared network drives
- Deploy a secondary domain controller
- Implement DHCP services
- Add centralized logging or SIEM monitoring
