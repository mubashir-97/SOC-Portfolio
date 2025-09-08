# Enterprise Homelab: Proxmox on VMware Workstation

## Overview
This project documents the build of a nested Proxmox VE homelab, designed to host a segmented enterprise network for blue team training, detection engineering, and incident response practice.

## 📚 Step-by-Step Build Guide

1.  **[BIOS Setup](01-bios-setup.md)** - Enabling hardware virtualization (SVM) and RAM profiling (DOCP)
2.  **[VMware Workstation Configuration](02-vmware-config.md)** - Creating the Proxmox VM with nested virtualization
3.  **[Proxmox VE Installation](03-proxmox-installation.md)** - Installing and configuring the hypervisor
4.  **[Troubleshooting KVM](04-troubleshooting-kvm.md)** - Solving nested virtualization issues

## 🏗️ Lab Architecture
The lab implements a three-tier network segmentation model:

| Network Segment | Purpose | CIDR Block |
| :--- | :--- | :--- |
| **CORP-LAN** | Corporate Domain Environment | `192.168.10.0/24` |
| **LAB-DMZ** | Isolated Attack/Test Range | `192.168.20.0/24` |
| **SEC-MGMT** | Security Tools Management | `192.168.30.0/24` |

## 🛠️ Technologies Used
- **Host Hypervisor:** VMware Workstation 17 Pro
- **Lab Hypervisor:** Proxmox VE 8.1
- **Firewall/Router:** pfSense CE 2.8.0
- **CPU:** AMD Ryzen 9 5900X (12 cores/24 threads)

## 📝 License
This project is licensed under the MIT License.
