
# Phase 2: Active Directory Services, Identity, Cross-Platform SSO & Core IP Infrastructure

Welcome to the detailed technical documentation for **Phase 2**. This phase covers the implementation of centralized Enterprise Identity Management, High-Availability Active Directory Services, Dynamic IP Allocation, Cross-Platform SSO Integration, and Secure Departmental Storage Isolation.

---

## 📐 Phase 2 Architecture Scope & Service Integration

![Phase 2 Directory & Network Scope](../../docs/phase-2/images/Topology.jpeg)

> [!NOTE]
> **Implementation Scope:** This document details the step-by-step configuration, Organizational Unit (OU) structure, Group Policy Objects (GPO), Linux SSSD domain onboarding, DHCP Relay setup, and verification tests for the enterprise identity layer.

### 📊 Quick Technical Summary
| Domain | Implementation Standard |
| :--- | :--- |
| **Directory Services** | Redundant DCs (`KSA-JED-DC01` & `KSA-JED-DC02`) in `kob.local` Domain |
| **IP & Name Services** | Centralized DNS (Forward/Reverse) & DHCP Scopes with L3 IP Helper Relay |
| **Cross-Platform SSO** | Linux Ubuntu Integration via SSSD, Kerberos, and Realmd Integration |
| **Governance & Access** | Tiered OU Model, Targeted GPOs, and Isolated Departmental SMB Shares |

---

## 🎯 Key Objectives & Engineering Goals
- **High Availability Identity:** Active Directory redundancy and fault tolerance eliminating single points of failure for domain authentication.
- **Centralized Network Services:** Integrated DNS zone resolution and DHCP scope distribution via Layer 3 `ip helper-address` relays on Core Multi-Layer Switches.
- **Cross-Platform Identity Federation:** Seamless Single Sign-On (SSO) and Kerberos authentication across Windows 10 and Ubuntu Linux endpoints.
- **Least Privilege Governance:** Hierarchical Organizational Unit (OU) structure adhering to the Enterprise Tiering Model for strict administrative isolation.
- **Data & Access Security:** Tailored Group Policy Objects (GPOs) and SMB/CIFS departmental network shares with strict NTFS and Share Access Control Lists (ACLs).

---
---
## 🛠️ Technical Highlights & Implementation Scope
## 1. Base Server Setup & Initial Configuration

### Timezone Setup
<img src="docs/phase-2/images/Timezone-Setup.png" alt="Timezone Setup" width="750">

### Server Name Configuration
<img src="docs/phase-2/images/Server-Name-Change.png" alt="Server Name Change" width="750">

### Server Properties Overview
<img src="docs/phase-2/images/Server-Properties-Overview.png" alt="Server Properties Overview" width="750">

### Static IP Address Configuration
<img src="docs/phase-2/images/Static-IP-Configuration-WIN-Server.png" alt="Static IP Configuration" width="750">

---

## 2. Active Directory Services & Infrastructure Deployment

### AD DS Roles & Features Wizard
<img src="docs/phase-2/images/Roles-Features-Wizard-Start.png" alt="Roles Features Wizard Start" width="750">

### Select Server Roles
<img src="docs/phase-2/images/Select-Server-Roles.png" alt="Select Server Roles" width="750">

### Installation Progress
<img src="docs/phase-2/images/Installation-Progress.png" alt="Installation Progress" width="750">

### Post-Deployment Configuration
<img src="docs/phase-2/images/Post-Deployment-Notifications.png" alt="Post Deployment Notifications" width="750">

### New Forest Deployment
<img src="docs/phase-2/images/AD-New-Forest-Setup.png" alt="AD New Forest Setup" width="750">

### Prerequisites Check
<img src="docs/phase-2/images/AD-Prerequisites-Check.png" alt="AD Prerequisites Check" width="750">

### Forest & Domain Selection
<img src="docs/phase-2/images/AD-Forest-Domain-Selection.png" alt="AD Forest Domain Selection" width="750">

### Secondary Domain Controller Deployment
<img src="docs/phase-2/images/AD-Additional-DC-Deployment.png" alt="AD Additional DC Deployment" width="750">

### Secondary DC Hostname Change
<img src="docs/phase-2/images/Secondary-DC-Name-Change.png" alt="Secondary DC Name Change" width="750">

### Secondary DC Static IP Configuration
<img src="docs/phase-2/images/Secondary-DC-IP-Config-WIN-Server.png" alt="Secondary DC IP Configuration" width="750">

### AD Replication Options
<img src="docs/phase-2/images/AD-Replication-Options.png" alt="AD Replication Options" width="750">

### AD Replication Health Summary
<img src="docs/phase-2/images/AD-Replication-Health-Summary.png" alt="AD Replication Health Summary" width="750">

---

## 3. Core Network Services: DNS, NTP & DHCP Infrastructure

### 🌐 Domain Name System (DNS)
#### New Zone Wizard Setup
<img src="docs/phase-2/images/DNS-New-Zone-Wizard.png" alt="DNS New Zone Wizard" width="750">

#### Reverse Lookup Zone Network ID
<img src="docs/phase-2/images/DNS-Reverse-Zone-NetworkID.png" alt="DNS Reverse Zone Network ID" width="750">

#### Host (A) Record Properties
<img src="docs/phase-2/images/DNS-Host-A-Record-Properties.png" alt="DNS Host A Record Properties" width="750">

#### Forwarders Configuration
<img src="docs/phase-2/images/DNS-Forwarders-Config.png" alt="DNS Forwarders Configuration" width="750">

#### DNS Nslookup Verification
<img src="docs/phase-2/images/DNS-Nslookup-Verification.png" alt="DNS Nslookup Verification" width="750">

#### Multi-DC DNS Verification
<img src="docs/phase-2/images/DNS-Multi-DC-Nslookup-Verification.png" alt="DNS Multi DC Nslookup Verification" width="750">

#### Domain ICMP / Ping Verification
<img src="docs/phase-2/images/Domain-Ping-Verification.png" alt="Domain Ping Verification" width="750">

### 🕒 Network Time Protocol (NTP)
#### NTP Server Resolution & Config
<img src="docs/phase-2/images/NTP-Server-Config-Resolve.png" alt="NTP Server Config Resolve" width="750">

#### NTP Status Query
<img src="docs/phase-2/images/NTP-Status-Query.png" alt="NTP Status Query" width="750">

### 📡 DHCP & IP Helper / Relay
#### DHCP Scope Naming
<img src="docs/phase-2/images/DHCP-Scope-Name-Config.png" alt="DHCP Scope Name Config" width="750">

#### DHCP Address Pool Range
<img src="docs/phase-2/images/DHCP-IP-Range-Config.png" alt="DHCP IP Range Config" width="750">

#### DHCP DNS Options Configuration
<img src="docs/phase-2/images/DHCP-DNS-Server-Config.png" alt="DHCP DNS Server Config" width="750">

#### Cisco Switch IP Helper / Relay (VLAN 10)
<img src="docs/phase-2/images/Cisco-DHCP-Relay-Config-vlan-10.png" alt="Cisco DHCP Relay Config VLAN 10" width="750">

#### Cisco Switch IP Helper / Relay (VLAN 20)
<img src="docs/phase-2/images/Cisco-DHCP-Relay-Config-VLAN-20.png" alt="Cisco DHCP Relay Config VLAN 20" width="750">

#### VPCS Client DHCP Lease Verification
<img src="docs/phase-2/images/VPCS-DHCP-Lease-Verification.png" alt="VPCS DHCP Lease Verification" width="750">

---

## 4. Active Directory Identity & Access Management

### Organizational Unit (OU) Creation
<img src="docs/phase-2/images/AD-Create-Organizational-Unit.png" alt="AD Create Organizational Unit" width="750">

### Sub-OU Hierarchy Setup
<img src="docs/phase-2/images/AD-Sub-OU-Creation.png" alt="AD Sub OU Creation" width="750">

### Tiered OU Structure Overview
<img src="docs/phase-2/images/AD-Tiered-OU-Structure-Overview.png" alt="AD Tiered OU Structure Overview" width="750">

### User Account Provisioning
<img src="docs/phase-2/images/AD-User-Creation-Details.png" alt="AD User Creation Details" width="750">

### User Password & Security Options
<img src="docs/phase-2/images/AD-User-Password-Options.png" alt="AD User Password Options" width="750">

### Security Group Creation
<img src="docs/phase-2/images/AD-Security-Group-Creation.png" alt="AD Security Group Creation" width="750">

### Moving Objects Menu
<img src="docs/phase-2/images/AD-Move-Object-Menu.png" alt="AD Move Object Menu" width="750">

### Target OU Selection
<img src="docs/phase-2/images/AD-Move-Target-OU-Selection.png" alt="AD Move Target OU Selection" width="750">

---

## 5. Group Policy Objects (GPO) Implementation

### Account Lockout Policy
<img src="docs/phase-2/images/GPO-Account-Lockout-Policy.png" alt="GPO Account Lockout Policy" width="750">

### Restricted Groups Configuration
<img src="docs/phase-2/images/GPO-Restricted-Groups-Config.png" alt="GPO Restricted Groups Config" width="750">

### Group Policy Force Update (gpupdate /force)
<img src="docs/phase-2/images/Group-Policy-Force-Update.png" alt="Group Policy Force Update" width="750">

---

## 6. Client Domain Join & Multi-OS Integration

### 🪟 Windows Workstation Integration
#### DHCP Client IP Lease
<img src="docs/phase-2/images/DHCP-Client-Lease-Active.png" alt="DHCP Client Lease Active" width="750">

#### Client IP Network Details
<img src="docs/phase-2/images/Client-IP-Configuration-Details.png" alt="Client IP Configuration Details" width="750">

#### Domain Member Properties
<img src="docs/phase-2/images/Domain-Joined-Server-Properties.png" alt="Domain Joined Server Properties" width="750">

#### Domain Join Settings
<img src="docs/phase-2/images/Domain-Join-Configuration.png" alt="Domain Join Configuration" width="750">

#### Domain Join Success Confirmation
<img src="docs/phase-2/images/Domain-Join-Success.png" alt="Domain Join Success" width="750">

#### Domain User Logon Screen
<img src="docs/phase-2/images/Domain-User-Login-Screen.png" alt="Domain User Login Screen" width="750">

#### Domain Admin Authentication
<img src="docs/phase-2/images/Domain-Admin-Login.png" alt="Domain Admin Login" width="750">

### 🐧 Linux (Ubuntu) SSSD Enterprise Integration
#### Ubuntu DHCP Lease Verification
<img src="docs/phase-2/images/Ubuntu-DHCP-Lease-Active.png" alt="Ubuntu DHCP Lease Active" width="750">

#### Ubuntu Network Details
<img src="docs/phase-2/images/Ubuntu-IP-Address-Details.png" alt="Ubuntu IP Address Details" width="750">

#### SSSD & Realmd Package Installation
<img src="docs/phase-2/images/Ubuntu-SSSD-Packages-Install.png" alt="Ubuntu SSSD Packages Install" width="750">

#### Active Directory Domain Join Command
<img src="docs/phase-2/images/Ubuntu-AD-Domain-Join.png" alt="Ubuntu AD Domain Join" width="750">

#### Realm List Verification
<img src="docs/phase-2/images/Ubuntu-Realm-List-Verification.png" alt="Ubuntu Realm List Verification" width="750">

#### Terminal Domain DNS Lookup
<img src="docs/phase-2/images/Ubuntu-Terminal-Domain-Nslookup.png" alt="Ubuntu Terminal Domain Nslookup" width="750">

#### Ubuntu GDM Display Manager Login
<img src="docs/phase-2/images/Ubuntu-GDM-Login-Prompt.png" alt="Ubuntu GDM Login Prompt" width="750">

#### Domain User Interactive Session
<img src="docs/phase-2/images/Ubuntu-Domain-User-Login.png" alt="Ubuntu Domain User Login" width="750">

---

## 7. File Services & Storage Security

### SMB Advanced Folder Sharing
<img src="docs/phase-2/images/SMB-Folder-Advanced-Sharing.png" alt="SMB Folder Advanced Sharing" width="750">

### SMB Share Permissions
<img src="docs/phase-2/images/SMB-Share-Permissions-Config.png" alt="SMB Share Permissions Config" width="750">

### NTFS Security & Access Control List (ACL)
<img src="docs/phase-2/images/NTFS-Folder-Security-Permissions.png" alt="NTFS Folder Security Permissions" width="750">

### UNC Path Network Access Verification
<img src="docs/phase-2/images/UNC-Path-Run-Access.png" alt="UNC Path Run Access" width="750">
