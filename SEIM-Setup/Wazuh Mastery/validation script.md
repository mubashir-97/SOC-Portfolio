# 📖 User Manual – Wazuh Multinode Validation Script

## 1. Introduction

The **Wazuh Multinode Complete Validation Script** is a Bash-based utility designed to validate the health and connectivity of a **Wazuh multinode deployment**.
It performs **end-to-end checks** for:

* Wazuh Manager (Master/Worker)
* OpenSearch Indexer cluster
* Wazuh Dashboard
* Filebeat log shipping
* Wazuh API connectivity
* End-to-end alert pipeline (test log → Dashboard)

This script is intended for **SOC engineers, DevOps teams, and consultants** to quickly assess deployment health after installation or during routine audits.

---

## 2. Prerequisites

### 2.1 System Requirements

* Linux system with Bash shell (recommended: Ubuntu 20.04/22.04 or CentOS 7/8)
* Root or sudo privileges
* Running Wazuh multinode deployment (Manager, Indexer(s), Dashboard, Filebeat)

### 2.2 Required Tools

* `systemctl` (service management)
* `curl` (API queries)
* `logger` (for test log injection)
* `journalctl` (to read Filebeat logs)

### 2.3 Configuration

Update the following variables inside the script (`wazuh_multinode_validate_summary.sh`) to match your environment:

```bash
MASTER_MANAGER="192.168.0.221"
WORKER_MANAGER="192.168.0.222"
INDEXERS=("192.168.0.223" "192.168.0.224")
DASHBOARD="192.168.0.225"

WAZUH_API_USER="wazuh-wui"
WAZUH_API_PASS="wazuh-wui"
INDEXER_USER="admin"
INDEXER_PASS="admin"

DASH_CERT="/etc/wazuh-dashboard/certs/dashboard.pem"
DASH_KEY="/etc/wazuh-dashboard/certs/dashboard-key.pem"
```

---

## 3. Installation

1. Save the script on the **dashboard node**:

```bash
nano /usr/local/bin/wazuh_multinode_validate_summary.sh
```

2. Paste the script contents and save.

3. Make it executable:

```bash
chmod +x /usr/local/bin/wazuh_multinode_validate_summary.sh
```

---

## 4. Usage

Run the script with `sudo`:

```bash
sudo /usr/local/bin/wazuh_multinode_validate_summary.sh
```

---

## 5. Output Explanation

The script provides **color-coded output**:

* ✅ **Green** = Component healthy
* ❌ **Red** = Component error or misconfigured
* ⚠️ **Yellow** = Warning / Partial check

### Sections Checked:

1️⃣ **Services Status** → Ensures `wazuh-manager` and `wazuh-dashboard` are running
2️⃣ **Wazuh API Test** → Validates API on port `55000`
3️⃣ **Manager Cluster Test** → Ensures cluster nodes are connected (`cluster_control -l`)
4️⃣ **Indexer Connectivity** → Checks indexers, confirms Wazuh indices, shows cluster health
5️⃣ **Filebeat Output Test** → Confirms Filebeat can send logs and checks for recent errors
6️⃣ **End-to-End Log Test** → Injects a test log and verifies pipeline delivery
7️⃣ **Summary** → Final results in console + JSON report (`validation_report.json`)

---

## 6. Example Run

```bash
==============================
1️⃣ Services Status
==============================
wazuh-manager: Active
wazuh-dashboard: Active

==============================
2️⃣ Wazuh API Test
==============================
Wazuh API (192.168.0.221): OK

==============================
3️⃣ Manager Cluster Test
==============================
Manager Cluster Control: Connected

==============================
4️⃣ Indexer Connectivity
==============================
Indexer 192.168.0.223: OK
Indexer 192.168.0.224: OK
Cluster Health:
"status" : "green"

==============================
5️⃣ Filebeat Output Test
==============================
Filebeat Output: OK
Filebeat Recent Errors: None

==============================
6️⃣ End-to-End Log Test
==============================
End-to-End Test log: Generated

==============================
✅ Validation Summary
==============================
Filebeat: OK
Indexer 192.168.0.223: OK
Indexer 192.168.0.224: OK
Wazuh API: OK
Manager Cluster: OK
wazuh-manager: OK
wazuh-dashboard: OK
End-to-End Test: OK
==============================

📁 JSON report saved as validation_report.json
👉 Open https://192.168.0.225 and check Dashboard → Discover → Alerts for test log.
```

---

## 7. Troubleshooting

| Issue                                      | Possible Cause                   | Resolution                                               |
| ------------------------------------------ | -------------------------------- | -------------------------------------------------------- |
| ❌ `wazuh-manager: Inactive`                | Service not started              | `systemctl restart wazuh-manager`                        |
| ❌ `Wazuh API Error`                        | Wrong credentials / firewall     | Verify user/password in `api.yaml`, check port `55000`   |
| ❌ `Indexer Error`                          | TLS cert mismatch                | Verify cert/key paths, check indexer logs                |
| ❌ `Filebeat Error`                         | Wrong indexer creds / CA missing | Fix `filebeat.yml` → update `ssl.certificateAuthorities` |
| ❌ End-to-End Test not showing in Dashboard | Filebeat pipeline issue          | Check `/var/log/filebeat/filebeat` for dropped events    |

---

## 8. Best Practices

* Run the validation **after every deployment change**
* Keep `validation_report.json` for audit history
* Automate execution via **cron** or CI/CD for routine health checks

---
