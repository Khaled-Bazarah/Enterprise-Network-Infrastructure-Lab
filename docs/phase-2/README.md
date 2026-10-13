
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
![Timezone Setup](../../docs/phase-2/images/Timezone-Setup.png)

### Server Name Configuration
![Server Name Change](../../docs/phase-2/images/Server-Name-Change.png)

### Server Properties Overview
![Server Properties Overview](../../docs/phase-2/images/Server-Properties-Overview.png)

### Static IP Address Configuration
![Static IP Configuration](../../docs/phase-2/images/Static-IP-Configuration-WIN-Server.png)

---

## 2. Active Directory Services & Infrastructure Deployment

### AD DS Roles & Features Wizard
![Roles Features Wizard Start](../../docs/phase-2/images/Roles-Features-Wizard-Start.png)

### Select Server Roles
![Select Server Roles](../../docs/phase-2/images/Select-Server-Roles.png)

### Installation Progress
![Installation Progress](../../docs/phase-2/images/Installation-Progress.png)

### Post-Deployment Configuration
![Post Deployment Notifications](../../docs/phase-2/images/Post-Deployment-Notifications.png)

### New Forest Deployment
![AD New Forest Setup](../../docs/phase-2/images/AD-New-Forest-Setup.png)

### Prerequisites Check
![AD Prerequisites Check](../../docs/phase-2/images/AD-Prerequisites-Check.png)

### Forest & Domain Selection
![AD Forest Domain Selection](../../docs/phase-2/images/AD-Forest-Domain-Selection.png)

### Secondary Domain Controller Deployment
![AD Additional DC Deployment](../../docs/phase-2/images/AD-Additional-DC-Deployment.png)

### Secondary DC Hostname Change
![Secondary DC Name Change](../../docs/phase-2/images/Secondary-DC-Name-Change.png)

### Secondary DC Static IP Configuration
![Secondary DC IP Configuration](../../docs/phase-2/images/Secondary-DC-IP-Config-WIN-Server.png)

### AD Replication Options
![AD Replication Options](../../docs/phase-2/images/AD-Replication-Options.png)

### AD Replication Health Summary
![AD Replication Health Summary](../../docs/phase-2/images/AD-Replication-Health-Summary.png)

---

## 3. Core Network Services: DNS, NTP & DHCP Infrastructure

### 🌐 Domain Name System (DNS)
#### New Zone Wizard Setup
![DNS New Zone Wizard](../../docs/phase-2/images/DNS-New-Zone-Wizard.png)

#### Reverse Lookup Zone Network ID
![DNS Reverse Zone Network ID](../../docs/phase-2/images/DNS-Reverse-Zone-NetworkID.png)

#### Host (A) Record Properties
![DNS Host A Record Properties](../../docs/phase-2/images/DNS-Host-A-Record-Properties.png)

#### Forwarders Configuration
![DNS Forwarders Configuration](../../docs/phase-2/images/DNS-Forwarders-Config.png)

#### DNS Nslookup Verification
![DNS Nslookup Verification](../../docs/phase-2/images/DNS-Nslookup-Verification.png)

#### Multi-DC DNS Verification
![DNS Multi DC Nslookup Verification](../../docs/phase-2/images/DNS-Multi-DC-Nslookup-Verification.png)

#### Domain ICMP / Ping Verification
![Domain Ping Verification](../../docs/phase-2/images/Domain-Ping-Verification.png)

### 🕒 Network Time Protocol (NTP)
#### NTP Server Resolution & Config
![NTP Server Config Resolve](../../docs/phase-2/images/NTP-Server-Config-Resolve.png)

#### NTP Status Query
![NTP Status Query](../../docs/phase-2/images/NTP-Status-Query.png)

### 📡 DHCP & IP Helper / Relay
#### DHCP Scope Naming
![DHCP Scope Name Config](../../docs/phase-2/images/DHCP-Scope-Name-Config.png)

#### DHCP Address Pool Range
![DHCP IP Range Config](../../docs/phase-2/images/DHCP-IP-Range-Config.png)

#### DHCP DNS Options Configuration
![DHCP DNS Server Config](../../docs/phase-2/images/DHCP-DNS-Server-Config.png)

#### Cisco Switch IP Helper / Relay (VLAN 10)
![Cisco DHCP Relay Config VLAN 10](../../docs/phase-2/images/Cisco-DHCP-Relay-Config-vlan-10.png)

#### Cisco Switch IP Helper / Relay (VLAN 20)
![Cisco DHCP Relay Config VLAN 20](../../docs/phase-2/images/Cisco-DHCP-Relay-Config-VLAN-20.png)

#### VPCS Client DHCP Lease Verification
![VPCS DHCP Lease Verification](../../docs/phase-2/images/VPCS-DHCP-Lease-Verification.png)

---

## 4. Active Directory Identity & Access Management

### Organizational Unit (OU) Creation
![AD Create Organizational Unit](../../docs/phase-2/images/AD-Create-Organizational-Unit.png)

### Sub-OU Hierarchy Setup
![AD Sub OU Creation](../../docs/phase-2/images/AD-Sub-OU-Creation.png)

### Tiered OU Structure Overview
![AD Tiered OU Structure Overview](../../docs/phase-2/images/AD-Tiered-OU-Structure-Overview.png)

### User Account Provisioning
![AD User Creation Details](../../docs/phase-2/images/AD-User-Creation-Details.png)

### User Password & Security Options
![AD User Password Options](../../docs/phase-2/images/AD-User-Password-Options.png)

### Security Group Creation
![AD Security Group Creation](../../docs/phase-2/images/AD-Security-Group-Creation.png)

### Moving Objects Menu
![AD Move Object Menu](../../docs/phase-2/images/AD-Move-Object-Menu.png)

### Target OU Selection
![AD Move Target OU Selection](../../docs/phase-2/images/AD-Move-Target-OU-Selection.png)

---

## 5. Group Policy Objects (GPO) Implementation

### Account Lockout Policy
![GPO Account Lockout Policy](../../docs/phase-2/images/GPO-Account-Lockout-Policy.png)

### Restricted Groups Configuration
![GPO Restricted Groups Config](../../docs/phase-2/images/GPO-Restricted-Groups-Config.png)

### Group Policy Force Update (gpupdate /force)
![Group Policy Force Update](../../docs/phase-2/images/Group-Policy-Force-Update.png)

---

## 6. Client Domain Join & Multi-OS Integration

### 🪟 Windows Workstation Integration
#### DHCP Client IP Lease
![DHCP Client Lease Active](../../docs/phase-2/images/DHCP-Client-Lease-Active.png)

#### Client IP Network Details
![Client IP Configuration Details](../../docs/phase-2/images/Client-IP-Configuration-Details.png)

#### Domain Member Properties
![Domain Joined Server Properties](../../docs/phase-2/images/Domain-Joined-Server-Properties.png)

#### Domain Join Settings
![Domain Join Configuration](../../docs/phase-2/images/Domain-Join-Configuration.png)

#### Domain Join Success Confirmation
![Domain Join Success](../../docs/phase-2/images/Domain-Join-Success.png)

#### Domain User Logon Screen
![Domain User Login Screen](../../docs/phase-2/images/Domain-User-Login-Screen.png)

#### Domain Admin Authentication
![Domain Admin Login](../../docs/phase-2/images/Domain-Admin-Login.png)

### 🐧 Linux (Ubuntu) SSSD Enterprise Integration
#### Ubuntu DHCP Lease Verification
![Ubuntu DHCP Lease Active](../../docs/phase-2/images/Ubuntu-DHCP-Lease-Active.png)

#### Ubuntu Network Details
![Ubuntu IP Address Details](../../docs/phase-2/images/Ubuntu-IP-Address-Details.png)

#### SSSD & Realmd Package Installation
![Ubuntu SSSD Packages Install](../../docs/phase-2/images/Ubuntu-SSSD-Packages-Install.png)

#### Active Directory Domain Join Command
![Ubuntu AD Domain Join](../../docs/phase-2/images/Ubuntu-AD-Domain-Join.png)

#### Realm List Verification
![Ubuntu Realm List Verification](../../docs/phase-2/images/Ubuntu-Realm-List-Verification.png)

#### Terminal Domain DNS Lookup
![Ubuntu Terminal Domain Nslookup](../../docs/phase-2/images/Ubuntu-Terminal-Domain-Nslookup.png)

#### Ubuntu GDM Display Manager Login
![Ubuntu GDM Login Prompt](../../docs/phase-2/images/Ubuntu-GDM-Login-Prompt.png)

#### Domain User Interactive Session
![Ubuntu Domain User Login](../../docs/phase-2/images/Ubuntu-Domain-User-Login.png)

---

## 7. File Services & Storage Security

### SMB Advanced Folder Sharing
![SMB Folder Advanced Sharing](../../docs/phase-2/images/SMB-Folder-Advanced-Sharing.png)

### SMB Share Permissions
![SMB Share Permissions Config](../../docs/phase-2/images/SMB-Share-Permissions-Config.png)

### NTFS Security & Access Control List (ACL)
![NTFS Folder Security Permissions](../../docs/phase-2/images/NTFS-Folder-Security-Permissions.png)

### UNC Path Network Access Verification
![UNC Path Run Access](../../docs/phase-2/images/UNC-Path-Run-Access.png)
