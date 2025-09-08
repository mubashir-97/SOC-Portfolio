# 07 - Ubuntu 22.04 Server Installation & Configuration (Proxmox VM)

## Objective
To install and configure **Ubuntu Server 22.04 LTS** as a virtual machine on **Proxmox VE**, preparing it for lab use (DNS, web services, monitoring, etc.).

---

## Prerequisites
- Proxmox VE installed and running  
- ISO image of **Ubuntu Server 22.04 LTS** (download from [https://ubuntu.com/download/server](https://ubuntu.com/download/server))  
- Sufficient system resources (**2 vCPUs, 4GB RAM, 20GB disk** minimum)  
- DHCP server available (pfSense in this lab, providing `10.10.10.50/24` by default)  

---

## Step-by-Step Installation

### 1. Upload Ubuntu ISO to Proxmox
1. In Proxmox Web UI, go to:  
   **Datacenter > [Your Node] > Local (storage) > ISO Images**  
2. Click **Upload**  
3. Select the downloaded `ubuntu-22.04-live-server-amd64.iso` file  
4. Wait for upload to complete  

---

### 2. Create a New Virtual Machine
1. Go to **Datacenter > [Your Node]**  
2. Click **Create VM**  
3. Fill in the following:  

**General**  
- Node: Select your Proxmox node  
- VM ID: Auto or custom  
- Name: `ubuntu-server`  

**OS**  
- Use ISO image: Select `ubuntu-22.04-live-server-amd64.iso`  
- Guest OS: Linux → Ubuntu  

**System**  
- Machine: `q35` (recommended)  
- BIOS: `OVMF (UEFI)`  
- SCSI Controller: `VirtIO SCSI`  

**Disks**  
- Bus/Device: `VirtIO Block`  
- Disk Size: `20 GB` (or more if needed)  

**CPU**  
- Sockets: `1`  
- Cores: `2`  

**Memory**  
- RAM: `4096 MB` (4 GB minimum)  

**Network**  
- Bridge: `vmbr1` (LAN/VLAN trunk)  
- Model: `VirtIO (paravirtualized)`  

4. Review settings and click **Finish**  

---

### 3. Install Ubuntu Server
1. Start the VM → Open Console  
2. Select **Install Ubuntu Server**  
3. Follow installer steps:  
   - Language: `English` (or preferred)  
   - Keyboard: Auto-detect or manual  
   - Network: Use DHCP (will automatically get `10.10.10.50/24` from pfSense DHCP)  
   - Proxy: Leave blank (unless required)  
   - Mirror: Default (recommended)  
   - Storage: Use entire disk (default recommended)  
   - Profile Setup: Create user & strong password  
   - Server Name: `ubuntu-server`  
   - OpenSSH: ✅ Install OpenSSH server (recommended)  
   - Featured Server Snaps: Skip for now  

4. Reboot after installation completes  

---

### 4. Post-Installation Setup

#### 4.1 Update System
```bash
sudo apt update && sudo apt upgrade -y
