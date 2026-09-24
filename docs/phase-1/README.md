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
![SW2 DAI ARP Poisoning Prevention Logs](./images/sw2-dai-arp-poisoning-prevention-logs.png)
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

#### 📸 FortiGate CLI Interface IPv4 & Management Provisioning
![FortiGate CLI Interface IP Config](./images/fortigate-cli-interface-ip-config.png)
> **Explanation:** FortiGate CLI session demonstrating static IPv4 interface configuration on `port1` (`192.168.1.85/30`). Enforces management access protocols (`set allowaccess ping http ssh https`) and validates active interface bindings via `diagnose ip address list`.

<details>
<summary><b>📄 Click to expand FortiGate Interface CLI Commands</b></summary>

```bash
# Configure Interface IP & Management Rights
config system interface
    edit port1
        set mode static
        set ip 192.168.1.85 255.255.255.252
        set allowaccess ping http ssh https
    next
end

# Verify IP Binding Status
diagnose ip address list
```
</details>

---


#### 📸 FortiGate Web GUI Interface Provisioning & Security Zone Binding
![FortiGate GUI Interfaces and Zones](./images/fortigate-gui-interfaces-and-zones.png)
> **Explanation:** FortiGate Web GUI network overview (`Network > Interfaces`) displaying active physical interfaces (`port1`, `port2`, `port3`) assigned to dedicated `/30` Point-to-Point transit subnets. Demonstrates interface consolidation grouping core uplinks (`port2` & `port3`) into a single logical zone (`LAN_Zone`) to streamline firewall policy enforcement.



---

#### 📸 Ubuntu Server Static IPv4 Network Provisioning on Proxmox VE (VLAN 30)
![Ubuntu Server Static IP Installer Config](./images/ubuntu-server-static-ip-installer-config.png)
> **Explanation:** Proxmox VE noVNC console session configuring static IPv4 network settings for the Ubuntu Server VM (`VMID 101`). Assigns static IP `192.168.1.102/28` on interface `ens18` within the Server Farm subnet (VLAN 30) and points to the HSRP redundant default gateway (`192.168.1.97`).



---



#### 📸 Windows Server Active Directory Static Network Provisioning & HSRP Reachability
![Windows Server IP Config and Gateway Ping](./images/windows-server-ip-config-and-gateway-ping.png)
> **Explanation:** Proxmox VE noVNC console session (`VMID 100`) validating Windows Server Domain Controller network parameters (`192.168.1.101/29`). Demonstrates verified IPv4 static address configuration pointing to HSRP Default Gateway (`192.168.1.97`).

---

#### 📸 Windows Server Internet Reachability & End-to-End Connectivity Verification
![Windows Server Full Connectivity Ping Test](./images/windows-server-full-connectivity-ping-test.png)
> **Explanation:** CMD terminal verification on Windows Server (`VMID 100`) confirming successful bidirectional IP communication. Demonstrates end-to-end reachability across internal transit gateways (`192.168.1.1`), external Internet destinations (`8.8.8.8` Google Public DNS).

<details>
<summary><b>📄 Click to expand Windows Server Ping Audit CLI Commands</b></summary>

```cmd
# Verify Router, Public Internet, and LAN Reachability
ping 192.168.1.1
ping 8.8.8.8
```
</details>

---


#### 📸 Client End-Host Continuous Internet Reachability Verification (VPCS WAN Audit)
![VPC4 Continuous Internet Ping Reachability](./images/vpc4-continuous-internet-ping-reachability.png)
> **Explanation:** VPCS CLI session on client node `VPC4` verifying continuous outbound Internet reachability via extended ICMP ping operations (`8.8.8.8` Google DNS & `1.1.1.1` Cloudflare DNS). Confirms end-to-end dataplane traversal spanning Access Trunks, Active HSRP Gateway SVI, Core OSPF Routing, FortiGate NAT, and WAN Gateway.

<details>
<summary><b>📄 Click to expand VPCS Internet Reachability CLI Commands</b></summary>

```bash
# Verify Outbound Public Internet Reachability
ping 8.8.8.8 -t
ping 1.1.1.1 -t
```
</details>

---


#### 📸 Ubuntu Linux Client End-to-End Traceroute Verification to External Internet
![Linux Ubuntu End-to-End Traceroute Internet](./images/Linux%20Ubuntu-end-to-end-traceroute-internet-path.jpg)
> **Explanation:** Live Linux terminal session on an Ubuntu client node issuing `traceroute 8.8.8.8`. Confirms multi-hop traffic path traversal starting from the internal active HSRP gateway (`192.168.1.35`), hopping through the FortiGate firewall (`192.168.1.82`), passing the Edge Router transit interface (`192.168.1.86`), and successfully traversing the ISP infrastructure to reach Google Public DNS.

<details>
<summary><b>📄 Click to expand Ubuntu Linux Traceroute CLI Commands</b></summary>

```bash
# Verify End-to-End Hop-by-Hop Network Path
traceroute 8.8.8.8
```
</details>



---
---

### 4. Core Dynamic Routing & High Availability (HA)


#### 📸 Core Multi-Layer Switch L3 Interface & OSPF Point-to-Point Network Provisioning
![MLS OSPF Point-to-Point Config](./images/mls-ospf-point-to-point-config.png)
> **Explanation:** Side-by-side CLI provisioning on MLS1 and MLS2 converting physical interfaces (`g0/0`) into Layer 3 routed ports (`no switchport`). Enforces `ip ospf network point-to-point` to bypass unnecessary OSPF DR/BDR elections across point-to-point transit links, accelerating adjacency formation and OSPF topology convergence.

<details>
<summary><b>📄 Click to expand OSPF L3 Interface CLI Commands</b></summary>

```bash
# Core Switches (MLS1 / MLS2) - L3 Interface & OSPF Optimization
interface GigabitEthernet0/0
 no switchport
 ip ospf network point-to-point
```
</details>

---


#### 📸 OSPF Passive Interface Hardening on User & Management SVIs
![MLS1 OSPF Passive Interface Config](./images/mls1-ospf-passive-interface-config.png)
> **Explanation:** CLI execution on MLS1 configuring `passive-interface` settings across client and management SVIs (`Vlan10`, `Vlan20`, `Vlan99`). Suppresses unnecessary OSPF Hello packet transmissions toward edge access ports to optimize bandwidth, eliminate security risks, and isolate routing adjacencies while continuing to advertise internal subnets into OSPF Area 0.

<details>
<summary><b>📄 Click to expand OSPF Passive Interface CLI Commands</b></summary>

```bash
# Core Switch OSPF Hardening
router ospf 1
 passive-interface Vlan10
 passive-interface Vlan20
 passive-interface Vlan99
```
</details>

---


#### 📸 Core Layer OSPF Adjacency, LSDB Synchronization & Routing Table Verification
![MLS OSPF Routing Table and Neighbors](./images/mls-ospf-routing-table-and-neighbors.png)
> **Explanation:** Side-by-side CLI audit on MLS1 (`Router-ID 10.10.10.1`) and MLS2 (`Router-ID 10.10.10.2`) confirming complete OSPF Area 0 convergence. Proves active `FULL/DR` and `FULL/BDR` neighbor adjacencies across SVIs, synchronized Link-State Database (LSDB) states, and proper insertion of OSPF inter-VLAN routes alongside candidate default routes (`S* 0.0.0.0/0`) pointing toward the FortiGate firewall.

<details>
<summary><b>📄 Click to expand OSPF Operational Verification CLI Commands</b></summary>

```bash
# Core Switches OSPF Audit Commands
show ip route
show ip ospf interface brief
show ip ospf neighbor
show ip ospf database
```
</details>

---


#### 📸 FortiGate Web GUI Dynamic OSPF Area 0 Routing Provisioning
![FortiGate OSPF GUI Config](./images/fortigate-ospf-gui-config.png)
> **Explanation:** FortiGate Web GUI OSPF menu (`Network > OSPF`) displaying dynamic routing setup within Area 0 (`0.0.0.0`). Demonstrates network prefix declarations for transit point-to-point subnets (`192.168.1.76/30` and `192.168.1.80/30`) across active participating interfaces (`port2` and `port3`) to form OSPF adjacencies with MLS1 and MLS2.

---


#### 📸 FortiGate Web GUI Routing Monitor & Active OSPF ECMP Verification
![FortiGate GUI Routing Monitor Table](./images/fortigate-gui-routing-monitor-table.png)
> **Explanation:** FortiGate Routing Monitor widget dashboard (`Dashboard > Network > Routing`) displaying the active RIB containing 14 converged routes. Validates dual-homed OSPF Equal-Cost Multi-Path (ECMP) route execution across `port2` (MLS1) and `port3` (MLS2) for internal subnets (VLANs 10, 20, 30, and 99), alongside the default candidate route (`0.0.0.0/0 via 192.168.1.86`) pointing toward the Edge Gateway.


#### 📸 HSRP First Hop Redundancy, Active/Standby State & Interface Verification
![MLS HSRP Brief and IP Interface Summary](./images/mls-hsrp-brief-and-ip-interface-summary.png)
> **Explanation:** Dual-console CLI execution on core switches MLS1 and MLS2 running `show standby brief` and `show ip interface brief`. Validates optimal HSRP Load Sharing; MLS1 operates as Active Gateway for VLANs 10, 30, and 99 while serving as Standby for VLAN 20. Conversely, MLS2 operates as Active Gateway for VLAN 20 and Standby for VLANs 10 and 99, enforcing zero single point of failure (SPOF) across client default gateways.

<details>
<summary><b>📄 Click to expand HSRP Operational Audit CLI Commands</b></summary>

```bash
# Core Switches HSRP Operational Verification
show standby brief
show ip interface brief
```
</details>

---


#### 📸 Detailed HSRP Protocol Mechanics, Virtual MAC & Topology Alignment (VLAN 99 Audit)
![HSRP VLAN99 Active Standby Status](./images/hsrp-vlan99-active-standby-status.png)
> **Explanation:** Detailed HSRP CLI audit using `show standby` on core switches MLS1 and MLS2 alongside the full enterprise topology canvas. Proves active gateway state for Management VLAN 99 on MLS1 (`192.168.1.49`), demonstrating successful binding of the HSRP Virtual MAC (`0000.0c07.ac63`), configured priority metrics (`110` with preemption), and standby readiness on MLS2.

<details>
<summary><b>📄 Click to expand Detailed HSRP Inspection CLI Commands</b></summary>

```bash
# Detailed HSRP Inspection for Management VLAN
show standby vlan 99
show standby group 99
```
</details>

---

#### 📸 Dynamic HSRP Failover Simulation, Active Recovery & Client Traffic Convergence
![HSRP Failover Resilience Ping Test](./images/hsrp-failover-resilience-ping-test.png)
> **Explanation:** Live multi-console failover validation test triggering manual interface shutdown (`shutdown` / `no shutdown`) on MLS1 SVI. Syslog streams verify instantaneous state migration (`Standby -> Active`) on backup switch MLS2. Simultaneously, VPCS continuous ping outputs to `8.8.8.8` confirm minimal packet loss (only 4 drop sequence ticks during transition) before traffic dynamically re-converges, establishing robust High Availability (HA) across the core network layer.

<details>
<summary><b>📄 Click to expand HSRP Failover Test CLI Commands</b></summary>

```bash
# Trigger SVI Shutdown to Test Gateway Failover
interface Vlan10
 shutdown

# Re-enable Interface to Test Preemption Recovery
interface Vlan10
 no shutdown
```
</details>

---

#### 📸 Access Layer Inter-Switch Reachability & HSRP Management Gateway Ping Verification
![HSRP Management Ping Reachability](./images/hsrp-management-ping-reachability.png)
> **Explanation:** Multi-console execution across access switches SW1, SW2, and SW3 validating Layer 2/3 management connectivity on VLAN 99. Demonstrates 100% ICMP ping success rates targeting core physical SVI management IPs (`192.168.1.50` & `192.168.1.51`) alongside the virtual HSRP gateway address (`192.168.1.49`), confirming full in-band management reachability and proper trunking traversal.

<details>
<summary><b>📄 Click to expand Management Connectivity Verification CLI Commands</b></summary>

```bash
# Verify Management VLAN 99 Reachability
ping 192.168.1.49
ping 192.168.1.50
ping 192.168.1.51
```
</details>

---
---

### 5. Boundary Security, NAT & Edge Protection

#### 📸 FortiGate Web GUI Outbound IPv4 Firewall Policy & Dynamic NAT Provisioning
![FortiGate GUI Firewall Policy NAT](./images/fortigate-gui-firewall-policy-nat.png)

> **Explanation:** FortiGate Web GUI Policy engine (`Policy & Objects > Firewall Policy`) enforcing policy rule `LAN_TO_INTERNET`. Permits traffic originating from core aggregate ingress interfaces (`port2` & `port3`) routed out through egress interface (`port1`). Enforces Dynamic Source NAT (`NAT Enabled`) to map internal private VLAN IP addresses for public WAN routing, coupled with active traffic session logging.

---

#### 📸 Edge Router Interface Boundary Definition & Dynamic NAT Overload (PAT) Provisioning
![vIOS Edge NAT PAT Config](./images/vios-edge-nat-pat-config.png)
> **Explanation:** CLI provisioning session on `Router1` (vIOS) establishing Port Address Translation (PAT) boundary dynamics. Configures perimeter interfaces (`g0/0` as `ip nat outside` and `g0/1` as `ip nat inside`), defines internal ACL filter matching `192.168.1.0/24`, and applies `ip nat inside source list 1 interface g0/0 overload` to permit multiplexed internet outbound access.

<details>
<summary><b>📄 Click to expand Edge Router PAT CLI Commands</b></summary>

```bash
# Perimeter Interface NAT Direction Assignment
interface GigabitEthernet0/0
 ip nat outside

interface GigabitEthernet0/1
 ip nat inside

# Standard ACL Matching LAN Subnets
access-list 1 permit 192.168.1.0 0.0.0.255

# Apply Dynamic Port Address Translation (PAT)
ip nat inside source list 1 interface GigabitEthernet0/0 overload
```
</details>

---

#### 📸 Edge Router Control Plane Policing (CoPP) & ICMP Rate-Limiting Hardening
![Edge Router CoPP ICMP Policing Config](./images/edge-router-copp-icmp-policing-config.png)
> **Explanation:** MQC CLI configuration session on `Edge_Router` enforcing Control Plane Policing (CoPP). Classifies CPU-bound ICMP traffic via `class-map ICMP-TRAFFIC`, enforces aggressive rate-limiting policing (`police 8000 conform-action transmit exceed-action drop`) within `policy-map COPP-POLICY`, and binds the policy to the router's `control-plane` interface to shield the Control Processor from ICMP flood attacks and Denial-of-Service (DoS) exploits.

<details>
<summary><b>📄 Click to expand Edge Router CoPP Security CLI Commands</b></summary>

```bash
# Define ICMP Inspection ACL
access-list 100 permit icmp any any

# MQC Class-Map Configuration
class-map match-all ICMP-TRAFFIC
 match access-group 100
exit

# MQC Policy-Map Rate Limiting
policy-map COPP-POLICY
 class ICMP-TRAFFIC
  police 8000 conform-action transmit exceed-action drop
 exit
exit

# Bind Policy to Control Plane Subsystem
control-plane
 service-policy input COPP-POLICY
exit
```
</details>



---
---



### 6. Device Hardening & Infrastructure Management

#### 📸 EVE-NG Emulator Virtual Machine Hardware Provisioning & VMnet Binding (VMware Workstation)
![EVE-NG VM Settings](./images/eve-ng-vm-settings.png)
> **Explanation:** VMware Workstation management view illustrating hardware specifications for the primary EVE-NG emulation node. Confirms allocation of 8.0 GB RAM, 3 vCPUs with nested virtualization support, a 300 GB virtual storage drive, and dual custom network adapters (`VMnet0` Bridged and `VMnet1` Host-Only) enabling external integration with physical lab equipment and Proxmox hypervisors.

---

#### 📸 EVE-NG Emulation Server CLI Bootup & Web Management Interface IP Binding
![EVE-NG VMware Console](./images/eve-ng-vmware-console.png)
> **Explanation:** EVE-NG Linux CLI console banner (Ubuntu 22.04 LTS) running under VMware Workstation. Confirms active root system authentication and displays the web management IP binding on bridge interface `pnet0` (`192.168.8.126`), enabling HTTP/HTTPS web GUI connectivity for dynamic topology creation and multi-vendor device control.

---

#### 📸 WinSCP SFTP Management Session & EVE-NG Node Image Repository Provisioning
![EVE-NG SFTP WinSCP Connection](./images/eve-ng-sftp-winscp-connection.png)

> **Explanation:** WinSCP SFTP client interface initializing a secure SSH/SFTP session to the EVE-NG server (`192.168.8.136:22`). Illustrates local file staging on `D:\` containing multi-vendor appliances (such as FortiGate KVM/QEMU images and Cisco vIOS) prior to directory transfer into `/opt/unetlab/addons/qemu/` for emulator instantiation.

---

#### 📸 EVE-NG QEMU Appliance Directory Staging & Cisco vIOS Image Deployment (WinSCP SFTP)
![EVE-NG QEMU Images Directory Structure](./images/eve-ng-qemu-images-directory-structure.png)
> **Explanation:** WinSCP SFTP file manager connected to EVE-NG (`root@192.168.8.136`) navigating the core QEMU storage path (`/opt/unetlab/addons/qemu/`). Demonstrates structured directory staging for multi-vendor network images including Cisco vIOS L3 (`vios-adventerprisek9-m...`), Cisco vIOS L2 (`viosl2-adventerprisek9-m...`), FortiGate firewalls (`fortinet-v7.0.5` / `fortinet-v7.0.12`), and endpoint client OS images (`win10`).

<details>
<summary><b>📄 Click to expand EVE-NG QEMU Deployment & Fixpermissions CLI Commands</b></summary>

```bash
# Directory Creation Example for Cisco vIOS L3 Router Image
mkdir -p /opt/unetlab/addons/qemu/vios-adventerprisek9-m.SPA.159-3.M6

# Execute EVE-NG Permission Repair Utility Post-Upload
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```
</details>

---

#### 📸 Proxmox VE Datacenter Management Console & Automated Bulk Task Audit
![Proxmox VE Web Management Dashboard](./images/proxmox-ve-web-management-dashboard.png)
> **Explanation:** Proxmox VE Web Management UI (`https://192.168.1.100:8006`) highlighting node `pve` Datacenter options and local storage volumes (`local` and `local-lvm`). Bottom task panel validates successful execution (`Status OK`) of automated hypervisor bulk operational commands (`Bulk start/shutdown VMs and Containers`), proving compute readiness for enterprise host infrastructure.

---

#### 📸 Windows Server Initial OOBE Provisioning & Administrator Password Configuration (Proxmox VE)
![Windows Server OOBE Administrator Setup](./images/windows-server-oobe-administrator-setup.png)
> **Explanation:** Proxmox VE noVNC console session (`WinServer` VMID 100) completing the Windows Server Out-Of-Box Experience (OOBE) setup phase. Demonstrates setting local `Administrator` credentials prior to domain promotion, role installation (AD DS & DHCP), and Static IP assignment on Server Farm VLAN 30.

---

#### 📸 Zabbix Monitoring Server Console Bootup, Cloud-Init Staging & Static IP Verification
![Ubuntu Zabbix Server Post Install Console](./images/ubuntu-zabbix-server-post-install-console.jpg)
> **Explanation:** Proxmox VE console view displaying the initialization of the Ubuntu-based Zabbix Monitoring Server (`khaled@zabbix`). Confirms network interface `ens18` static IP binding (`192.168.1.102`), optimal hardware resource utilization, and successful execution of Cloud-Init scripts alongside SSH host key fingerprint generation for secure remote telemetry collection.

---

![MLS SSH Hardening Config](./images/mls-ssh-hardening-config.png)
#### 📸 Core Multi-Layer Switch Base Device Provisioning, Local AAA & SSH v2 Hardening
![MLS SSH Hardening Config](./images/mls-ssh-hardening-config.png)
> **Explanation:** Side-by-side CLI session on core layer switches MLS1 and MLS2 enforcing baseline security protocols. Configures local privilege level 15 admin accounts (`service password-encryption`), defines domain `kob.local` to generate 2048-bit RSA encryption keys (`crypto key generate rsa modulus 2048`), and locks down Line Console/VTY access (`exec-timeout 10 0`, `transport input ssh`, `login local`) to eliminate unencrypted clear-text Telnet vulnerabilities.

<details>
<summary><b>📄 Click to expand Core Switch SSH & AAA Hardening CLI Commands</b></summary>

```bash
# Set Hostname & Admin AAA Credentials
hostname MLS1
username admin privilege 15 password khale_d10
service password-encryption

# Crypto RSA Key & SSH v2 Activation
ip domain-name kob.local
crypto key generate rsa modulus 2048
ip ssh version 2

# Secure Line Console 0
line console 0
 exec-timeout 10 0
 login local
exit

# Secure Line VTY 0-15 (Restrict to SSH Only)
line vty 0 15
 exec-timeout 10 0
 transport input ssh
 login local
exit
```
</details>

---



#### 📸 FortiGate Web GUI Dashboard, Hardware Provisioning & System Status Audit
![FortiGate GUI Dashboard Status](./images/fortigate-gui-dashboard-status.png)
> **Explanation:** FortiGate Web Management Console (`Dashboard > Status`) displaying core system metrics for virtual firewall instance `FortiFirewall-VM64-KVM` running FortiOS v7.0.12. Validates KVM hypervisor hardware allocation (1 vCPU, 2 GB RAM with 49% utilization), current CPU load metrics, and active admin sessions (Console/HTTP) across the perimeter firewall boundary.

---




[⬅️ Back to Main Repository Overview](../../README.md)
