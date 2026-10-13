
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
