### 1️⃣ General System Checks

* Verify hostnames and `/etc/hosts` entries for all nodes.
* Ensure NTP is synced on all nodes:

```bash
timedatectl status
chronyc sources
```

* Confirm firewall allows required ports:

  * **Manager:** 1514 TCP/UDP (agent communication), 55000 TCP (API)
  * **Indexer:** 9200 TCP (OpenSearch), 9300 TCP (cluster)
  * **Dashboard:** 443 TCP (HTTPS)

---

### 2️⃣ Wazuh Manager / Worker

**Common issues:**

#### 2.1 Manager won’t start

```bash
systemctl status wazuh-manager -l
journalctl -u wazuh-manager -f
```

Check:

* Missing config in `/var/ossec/etc/ossec.conf`
* Port conflicts (1514, 55000)
* Permissions on `/var/ossec`

#### 2.2 Worker connection errors

* Verify worker points to master in `ossec.conf`:

```xml
<cluster>
  <node_name>worker</node_name>
  <node_type>worker</node_type>
  <manager>192.168.0.221</manager>
</cluster>
```

* Test TCP connectivity:

```bash
telnet 192.168.0.221 1516
```

* Check logs:

```bash
tail -f /var/ossec/logs/ossec.log
```

---

### 3️⃣ Wazuh Indexer (OpenSearch)

**Common issues:**

#### 3.1 Cluster health issues

```bash
curl -k -u admin:admin https://192.168.0.223:9200/_cluster/health?pretty
```

* Red / yellow cluster → check nodes connectivity:

```bash
curl -k -u admin:admin https://192.168.0.223:9200/_cat/nodes?v
```

#### 3.2 Authentication errors

* Verify credentials in `wazuh-dashboard.yml` and `filebeat.yml`
* Test Filebeat output:

```bash
filebeat test output
```

Expected:
`Connection to backoff(elasticsearch(...)) established.`

#### 3.3 TLS / certificate issues

* Ensure certificates exist and have correct permissions:

```bash
ls -l /etc/wazuh-dashboard/certs
```

* Use curl to test:

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cat/indices?v
```

---

### 4️⃣ Filebeat

**Common issues:**

#### 4.1 Logs not shipped

* Check Filebeat status:

```bash
systemctl status filebeat -l
journalctl -u filebeat -f
```

* Verify configuration (`filebeat.yml`) points to correct indexer and uses correct certs.

#### 4.2 Filebeat output errors

* Test output:

```bash
filebeat test output
```

* Errors → adjust `hosts`, `username`, `password`, `ssl.certificateAuthorities`

---

### 5️⃣ Wazuh Dashboard

**Common issues:**

#### 5.1 Dashboard won’t start

```bash
journalctl -u wazuh-dashboard -f
```

Check:

* YAML syntax in `/etc/wazuh-dashboard/wazuh-dashboard.yml`
* Certs permissions:

```bash
chmod 500 /etc/wazuh-dashboard/certs
chmod 400 /etc/wazuh-dashboard/certs/*
chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
```

* Correct `server.host` → must match dashboard IP (192.168.0.225)

#### 5.2 API connection error

* Verify `api.yaml` points to master node (192.168.0.221)
* Test:

```bash
curl -u wazuh-wui:wazuh-wui -k https://192.168.0.221:55000
```

* Check API logs:

```bash
tail -f /var/ossec/logs/api.log
```

#### 5.3 Indexer / SSL errors

* Test cluster access:

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cat/nodes?v
```

* If 401/403 → verify credentials in `wazuh-dashboard.yml`
* TLS errors → ensure root CA present

#### 5.4 Browser SSL issues

* Import `root-ca.pem` into trusted CAs
* Check cert validity:

```bash
openssl x509 -in /etc/wazuh-dashboard/certs/dashboard.pem -noout -subject -dates
```

#### 5.5 Dashboard loads but no data

* Verify Filebeat → Indexer → Dashboard pipeline:

```bash
filebeat test output
```

* Check index health:

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cluster/health?pretty
```

---

### 6️⃣ Logs to Check

| Component | Log File                        |
| --------- | ------------------------------- |
| Manager   | `/var/ossec/logs/ossec.log`     |
| API       | `/var/ossec/logs/api.log`       |
| Dashboard | `journalctl -u wazuh-dashboard` |
| Filebeat  | `/var/log/filebeat/filebeat`    |

---

