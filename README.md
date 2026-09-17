# 🛡️ FortiGate NGFW Deployment & Security Configuration Lab

> A hands-on Network Security lab focused on deploying, configuring, hardening, and validating a FortiGate Next-Generation Firewall in a virtualized VMware environment.

![FortiGate](https://img.shields.io/badge/FortiGate-VM64-red?style=for-the-badge&logo=fortinet)
![FortiOS](https://img.shields.io/badge/FortiOS-7.6.7-orange?style=for-the-badge)
![VMware](https://img.shields.io/badge/VMware-Workstation%20Pro-607078?style=for-the-badge&logo=vmware)
![Network Security](https://img.shields.io/badge/Focus-Network%20Security-blue?style=for-the-badge)

---

## 📌 Overview

This project documents the deployment and initial security configuration of a **FortiGate-VM64 Next-Generation Firewall** running **FortiOS v7.6.7 (Build 3704)** within a virtualized **VMware Workstation Pro** lab environment.

The lab combines **FortiOS CLI and Web GUI administration** and covers system initialization, network verification, secure management access, management hardening, Role-Based Access Control (RBAC), address objects, time-based access control, service groups, NAT, and firewall policy enforcement.

---

## 🏗️ Lab Environment

| Component | Configuration |
|---|---|
| **Firewall** | FortiGate-VM64 |
| **FortiOS** | v7.6.7 (Build 3704) |
| **Architecture** | x86_64 Virtual Appliance |
| **Hypervisor** | VMware Workstation Pro |
| **Environment** | Virtualized Lab |
| **Management Interface** | port1 |
| **WAN Interface** | port2 |
| **LAN Interface** | port3 |

---

## 🎯 Project Objectives

- Deploy FortiGate-VM64 in a VMware environment.
- Perform initial FortiOS system initialization.
- Verify network interfaces and IP addressing.
- Inspect routing and forwarding information.
- Validate DNS resolution and Internet connectivity.
- Configure secure HTTPS administrative access.
- Apply management access hardening.
- Configure a Dedicated Management Port profile.
- Implement Role-Based Access Control (RBAC).
- Configure restricted administrative privileges.
- Configure FQDN and Geography address objects.
- Create time-based access schedules.
- Organize network services using Service Groups.
- Configure outbound firewall policies.
- Enable Network Address Translation (NAT).
- Verify administrative privilege isolation.
- Verify time-based firewall policy enforcement.

---

# 🔧 Technologies & Concepts

## Firewall & Virtualization

- FortiGate-VM64
- FortiOS 7.6.7
- VMware Workstation Pro
- x86_64 Virtual Appliance

## Networking

- IPv4
- DHCP
- DNS
- ARP
- Routing
- RIB
- FIB
- NAT
- Network Interfaces
- Default Gateway
- Layer 2 / Layer 3 Address Resolution

## Security

- Next-Generation Firewall Administration
- Secure Management Access
- Management Hardening
- Role-Based Access Control (RBAC)
- Principle of Least Privilege
- FQDN-based Security Objects
- Geography-based Security Objects
- Time-Based Access Control
- Service-Based Traffic Filtering
- Firewall Policy Enforcement

---

# 🖥️ 1. Deployment & System Initialization

The FortiGate virtual appliance was deployed using the **FortiGate-VM64 OVF template** within VMware Workstation Pro.

The initial deployment included:

- Virtual machine import
- Virtual disk initialization
- FortiOS system startup
- Storage partitioning
- Initial administrator authentication
- Mandatory administrator password update

### Virtual Appliance Specifications

| Specification | Details |
|---|---|
| **Appliance** | FortiGate-VM64 |
| **Firmware** | FortiOS v7.6.7 |
| **Build** | 3704 |
| **Architecture** | x86_64 |
| **Hypervisor** | VMware Workstation Pro |

---

# 💻 2. CLI System Verification

FortiOS CLI commands were used throughout the lab to inspect the system and validate its operational state.

---

## 2.1 Interface Status Verification

```bash
show system interface
```

The `show system interface` command was used to retrieve the configured network interface settings and inspect the initial interface configuration.

### Key Configuration Observed

- **port1 (WAN/Management):** Configured via DHCP
- **Assigned IP Address:** `192.168.233.135/24`

The command was used to inspect the static configuration defined for the FortiGate interfaces.

---

## 2.2 System Performance Monitoring

```bash
get system performance status
```

The command was used to inspect real-time resource utilization and verify system stability prior to traffic routing.

### Observed Lab Status

| Metric | Result |
|---|---:|
| **CPU Idle State** | 100% idle |
| **CPU User / System Utilization** | 0% |
| **RAM Consumption** | 47.5% |
| **Total Memory Allocation** | 2.0 GB |
| **Active Sessions** | 3 |

---

## 2.3 FortiOS CLI Command Types

FortiOS provides different command families for configuration inspection, operational information, and diagnostics.

| Command | Data Type | Purpose |
|---|---|---|
| `show` | Configuration | Displays static configurations defined by the administrator. |
| `get` | Operational / Runtime | Displays active operational status and dynamically assigned values. |
| `diagnose` | Troubleshooting | Provides deeper system diagnostics and troubleshooting information. |

---

## 2.4 Low-Level IP Address Diagnostics

```bash
diagnose ip address list
```

The command was executed to perform a low-level system query and list active IPv4 addresses bound to physical and logical interfaces.

### Key Technical Observations

- **port1**
  - Primary Management Interface
  - IP Address: `192.168.233.135/24`
  - Interface Index: `3`

- **fortilink**
  - FortiSwitch Integration Interface
  - IP Address: `10.255.1.1/24`
  - Interface Index: `17`

- **Logical & Internal Interfaces**
  - `root`
  - `vsys_ha`
  - `vsys_fgfm`
  - Local loopback address: `127.0.0.1`

These internal interfaces support internal inter-process communication, High Availability (HA) signaling, and FortiManager-related operations.

---

## 2.5 Routing Table Verification

```bash
get router info routing-table all
```

The command was executed to inspect the active **Routing Information Base (RIB)** within the default virtual routing domain.

### Key Routing Entries

#### Default Route

```text
S* 0.0.0.0/0
```

The default route was automatically provisioned through DHCP on `port1`.

| Parameter | Value |
|---|---|
| **Next-Hop Gateway** | `192.168.233.2` |
| **Administrative Distance** | `5` |
| **Metric** | `0` |
| **Interface** | `port1` |

#### Connected Subnet

```text
C 192.168.233.0/24
```

This represents the directly connected local broadcast domain assigned to `port1`.

---

## 2.6 Kernel Forwarding Information Base Inspection

```bash
get router info kernel
```

The command was used to inspect the active **Forwarding Information Base (FIB)** programmed into the FortiOS Linux kernel.

The RIB contains routing information maintained by the routing system, while the FIB represents the active forwarding information used for packet forwarding decisions.

### Key Kernel Table Information

#### Active Forwarding Route

```text
tab=65534
0.0.0.0/0
```

The active default gateway entry directed traffic through:

- **Interface:** `dev=3 (port1)`
- **Next-Hop:** `192.168.233.2`

#### Local Interface Binding

```text
tab=65535
```

The local table contained host-route entries such as:

```text
192.168.233.135/32
10.255.1.1/32
```

These entries support administrative control-plane handling and local packet termination.

---

## 2.7 Internet Connectivity & DNS Resolution Testing

```bash
execute ping google.com
```

The command was executed from the FortiGate CLI to verify outbound WAN reachability and DNS resolution capability directly from the FortiGate control plane.

### Diagnostic Results

| Test | Result |
|---|---|
| **DNS Resolution** | Successful |
| **Resolved IPv4 Address** | `142.251.39.174` |
| **ICMP Echo Requests** | 5 |
| **Packet Loss** | 0% |
| **Average RTT** | 45.9 ms |
| **Exit Interface** | port1 |
| **Default Gateway** | `192.168.233.2` |

### Verification Outcome

The test confirmed active Internet connectivity through `port1` using the assigned default gateway.

---

## 2.8 DNS Proxy Cache & FQDN Inspection

```bash
diagnose test application dnsproxy 6
```

The command was executed to evaluate the active **FQDN (Fully Qualified Domain Name) cache** maintained by the FortiOS DNS Proxy daemon.

### Key Diagnostic Results

The diagnostic output demonstrated active IP resolution for domain objects including:

```text
login.windows.net
*.dropbox.com
```

The DNS proxy displayed cached IPv4 address lists together with associated **Time-To-Live (TTL)** values.

The diagnostic output also confirmed:

```text
FQDN num=6
```

indicating six active FQDN entries maintained in the proxy memory structure.

---

## 2.9 ARP Cache Verification

```bash
get system arp
```

The command was executed to inspect the active **Layer 2 to Layer 3 address mappings** maintained within the FortiOS ARP cache.

### Key ARP Entries

#### VMware Virtual Host Interface

```text
IP Address: 192.168.233.1
MAC Address: 00:50:56:c0:00:08
Interface: port1
Entry Age: 31 minutes
```

#### VMware Virtual Gateway

```text
IP Address: 192.168.233.2
MAC Address: 00:50:56:fc:df:df
Interface: port1
Entry Age: 0 minutes
```

The gateway entry reflected recent control-plane ping interactions.

### Verification Outcome

The ARP table confirmed successful Layer 2 neighbor discovery and operational ARP table construction across the `port1` broadcast domain.

---

# 🌐 3. Web GUI Provisioning & Host Connectivity

The FortiGate Web GUI was used alongside the CLI to configure and verify administrative and network settings.

The management connection was established through **port1** using the assigned management IP address.

The GUI configuration included:

- Network interface configuration
- Administrative access settings
- Management access hardening
- Dedicated Management Port configuration
- Trusted management subnet configuration
- Security Rating verification

---

# 🔐 4. Management Access Hardening

Management access was reviewed and hardened to reduce unnecessary administrative exposure.

The configuration included:

- HTTPS for secure Web GUI administration
- SSH where required for CLI administration
- PING for connectivity testing
- HTTP disabled
- Unnecessary SNMP access disabled
- Trusted management subnet configured
- Dedicated Management Port profile enabled

The FortiGate Security Rating was reviewed after applying the management hardening changes.

---

# 👥 5. Administrative Accounts & RBAC

Role-Based Access Control was implemented using separate administrative profiles.

### Administrator Accounts

| Account | Administrative Profile |
|---|---|
| `Mahmoud_Hamza` | `super_admin` |
| `Mennahtullah` | `Customized profile` |

The restricted administrator account was tested to verify that administrative capabilities were limited according to the assigned profile.

This demonstrates the **Principle of Least Privilege** by separating full administrative access from restricted administrative capabilities.

---

# 🌍 6. Firewall Address Objects

Address objects were configured to provide logical references for security policies.

## 6.1 FQDN Address Object

```text
Name: youtube_address
Type: FQDN
FQDN: youtube.com
Interface: any
```

The FQDN object provides a logical representation of the specified domain.

---

## 6.2 Geography Address Object

```text
Name: Upcoming headlines from Australia
Type: Geography
Country: Australia
Interface: any
```

The Geography object demonstrates the use of geographic-based address matching within FortiGate security configuration.

---

# ⏰ 7. Time-Based Access Control

A firewall schedule named:

```text
Students
```

was configured with the following time window:

| Days | Time |
|---|---|
| Sunday – Thursday | 08:00 – 14:00 |

The schedule was associated with the student Internet access firewall policy.

This allows traffic enforcement to be controlled automatically according to the configured schedule.

---

# 🧩 8. Service Groups

Service groups were created to organize commonly used protocols and simplify firewall policy configuration.

## 8.1 Email Access

```text
DNS
IMAP
IMAPS
POP3
```

---

## 8.2 Web Access

```text
DNS
HTTP
HTTPS
```

---

## 8.3 Exchange Server & Windows AD

```text
DCE-RPC
DNS
HTTPS
```

Using service groups simplifies firewall policy management by allowing multiple related services to be referenced through a single logical object.

---

# 🛡️ 9. Firewall Policy Construction

The primary outbound firewall policy was configured as:

```text
Students-Internet-Access
```

### Policy Configuration

| Setting | Configuration |
|---|---|
| **Incoming Interface** | `lan (port3)` |
| **Outgoing Interface** | `Wan (port2)` |
| **Source** | `port3 address` |
| **Destination** | `all` |
| **Schedule** | `Students` |
| **Services** | Email Access + Web Access |
| **Action** | ACCEPT |
| **NAT** | Enabled |
| **SSL Inspection** | No Inspection |

The policy permits the defined student traffic from the LAN interface toward the WAN interface during the configured schedule.

---

# 🔄 10. Policy Enforcement & Implicit Deny

The configured time-based schedule is directly associated with the student Internet access firewall policy.

### Active Schedule

```text
Sunday – Thursday
08:00 – 14:00
```

During the configured schedule, traffic matching the policy is permitted according to the selected services.

Outside the configured schedule, the policy no longer matches the traffic. Traffic is therefore subject to FortiGate's subsequent policy evaluation and, where no matching policy permits the traffic, the **implicit deny** behavior.

This demonstrates how **firewall policies, schedules, service groups, and NAT** can be combined to implement time-based network access control.

---

# 📸 11. Screenshots & Evidence

The project documentation includes screenshots covering the major deployment, configuration, and verification stages.

### Deployment

- FortiGate VM deployment
- VMware virtual appliance configuration
- Initial system startup
- Storage initialization
- Administrator authentication

### CLI

- Interface configuration
- System performance status
- IP address diagnostics
- Routing table
- Kernel forwarding information
- DNS and Internet connectivity
- DNS proxy diagnostics
- ARP table

### Security

- Management access configuration
- HTTPS administration
- Management hardening
- Trusted management subnet
- Dedicated Management Port

### RBAC

- Administrator accounts
- Custom administrator profile
- Restricted administrator access

### Firewall Configuration

- FQDN address object
- Geography address object
- Time-based schedule
- Service groups
- Students Internet Access policy
- NAT configuration

---

# 📄 12. Technical Documentation

A complete technical report is included in this repository documenting the deployment, configuration, verification, and security controls implemented throughout the lab.

### Technical Report

**FortiGate NGFW Deployment & System Configuration Report**

The report contains detailed implementation steps, configuration screenshots, CLI analysis, GUI configuration, and verification evidence.

The report is available under:

```text
Documentation/
└── FortiGate_NGFW_Deployment_Report.pdf
```

---

# 📁 13. Repository Structure

```text
FortiGate-NGFW-Lab/
│
├── README.md
│
├── Documentation/
│   ├── FortiGate_NGFW_Deployment_and_Configuration_Report.pdf
│   └── VM-Setup-Guide.md
│
├── Configuration/
│   └── CLI-Commands.md
│
└── Screenshots/
    │
    ├── Deployment/
    │
    ├── Networking/
    │
    ├── Security/
    │
    ├── RBAC/
    │
    └── Firewall-Policies/
```

---

# ✅ 14. Verification Summary

The lab successfully demonstrated:

- [x] FortiGate-VM64 deployment
- [x] FortiOS system initialization
- [x] Virtual appliance configuration
- [x] Interface configuration and verification
- [x] IP addressing inspection
- [x] System resource monitoring
- [x] Routing table inspection
- [x] Kernel forwarding verification
- [x] DNS resolution
- [x] Internet connectivity testing
- [x] DNS proxy inspection
- [x] ARP table inspection
- [x] Secure management access
- [x] Management hardening
- [x] Trusted management subnet
- [x] Dedicated Management Port profile
- [x] Role-Based Access Control
- [x] Restricted administrator profile
- [x] FQDN address object
- [x] Geography address object
- [x] Time-based schedule
- [x] Service groups
- [x] Outbound firewall policy
- [x] NAT
- [x] Time-based policy enforcement

---

# 🧠 15. Key Learning Outcomes

Through this lab, I gained practical experience with:

- FortiGate virtual appliance deployment
- FortiOS CLI administration
- FortiGate Web GUI administration
- Network interface configuration
- IPv4 addressing
- DHCP-based network configuration
- Routing and forwarding verification
- RIB and FIB inspection
- DNS troubleshooting
- Internet connectivity validation
- ARP inspection
- Secure firewall management
- Management access hardening
- Administrative privilege separation
- Role-Based Access Control
- FQDN-based security objects
- Geography-based security objects
- Time-based traffic control
- Service-based firewall policies
- NAT configuration
- Firewall policy evaluation
- Traffic enforcement

The project strengthened my understanding of how **networking, security controls, administration, and traffic enforcement** interact within a modern Next-Generation Firewall environment.

---

# 🚀 16. Project Workflow

```text
Deploy
  ↓
Initialize
  ↓
Configure
  ↓
Verify
  ↓
Harden
  ↓
Implement RBAC
  ↓
Create Security Objects
  ↓
Configure Schedules & Services
  ↓
Build Firewall Policy
  ↓
Enable NAT
  ↓
Validate Enforcement
```

---

# 👨‍💻 Author

**Mahmoud Abdellateif Hamza**

**Network & Security Systems Engineer**

Information Technology Student  
**Assiut International Technological University (AITU)**

### Areas of Focus

`Networking` · `Cybersecurity` · `Network Security` · `Data Analytics`

---

# ⚠️ Disclaimer

This project was developed as a **personal virtualized FortiGate lab environment** for educational and practical training purposes.

The configurations presented in this repository are intended for lab experimentation and learning and should be adapted, tested, and reviewed before being used in production environments.

---

# 🔥 Project Highlights

> **Deploy → Configure → Harden → Verify → Enforce**

A hands-on FortiGate NGFW workflow covering:

**Virtual Deployment**  
↓  
**System Initialization**  
↓  
**CLI & GUI Administration**  
↓  
**Network Verification**  
↓  
**Management Hardening**  
↓  
**RBAC**  
↓  
**Security Objects**  
↓  
**Time-Based Access Control**  
↓  
**Service Groups**  
↓  
**Firewall Policy & NAT**  
↓  
**Traffic Enforcement**

---

## 🛠️ Technologies

`FortiGate-VM64` · `FortiOS 7.6.7` · `VMware Workstation Pro` · `IPv4` · `DHCP` · `DNS` · `ARP` · `RIB` · `FIB` · `Routing` · `NAT` · `RBAC` · `FQDN` · `GeoIP` · `Firewall Policies` · `Network Security`
