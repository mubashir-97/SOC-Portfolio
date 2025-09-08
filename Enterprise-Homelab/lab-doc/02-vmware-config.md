# Step 2: VMware Workstation Setup

## Goal
Create a virtual machine for Proxmox.

## Steps
1.  Open VMware Workstation.
2.  Click "Create a New Virtual Machine".
3.  Select the Proxmox ISO file you downloaded.
4.  Set Guest OS to: `Linux` > `Debian 11.x 64-bit`.
5.  Give it a name: `Proxmox-VE-Lab`.
6.  Set Hard Disk size to **100 GB**.
7.  Click "Customize Hardware":
    - Set **Memory: 16-24 GB**
    - Set **Processors: 4-6 Cores**
    - Set **Network Adapter: Bridged**
8.  Finish and power on the VM.
