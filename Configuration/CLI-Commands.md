# FortiGate CLI Commands

This document contains the main FortiOS CLI commands used during the FortiGate NGFW deployment and security configuration lab.

The commands were used for system inspection, interface verification, performance monitoring, IP diagnostics, routing analysis, forwarding information inspection, DNS resolution, FQDN diagnostics, and ARP verification.

---

## 1. Interface Status Verification

```bash
show system interface
```

The `show system interface` command was used to retrieve the configured network interface settings and inspect the initial interface configuration.

### Key Configuration Observed

- **port1 (WAN/Management):** Configured via DHCP
- **Assigned IP Address:** `192.168.233.135/24`

The command provides the static configuration defined for the FortiGate interfaces. :contentReference[oaicite:1]{index=1}

---

## 2. System Performance Monitoring

```bash
get system performance status
```

The `get system performance status` command was used to inspect real-time system resource utilization and verify FortiGate system stability prior to traffic routing.

### Observed Lab Status

| Metric | Result |
|---|---:|
| **CPU Idle State** | 100% idle |
| **CPU User / System Utilization** | 0% |
| **RAM Consumption** | 47.5% |
| **Total Memory Allocation** | 2.0 GB |
| **Active Sessions** | 3 |

The results confirmed the operational resource state of the FortiGate virtual appliance during the lab. :contentReference[oaicite:2]{index=2}

---

## 3. FortiOS CLI Command Types

FortiOS provides different command families for configuration, operational information, and diagnostics.

| Command | Data Type | Purpose & Scope |
|---|---|---|
| `show` | Configuration File | Displays static configurations defined by the administrator in the configuration file. |
| `get` | Operational / Runtime | Displays active operational status, including dynamically assigned values such as DHCP IP addresses. |
| `diagnose` | Troubleshooting | Used for real-time packet tracing, kernel debugging, and deep system diagnostics. |

This distinction was used throughout the lab to differentiate between configuration inspection, runtime information, and troubleshooting-level diagnostics. :contentReference[oaicite:3]{index=3}

---

## 4. Low-Level IP Address Diagnostics

```bash
diagnose ip address list
```

The `diagnose ip address list` command was executed to perform a low-level kernel query and list active IPv4 addresses bound to physical and logical interfaces.

### Key Technical Observations

- **port1 (Primary Management Interface):**
  - IP Address: `192.168.233.135/24`
  - Interface Index: `3`

- **fortilink (FortiSwitch Integration Interface):**
  - IP Address: `10.255.1.1/24`
  - Interface Index: `17`

- **Logical & Internal Interfaces:**
  - `root`
  - `vsys_ha`
  - `vsys_fgfm`
  - Assigned local loopback addresses: `127.0.0.1`

These internal interfaces support functions including internal inter-process communication, High Availability (HA) signaling, and FortiManager (FGFM) daemon operations. :contentReference[oaicite:4]{index=4}

---

## 5. Routing Table Verification

```bash
get router info routing-table all
```

The `get router info routing-table all` command was executed to inspect the active **Routing Information Base (RIB)** within the default virtual routing domain.

### Key Routing Entries

#### Default Route

```text
S* 0.0.0.0/0
```

The default route was automatically provisioned through DHCP on `port1`.

- **Next-Hop Gateway:** `192.168.233.2`
- **Administrative Distance:** `5`
- **Metric:** `0`

External traffic was therefore forwarded toward the VMware virtual gateway through `port1`.

#### Connected Subnet

```text
C 192.168.233.0/24
```

This represents the directly connected local broadcast domain assigned to `port1`. :contentReference[oaicite:5]{index=5}

---

## 6. Kernel Forwarding Information Base Inspection

```bash
get router info kernel
```

The `get router info kernel` command was used to inspect the active **Forwarding Information Base (FIB)** programmed directly into the FortiOS Linux kernel.

The RIB represents the routing information maintained by the routing system, while the FIB represents the active data-plane forwarding information used for packet forwarding decisions.

### Key Kernel Table Breakdown

#### Active Forwarding Route

```text
tab=65534
0.0.0.0/0
```

The active default gateway entry directs traffic through:

- **Interface:** `dev=3 (port1)`
- **Next-Hop:** `192.168.233.2`

#### Local Interface Binding

```text
tab=65535
```

The local table contains host-route entries such as:

```text
192.168.233.135/32
10.255.1.1/32
```

These host routes support administrative control-plane handling and local packet termination. :contentReference[oaicite:6]{index=6}

---

## 7. Internet Connectivity & DNS Resolution Testing

```bash
execute ping google.com
```

The `execute ping google.com` command was executed from the FortiGate CLI to verify outbound WAN reachability and Domain Name System (DNS) resolution capability directly from the FortiGate control plane.

### Diagnostic Results

| Test | Result |
|---|---|
| **DNS Resolution** | Successful |
| **Resolved IPv4 Address** | `142.251.39.174` |
| **ICMP Echo Requests** | 5 |
| **Packet Loss** | 0% |
| **Average RTT** | 45.9 ms |
| **Outbound Connectivity** | Successful |
| **Exit Interface** | port1 |
| **Default Gateway** | `192.168.233.2` |

### Verification Outcome

The test confirmed active Internet connectivity through `port1` using the assigned default gateway.

DNS resolution successfully translated `google.com` into a public IPv4 address, while all five ICMP requests were successfully returned with 0% packet loss. :contentReference[oaicite:7]{index=7}

---

## 8. DNS Proxy Cache & FQDN Inspection

```bash
diagnose test application dnsproxy 6
```

The `diagnose test application dnsproxy 6` command was executed to evaluate the active **FQDN (Fully Qualified Domain Name) cache** maintained by the FortiOS DNS Proxy daemon.

### Key Diagnostic Results

#### FQDN Object Resolution

The diagnostic output demonstrated active IP resolution for system domain objects including:

```text
login.windows.net
*.dropbox.com
```

#### DNS Caching Mechanism

The DNS proxy displayed cached IPv4 address lists together with their associated **Time-To-Live (TTL)** values.

This caching mechanism supports optimized packet processing and rapid policy enforcement for FQDN-based firewall rules.

#### Cached Objects

The diagnostic output confirmed:

```text
FQDN num=6
```

indicating six active domain entries maintained in the DNS proxy memory structure. :contentReference[oaicite:8]{index=8}

---

## 9. Address Resolution Protocol (ARP) Cache Verification

```bash
get system arp
```

The `get system arp` command was executed to inspect the active **Layer 2 to Layer 3 address mappings** maintained within the FortiOS ARP cache.

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

The ARP table confirmed successful Layer 2 neighbor discovery and operational ARP table construction across the `port1` broadcast domain. :contentReference[oaicite:9]{index=9}

---

# CLI Verification Summary

The CLI commands documented in this file were used to verify multiple layers of FortiGate operation:

- Interface configuration
- IP addressing
- System resource utilization
- Routing Information Base (RIB)
- Forwarding Information Base (FIB)
- Default gateway operation
- DNS resolution
- Internet connectivity
- DNS proxy operation
- FQDN cache resolution
- ARP neighbor discovery
- Layer 2 to Layer 3 address mappings

Together, these commands provided low-level verification of the FortiGate virtual firewall during the deployment and configuration lab.

---

# Command Reference

| Category | Command |
|---|---|
| **Interface Configuration** | `show system interface` |
| **System Performance** | `get system performance status` |
| **IP Diagnostics** | `diagnose ip address list` |
| **Routing Table** | `get router info routing-table all` |
| **Kernel Routing / FIB** | `get router info kernel` |
| **Connectivity & DNS** | `execute ping google.com` |
| **DNS Proxy Diagnostics** | `diagnose test application dnsproxy 6` |
| **ARP Table** | `get system arp` |

---

## Related Documentation

For the complete deployment process, Web GUI configuration, management hardening, RBAC implementation, address objects, schedules, service groups, firewall policies, NAT configuration, and screenshots, see the full technical report:

```text
 Documentation/
   ├── FortiGate_NGFW_Deployment_Report.pdf
   └── VM-Setup-Guide.md
```

---

## Lab Platform

- **Firewall:** FortiGate-VM64
- **FortiOS:** v7.6.7 (Build 3704)
- **Architecture:** x86_64
- **Hypervisor:** VMware Workstation Pro
- **Environment:** Virtualized Network Security Lab
