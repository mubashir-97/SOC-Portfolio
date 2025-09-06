# Step 4: Fixing the KVM Error

## The Problem
After install, Proxmox said: "No support for hardware accelerated KVM virtualization."

## The Solution
1.  **Shut down** the Proxmox VM.
2.  On your Windows PC, find the VM's files.
3.  Right-click the file ending in **`.vmx`** and open it with Notepad.
4.  Add these two lines to the **very end** of the file:
    ```ini
    vhv.enable = "TRUE"
    hypervisor.cpuid.v0 = "FALSE"
    ```
5.  **Save** the file.
6.  Start the Proxmox VM again. The error should be gone.

## How to Check
1.  Log into the Proxmox web interface.
2.  Open the "Shell" (top-right corner).
3.  Type: `kvm-ok`
4.  It should say: `INFO: /dev/kvm exists. KVM acceleration can be used.`
