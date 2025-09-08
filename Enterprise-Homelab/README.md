# Enterprise Blue Team Homelab

## Overview
A professionally segmented Proxmox VE homelab running as a nested VM in VMware Workstation. This environment serves as a cyber range for practicing threat detection, incident response, and security engineering in an enterprise-like setting.

## 🏗️ Lab Architecture
The lab implements a multi-VLAN architecture with strict segmentation for different security functions:

| VLAN ID | Network Segment | CIDR Block | DHCP Range | Purpose | Systems |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **VLAN 30** | **SEC-MGMT** | `10.10.30.0/24` | `.50` - `.100` | Security Tools Management | TheHive, Cortex, Caldera, Kali, Wazuh, Nessus, Security Onion |
| **VLAN 10** | **LAB-DMZ** | `10.10.10.0/24` | `.50` - `.100` | Isolated Attack Range | Metasploit, VulnHub, bWapp, DVWA, vulnerable VMs |
| **VLAN 20** | **CORP-LAN** | `10.10.20.0/24` | `.50` - `.500` | Corporate Domain | Windows 10, Active Directory, Ubuntu Server, Portainer, Docker |

## 🛠️ Technology Stack
- **Host Hypervisor:** VMware Workstation 17 Pro
- **Lab Hypervisor:** Proxmox VE 8.1
- **Firewall/Router:** pfSense CE 2.8.0
- **Containerization:** Docker + Portainer
- **Hardware:** AMD Ryzen 9 5900X (12 cores/24 threads), 32GB RAM

## 📚 Build Guide
1.  **[BIOS Setup](lab-doc/01-bios-setup.md)** - Enabling SVM and DOCP
2.  **[VMware Configuration](lab-doc/02-vmware-config.md)** - Nested virtualization setup
3.  **[Proxmox Installation](lab-doc/03-proxmox-installation.md)** - Hypervisor deployment
4.  **[KVM Troubleshooting](lab-doc/04-troubleshooting-kvm.md)** - Fixing nested virtualization
5.  **[pfSense Configuration](lab-doc/05-pfsense-configuration.md)** - Firewall and VLAN setup

## 🔧 Key Features
- **Network Segmentation:** Isolated VLANs for different security functions
- **Enterprise Monitoring:** Wazuh SIEM integration across all segments  
- **Attack Simulation:** Dedicated range for red team activities
- **Containerization:** Docker environment for security tools
- **Active Directory:** Realistic Windows domain environment

## 🚀 Getting Started
1. Clone this repository
2. Follow the build guide sequentially
3. Deploy VMs to appropriate network segments
4. Configure pfSense firewall rules for inter-VLAN traffic

## 📝 License
This project is licensed under the MIT License. See LICENSE file for details.

## 🤝 Contributing
This is a personal learning project, but suggestions and feedback are welcome through issues or discussions.

---

**Next Step:** [Begin with BIOS Setup](lab-doc/01-bios-setup.md)
