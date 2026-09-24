# Phase 1: Core Network Topology, Dynamic Routing, HA & Security Hardening

Welcome to the detailed technical documentation for **Phase 1**. This phase covers the foundational infrastructure, Layer 2/3 security hardening, core high availability, dynamic routing, boundary security, and device management.

---

## 📐 Phase 1 Network Topology & Architecture Scope

![Phase 1 Network Topology](../../docs/topologies/topology.png)

> [!NOTE]
> **Implementation Scope:** This document details the baseline configuration, command-line interface (CLI) outputs, topology logic, and validation steps for the core network foundation and security edge.

### 📊 Quick Technical Summary
| Domain | Implementation Standard |
| :--- | :--- |
| **Dynamic Routing** | Single-Area OSPF (Area 0) with dedicated `/32` Loopback IDs |
| **High Availability** | HSRP Gateway Redundancy paired with Rapid PVST+ Root Alignment |
| **Layer 2 Hardening** | DHCP Snooping, DAI, Port Security, Native VLAN 999 Isolation |
| **Edge Defense** | FortiGate NGFW Stateful Inspection, Dynamic PAT & CoPP |

---

## 🎯 Key Objectives & Engineering Goals
* **Core Resiliency:** Elimination of single points of failure via HSRP and Rapid PVST+.
* **Dynamic Routing:** Single-Area OSPF (Area 0) for core-to-firewall route propagation.
* **Network Segmentation & VLSM:** Efficient IP subnetting and VLAN separation across departments (Engineering, HR, Server Farm, Management) for strict traffic isolation and scalable Inter-VLAN routing.
* **Perimeter Defense:** Edge inspection, Dynamic NAT/PAT, and WAN access via FortiGate NGFW.
* **Infrastructure Protection:** Layer 2 hardening protocols (DHCP Snooping, DAI, Port Security, BPDU Guard).

---
---
## 🛠️ Technical Highlights & Implementation Scope

### 1. Layer 2 Switching, Trunking & Link Aggregation

#### 📸 Multi-Switch Trunk & Access Interface Provisioning (CLI Configuration)
![Multi-Switch Trunk and Access Config](./images/multi-switch-trunk-access-config.png)
> **Explanation:** Multi-window CLI output showing core and access switch interface provisioning. Demonstrates configuration of 802.1Q encapsulated trunk links (`switchport trunk encapsulation dot1q`), LACP Port-Channel 10 aggregation between MLS1 and MLS2, and static edge access port assignments across SW1, SW2, and SW3.

<details>
<summary><b>📄 Click to expand Layer 2 Trunking & Access Interface CLI Commands</b></summary>

```bash
# Core Switches (MLS1 / MLS2) - Inter-Switch Port-Channel Trunking
interface Port-Channel10
 switchport trunk encapsulation dot1q
 switchport mode trunk
 description MLS1_TO_MLS2_Bundle

# Access Switches (SW1 / SW2 / SW3) - Trunk Uplinks
interface range GigabitEthernet0/0 - 1
 switchport trunk encapsulation dot1q
 switchport mode trunk

# Access Switches - Edge Host Ports
interface range GigabitEthernet0/2 - 3
 switchport mode access
```
</details>

---

#### 📸 VLAN Database Provisioning & Local Subnet Ping Reachability Verification
![VLAN Database and Ping Test](./images/vlan-database-and-ping-test.png)
> **Explanation:** Multi-switch CLI execution verifying the active VLAN database (`show vlan brief`) across MLS1, SW1, SW2, and SW3. Confirms operational status for VLAN 10 (Engineers), VLAN 20 (HR), and VLAN 99 (Management), alongside successful ICMP ping reachability (`ping 192.168.1.50`) on MLS2 across the management subnet.

<details>
<summary><b>📄 Click to expand VLAN Database Setup & Verification CLI Commands</b></summary>

```bash
# Core & Access Switches - VLAN Database Creation
vlan 10
 name Engineers
vlan 20
 name HR
vlan 99
 name Management

# Verification Commands
show vlan brief

# ICMP Reachability Test
ping 192.168.1.50
```
</details>


---


#### 📸 LACP EtherChannel Bundle Verification (MLS1 & MLS2 Status)
![MLS LACP EtherChannel Status](./images/mls-lacp-etherchannel-status.png)

> **Explanation:** Side-by-side CLI verification using `show etherchannel summary` on MLS1 and MLS2. Confirms active status `Po10(SU)` utilizing LACP protocol with physical member interfaces `Gi1/0(P)` and `Gi1/3(P)` successfully bundled to deliver link redundancy and aggregated throughput.

<details>
<summary><b>📄 Click to expand LACP EtherChannel Setup & Verification CLI Commands</b></summary>

```bash
# Core Switches (MLS1 / MLS2) - LACP Port-Channel Provisioning
interface range GigabitEthernet1/0 , GigabitEthernet1/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 10 mode active
 no shutdown

# Operational Verification
show etherchannel summary
```
</details>

---

#### 📸 Rapid-PVST+ Spanning Tree & Root Bridge Load Balancing Configuration
![Rapid PVST Root Bridge Config](./images/rapid-pvst-root-bridge-config.png)
> **Explanation:** Multi-switch CLI execution enforcing `spanning-tree mode rapid-pvst` across all nodes for rapid convergence. Demonstrates STP Root Bridge load distribution; assigning MLS1 as Primary Root (`priority 4096`) for VLANs 10 and 99 and Secondary Root for VLAN 20, while assigning MLS2 as Primary Root for VLAN 20 and Secondary Root (`priority 8192`) for VLANs 10 and 99.

<details>
<summary><b>📄 Click to expand Rapid-PVST+ Root Bridge CLI Commands</b></summary>

```bash
# Core & Access Switches - Global Rapid-PVST+ Enablement
spanning-tree mode rapid-pvst

# MLS1 - Primary Root for VLAN 10,99 | Secondary for VLAN 20
spanning-tree vlan 10,99 priority 4096
spanning-tree vlan 20 root secondary

# MLS2 - Primary Root for VLAN 20 | Secondary for VLAN 10,99
spanning-tree vlan 20 root primary
spanning-tree vlan 10,99 priority 8192

```
</details>

---

#### 📸 Rapid-PVST+ Spanning Tree Root Bridge Alignment & Loop Prevention Verification
![Rapid PVST Root Bridge Verification](./images/rapid-pvst-root-bridge-verification.png)
> **Explanation:** Multi-switch CLI verification using `show spanning-tree` output proving successful Rapid-PVST+ topology calculations. Confirms MLS1 as Active Root Bridge for VLAN 99 (`This bridge is the root`) and MLS2 as Active Root Bridge for VLAN 20, while access switches (SW1, SW2, SW3) dynamically block redundant uplink paths (`Altn BLK`) to prevent Layer 2 loops.

<details>
<summary><b>📄 Click to expand Spanning Tree Verification CLI Commands</b></summary>

```bash
# Core & Access Switches - Operational Status Verification
show spanning-tree vlan 10
show spanning-tree vlan 20
show spanning-tree vlan 99
```
</details>





---
---



### 2. Switching & Layer 2 Security Hardening

#### 📸 Native VLAN Isolation & Security Hardening (VLAN Hopping Mitigation)
![SW1 Native VLAN Security Hardening](./images/sw1-native-vlan-security-hardening.png)
> **Explanation:** CLI execution on SW1 establishing a dedicated black-hole Native VLAN (`vlan 999 Native_Vlan`) and reassigning trunk uplinks using `switchport trunk native vlan 999`. This neutralizes Default Native VLAN 1 exploitation risks, mitigating VLAN Hopping and Double-Tagging attack vectors across trunk connections.

<details>
<summary><b>📄 Click to expand Native VLAN Security Hardening CLI Commands</b></summary>

```bash
# Create Isolated Native VLAN
vlan 999
 name Native_Vlan

# Assign to Trunk Interfaces
interface range GigabitEthernet0/0 - 1
 switchport trunk native vlan 999
```
</details>

---

#### 📸 Edge Port Security, BPDU Guard & Sticky MAC Hardening
![SW1 Port Security and BPDU Guard Config](./images/sw1-port-security-and-bpduguard-config.png)
> **Explanation:** CLI interface range configuration on SW1 enforcing Layer 2 access port hardening. Enforces `spanning-tree portfast` for fast link transition, `spanning-tree bpduguard enable` to block unauthorized switch insertions, and `switchport port-security` capped at a maximum of 2 sticky MAC addresses with `restrict` violation action.

<details>
<summary><b>📄 Click to expand Port Security & BPDU Guard CLI Commands</b></summary>

```bash
# Edge Access Port Security Hardening
interface range GigabitEthernet0/2 - 3
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```
</details>

---

#### 📸 Comprehensive STP Security Framework (Root Guard, BPDU Guard & PortFast Enforcement)
![STP Security RootGuard BPDUGuard PortFast Config](./images/stp-security-rootguard-bpduguard-portfast-config.png)
> **Explanation:** Multi-switch CLI execution deploying full Spanning Tree defense protocols. Displays `spanning-tree guard root` configured on core links (MLS1 & MLS2) to prevent unauthorized Root Bridge hijacking, alongside `spanning-tree portfast` and `spanning-tree bpduguard enable` enforced on access switch edge interfaces (SW1, SW2, SW3) to block unauthorized switch insertions.

<details>
<summary><b>📄 Click to expand Spanning Tree Security Hardening CLI Commands</b></summary>

```bash
# Core Switches (MLS1 / MLS2) - Enable Root Guard
interface range GigabitEthernet0/1 - 2 , GigabitEthernet1/1
 spanning-tree guard root

# Access Switches (SW1 / SW2 / SW3) - Enable BPDU Guard & PortFast
interface range GigabitEthernet0/2 - 3
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
```
</details>

---

#### 📸 DHCP Snooping & Dynamic ARP Inspection (DAI) Security Enforcement
![SW1 DHCP Snooping and DAI Config](./images/sw1-dhcp-snooping-and-dai-config.png)
> **Explanation:** CLI execution on SW1 implementing Layer 2 mitigation against Rogue DHCP servers and ARP Poisoning attacks. Enables `ip dhcp snooping` and `ip arp inspection` on VLAN 10 while establishing explicit trust boundaries (`ip dhcp snooping trust` and `ip arp inspection trust`) across trunk uplinks connected toward the core layer.

<details>
<summary><b>📄 Click to expand DHCP Snooping & DAI CLI Commands</b></summary>

```bash
# Global & VLAN Enablement
ip dhcp snooping
ip dhcp snooping vlan 10
ip arp inspection vlan 10

# Configure Trusted Uplinks
interface range GigabitEthernet0/0 - 1
 ip dhcp snooping trust
 ip arp inspection trust
```
</details>

---

#### 📸 Dynamic ARP Inspection (DAI) Active Mitigation & Invalid ARP Denial Logs
![SW2 DAI Invalid ARP Mitigation Logs](./images/sw2-dai-invalid-arp-mitigation-logs.png)
> **Explanation:** Live Syslog security execution on SW2 displaying active packet suppression (`%SW_DAI-4-DHCP_SNOOPING_DENY`). Verifies that unauthorized ARP requests failing match against the trusted DHCP Snooping binding table on VLAN 20 are dynamically dropped to prevent Man-in-the-Middle (MitM) ARP poisoning attacks.

<details>
<summary><b>📄 Click to expand DAI Mitigation Verification CLI Commands</b></summary>

```bash
# Verify DAI Statistics & Active Security Violations
show ip arp inspection statistics
show logging | include SW_DAI
```
</details>

---

#### 📸 DAI Active ARP Poisoning Prevention & Gateway Protection Logs
![SW2 DAI ARP Poisoning Prevention Logs](./images/sw2-dal-arp-poisoning-prevention-logs.png)
> **Explanation:** Extended Syslog audit on SW2 showing real-time mitigation against spoofed ARP requests targeting VLAN 20 subnets and gateway addresses (`192.168.1.34` & `192.168.1.49`). Validates that all ARP traffic lacking matching bindings in the DHCP Snooping database is blocked to preserve Layer 2 data integrity.

<details>
<summary><b>📄 Click to expand DAI Security Audit CLI Commands</b></summary>

```bash
# Verify Active Log Events and Inspections
show logging | include SW_DAI
show ip arp inspection vlan 20
```
</details>



---
---

### 3. Addressing, Layer 3 Switching & Inter-VLAN Routing

![FortiGate CLI Interface IP Config](./images/fortigate-cli-interface-ip-config.png)

![FortiGate GUI Interfaces and Zones](./images/fortigate-gui-interfaces-and-zones.png)

![Ubuntu Server Static IP Installer Config](./images/ubuntu-server-static-ip-installer-config.png)

![Windows Server IP Config and Gateway Ping](./images/windows-server-ip-config-and-gateway-ping.png)

![Windows Server Full Connectivity Ping Test](./images/windows-server-full-connectivity-ping-test.png)

![VPC4 Continuous Internet Ping Reachability](./images/vpc4-continuous-internet-ping-reachability.png)

![Linux Ubuntu End-to-End Traceroute Internet](./images/Linux%20Ubuntu-end-to-end-traceroute-internet-path.jpg)

---

### 4. Core Dynamic Routing & High Availability (HA)

![MLS OSPF Point-to-Point Config](./images/mls-ospf-point-to-point-config.png)

![MLS1 OSPF Passive Interface Config](./images/mls1-ospf-passive-interface-config.png)

![MLS OSPF Routing Table and Neighbors](./images/mls-ospf-routing-table-and-neighbors.png)

![FortiGate OSPF GUI Config](./images/fortigate-ospf-gui-config.png)

![FortiGate GUI Routing Monitor Table](./images/fortigate-gui-routing-monitor-table.png)

![MLS HSRP Brief and IP Interface Summary](./images/mls-hsrp-brief-and-ip-interface-summary.png)

![HSRP VLAN99 Active Standby Status](./images/hsrp-vlan99-active-standby-status.png)

![HSRP Failover Resilience Ping Test](./images/hsrp-failover-resilience-ping-test.png)

![HSRP Management Ping Reachability](./images/hsrp-management-ping-reachability.png)

---

### 5. Boundary Security, NAT & Edge Protection

![FortiGate GUI Firewall Policy NAT](./images/fortigate-gui-firewall-policy-nat.png)

![vIOS Edge NAT PAT Config](./images/vios-edge-nat-pat-config.png)

![Edge Router NAT ACL Matches Verification](./images/edge-router-nat-acl-matches-verification.png)

![Edge Router CoPP ICMP Policing Config](./images/edge-router-copp-icmp-policing-config.png)

---

### 6. Device Hardening & Infrastructure Management

![EVE-NG VM Settings](./images/eve-ng-vm-settings.png)

![EVE-NG VMware Console](./images/eve-ng-vmware-console.png)

![EVE-NG SFTP WinSCP Connection](./images/eve-ng-sftp-winscp-connection.png)

![EVE-NG QEMU Images Directory Structure](./images/eve-ng-qemu-images-directory-structure.png)

![Proxmox VE Web Management Dashboard](./images/proxmox-ve-web-management-dashboard.png)

![Proxmox Windows Server VM Summary](./images/proxmox-windows-server-vm-summary.png)

![Windows Server OOBE Administrator Setup](./images/windows-server-oobe-administrator-setup.png)

![Ubuntu Zabbix Server Post Install Console](./images/ubuntu-zabbix-server-post-install-console.jpg)

![MLS SSH Hardening Config](./images/mls-ssh-hardening-config.png)

![FortiGate GUI Dashboard Status](./images/fortigate-gui-dashboard-status.png)


---




[⬅️ Back to Main Repository Overview](../../README.md)
