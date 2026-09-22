# Enterprise Multi-Tier Network & Infrastructure Architecture Lab 🚀

Welcome to the repository for my **Enterprise Network & Systems Infrastructure Lab**. This project features a multi-tiered, highly available, and secured enterprise-grade topology designed and emulated using **EVE-NG**, integrated seamlessly with a physical **Proxmox VE** hypervisor host.

---

## 📐 Network Topology

![Enterprise Network Topology](./docs/topologies/topology.png)

> **Architecture Overview:** 
> - **Edge / Gateway:** Border Router (`vIOS`) managing external NAT and Control Plane Protection.
> - **Next-Generation Firewall:** **Fortinet FortiGate** enforcing edge security policies, UTM profiles, and NAT.
> - **Core / Aggregation Layer:** Dual Multi-Layer Switches (**MLS1 & MLS2**) handling Inter-VLAN Routing, OSPF, HSRP, and Rapid PVST+.
> - **Access Layer:** Layer 2 Switches (**SW1, SW2, SW3**) with hardened port security, DAI, and DHCP Snooping.
> - **Virtualization Node:** Physical **Proxmox VE Server** hosting virtualized Windows Server and Ubuntu Server (Zabbix) connected to Core MLS.

---

## 🛠️ Key Technical Highlights & Implemented Protocols

* **Addressing & Subnetting (VLSM):** Customized IP allocation using `/27`, `/28`, and `/30` subnets to eliminate IP wastage across departments.
* **Routing & Redundancy:** 
  * **OSPF Area 0 (Process ID 1):** Configured across MLS1, MLS2, FortiGate, and Edge Router with dedicated `/32` Loopback Router IDs (`10.10.10.1` to `10.10.10.4`).
  * **HSRP Gateway Redundancy & Load Balancing:** Active/Standby state distribution with preemption enabled.
  * **Rapid PVST+ & STP Root Load Balancing:** Dual Root Bridge configuration matching HSRP active roles.
  * **Link Aggregation:** LACP EtherChannel (`Port-Channel 1`) trunks carrying explicitly allowed VLANs.
* **Switching & Layer 2/3 Hardening:**
  * Strict Access/Trunk encapsulation with dedicated Native VLAN 999 to mitigate VLAN Hopping.
  * **Port Security:** `maximum` MAC limits, `restricting` violations, and `sticky` MAC learning.
  * **Spanning Tree Security:** `PortFast` and `BPDU Guard` enabled on end-user access ports.
  * **Snooping & Spoofing Mitigation:** `DHCP Snooping` and `Dynamic ARP Inspection (DAI)` on untrusted interfaces.
* **Boundary Security & Edge:**
  * **FortiGate NGWF:** IPv4 Security Policies, NAT, and UTM Profiles for secure internet exit.
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

* **End-User Static Testing Hosts:**
  * `Windows PC (VLAN 10)`: Assigned static IP within `192.168.1.0/27` range.
  * `Linux PC (VLAN 20)`: Assigned static IP within `192.168.1.32/27` range.

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
* 🔗 [LinkedIn Profile](https://www.linkedin.com/in/) *(قم بوضع رابط حسابك هنا)*
