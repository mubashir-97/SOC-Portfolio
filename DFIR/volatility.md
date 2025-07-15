
# 🧠 Volatility 3 Memory Forensics Toolkit — Full Guide with Real Case Investigations

Welcome to this comprehensive guide and casebook for using [Volatility 3](https://github.com/volatilityfoundation/volatility3), one of the most powerful open-source memory forensics tools available today. This guide includes everything from beginner-friendly concepts to advanced plugin usage and real-world case investigations from TryHackMe.

---

## 📌 Table of Contents

1. [What is Volatility?](#what-is-volatility)
2. [Why Use Memory Forensics?](#why-use-memory-forensics)
3. [Volatility 2 vs Volatility 3](#volatility-2-vs-volatility-3)
4. [Installation](#installation)
5. [Core Concepts](#core-concepts)
6. [Important Plugins (Explained)](#important-plugins)
7. [Advanced Hunting Plugins](#advanced-hunting-plugins)
8. [Custom YARA Hunting](#custom-yara-hunting)
9. [Case Studies (TryHackMe)](#case-studies)
10. [References & Resources](#references--resources)

---

## 🧠 What is Volatility?

**Volatility** is a free and open-source memory forensics framework used to extract digital artifacts from volatile memory (RAM) snapshots. It is widely used in incident response, malware analysis, reverse engineering, and threat hunting.

---

## 🔒 Why Use Memory Forensics?

- Detect fileless malware that leaves no disk footprint  
- Identify code injections and runtime process hollowing  
- Extract evidence of network connections, commands, registry keys, and more  
- Investigate insider threats or APTs  
- Perform incident triage on compromised endpoints

---

## 🆚 Volatility 2 vs Volatility 3

| Feature             | Volatility 2 (Python 2) | Volatility 3 (Python 3) |
|---------------------|-------------------------|-------------------------|
| Language            | Python 2                | Python 3 (actively maintained) |
| OS Profile Needed   | ✅ Yes                  | ❌ No (auto-detected) |
| Plugin Format       | Global plugin space     | OS-specific (`windows.`, `linux.`) |
| Performance         | Slower                  | Faster, modular |
| Status              | Legacy                  | Recommended ✅ |

---

## ⚙️ Installation

### 🐧 Linux / WSL:
```bash
git clone https://github.com/volatilityfoundation/volatility3.git
cd volatility3
pip install -r requirements.txt
python3 vol.py -h
```

### 🪟 Windows (Optional):
Download `.zip` from:
https://github.com/volatilityfoundation/volatility3/releases

---

## 🧠 Core Concepts

| Term | Meaning |
|------|---------|
| **Memory Dump** | A snapshot of a system's RAM (e.g., `.raw`, `.vmem`) |
| **Plugin** | A specific Volatility module used to extract info |
| **Kernel Space** | Core system memory where rootkits often hide |
| **User Space** | Normal processes and apps (Word, Chrome, etc.) |
| **Hooking** | Malicious redirection of system calls |
| **Fileless Malware** | Malware that resides only in memory |

---

## 🧩 Important Plugins

| Plugin | Purpose |
|--------|---------|
| `windows.pslist` | List processes (like Task Manager) |
| `windows.pstree` | Tree view of processes (parent-child relationship) |
| `windows.psscan` | Detect hidden/unlinked processes |
| `windows.malfind` | Find injected shellcode or code-injected regions |
| `windows.dlllist` | DLLs loaded by each process |
| `windows.cmdline` | Get command-line args for all processes |
| `windows.netstat` | List network connections |
| `windows.handles` | Open handles (files, registry, events, etc.) |
| `windows.filescan` | Locate file objects in memory |

---

## 🧬 Advanced Hunting Plugins

| Plugin | Use Case |
|--------|----------|
| `windows.ssdt` | Detect SSDT hooks (rootkit indicator) |
| `windows.modules` | List active kernel drivers |
| `windows.driverscan` | Detect hidden/malicious drivers |
| `windows.mutantscan` | Find suspicious mutexes |
| `windows.callbacks` | Dump system callback hooks |
| `windows.apihooks` | Detect API-level user-mode hooks |
| `windows.modscan` | Scan for loaded kernel modules |
| `windows.driverirp` | IRP hooking in drivers |

---

## 🧪 Custom YARA Hunting

You can write custom **YARA rules** to detect malware patterns inside memory.

### Example Rule:
```bash
python3 vol.py -f memory.raw windows.yarascan --yara-rule "rule detect_mz { strings: $mz = {4D 5A} condition: $mz }"
```

YARA Rule:
```yara
rule detect_mz {
    strings:
        $mz = { 4D 5A }  // MZ Header
    condition:
        $mz
}
```

---

## 📂 Case Studies

### 🕵️ Case 001 – Banking Trojan via Fake Adobe Document

**Memory File:** `/Scenarios/Investigations/Investigation-1.vmem`

**Tasks Solved:**
- Extracted system build version
- Identified acquisition timestamp
- Found suspicious `Adobe.exe` process
- PID and PPID confirmed via `pstree`
- Network connection found to `41.168.5.140`
- User-agent extracted via `cmdline`
- Chase.com identified as a domain in memory

---

### 🕵️ Case 002 – Ransomware Post-Incident Analysis

**Memory File:** `/Scenarios/Investigations/Investigation-2.raw`

**Tasks Solved:**
- PID 740 identified as suspicious process
- Parent and path of decryptor located
- DLL used for socket creation: `ws2_32.dll`
- Mutex identified as malware IOC
- Malware name identified via `malfind` and `yarascan`
- `filescan` used to explore malware working directory

---

## 📚 References & Resources

- [Volatility 3 GitHub](https://github.com/volatilityfoundation/volatility3)
- [TryHackMe - Volatility Room](https://tryhackme.com/room/volatility)
- [YARA Rules Guide](https://virustotal.github.io/yara/)
- [Malware Analysis with Volatility Book](https://nostarch.com/malwareanalysis)

---
