
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

**Description:**  
Configures the standardized system Time Zone on **Windows Server 2022** to `(UTC+03:00) Kuwait, Riyadh`. Clock synchronization and correct time zone settings are critical prerequisites in Active Directory Domain Services (AD DS) environments to prevent Kerberos authentication failures, ensure seamless Domain Controller replication, and maintain accurate event logging across the enterprise network.

**Configuration Steps:**
1. Open **Server Manager** and select **Local Server**.
2. Click on the current **Time zone** setting to open the **Date and Time** dialog.
3. Click **Change time zone...**.
4. Select `(UTC+03:00) Kuwait, Riyadh` from the drop-down menu and click **OK** to apply.

---

### Server Name Configuration
![Server Name Change](../../docs/phase-2/images/Server-Name-Change.png)

**Description:**  
Renames the primary server host from its default randomized NetBIOS name to a structured enterprise convention: `KSA-JED-DC01`. Assigning a clear, standard hostname prior to Active Directory Domain Services (AD DS) installation is a best practice that simplifies DNS record mapping, identity tracking, and server management across the enterprise architecture.

**Configuration Steps:**
1. Open **Server Manager** and navigate to **Local Server**.
2. Click on the current **Computer name** entry to open **System Properties**.
3. Under the **Computer Name** tab, click **Change...**.
4. Enter `KSA-JED-DC01` in the **Computer name** field and click **OK**.
5. Restart the server when prompted to apply the new hostname.

---

### Server Properties Overview
![Server Properties Overview](../../docs/phase-2/images/Server-Properties-Overview.png)

**Description:**  
Displays the **Local Server** dashboard overview in Server Manager following initial baseline configurations. This verification step confirms that core system properties—including the updated hostname (`KSA-JED-DC01`), time zone (`(UTC+03:00) Kuwait, Riyadh`), Remote Desktop accessibility, and local network adapter settings—are correctly applied before proceeding with role installations.

**Verification Steps:**
1. Open **Server Manager** and click on **Local Server** in the left navigation pane.
2. Review the **Properties** panel for `KSA-JED-DC01` to verify the hostname, active Workgroup status, network adapter binding, and time zone settings.

---

### Static IP Address Configuration
![Static IP Configuration](../../docs/phase-2/images/Static-IP-Configuration-WIN-Server.png)

**Description:**  
Configures a static IPv4 address, custom subnet mask, default gateway, and loopback/primary DNS reference on the server's network adapter (`Ethernet`). Assigning a static IP address is a fundamental prerequisite before promoting a server to a Domain Controller (DC) or DNS server, ensuring consistent network reachability, reliable resolution services, and predictable routing across the subnet.

**Configuration Parameters:**
- **IP Address:** `192.168.1.101`
- **Subnet Mask:** `255.255.255.248`
- **Default Gateway:** `192.168.1.97`
- **Preferred DNS Server:** `192.168.1.101` (Self-referencing for Active Directory integrated DNS)

**Configuration Steps:**
1. Open **Network Connections** (`ncpa.cpl`) or click on the **Ethernet** interface in **Server Manager**.
2. Right-click the active adapter and select **Properties**.
3. Select **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**.
4. Choose **Use the following IP address** and enter the static parameters (`IP: 192.168.1.101`, `Subnet: 255.255.255.248`, `Gateway: 192.168.1.97`).
5. Select **Use the following DNS server addresses** and set **Preferred DNS server** to `192.168.1.101`.
6. Click **OK** and close to apply the network bindings.



---

---

## 2. Active Directory Services & Infrastructure Deployment

### AD DS Roles & Features Wizard
![Roles Features Wizard Start](../../docs/phase-2/images/Roles-Features-Wizard-Start.png)


**Description:**  
Launches the **Add Roles and Features Wizard** on `KSA-JED-DC01` to initiate the deployment of Active Directory Domain Services (AD DS). The "Before You Begin" screen acts as a prerequisite checklist, ensuring that administrator password security, static IP address assignment, and essential system updates are verified before installing server roles.

**Configuration Steps:**
1. Open **Server Manager** and click on **Manage** in the top-right menu.
2. Select **Add Roles and Features**.
3. Review the prerequisite checklist on the **Before You Begin** page.
4. Click **Next** to proceed to the Installation Type selection.


---

### Select Server Roles
![Select Server Roles](../../docs/phase-2/images/Select-Server-Roles.png)

**Description:**  
Selects the core identity and network infrastructure roles to install on `KSA-JED-DC01`. Bundling **Active Directory Domain Services (AD DS)**, **DNS Server**, and **DHCP Server** establishes the primary domain controller and centralized network management services required for authentication, domain name resolution, and automated IP address management.

**Selected Server Roles:**
- **Active Directory Domain Services:** Centralized identity and access management database.
- **DNS Server:** Name resolution service integrated with Active Directory.
- **DHCP Server:** Automated IP address, subnet mask, and gateway assignment for network clients.

**Configuration Steps:**
1. In the **Add Roles and Features Wizard**, proceed to the **Server Roles** step.
2. Check **Active Directory Domain Services**, **DNS Server**, and **DHCP Server**.
3. Confirm the addition of required management tools when prompted for each role.
4. Click **Next** to proceed to the Features selection.

---

### Installation Progress
![Installation Progress](../../docs/phase-2/images/Installation-Progress.png)

**Description:**  
Tracks the active binary installation process for the selected roles and management features on `KSA-JED-DC01`. This includes deploying Active Directory Domain Services, DNS Server, DHCP Server, Group Policy Management, and the necessary Remote Server Administration Tools (RSAT).

**Key Components Installed:**
- **Active Directory Domain Services & Management Tools** (AD Administrative Center, AD DS Snap-ins, PowerShell Modules).
- **DNS Server Management Tools**.
- **DHCP Server Management Tools**.
- **Group Policy Management Console (GPMC)**.


---

### Post-Deployment Configuration
![Post Deployment Notifications](../../docs/phase-2/images/Post-Deployment-Notifications.png)

**Description:**  
Displays the Server Manager **Notification Center** after the role binaries are successfully installed. This step highlights the required post-deployment configuration tasks needed to finalize the setup, specifically promoting the server to a Domain Controller and completing the DHCP initial configuration.

**Required Post-Deployment Tasks:**
1. **Active Directory Domain Services:** Click **Promote this server to a domain controller** to start the Active Directory Configuration Wizard.
2. **DHCP Server:** Click **Complete DHCP configuration** to authorize the DHCP server in Active Directory and create necessary security groups.

---



### New Forest Deployment
![AD New Forest Setup](../../docs/phase-2/images/AD-New-Forest-Setup.png)

**Description:**  
Configures the initial deployment operation in the **Active Directory Domain Services Configuration Wizard**. Selecting **Add a new forest** creates the root domain for a brand-new Active Directory identity structure named `kob.local`.

**Configuration Parameters:**
- **Deployment Operation:** Add a new forest
- **Root Domain Name:** `kob.local`

**Configuration Steps:**
1. In the **Deployment Configuration** tab, select **Add a new forest**.
2. Type `kob.local` in the **Root domain name** field.
3. Click **Next** to proceed to the Domain Controller Options.

---

### Prerequisites Check
![AD Prerequisites Check](../../docs/phase-2/images/AD-Prerequisites-Check.png)

**Description:**  
Validates all system requirements and pre-requisite configurations on `KSA-JED-DC01` before executing the primary Domain Controller promotion. The wizard confirms that all prerequisite checks passed successfully, allowing the promotion process to proceed.

**Verification Highlights:**
- **Status:** All prerequisite checks passed successfully.
- **DNS Delegation Warning:** Expected notification indicating that a parent delegation cannot be found for isolated local top-level domains (`.local`), which is normal behavior in lab environments.
- **Action:** Click **Install** to begin the promotion and automated reboot process.

---

### Forest & Domain Selection
![AD Forest Domain Selection](../../docs/phase-2/images/AD-Forest-Domain-Selection.png)

**Description:**  
Queries and selects the target domain (`kob.local`) from the active forest during the deployment of a redundant secondary Domain Controller (`KSA-JED-DC02`).

**Configuration Steps:**
1. In the deployment wizard on `KSA-JED-DC02`, click **Select...** next to the Domain field.
2. Select `kob.local` from the list of discovered forest domains.
3. Supply enterprise administrator credentials (`kob.local\administrator`) to authenticate the domain addition.
4. Click **OK** to confirm the target domain binding.

---
### Secondary Domain Controller Deployment
![AD Additional DC Deployment](../../docs/phase-2/images/AD-Additional-DC-Deployment.png)


**Description:**  
Configures the deployment operation on `KSA-JED-DC02` to join an existing Active Directory domain as a secondary/replica Domain Controller. Adding redundant Domain Controllers establishes Active Directory High Availability (HA) and fault tolerance across the enterprise infrastructure.

**Configuration Parameters:**
- **Target Server:** `KSA-JED-DC02`
- **Deployment Operation:** Add a domain controller to an existing domain
- **Target Domain:** `kob.local`

**Configuration Steps:**
1. In the **Deployment Configuration** tab, select **Add a domain controller to an existing domain**.
2. Specify `kob.local` in the **Domain** field.
3. Click **Change...** to provide domain administrator credentials (`kob.local\administrator`).
4. Click **Next** to proceed to the Domain Controller Options and Active Directory replication setup.

---
### Secondary DC Hostname Change
![Secondary DC Name Change](../../docs/phase-2/images/Secondary-DC-Name-Change.png)

**Description:**  
Renames the secondary server host to `KSA-JED-DC02` following standard enterprise naming conventions prior to promoting it as an additional Domain Controller for the domain.

**Configuration Steps:**
1. Open **Server Manager** on the secondary server and navigate to **Local Server**.
2. Click on the current **Computer name** field to open **System Properties**.
3. Under the **Computer Name** tab, click **Change...**.
4. Enter `KSA-JED-DC02` in the **Computer name** field and click **OK**.
5. Restart the server to apply the updated hostname.


### Secondary DC Static IP Configuration
![Secondary DC IP Configuration](../../docs/phase-2/images/Secondary-DC-IP-Config-WIN-Server.png)

**Description:**  
Configures a static IPv4 address and DNS binding on `KSA-JED-DC02`. Setting the Primary DNS server to point to the primary DC (`192.168.1.101`) allows domain discovery during promotion, while pointing the Alternate DNS to itself (`192.168.1.104`) ensures localized DNS resiliency.

**Configuration Parameters:**
- **IP Address:** `192.168.1.104`
- **Subnet Mask:** `255.255.255.240`
- **Default Gateway:** `192.168.1.97`
- **Preferred DNS Server:** `192.168.1.101` (Primary DC: `KSA-JED-DC01`)
- **Alternate DNS Server:** `192.168.1.104` (Secondary DC self-reference)

**Configuration Steps:**
1. Open **Network Connections** (`ncpa.cpl`) on `KSA-JED-DC02`.
2. Right-click the network interface and select **Properties**.
3. Select **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**.
4. Configure static IP address parameters (`192.168.1.104`, `255.255.255.240`, `192.168.1.97`).
5. Set **Preferred DNS** to `192.168.1.101` and **Alternate DNS** to `192.168.1.104`.
6. Click **OK** to save network settings.


### AD Replication Options
![AD Replication Options](../../docs/phase-2/images/AD-Replication-Options.png)

**Description:**  
Specifies the initial Active Directory replication source during the promotion wizard for `KSA-JED-DC02`. Directing initial replication from `KSA-JED-DC01.kob.local` populates domain partition data, schema, and configuration settings directly from the primary domain controller.

**Configuration Parameters:**
- **Target Server:** `KSA-JED-DC02`
- **Replicate from:** `KSA-JED-DC01.kob.local`

**Configuration Steps:**
1. In the **Additional Options** step of the AD DS Configuration Wizard, locate **Specify additional replication options**.
2. Select `KSA-JED-DC01.kob.local` from the **Replicate from** drop-down menu.
3. Click **Next** to validate paths and proceed to the Prerequisites Check.


### AD Replication Health Summary
![AD Replication Health Summary](../../docs/phase-2/images/AD-Replication-Health-Summary.png)

**Description:**  
Verifies Active Directory replication status and health between `KSA-JED-DC01` and `KSA-JED-DC02` using administrative command-line utilities in PowerShell. Running `repadmin /replsummary` confirms zero replication errors and active bidirectional data sync across both Domain Controllers.

**Verification Command:**
```powershell
repadmin /replsummary
```
</details>

---
---

## 3. Core Network Services: DNS, NTP & DHCP Infrastructure

### 🌐 Domain Name System (DNS)
#### New Zone Wizard Setup
![DNS New Zone Wizard](../../docs/phase-2/images/DNS-New-Zone-Wizard.png)

**Description:**  
Launches the **New Zone Wizard** within DNS Manager (`dnsmgmt.msc`) on `KSA-JED-DC01` to create a primary Reverse Lookup Zone. Reverse Lookup Zones map IPv4 addresses to fully qualified domain names (FQDNs), enabling essential network diagnostics, security auditing, and reverse PTR record resolution.

**Configuration Steps:**
1. Open **DNS Manager** on `KSA-JED-DC01`.
2. Expand the server node, right-click **Reverse Lookup Zones**, and select **New Zone...**.
3. Click **Next** on the welcome screen to begin zone setup.

---


#### Reverse Lookup Zone Network ID
![DNS Reverse Zone Network ID](../../docs/phase-2/images/DNS-Reverse-Zone-NetworkID.png)

**Description:**  
Defines the Network ID for the IPv4 Reverse Lookup Zone (`192.168.1.x`). Specifying the network prefix automatically creates the corresponding `1.168.192.in-addr.arpa` authoritative reverse zone structure within Active Directory.

**Configuration Parameters:**
- **Network ID:** `192.168.1`
- **Generated Zone Name:** `1.168.192.in-addr.arpa`

**Configuration Steps:**
1. Select **IPv4 Reverse Lookup Zone** in the wizard.
2. Choose **Primary Zone** and enable Active Directory integration.
3. Select the Network ID option and enter `192.168.1`.
4. Click **Next** to finalize zone creation.

---

#### Host (A) Record Properties
![DNS Host A Record Properties](../../docs/phase-2/images/DNS-Host-A-Record-Properties.png)

**Description:**  
Configures the Host (A) record properties for `ksa-jed-dc01` in the Forward Lookup Zone (`kob.local`). Enabling the option to update the associated Pointer (PTR) record ensures automatic synchronization between the forward A record and the newly created Reverse Lookup Zone.

**Record Parameters:**
- **Host Name:** `ksa-jed-dc01`
- **FQDN:** `ksa-jed-dc01.kob.local`
- **IP Address:** `192.168.1.101`
- **Option Checked:** `Update associated pointer (PTR) record`

**Configuration Steps:**
1. Open **Forward Lookup Zones** -> `kob.local`.
2. Right-click the `ksa-jed-dc01` Host (A) record and select **Properties**.
3. Check **Update associated pointer (PTR) record**.
4. Click **Apply** and **OK**.

---
#### Forwarders Configuration
![DNS Forwarders Configuration](../../docs/phase-2/images/DNS-Forwarders-Config.png)

**Description:**  
Configures upstream external DNS forwarders on `KSA-JED-DC01`. Forwarding non-authoritative Internet queries to public DNS resolvers (Google Public DNS and Cloudflare) ensures reliable Internet name resolution for enterprise clients while maintaining internal Active Directory resolution locally.

**Configured Forwarders:**
- `8.8.8.8` (`dns.google`)
- `8.8.4.4` (`dns.google`)
- `1.1.1.1` (`one.one.one.one`)
- `1.0.0.1` (`one.one.one.one`)

**Configuration Steps:**
1. Right-click `KSA-JED-DC01` in DNS Manager and select **Properties**.
2. Navigate to the **Forwarders** tab and click **Edit...**.
3. Add the IPv4 addresses for Google (`8.8.8.8`, `8.8.4.4`) and Cloudflare (`1.1.1.1`, `1.0.0.1`).
4. Ensure **Use root hints if no forwarders are available** is checked, then click **Apply** and **OK**.

---
#### DNS Nslookup Verification
![DNS Nslookup Verification](../../docs/phase-2/images/DNS-Nslookup-Verification.png)

**Description:**  
Verifies local Active Directory domain resolution using the `nslookup` command-line utility on `KSA-JED-DC01`. Successfully resolving `kob.local` to `192.168.1.101` validates that the internal DNS server is responding correctly to local queries.

**Verification Command:**
```cmd
nslookup kob.local
```
</details>

---

#### Multi-DC DNS Verification
![DNS Multi DC Nslookup Verification](../../docs/phase-2/images/DNS-Multi-DC-Nslookup-Verification.png)

**Description:**  
Executes an `nslookup` command on the primary Domain Controller to verify round-robin and HA DNS resolution for the Active Directory domain `kob.local`. Returning both IP addresses (`192.168.1.101` and `192.168.1.104`) confirms that both Domain Controllers are registered as authoritative DNS providers for the forest.

**Verification Command:**
```cmd
nslookup kob.local
```
</details>


#### Domain ICMP / Ping Verification
![Domain Ping Verification](../../docs/phase-2/images/Domain-Ping-Verification.png)


**Description:**  
Verifies Network layer reachability and name resolution efficiency by executing an ICMP ping request to `kob.local`. Achieving `0%` packet loss with sub-millisecond response times validates low-latency communication with the local domain controller.

**Verification Command:**
```cmd
ping kob.local
```
</details>

### 🕒 Network Time Protocol (NTP)
#### NTP Server Resolution & Config
![NTP Server Config Resolve](../../docs/phase-2/images/NTP-Server-Config-Resolve.png)


**Description:**  
Configures the Primary Domain Controller (`KSA-JED-DC01`) as an authoritative time source using external NTP peers (`sa.pool.ntp.org`). Synchronizing the root PDC to a reliable external NTP stratum ensures consistent time tracking across all domain controllers, workstations, and network devices.

#### NTP Status Query
![NTP Status Query](../../docs/phase-2/images/NTP-Status-Query.png)

**Description**
Queries the operational status and active synchronization source of the Windows Time service (w3time) on KSA-JED-DC01 to ensure stratum alignment and clock accuracy.

### 📡 DHCP & IP Helper / Relay
#### DHCP Scope Naming
![DHCP Scope Name Config](../../docs/phase-2/images/DHCP-Scope-Name-Config.png)

**Description:**  
Initiates the **New Scope Wizard** in DHCP Manager (`dhcpmgmt.msc`) on `KSA-JED-DC01` to create an automated IPv4 allocation scope for the Engineering department segment (`VLAN10_Engineers`).

**Scope Parameters:**
- **Scope Name:** `VLAN10_Engineers`
- **Target Network Segment:** Engineering Department (VLAN 10)

**Configuration Steps:**
1. Open **DHCP Manager**, expand `ksa-jed-dc01.kob.local` -> **IPv4**.
2. Right-click **IPv4** and select **New Scope...**.
3. Type `VLAN10_Engineers` in the Scope Name field and click **Next**.




---
#### DHCP Address Pool Range
![DHCP IP Range Config](../../docs/phase-2/images/DHCP-IP-Range-Config.png)


**Description:**  
Defines the dynamic IPv4 address pool distribution range, CIDR prefix length, and subnet mask for the newly created DHCP scope. Establishing a defined address range ensures controlled dynamic allocation while preserving static IP boundaries for key network infrastructure.

**Configuration Parameters:**
- **Start IP Address:** `192.168.1.5`
- **End IP Address:** `192.168.1.30`
- **Subnet Mask Length:** `27` (`/27`)
- **Subnet Mask:** `255.255.255.224`

**Configuration Steps:**
1. In the **IP Address Range** page of the New Scope Wizard, enter `192.168.1.5` in **Start IP address**.
2. Enter `192.168.1.30` in **End IP address**.
3. Set the **Length** to `27` (which automatically updates the **Subnet mask** to `255.255.255.224`).
4. Click **Next** to proceed to Exclusion and Delay settings.
---

#### DHCP DNS Options Configuration
![DHCP DNS Server Config](../../docs/phase-2/images/DHCP-DNS-Server-Config.png)


**Description:**  
Configures parent domain suffix options and primary/secondary DNS server references pushed to DHCP clients upon lease acquisition. Binding Active Directory DNS servers directly to DHCP leases ensures seamless domain resource resolution and client-side Active Directory discovery.

**Configuration Parameters:**
- **Parent Domain:** `kob.local`
- **DNS Server 1:** `192.168.1.101` (`KSA-JED-DC01`)
- **DNS Server 2:** `192.168.1.104` (`KSA-JED-DC02`)

**Configuration Steps:**
1. In the **Domain Name and DNS Servers** step, specify `kob.local` in the **Parent domain** field.
2. Add the IPv4 address for the primary Domain Controller (`192.168.1.101`).
3. Add the IPv4 address for the secondary Domain Controller (`192.168.1.104`).
4. Use the **Up/Down** controls to prioritize `192.168.1.101` as primary, then click **Next**.

---

#### Cisco Switch IP Helper / Relay (VLAN 10)
![Cisco DHCP Relay Config VLAN 10](../../docs/phase-2/images/Cisco-DHCP-Relay-Config-vlan-10.png)


**Description:**
Configures DHCP Relay Agent (`ip helper-address`) capabilities on Layer 3 Switch Virtual Interfaces (SVIs) across both Core Switches (`MLS1` & `MLS2`). Forwarding broadcast DHCP requests directly as unicast traffic to the central Active Directory DHCP Server (`192.168.1.101`) ensures high availability and seamless IP address assignment across isolated client VLANs.

**Configuration Parameters:**
- **Target VLANs:** `VLAN 10`, `VLAN 20`
- **Primary DHCP Server IP:** `192.168.1.101` (`KSA-JED-DC01`)
- **Redundancy Nodes:** `MLS1`, `MLS2`

**Configuration Steps:**
1. Enter global configuration mode on the Core Switch using `configure terminal`.
2. Access the target Layer 3 SVI interface using `interface vlan <vlan-id>` (e.g., `interface vlan 20`).
3. Specify the destination DHCP Server IP address using `ip helper-address 192.168.1.101`.
4. Repeat the SVI interface selection for `vlan 10` and apply the same `ip helper-address 192.168.1.101` command.
5. Apply identical configurations on both `MLS1` and `MLS2` to maintain core network redundancy.


#### Cisco Switch IP Helper / Relay (VLAN 20)
![Cisco DHCP Relay Config VLAN 20](../../docs/phase-2/images/Cisco-DHCP-Relay-Config-VLAN-20.png)

**Description:**
Verifies end-to-end DHCP Relay functionality for clients located in the HR department subnet (`VLAN_HR`). Executing `ip dhcp` on the virtual PC (`VPC4`) initiates the DORA process, successfully obtaining dynamic IPv4 configuration parameters from the central Windows Server Domain Controller (`ksa-jed-dc01.kob.local`) via the Layer 3 Relay Agent.

**Configuration Parameters:**
- **Target Client:** `VPC4`
- **Assigned IP Address:** `192.168.1.36/28`
- **Default Gateway:** `192.168.1.33`
- **DHCP Scope:** `Scope [192.168.1.32] VLAN_HR`
- **DHCP Server:** `192.168.1.101` (`ksa-jed-dc01.kob.local`)

**Verification Steps:**
1. Open the CLI terminal on virtual client `VPC4`.
2. Issue the `ip dhcp` command to initiate the DHCP discovery broadcast.
3. Confirm the successful completion of the DORA process (`DORA IP 192.168.1.36/28 GW 192.168.1.33`).
4. Access the Windows Server Management Console on `ksa-jed-dc01.kob.local`.
5. Navigate to **DHCP Management Console** > **IPv4** > **Scope [192.168.1.32] VLAN_HR** > **Address Leases**.
6. Verify that `192.168.1.36` is actively registered under `VPCS.kob.local`.


#### VPCS Client DHCP Lease Verification
![VPCS DHCP Lease Verification](../../docs/phase-2/images/VPCS-DHCP-Lease-Verification.png)

**Description:**
Validates successful IP address lease distribution for the Engineering department subnet (`VLAN10_Engineers`). The client device (`VPC12`) completes the DHCP handshake process across the Core Switch Relay Agent, populated directly in the Active Directory DHCP database under active leases.

**Configuration Parameters:**
- **Target Client:** `VPC12`
- **Assigned IP Address:** `192.168.1.5/27`
- **Default Gateway:** `192.168.1.1`
- **DHCP Scope:** `Scope [192.168.1.0] VLAN10_Engineers`
- **DHCP Server:** `192.168.1.101` (`ksa-jed-dc01.kob.local`)

**Verification Steps:**
1. Launch the command line interface on virtual terminal `VPC12`.
2. Run the `ip dhcp` command to acquire an IP address automatically.
3. Observe the full DORA exchange confirmation output (`DORA IP 192.168.1.5/27 GW 192.168.1.1`).
4. Open the Windows Server **DHCP Manager** on `ksa-jed-dc01.kob.local`.
5. Expand **IPv4** > **Scope [192.168.1.0] VLAN10_Engineers** > **Address Leases**.
6. Confirm the binding entry for client IP `192.168.1.5` mapped to Hostname `VPCS.kob.local`.

---
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
