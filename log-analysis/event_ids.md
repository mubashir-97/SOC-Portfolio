# 🧾 Windows Event ID Notes

This file contains my notes and use-cases for common security-relevant Windows Event IDs.

---

## ✅ Event ID 4624 – Successful Logon

- **Description:** A user successfully logged into the system.
- **Logon Types:** 2 (interactive), 3 (network), 10 (remote desktop)
- **Detection Use:** Confirm legit login OR attack success after brute force
- **Correlate With:** 4625, 4672 (privilege), 4776 (NTLM)

---

## ❌ Event ID 4625 – Failed Logon

- **Description:** A failed login attempt occurred.
- **Use Case:** Password spray, brute-force detection
- **Too Many in Short Time?** → Raise alert!
- **Important Fields:** Account Name, Source IP, Logon Type

---

## ⚙️ Event ID 4688 – Process Creation

- **Description:** A new process has been started.
- **Why It Matters:** Used to detect malware, PowerShell, cmd.exe
- **Pair With:** Sysmon logs for full command-line visibility
- **Watch For:** regsvr32, mshta, rundll32, wscript, etc.

---

## 👁️ Event ID 1102 – Audit Log Cleared

- **Description:** Security log was cleared.
- **Use Case:** A potential attacker may be trying to cover tracks.
- **Critical Alert Trigger:** Yes

---

