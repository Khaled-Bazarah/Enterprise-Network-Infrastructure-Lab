# Enterprise Multi-Tier Network & Infrastructure Architecture Lab 🚀

Welcome to the repository for my **Enterprise Network & Systems Infrastructure Lab**. This project showcases a multi-tiered, highly available, and secure enterprise-grade topology emulated in **EVE-NG** and integrated with a physical bare-metal **Proxmox VE** hypervisor node over a physical Ethernet link.

---

## 📐 Network Topology & Physical Lab Architecture

![Enterprise Network Topology](./docs/topologies/topology.png)

> **Physical Hardware & Hybrid Setup Overview:** 
> - **Primary Laptop:** Hosts **VMware Workstation Pro** running the **EVE-NG** emulation environment.
> - **Secondary Laptop:** Deployed as a bare-metal **Proxmox VE Hypervisor** using a bootable USB created with **Rufus** to utilize full hardware performance. Hosts virtualized **Windows Server** (Domain Controller) and **Ubuntu Server** (Zabbix Monitoring).
> - **Physical Interconnect:** Both laptops are physically bridged via an **Ethernet Cable (LAN)**, seamlessly linking Proxmox virtual machines directly into EVE-NG's **Server VLAN 30**.

---

## 🛠️ Software, OS Images & Administrative Tools

* **Network Devices & Firewalls:**
  * **Cisco Systems:** Official Cisco `vIOS` (Router) and `vIOS-L2` (Access & Multi-Layer Switches) images.
  * **Fortinet:** **FortiGate NGWF** (FortiOS) image.
* **Host & Server Systems:**
  * **Hypervisor:** **Proxmox VE** (Flashed using **Rufus** for bare-metal installation).
  * **Virtual Servers:** Windows Server & Ubuntu Server (running on Proxmox VE).
  * **Endpoints:** Windows 10 Desktop & Ubuntu Linux Client.
* **Management & Administration Tools:**
  * **Proxmox VE Management:** Administered via **Proxmox Web GUI** for VM creation/resource allocation and **Proxmox Terminal / Shell (CLI)** for  networking, and package configurations.
  * **FortiGate Management:** Dual management via **Web GUI (HTTPS)** for security policy visual configuration and **CLI (SSH)** for advance network routing and system administration.
  * **Remote Access & CLI:** **PuTTY** and **MobaXterm** for SSH/Console session management across Cisco & Linux nodes.
  * **File Transfer:** **WinSCP** for transferring images, configurations, and scripts.

---

## 💡 Key Technical Highlights & Implemented Protocols

* **Addressing & Subnetting (VLSM):** Efficient IP allocation using `/27`, `/28`, and `/30` subnets to optimize address space and separate departments.
* **Routing & Redundancy:** 
  * **OSPF Area 0 (Process ID 1):** Configured across MLS1, MLS2, FortiGate, and Edge Router with dedicated `/32` Loopback Router IDs (`10.10.10.1` to `10.10.10.4`).
  * **HSRP Gateway Redundancy & Load Balancing:** Active/Standby state distribution with preemption enabled.
  * **Rapid PVST+ & STP Root Load Balancing:** Dual Root Bridge configuration matching HSRP active roles.
  * **Link Aggregation:** LACP EtherChannel (`Port-Channel 1`) trunks carrying explicitly allowed VLANs.
* **Switching & Layer 2/3 Hardening:**
  * **Unused Port Security (Physical Protection):** Manually disabled all unused/inactive switchports (`shutdown`) across Access and Core switches to block unauthorized physical connections.
  * Strict Access/Trunk encapsulation with dedicated Native VLAN 999 to mitigate VLAN Hopping.
  * **Port Security:** `maximum` MAC limits, `restricting` violations, and `sticky` MAC learning.
  * **Spanning Tree Security:** `PortFast` and `BPDU Guard` enabled on end-user access ports.
  * **Snooping & Spoofing Mitigation:** `DHCP Snooping` and `Dynamic ARP Inspection (DAI)` on untrusted interfaces.
* **Boundary Security & Edge:**
  * **FortiGate NGWF:** IPv4 Security Policies, NAT, and UTM Profiles for secure internet access configured via Web GUI & CLI.
  * **Edge Router:** Access Control Lists (ACLs), `ip nat inside/outside`, and Control Plane Policing (CoPP) for DoS protection.
* **Device Hardening & Line Security:** Local user accounts with `secret` passwords, `service password-encryption`, SSH v2 with RSA key pairs, line console/vty hardening, and dedicated Out-of-Band Management (VLAN 99).

---

## 🚀 Implementation Roadmap & Status

- [x] **Phase 1: Core Network Topology, Dynamic Routing, HA & Security Hardening**
- [ ] **Phase 2: Active Directory Services, Identity, DNS, DHCP & GPO Integration**
- [ ] **Phase 3: Centralized Infrastructure Monitoring (Zabbix & Deep Packet Inspection)**
- [ ] **Phase 4: Disaster Recovery & Automated Enterprise Backup (Veeam)**
- [ ] **Phase 5: Secure Remote Work (FortiGate SSL-VPN & Multi-Factor Authentication)**

---

## 📑 Detailed IP Addressing & Subnet Architecture

### 1. VLAN & Gateway Redundancy Scheme (HSRP & STP Roles)

| VLAN ID | Subnet / Mask | Department / Purpose | HSRP VIP | MLS1 Role & IP | MLS2 Role & IP | STP Root Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **VLAN 10** | `192.168.1.0/27` | Engineers | `192.168.1.1` | **Active** (Pri 110) - `192.168.1.2` | **Standby** (Pri 100) - `192.168.1.3` | **MLS1 Primary Root** |
| **VLAN 20** | `192.168.1.32/27` | HR | `192.168.1.33` | **Standby** (Pri 100) - `192.168.1.34` | **Active** (Pri 110) - `192.168.1.35` | **MLS2 Primary Root** |
| **VLAN 30** | `192.168.1.96/28` | Server Farm | `192.168.1.97` | SVI: `192.168.1.98` | **Active** (Pri 110) - `192.168.1.99` | **MLS2 Primary Root** |
| **VLAN 99** | `192.168.1.48/28` | Management (OOB) | `192.168.1.49` | **Active** (Pri 110) - `192.168.1.50` | SVI: `192.168.1.51` | **MLS1 Primary Root** |

> **Native VLAN:** `VLAN 999` (Used exclusively across all trunks for security).

---

### 2. Infrastructure & Host Static IP Assignments

* **Out-of-Band Switch Management (VLAN 99 - `192.168.1.48/28`):**
  * `SW1 SVI`: `192.168.1.52/28`
  * `SW2 SVI`: `192.168.1.53/28`
  * `SW3 SVI`: `192.168.1.54/28`

* **Server Farm Infrastructure (VLAN 30 - `192.168.1.96/28`):**
  * `Proxmox Hypervisor Host`: `192.168.1.100/28`
  * `Windows Server (Domain Controller)`: `192.168.1.101/28`
  * `Ubuntu Server (Zabbix Monitoring)`: `192.168.1.102/28`

* **End-User Testing Hosts:**
  * `Windows 10 PC (VLAN 10)`: Assigned static IP within `192.168.1.0/27` range.
  * `Ubuntu Linux PC (VLAN 20)`: Assigned static IP within `192.168.1.32/27` range.

---

### 3. OSPF Area 0 Infrastructure & Point-to-Point Links (`/30` Subnets)

* **MLS1 Router ID:** `10.10.10.1` | **MLS2 Router ID:** `10.10.10.2`
* **FortiGate Router ID:** `10.10.10.3` | **vIOS Router ID:** `10.10.10.4`

* **MLS1 ↔ MLS2 Routed Link / LACP (`Port-Channel 1`):** `192.168.1.72/30`
  * MLS1 (`Gi1/0`): `192.168.1.73` | MLS2 (`Gi1/0`): `192.168.1.74`
* **MLS1 ↔ FortiGate:** `192.168.1.76/30`
  * MLS1 (`Gi0/0`): `192.168.1.77` | FortiGate (`port2`): `192.168.1.78`
* **MLS2 ↔ FortiGate:** `192.168.1.80/30`
  * MLS2 (`Gi0/0`): `192.168.1.81` | FortiGate (`port3`): `192.168.1.82`
* **FortiGate ↔ Edge Router (vIOS):** `192.168.1.84/30`
  * FortiGate (`port1`): `192.168.1.85` | vIOS (`Gi0/1`): `192.168.1.86`

---

## 👤 Author & Engineer

**Khaled Bazarah**  
*Computer & Network Engineer*  
* Certified: Cisco Certified Network Associate (CCNA 200-301)  
* Accredited Computer Engineer – Saudi Council of Engineers  
* 🔗 [LinkedIn Profile](https://www.linkedin.com/in/10khaled-bazarah)
