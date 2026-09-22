# Enterprise Multi-Tier Network & Infrastructure Architecture Lab 🚀

Welcome to the repository for my **Enterprise Network & Systems Infrastructure Lab**. This project features a multi-tiered, highly available, and secured enterprise-grade topology designed and emulated using **EVE-NG**, integrated seamlessly with a physical **Proxmox VE** hypervisor node.

---

## 📐 Network Topology

![Enterprise Network Topology](./docs/topologies/topology.png)

> **Architecture Overview:** 
> - **Edge / Gateway:** ISP Router connected to a Next-Generation Firewall (**Fortinet FortiGate**).
> - **Core / Aggregation Layer:** Dual Multi-Layer Switches (**MLS1 & MLS2**) for Layer 3 Routing & High Availability.
> - **Access Layer:** Access Switches (**SW1, SW2, SW3**) providing secure port connectivity to end-hosts and servers.
> - **Virtualization Node:** Physical **Proxmox VE Server** hosting Windows Server (AD DS) and Ubuntu Server (Monitoring).

---

## 🛠️ Key Technical Highlights & Capabilities

* **Routing & Redundancy:** OSPF Dynamic Routing, HSRP (Hot Standby Router Protocol), LACP (Link Aggregation / EtherChannel), Subnetting & VLSM (Variable Length Subnet Masking).
* **Network & Boundary Security:** FortiGate Firewall Policies & NAT, Port Security, Access Control Lists (ACLs), DHCP Snooping, Dynamic ARP Inspection (DAI), Control Plane Protection (CoPP / DoS Mitigation).
* **Virtualization & Identity Management:** Proxmox VE, Active Directory Domain Services (AD DS), DNS, DHCP, Group Policy Objects (GPO).
* **Monitoring & Operations:** Zabbix Monitoring Solution, Wireshark Packet Analysis, Out-of-Band Management VLAN.
* **Disaster Recovery:** Automated Infrastructure Backup via Veeam.

---

## 🚀 Implementation Roadmap & Status

- [x] **Phase 1: Core Network Topology, HA Routing/Switching & Layer 2/3 Hardening**
- [ ] **Phase 2: Active Directory Services, Identity, DNS, DHCP & GPO Integration**
- [ ] **Phase 3: Centralized Infrastructure Monitoring (Zabbix & Deep Packet Inspection)**
- [ ] **Phase 4: Disaster Recovery & Automated Enterprise Backup (Veeam)**
- [ ] **Phase 5: Secure Remote Access (FortiGate SSL-VPN & Multi-Factor Authentication)**

---

## 📑 Phase 1 Detailed Implementation

### 1. Address Space Optimization (VLSM)
Designed custom subnetting schemes (VLSM) across all VLANs to maximize IP address utilization and eliminate address space waste, ensuring dedicated broadcast domains for users, servers, and management.

### 2. High Availability & Link Aggregation
* **Gateway Redundancy:** Implemented **HSRP** across Core Multi-Layer Switches (`MLS1` & `MLS2`) to eliminate single points of failure for default gateways.
* **Bandwidth Aggregation:** Configured **LACP EtherChannel** trunks between Core and Access switches to aggregate throughput and supply instant link-level failover.

### 3. Layer 2 / Layer 3 Hardening & DoS Prevention
* **Access Port Hardening:** Enabled **Port Security** on user-facing switchports.
* **Mitigating Rogue Services & Attacks:** Deployed **DHCP Snooping** and **Dynamic ARP Inspection (DAI)** to prevent Man-in-the-Middle (MitM) and ARP poisoning attacks.
* **Control Plane Protection:** Implemented **CoPP** policies and ACL rate-limiting on core devices to protect hardware control planes against DoS/DDoS flooding.

### 4. Management & OOB Administration
* Isolated **Management VLAN** configured across all switches and routers with SSH access enabled.
* Direct Out-of-Band (OOB) bridging enabled from host machine to FortiGate Web GUI and Proxmox VE Administration console.

---

## 👤 Author & Engineer

**Khaled Bazarah**  
*Computer & Network Engineer*  
* Certified: Cisco Certified Network Associate (CCNA 200-301)  
* Accredited Computer Engineer – Saudi Council of Engineers  
* 🔗 [LinkedIn Profile](https://www.linkedin.com/in/10khaled-bazarah)
