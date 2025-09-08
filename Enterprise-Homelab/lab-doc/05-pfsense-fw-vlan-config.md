# 05 - pfSense Firewall Configuration with VLANs

## Objective
To configure **pfSense CE 2.8.0** with VLANs on a single interface for segmented network management, DHCP services, and firewall rules.

---

## Physical Interface Assignment

| Interface | Physical Connection | Purpose |
|-----------|---------------------|---------|
| **vtnet0** | `vmbr0` (Proxmox)  | **WAN** (Internet Connection) |
| **vtnet1** | `vmbr1` (Proxmox)  | **LAN** (Trunk for all VLANs) |

---

## Step-by-Step Configuration

### 1. VLAN Creation
**Navigate to:** `Interfaces > Assignments > VLANs`

**Add VLANs:**

- **VLAN 10**
  - Parent Interface: `vtnet1`  
  - VLAN Tag: `10`  
  - Description: `LAB-DMZ`

- **VLAN 20**
  - Parent Interface: `vtnet1`  
  - VLAN Tag: `20`  
  - Description: `CORP-LAN`

- **VLAN 30**
  - Parent Interface: `vtnet1`  
  - VLAN Tag: `30`  
  - Description: `SEC-MGMT`

---

### 2. Interface Assignment & Renaming
**Navigate to:** `Interfaces > Assignments`

**Assign and Rename VLAN Interfaces:**

- **VLAN10:** `VLAN 10 on vtnet1` → `LAB-DMZ`  
- **VLAN20:** `VLAN 20 on vtnet1` → `CORP-LAN`  
- **VLAN30:** `VLAN 30 on vtnet1` → `SEC-MGMT`  

**To rename interfaces:**
1. Go to `Interfaces > [Interface]`  
2. Change **Description** to desired name  
3. Click **Save**  
4. Apply changes when prompted  

---

### 3. Interface Configuration
**For each VLAN interface (VLAN10, VLAN20, VLAN30):**

1. ☑ **Enable interface**  
2. **Description:** LAB-DMZ / CORP-LAN / SEC-MGMT  
3. **IPv4 Configuration Type:** `Static IPv4`  
4. **IPv4 Address:**  
   - VLAN10 (LAB-DMZ): `10.10.10.1/24`  
   - VLAN20 (CORP-LAN): `10.10.20.1/24`  
   - VLAN30 (SEC-MGMT): `10.10.30.1/24`  
5. Click **Save**  

---

### 4. DHCP Server Setup
**Navigate to:** `Services > DHCP Server > [Interface]`

- **VLAN10 (LAB-DMZ):**  
  ✅ Enable DHCP server  
  Range: `10.10.10.50 - 10.10.10.100`  

- **VLAN20 (CORP-LAN):**  
  ✅ Enable DHCP server  
  Range: `10.10.20.50 - 10.10.20.100`  

- **VLAN30 (SEC-MGMT):**  
  ✅ Enable DHCP server  
  Range: `10.10.30.50 - 10.10.30.100`  

---

### 5. Firewall Rules Setup
**Navigate to:** `Firewall > Rules > [Interface]`

**Copy rules to each VLAN interface:**
1. Go to `Firewall > Rules > LAN`  
2. Select existing rules  
3. Click **Copy**  
4. Select target VLAN interface (VLAN10, VLAN20, VLAN30)  
5. Click **Copy**  
6. Repeat for each VLAN  

**Default Rule Set (on each VLAN):**
- Allow all IPv4 traffic within VLAN  
- Deny inter-VLAN traffic by default  
- Allow established/related connections  
- Add specific inter-VLAN rules only when needed  

---

### 6. Verification Checklist
- [x] VLAN interfaces show correct IP addresses  
- [x] DHCP leases working on all VLANs  
- [x] Web interface accessible at `https://10.10.20.1`  
- [x] Internet access from CORP-LAN  
- [x] Inter-VLAN traffic blocked by default  
- [x] VLAN isolation verified  

---

## Security Notes
- Restrict pfSense web interface to **CORP-LAN VLAN only**  
- Maintain **default deny** rules between VLANs  
- Apply **regular security updates**  
- Use a **strong admin password**  

---

## Next Steps
👉 [06 - Kali Linux Installation & Configuration](../lab-doc/06-kali-installation.md)
