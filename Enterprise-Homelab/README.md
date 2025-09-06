# Enterprise Homelab: Proxmox on VMware Workstation

## Overview
This project documents the build of my nested Proxmox VE homelab, designed to host a segmented enterprise network for blue team training, detection engineering, and incident response practice. The lab is virtualized on VMware Workstation 17 Pro, leveraging an AMD Ryzen CPU with SVM enabled.

## Objectives
- Create a portable, isolated lab environment on a single physical host
- Implement network segmentation (LAN, DMZ, MGMT) using pfSense
- Master Proxmox VE management and nested virtualization
- Provide a foundation for all other security projects in this portfolio

## Technologies Used
- **Host Hypervisor:** VMware Workstation 17 Pro
- **Lab Hypervisor:** Proxmox VE 8.1
- **Networking:** pfSense firewall, VLAN segmentation
- **Monitoring:** Wazuh SIEM, Elastic Stack
- **Endpoint Security:** Wazuh agents, Sysmon

## Lab Architecture
The lab uses a three-tier network architecture:
- **CORP-LAN:** Internal network for domain-joined systems
- **LAB-DMZ:** Isolated network for testing and attack simulations
- **MGMT:** Management network for security tools and infrastructure

## Build Challenges & Solutions

### Challenge: Nested Virtualization Support
**Problem:** Proxmox reported "No support for hardware accelerated KVM virtualization" despite SVM being enabled in BIOS.

**Solution:** Added the following parameters to the VMware .vmx configuration file:
```ini
# vhv.enable = "TRUE"
# hypervisor.cpuid.v0 = "FALSE"
Verification: After implementing the fix, running kvm-ok within Proxmox confirms:
    INFO: /dev/kvm exists
    KVM acceleration can be used
After Reboot the error was fixed.
