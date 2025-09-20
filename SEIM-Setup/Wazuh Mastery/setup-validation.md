---

# ✅ Wazuh Multinode Deployment Checklist

## Part 5 – Complete Validation

This section ensures **all nodes are properly connected, secure, and communicating**.

---

### 1️⃣ Manager / Worker Validation

**1.1 Manager status**

```bash
systemctl status wazuh-manager -l
```

Expected: `active (running)`

**1.2 Worker status**

```bash
systemctl status wazuh-worker -l
```

Expected: `active (running)`

**1.3 Cluster nodes connectivity**
On master manager (`192.168.0.221`):

```bash
/var/ossec/bin/cluster_control -l
```

Expected: All nodes listed as `connected` with recent `keep alive`.

**1.4 Agent registration test**

* List agents on master:

```bash
/var/ossec/bin/agent_control -l
```

* Register a new agent (optional test):

```bash
/var/ossec/bin/agent_control -a
```

Expected: Agent appears on master dashboard.

---

### 2️⃣ Indexer (OpenSearch) Validation

**2.1 Cluster health**

```bash
curl -k -u admin:admin https://192.168.0.223:9200/_cluster/health?pretty
```

Expected: `"status" : "green"` (or `"yellow"` if replicas not allocated).

**2.2 Node list**

```bash
curl -k -u admin:admin https://192.168.0.223:9200/_cat/nodes?v
```

Expected: Both indexer nodes (`192.168.0.223` and `192.168.0.224`) appear.

**2.3 Indices check**

```bash
curl -k -u admin:admin https://192.168.0.223:9200/_cat/indices?v | grep wazuh
```

Expected: Wazuh indices exist (e.g., `wazuh-alerts-4.x`).

---

### 3️⃣ Filebeat Validation

**3.1 Test output to Indexer**

```bash
filebeat test output
```

Expected:

```
Connection to backoff(elasticsearch(...)) established
```

**3.2 Send a test log**

```bash
logger "Wazuh validation test log"
```

Check Wazuh Dashboard → **Discover / Alerts** → log appears within seconds.

---

### 4️⃣ Wazuh Dashboard Validation

**4.1 Service status**

```bash
systemctl status wazuh-dashboard -l
```

Expected: `active (running)`

**4.2 Wazuh API connectivity**

```bash
curl -u wazuh-wui:wazuh-wui -k https://192.168.0.221:55000
```

Expected: JSON response confirming API is up.

**4.3 Indexer connectivity**

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cat/indices?v | grep wazuh
```

Expected: Wazuh indices listed.

**4.4 Browser access**

Open in browser:

```
https://192.168.0.225
```

* Login with `admin:admin` (or configured user)
* Check **Dashboard → Overview** → see agents, alerts, and indices

---

### 5️⃣ End-to-End Validation

1. Generate a test alert on any node:

```bash
logger "Test Wazuh alert from node"
```

2. Wait a few seconds and check:

   * Manager logs → `/var/ossec/logs/ossec.log`
   * Filebeat logs → `journalctl -u filebeat`
   * Dashboard → **Discover** → alert appears

3. Confirm alert indexed in OpenSearch:

```bash
curl -k -u admin:admin https://192.168.0.223:9200/wazuh-alerts-4.x-*/_search?q=Test
```

---
