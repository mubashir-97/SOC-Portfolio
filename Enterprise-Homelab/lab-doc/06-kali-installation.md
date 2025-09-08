# 06 - Kali Linux Installation & Configuration (Proxmox VM)

## Objective
To install and configure **Kali Linux (latest version)** as a virtual machine on **Proxmox VE**, preparing it for penetration testing and lab exercises.

---

## Prerequisites
- Proxmox VE installed and running  
- ISO image of **Kali Linux** (download from [https://www.kali.org/get-kali/](https://www.kali.org/get-kali/))  
- Sufficient system resources (at least 2 vCPUs, 4GB RAM, 30GB disk recommended)  

---

## Step-by-Step Installation

### 1. Upload Kali ISO to Proxmox
1. In Proxmox Web UI, go to:  
   **Datacenter > [Your Node] > Local (storage) > ISO Images**  
2. Click **Upload**  
3. Select the downloaded `kali-linux-*.iso` file  
4. Wait for upload to complete  

---

### 2. Create a New Virtual Machine
1. Go to **Datacenter > [Your Node]**  
2. Click **Create VM**  
3. Fill in the following:  

- **General**  
  - Node: Select your Proxmox node  
  - VM ID: Auto or custom  
  - Name: `kali-vm`  

- **OS**  
  - Use ISO image: Select `kali-linux-*.iso`  
  - Guest OS: Linux → Debian  

- **System**  
  - Machine: `q35` (recommended)  
  - BIOS: `OVMF (UEFI)`  
  - SCSI Controller: `VirtIO SCSI`  

- **Disks**  
  - Bus/Device: `VirtIO Block`  
  - Disk Size: `30 GB` (or more if needed)  

- **CPU**  
  - Sockets: `1`  
  - Cores: `2` (minimum)  

- **Memory**  
  - RAM: `4096 MB` (4 GB minimum)  

- **Network**  
  - Bridge: `vmbr1` (LAN/VLAN trunk)  
  - Model: `VirtIO (paravirtualized)`  

4. Review settings and click **Finish**  

---

### 3. Install Kali Linux
1. Start the VM → Open Console  
2. Select **Graphical Install**  
3. Follow installer steps:  
   - Language: `English` (or preferred)  
   - Location & Keyboard: Choose as needed  
   - Hostname: `kali-vm`  
   - Domain: leave blank or lab domain  
   - User & Password: Create a strong password  
   - Partition Disks: Use entire disk (default recommended)  
   - Software Selection: Choose **Kali Linux** default tools (can add more later)  
   - Install GRUB: Yes, to primary disk  

4. Reboot after installation completes  

---

### 4. Post-Installation Setup
After logging in:  

- **Update System**  
  ```bash
  sudo apt update && sudo apt full-upgrade -y

# Kali Linux Post-Installation Configuration

## 1. Install Guest Utilities (for Proxmox Optimization)

Run the following commands inside your Kali VM:

# Kali Linux Post-Installation

## Install Guest Utilities (for Proxmox Optimization)

Run inside Kali VM:

```bash
sudo apt install -y qemu-guest-agent spice-vdagent
sudo systemctl enable qemu-guest-agent --now

# Kali Linux VM Enhancements & Security

## 3. VM Enhancements
- Enable **Clipboard & File Sharing** using `spice-vdagent` (if using SPICE display)  
- Create **Snapshots** before major changes  
- Optionally add **USB passthrough** for Wi-Fi adapters (needed for wireless pentesting)  

---

## 4. Verification Checklist
- [x] VM boots into Kali Linux GUI  
- [x] Networking working (IP from pfSense DHCP)  
- [x] Internet access verified  
- [x] Proxmox guest agent running  
- [x] Updates installed  

---

## 5. Security Notes
- Use a **strong user password**  
- Keep system updated (`apt upgrade`)  
- Restrict remote SSH access if not needed  
- Use **snapshots** to roll back after pentests  
