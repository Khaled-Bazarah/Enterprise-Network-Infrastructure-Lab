# Phase 1: Core Network Topology, Dynamic Routing, HA & Security Hardening

Welcome to the detailed technical documentation for **Phase 1**. This phase covers the foundational infrastructure, Layer 2/3 security hardening, core high availability, dynamic routing, boundary security, and device management.

---

## 🛠️ Technical Highlights & Implementation Scope

### 1. Layer 2 Switching, Trunking & Link Aggregation
* **Link Aggregation:** Configured LACP EtherChannel (`Port-Channel 1`) trunks carrying explicitly allowed VLANs between core switches for high-bandwidth dynamic trunking.
* **Rapid PVST+ & STP Root Load Balancing:** Deployed Rapid Spanning Tree Protocol (RPVST+) with a dual Root Bridge hierarchy aligned directly with HSRP active roles to prevent loops and optimize Layer 2 paths.

---

### 2. Switching & Layer 2 Security Hardening
* **Unused Port Security (Physical Protection):** Manually disabled all unused/inactive switchports (`shutdown`) across Access and Core switches to block unauthorized physical connections.
* **Trunk Security:** Enforced strict Access/Trunk encapsulation with a dedicated Native VLAN 999 across all trunk lines to mitigate VLAN Hopping attacks.
* **Port Security:** Configured `maximum` MAC address limits, `restrict` violation actions, and `sticky` MAC address learning on user-facing access ports.
* **Spanning Tree Security:** Enabled `PortFast` and `BPDU Guard` on all end-user access ports for instant transition and protection against rogue switch insertion.
* **Snooping & Spoofing Mitigation:** Implemented `DHCP Snooping` and `Dynamic ARP Inspection (DAI)` on untrusted interfaces to block rogue DHCP servers and ARP poisoning attacks.

---

### 3. Addressing, Layer 3 Switching & Inter-VLAN Routing
* **Addressing & Subnetting (VLSM):** Designed an efficient Variable Length Subnet Masking (VLSM) IP scheme using `/27`, `/28`, and `/30` subnets to optimize address space and isolate departmental traffic.
* **Layer 3 Switching & Inter-VLAN Routing:**
  * Enabled global IP Routing (`ip routing`) on Multi-Layer Switches (`MLS1` & `MLS2`).
  * Configured Switch Virtual Interfaces (SVIs) across all VLANs (`VLAN 10, 20, 30, 99`) to allow MLS devices to function as Layer 3 core gateways and perform high-speed line-rate Inter-VLAN routing.

---

### 4. Core Dynamic Routing & High Availability (HA)
* **OSPF Area 0 (Process ID 1):** Configured single-area OSPF across `MLS1`, `MLS2`, and `FortiGate` using dedicated `/32` Loopback Router IDs (`10.10.10.1` to `10.10.10.3`) for dynamic route propagation.
* **HSRP Gateway Redundancy & Load Balancing:** Deployed Hot Standby Router Protocol (HSRP) with active/standby state distribution and preemption enabled across SVIs to ensure seamless gateway failover.

---

### 5. Boundary Security, NAT & Edge Protection
* **FortiGate NGFW & Edge Router:** Configured IPv4 Security Policies, Dynamic NAT/PAT, and UTM Profiles for secure internet egress via Web GUI & CLI.
* **Default Internet Routing:** Configured a Static Default Route (`0.0.0.0 0.0.0.0`) pointing to the ISP upstream interface (`192.168.8.x`) on the Edge device (`192.168.8.250`) to provide full internet outbound reachability for all internal subnets.
* **Traffic Filtering & Control Plane Security:** Applied Access Control Lists (ACLs) and Control Plane Policing (CoPP) on edge routing interfaces for infrastructure protection and DoS mitigation.

---

### 6. Device Hardening & Infrastructure Management
* **Device Hardening & Line Security:** Enforced local user accounts with `secret` passwords, `service password-encryption`, SSH v2 with RSA key pairs, line console/vty hardening, and a dedicated Out-of-Band Management network (`VLAN 99`).

---

[⬅️ Back to Main Repository Overview](../../README.md)
