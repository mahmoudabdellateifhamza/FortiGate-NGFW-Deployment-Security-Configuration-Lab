# 🖥️ FortiGate-VM Setup & Deployment Guide

> A general-purpose guide for downloading, deploying, initializing, and accessing FortiGate-VM in a virtualized lab environment.

This guide is intended for anyone who wants to deploy a FortiGate virtual appliance and use it as the foundation for a network security lab.

The procedures below are intentionally written as **general guidance**. Network addresses, interface names, hardware resources, FortiOS versions, virtualization platforms, and available features may differ between environments.

---

## 📌 Table of Contents

- [1. Before You Start](#1--before-you-start)
- [2. Obtain the FortiGate-VM Image](#2--obtain-the-fortigate-vm-image)
- [3. Choose the Correct VM Image](#3--choose-the-correct-vm-image)
- [4. VMware Deployment](#4--vmware-deployment)
- [5. Review Virtual Hardware](#5--review-virtual-hardware)
- [6. Configure Virtual Network Adapters](#6--configure-virtual-network-adapters)
- [7. Power On the FortiGate VM](#7--power-on-the-fortigate-vm)
- [8. Access the FortiGate CLI](#8--access-the-fortigate-cli)
- [9. Identify the Management Interface](#9--identify-the-management-interface)
- [10. Access the Web GUI](#10--access-the-web-gui)
- [11. Initial System Verification](#11--initial-system-verification)
- [12. Test Network Connectivity](#12--test-network-connectivity)
- [13. Verify Routing](#13--verify-routing)
- [14. Configure Additional Interfaces](#14--configure-additional-interfaces)
- [15. Secure Management Access](#15--secure-management-access)
- [16. Configure Administrative Accounts](#16--configure-administrative-accounts)
- [17. Continue Building the Lab](#17--continue-building-the-lab)
- [18. Troubleshooting](#18--troubleshooting)
- [19. Security & Privacy](#19--security--privacy)
- [20. Recommended Lab Workflow](#20--recommended-lab-workflow)
- [21. Useful Resources](#21--useful-resources)
- [22. Deployment Checklist](#22--deployment-checklist)

---

# 1. 📝 Before You Start

Before deploying FortiGate-VM, make sure you have:

- A supported virtualization platform
- Sufficient CPU and RAM resources
- Sufficient disk space
- A Fortinet account if required for the selected image
- Access to the appropriate FortiGate-VM image
- A basic understanding of IP addressing and networking
- A network topology or lab design in mind

For a VMware-based lab, this guide assumes that VMware Workstation Pro or another supported VMware environment is already installed.

---

# 2. 📥 Obtain the FortiGate-VM Image

FortiGate-VM deployment images are distributed through the official Fortinet Support Portal.

## Official Fortinet VM Images

👉 **[Fortinet Support – VM Images](https://support.fortinet.com/support/#/downloads/vm)**

After signing in:

1. Open the Fortinet Support Portal.
2. Navigate to the VM Images section.
3. Select **FortiGate** as the product.
4. Select the virtualization platform you are using.
5. Select the required FortiOS version.
6. Select the appropriate VM image/package.
7. Download the image to your computer.

> **Important:** Make sure you select **FortiGate**, not **FortiFirewall**. Fortinet's support documentation distinguishes between these products and their deployment images.

Fortinet documents the VM download process through the Support Portal under **Support → VM Images**. :contentReference[oaicite:1]{index=1}

---

# 3. 🧩 Choose the Correct VM Image

The required image depends on the environment where FortiGate will run.

Examples of virtualization environments include:

- VMware
- Hyper-V
- KVM
- Public cloud platforms
- Other supported environments

The VM image should match the target platform.

For example:

```text
VMware      → VMware-compatible FortiGate image
Hyper-V     → Hyper-V-compatible FortiGate image
KVM         → KVM-compatible FortiGate image
