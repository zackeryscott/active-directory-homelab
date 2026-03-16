# Active Directory Homelab

<p align="left">
  <img src="https://img.shields.io/badge/Windows%20Server-2025-0078D6?style=for-the-badge&logo=windows" alt="Windows Server 2025" />
  <img src="https://img.shields.io/badge/Windows-11-0078D6?style=for-the-badge&logo=windows" alt="Windows 11" />
  <img src="https://img.shields.io/badge/Oracle-VirtualBox-183A61?style=for-the-badge&logo=virtualbox" alt="Oracle VirtualBox" />
  <img src="https://img.shields.io/badge/Active%20Directory-Configured-0A66C2?style=for-the-badge" alt="Active Directory" />
  <img src="https://img.shields.io/badge/DNS-Configured-2EA44F?style=for-the-badge" alt="DNS Configured" />
  <img src="https://img.shields.io/badge/Status-Completed-2EA44F?style=for-the-badge" alt="Project Completed" />
</p>

## Overview

This project demonstrates the design and deployment of a small enterprise-style **Active Directory homelab** built with **Oracle VirtualBox**, **Windows Server 2025**, and **Windows 11**.

The environment simulates a real-world business network with a:

- **Domain Controller**
- **Member Server**
- **Server Core system**
- **Domain-joined Windows 11 client**

This lab was built to strengthen hands-on skills in:

- Windows Server administration
- Active Directory Domain Services (AD DS)
- DNS configuration
- Virtual networking
- Domain management
- Technical documentation

---

## Table of Contents

- [Network Architecture](#network-architecture)
- [Lab Environment](#lab-environment)
- [Technologies Used](#technologies-used)
- [Skills Demonstrated](#skills-demonstrated)
- [Project Highlights](#project-highlights)
- [Full Lab Guide](#full-lab-guide)
- [Build Summary](#build-summary)
- [Lessons Learned](#lessons-learned)
- [Why This Project Matters](#why-this-project-matters)

---

## Network Architecture

This lab uses an internal VirtualBox network to simulate a small enterprise Active Directory environment.

![Network Diagram](images/network-diagram.png)

All systems communicate through an internal virtual network, allowing domain services, authentication, and administrative testing in a controlled lab environment.

---

## Lab Environment

| Machine Name | Operating System | Role | IP Address |
|---|---|---|---|
| GA-DC-01 | Windows Server 2025 | Domain Controller, DNS | 192.168.1.250 |
| GA-SVR1 | Windows Server 2025 | Member Server | 192.168.1.251 |
| GA-Core | Windows Server 2025 Core | Server Core System | 192.168.1.252 |
| GA-Client | Windows 11 | Domain-Joined Workstation | 192.168.1.100 |

---

## Technologies Used

- Oracle VirtualBox
- Windows Server 2025
- Windows Server Core
- Windows 11
- Active Directory Domain Services (AD DS)
- DNS
- Windows networking
- Virtual lab infrastructure

---

## Skills Demonstrated

- Active Directory deployment
- Domain Controller promotion
- DNS configuration
- Static IP addressing
- Organizational Unit creation
- User and account management
- Domain joining Windows clients
- Windows Server administration
- Virtual networking
- Authentication and connectivity testing
- Technical documentation

---

## Project Highlights

### Domain Controller Setup
Configured a Windows Server 2025 machine as the Domain Controller and installed Active Directory Domain Services.

![Domain Controller Setup](screenshots/step4-verification.png)

### DNS Configuration
Configured DNS to support name resolution and domain services across the lab environment.

![DNS Configuration](screenshots/step3-win-server-staticip4.png)

### Active Directory Users and Computers
Created Organizational Units and user accounts to simulate a structured enterprise environment.

![AD Users and Computers](screenshots/step8-final-result.png)

### Domain Join
Joined the Windows 11 client workstation to the Active Directory domain.

![Domain Join](screenshots/step6-joinclient3.png)

### Authentication Testing
Verified successful domain authentication by signing in with a domain user account.

![Authentication Test](screenshots/step7-verify.png)

---

## Full Lab Guide

For the complete step-by-step walkthrough, including prerequisites, VM creation, networking, server promotion, DNS setup, and domain joining, view the full guide here:

[Active Directory Lab Guide](docs/active-directory-lab-guide.md)

---

## Build Summary

This homelab was built in the following phases:

1. Prepared the virtualization environment and downloaded the required operating system images
2. Created virtual machines in Oracle VirtualBox
3. Configured internal networking and static IP addressing
4. Installed and promoted the Domain Controller
5. Configured DNS and validated connectivity
6. Created a structured Active Directory environment
7. Joined systems to the domain
8. Tested authentication and administrative functionality

---

## Lessons Learned

Through this project, I strengthened my understanding of:

- how Active Directory environments are structured
- the importance of DNS in domain functionality
- how domain-joined systems authenticate in a Windows environment
- how virtualization can be used to safely build and test enterprise infrastructure
- how technical documentation improves both learning and portfolio presentation

---

## Why This Project Matters

This homelab demonstrates practical, hands-on experience with core IT and cybersecurity technologies used in real enterprise environments.

Rather than only studying concepts in theory, this project shows the ability to:

- build and configure Windows infrastructure
- manage identity and authentication services
- document technical work clearly
- troubleshoot systems in a controlled lab environment

