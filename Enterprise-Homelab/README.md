# Enterprise Homelab: Proxmox on VMware Workstation

## Overview
This project documents the build of a nested Proxmox VE homelab, designed to host a segmented enterprise network for blue team training, detection engineering, and incident response practice. The lab is virtualized on VMware Workstation 17 Pro, leveraging an AMD Ryzen 9 5900X CPU with SVM enabled.

## 🎯 Objectives
- Create a portable, isolated lab environment on a single physical host
- Implement network segmentation (CORP-LAN, LAB-DMZ, SEC-MGMT) using pfSense
- Master Proxmox VE management and nested virtualization
- Provide a foundation for security testing and tool development

## 🏗️ Lab Architecture
The lab implements a three-tier network segmentation model:

| Network Segment | CIDR Block | Purpose | Sample VMs |
| :--- | :--- | :--- | :--- |
| **CORP-LAN** | `192.168.10.0/24` | Corporate Domain Environment | Windows AD, Windows 10/11 |
| **LAB-DMZ** | `192.168.20.0/24` | Isolated Attack/Test Range | DVWA, bWapp, Metasploitable |
| **SEC-MGMT** | `192.168.30.0/24` | Security Tools Management | Wazuh, Kali, TheHive, Cortex |

## 🛠️ Technologies Used
- **Host Hypervisor:** VMware Workstation 17 Pro
- **Lab Hypervisor:** Proxmox VE 8.1
- **Firewall/Router:** pfSense CE 2.8.0
- **Networking:** Linux Bridges (vmbr0, vmbr1, vmbr2, vmbr3)
- **Monitoring:** Wazuh SIEM, Elastic Stack
- **Endpoint Security:** Wazuh agents, Sysmon

## ⚡ Build Challenges & Solutions

### Challenge: Nested Virtualization Support
**Problem:** After installation, Proxmox reported "No support for hardware accelerated KVM virtualization" despite SVM being enabled in BIOS.

**Root Cause:** VMware Workstation does not expose the host's CPU virtualization features to guest VMs by default.

**Solution:** Added the following parameters to the Proxmox VM's `.vmx` configuration file:
```ini
vhv.enable = "TRUE"
hypervisor.cpuid.v0 = "FALSE"

###Verification: After implementing the fix and rebooting, running kvm-ok within Proxmox confirms:###
INFO: /dev/kvm exists
KVM acceleration can be used
