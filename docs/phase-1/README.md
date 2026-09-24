# Phase 1: Core Network Topology, Dynamic Routing, HA & Security Hardening

Welcome to the detailed technical documentation for **Phase 1**. This phase covers the foundational infrastructure, Layer 2/3 security hardening, core high availability, dynamic routing, boundary security, and device management.

---

## 📐 Phase 1 Network Topology & Overview

![Phase 1 Network Topology](../../docs/topologies/topology.png)

> [!NOTE]
> **Phase 1 Scope:** This documentation details the implementation of the core network foundation, Layer 2/3 redundancy, edge firewall integration, and baseline device hardening.

---

## 🎯 Key Phase 1 Objectives
* **Core Resiliency:** Elimination of single points of failure via HSRP and Rapid PVST+.
* **Dynamic Routing:** Single-Area OSPF (Area 0) for core-to-firewall route propagation.
* **Network Segmentation & VLSM:** Efficient IP subnetting and VLAN separation across departments (Engineering, HR, Server Farm, Management) for strict traffic isolation and scalable Inter-VLAN routing.
* **Perimeter Defense:** Edge inspection, Dynamic NAT/PAT, and WAN access via FortiGate NGFW.
* **Infrastructure Protection:** Layer 2 hardening protocols (DHCP Snooping, DAI, Port Security, BPDU Guard).

---

## 🛠️ Technical Highlights & Implementation Scope

### 1. Layer 2 Switching, Trunking & Link Aggregation

![Multi-Switch Trunk and Access Config](./multi-switch-trunk-access-config.png)
![MLS LACP EtherChannel Status](./mls-lacp-etherchannel-status.png)
![VLAN Database and Ping Test](./vlan-database-and-ping-test.png)
![Rapid PVST Root Bridge Config](./rapid-pvst-root-bridge-config.png)
![Rapid PVST Root Bridge Verification](./rapid-pvst-root-bridge-verification.png)

---

### 2. Switching & Layer 2 Security Hardening

![SW1 Port Security and BPDU Guard Config](./sw1-port-security-and-bpduguard-config.png)
![STP Security RootGuard BPDUGuard PortFast Config](./stp-security-rootguard-bpduguard-portfast-config.png)
![SW1 DHCP Snooping and DAI Config](./sw1-dhcp-snooping-and-dai-config.png)
![SW2 DAI ARP Poisoning Prevention Logs](./sw2-dai-arp-poisoning-prevention-logs.png)
![SW2 DAI Invalid ARP Mitigation Logs](./sw2-dai-invalid-arp-mitigation-logs.png)
![SW1 Native VLAN Security Hardening](./sw1-native-vlan-security-hardening.png)

---

### 3. Addressing, Layer 3 Switching & Inter-VLAN Routing

![FortiGate CLI Interface IP Config](./fortigate-cli-interface-ip-config.png)
![FortiGate GUI Interfaces and Zones](./fortigate-gui-interfaces-and-zones.png)
![Ubuntu Server Static IP Installer Config](./ubuntu-server-static-ip-installer-config.png)
![Windows Server IP Config and Gateway Ping](./windows-server-ip-config-and-gateway-ping.png)
![Windows Server Full Connectivity Ping Test](./windows-server-full-connectivity-ping-test.png)
![VPC4 Continuous Internet Ping Reachability](./vpc4-continuous-internet-ping-reachability.png)
![Linux Ubuntu End-to-End Traceroute Internet](./Linux%20Ubuntu-end-to-end-traceroute-internet-path.png)

---

### 4. Core Dynamic Routing & High Availability (HA)

![MLS OSPF Point-to-Point Config](./mls-ospf-point-to-point-config.png)
![MLS1 OSPF Passive Interface Config](./mls1-ospf-passive-interface-config.png)
![MLS OSPF Routing Table and Neighbors](./mls-ospf-routing-table-and-neighbors.png)
![FortiGate OSPF GUI Config](./fortigate-ospf-gui-config.png)
![FortiGate GUI Routing Monitor Table](./fortigate-gui-routing-monitor-table.png)
![MLS HSRP Brief and IP Interface Summary](./mls-hsrp-brief-and-ip-interface-summary.png)
![HSRP VLAN99 Active Standby Status](./hsrp-vlan99-active-standby-status.png)
![HSRP Failover Resilience Ping Test](./hsrp-failover-resilience-ping-test.png)
![HSRP Management Ping Reachability](./hsrp-management-ping-reachability.png)

---

### 5. Boundary Security, NAT & Edge Protection

![FortiGate GUI Firewall Policy NAT](./fortigate-gui-firewall-policy-nat.png)
![vIOS Edge NAT PAT Config](./vios-edge-nat-pat-config.png)
![Edge Router NAT ACL Matches Verification](./edge-router-nat-acl-matches-verification.png)
![Edge Router CoPP ICMP Policing Config](./edge-router-copp-icmp-policing-config.png)

---

### 6. Device Hardening & Infrastructure Management

![EVE-NG VM Settings](./eve-ng-vm-settings.png)
![EVE-NG VMware Console](./eve-ng-vmware-console.png.png)
![EVE-NG SFTP WinSCP Connection](./eve-ng-sftp-winscp-connection.png)
![EVE-NG QEMU Images Directory Structure](./eve-ng-qemu-images-directory-structure.png)
![MLS SSH Hardening Config](./mls-ssh-hardening-config.png)
![FortiGate GUI Dashboard Status](./fortigate-gui-dashboard-status.png)
![Proxmox VE Web Management Dashboard](./proxmox-ve-web-management-dashboard.png)
![Proxmox Windows Server VM Summary](./proxmox-windows-server-vm-summary.png)
![Windows Server OOBE Administrator Setup](./windows-server-oobe-administrator-setup.png)
![Ubuntu Zabbix Server Post Install Console](./ubuntu-zabbix-server-post-install-console.jpg)

---




[⬅️ Back to Main Repository Overview](../../README.md)
