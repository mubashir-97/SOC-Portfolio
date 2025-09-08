# 03 - Proxmox VE Installation & Network Architecture

## Objective
To deploy a production-grade Proxmox VE 8.1 hypervisor as a nested VM within VMware Workstation, configured with segmented networking for enterprise security lab operations.

## Hardware Allocation (VMware VM Settings)
- **vCPUs:** 8 Cores (of AMD Ryzen 9 5900X)
- **RAM:** 20 GB
- **Storage:** 250 GB (Thin Provisioned)
- **Network Adapter:** VMXNET3 (Bridged Mode)

## Phase 1: Proxmox VE Installation
1. Downloaded Proxmox VE 8.1 ISO from official repository
2. Created VMware VM with specified hardware allocation
3. Booted from ISO and completed graphical installation
4. Set strong root password and configured network for DHCP
5. Post-installation reboot completed successfully

## Phase 2: Troubleshooting Nested Virtualization
**Problem:** Initial boot showed "No support for hardware accelerated KVM virtualization"

**Solution:** 
- Edited VMware `.vmx` configuration file
- Added critical parameters:
  ```ini
  vhv.enable = "TRUE"
  hypervisor.cpuid.v0 = "FALSE"
## Rebooted Proxmox VM
## Verfication
kvm-ok
Output: INFO: /dev/kvm exists. KVM acceleration can be used.

## Phase 3: Network Architecture
### Active Network Configuration
| Interface | IP Address/CIDR | Purpose | Notes |
| :--- | :--- | :--- | :--- |
| **Proxmox Management** | `192.168.0.98/24` | Web Interface | Access via `https://192.168.0.98:8006` |
| **vmbr0** | DHCP (Router assigned) | WAN Connection | Connected to physical network |
| **vmbr1** | `10.10.1.0/24` | CORP-LAN | Internal lab network segment |

### Bridge Configuration
- **vmbr0:** Default bridge for internet connectivity
- **vmbr1:** Custom bridge for internal lab environment
- Firewall rules configured to allow management access only from trusted networks

## Phase 4: ISO Repository Preparation
Uploaded the following installation media to Proxmox storage:
- `pfSense-CE-2.8.0-RELEASE-amd64.iso` - Firewall distribution
- `kali-linux-2024.2-installer-amd64.iso` - Penetration testing OS
- `ubuntu-22.04-live-server.iso` - Server infrastructure
- `ubuntu-22.04-desktop-amd64.iso` - GUI workstation environment

## Verification Checklist
- [x] Proxmox web interface accessible at `https://192.168.0.98:8006`
- [x] Nested virtualization confirmed working with `kvm-ok`
- [x] Network bridges `vmbr0` and `vmbr1` operational
- [x] All installation ISOs available in storage repository
- [x] System stable with allocated resources (20GB RAM, 8 vCPUs)

## Security Notes
- Root password secured with strong credentials
- Web interface exposed only on internal network
- Regular updates scheduled via Proxmox package manager
