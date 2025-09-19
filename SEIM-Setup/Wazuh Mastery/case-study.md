# 🛡 Wazuh Multinode Deployment – Case Study

**Client:** Confidential
**Prepared by:** K M Mubashir Rahaman
**Date:** 2025-09-19

---

## 1️⃣ Executive Summary

The client reported **dashboard failures, API connectivity issues, and missing alerts** across a Wazuh multinode deployment.

**Objectives:**

* Restore full functionality of the Wazuh environment
* Validate service, API, indexer, and Filebeat connectivity
* Automate end-to-end validation for monitoring and troubleshooting

---

## 2️⃣ Environment Overview

| Component              | IP Address    | Role           |
| ---------------------- | ------------- | -------------- |
| Wazuh Manager (Master) | 192.168.0.221 | Master Node    |
| Wazuh Manager (Worker) | 192.168.0.222 | Worker Node    |
| Wazuh Indexer 1        | 192.168.0.223 | Indexer Node   |
| Wazuh Indexer 2        | 192.168.0.224 | Indexer Node   |
| Wazuh Dashboard        | 192.168.0.225 | Dashboard Node |

**Tools & Methods Used:**

* Bash scripting for automation
* `curl`, Filebeat, OpenSSL, and `systemctl` commands
* Log analysis, certificate verification, and troubleshooting workflows

---

## 3️⃣ System Architecture & Workflow

```
          +--------------------+
          |  Wazuh Dashboard   |
          |   192.168.0.225   |
          +---------+----------+
                    |
                    | HTTPS 443 + TLS
                    |
          +---------v---------+
          |    Wazuh Indexer  |
          | 192.168.0.223-224 |
          +---------+---------+
                    |
                    | Index/Data Sync
                    |
          +---------v---------+
          |   Wazuh Managers  |
          | Master: 192.168.0.221
          | Worker: 192.168.0.222
          +-------------------+
```

**Workflow:**

1. **Agents → Wazuh Managers**
   Agents send logs and alerts to Master & Worker nodes.

2. **Managers → Indexers**
   Managers forward events for indexing and storage in OpenSearch clusters.

3. **Dashboard → Indexers & Managers**
   Dashboard pulls visualizations from indexers and communicates with managers via API.

4. **Filebeat → Indexers → Dashboard**
   Filebeat ensures reliable log forwarding from managers to indexers and dashboard.

---

## 4️⃣ Summary of Actions Taken

* Verified **hostnames**, `/etc/hosts`, and firewall configurations
* Ensured **time synchronization** across all nodes with `chrony`
* Generated and deployed **TLS certificates** for Indexer, Manager, and Dashboard
* Installed and configured **Wazuh Indexer cluster** (d3 & d4) with proper CN verification
* Installed **Wazuh Manager nodes** and connected to Indexers
* Installed **Wazuh Dashboard** and verified API connectivity
* Validated **Filebeat output** and log forwarding pipeline
* Created **automated validation script** for ongoing monitoring

---

## 5️⃣ Key Highlights

* All nodes restored to **active, running status**
* Certificates verified with CN matching and permissions secured
* End-to-end log flow validated through Filebeat → Indexers → Dashboard
* Troubleshooting workflow documented for **future incidents**

---
