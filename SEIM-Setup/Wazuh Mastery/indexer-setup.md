# ✅ Wazuh Multinode Deployment Checklist

## Part 1 – Indexer Setup

---

### 0) Prerequisites / Notes (Read First)

* **Supported OS:** Ubuntu 20.04/22.04, RHEL/CentOS 7/8/9 (as per Wazuh documentation).

* **Access:** Root (sudo) privileges on all nodes.

* **Time sync:** Install and enable `ntp` or `chrony` to keep clocks synchronized.

* **Host entries:** Add all nodes to `/etc/hosts` (or configure DNS). Example:

  ```
  192.168.0.221  d1   # Wazuh server (master)
  192.168.0.222  d2   # Wazuh server (worker)
  192.168.0.223  d3   # Indexer 1
  192.168.0.224  d4   # Indexer 2
  192.168.0.225  d5   # Dashboard
  ```

* **Firewall ports:**

  * 1514/tcp, 1515/tcp, 1516/tcp → Wazuh agents/server comms
  * 55000/tcp → Wazuh API
  * 9200/tcp → Indexer API
  * 9300–9400/tcp → Indexer cluster traffic
  * 443/tcp → Dashboard

---

### 1) Plan Certificates (One-Time)

Wazuh requires TLS certificates between all central components. Certificates are generated once, then distributed to all nodes.

* Download certificate tool & sample config:

  ```bash
  curl -sO https://packages.wazuh.com/4.x/wazuh-certs-tool.sh
  curl -sO https://packages.wazuh.com/4.x/config.yml
  chmod +x ./wazuh-certs-tool.sh
  ```

* Edit `config.yml` and define your nodes:

  ```yaml
  nodes:
    indexer:
      - name: d3
        ip: "192.168.0.223"
      - name: d4
        ip: "192.168.0.224"

    server:
      - name: d1
        ip: "192.168.0.221"
        node_type: master
      - name: d2
        ip: "192.168.0.222"
        node_type: worker

    dashboard:
      - name: d5
        ip: "192.168.0.225"
  ```

* Generate and package certificates:

  ```bash
  bash ./wazuh-certs-tool.sh -A
  tar -cvf wazuh-certificates.tar -C ./wazuh-certificates/ .
  ```

* Copy certificate tarball to each node:

  ```bash
  scp wazuh-certificates.tar root@192.168.0.221:/tmp/
  scp wazuh-certificates.tar root@192.168.0.222:/tmp/
  scp wazuh-certificates.tar root@192.168.0.223:/tmp/
  scp wazuh-certificates.tar root@192.168.0.224:/tmp/
  scp wazuh-certificates.tar root@192.168.0.225:/tmp/
  ```

---

### 2) Install & Configure Indexer Cluster (d3, d4)

**A. Install Wazuh Indexer**
On each indexer node:

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | \
  gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && \
  chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | \
  tee /etc/apt/sources.list.d/wazuh.list

apt-get update
apt-get -y install wazuh-indexer
```

**B. Deploy certificates (per node)**
Example for node **d3** (repeat for d4):

```bash
NODE_NAME=d3   # change to d4 on the other node
mkdir -p /etc/wazuh-indexer/certs
tar -xf /tmp/wazuh-certificates.tar -C /etc/wazuh-indexer/certs ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem

mv /etc/wazuh-indexer/certs/$NODE_NAME.pem /etc/wazuh-indexer/certs/indexer.pem
mv /etc/wazuh-indexer/certs/$NODE_NAME-key.pem /etc/wazuh-indexer/certs/indexer-key.pem

chmod 500 /etc/wazuh-indexer/certs
chmod 400 /etc/wazuh-indexer/certs/*
chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs
```

**C. Verify certificate CNs**

```bash
openssl x509 -in /etc/wazuh-indexer/certs/indexer.pem -noout -subject
openssl x509 -in /etc/wazuh-indexer/certs/admin.pem -noout -subject
```

On **d3**, you should see something like:

```
subject= CN=d3, OU=Wazuh, O=Wazuh, L=California, C=US
subject= CN=admin, OU=Wazuh, O=Wazuh, L=California, C=US
```

Then repeat on **d4** for its `indexer.pem`:

```bash
openssl x509 -in /etc/wazuh-indexer/certs/indexer.pem -noout -subject
```

That should show:

```
subject= CN=d4, OU=Wazuh, O=Wazuh, L=California, C=US
```

**D. Configure OpenSearch (per node)**
Edit `/etc/wazuh-indexer/opensearch.yml`:

```yaml
network.host: 192.168.0.223       # IP of this node
node.name: d3                     # Change for d4
path.data: /var/lib/wazuh-indexer
path.logs: /var/log/wazuh-indexer

discovery.seed_hosts:
  - "192.168.0.223"
  - "192.168.0.224"

cluster.initial_master_nodes:
  - "d3"
  - "d4"

plugins.security.ssl.transport.pemcert_filepath: certs/indexer.pem
plugins.security.ssl.transport.pemkey_filepath: certs/indexer-key.pem
plugins.security.ssl.transport.pemtrustedcas_filepath: certs/root-ca.pem

plugins.security.nodes_dn:
  - "CN=d3,OU=Wazuh,O=Wazuh,L=,C="
  - "CN=d4,OU=Wazuh,O=Wazuh,L=,C="
```

**E. Start services & initialize security**

* Run on both nodes:

  ```bash
  systemctl daemon-reload
  systemctl enable wazuh-indexer
  systemctl start wazuh-indexer
  ```

* Run once on any indexer node:

  ```bash
  /usr/share/wazuh-indexer/bin/indexer-security-init.sh
  ```

**F. Validate cluster**

```bash
curl -k -u admin:admin https://192.168.0.223:9200/
curl -k -u admin:admin https://192.168.0.223:9200/_cat/nodes?v
```

✅ Expected: list of nodes **d3, d4** showing up.

---

### 🔧 Troubleshooting – Wazuh Indexer

1. **Cluster Health Issues**

   * Symptom: `curl -k -u admin:admin https://<node_ip>:9200/_cluster/health?pretty` shows `status: red` or only one node.
   * Fixes:

     * Check `discovery.seed_hosts` and `cluster.initial_master_nodes`.
     * Ensure CNs from certs match `nodes_dn` in `opensearch.yml`.
     * Verify firewall ports 9300–9400/tcp are open.
     * Confirm `ntp` or `chrony` running (TLS fails with unsynced clocks).

2. **Security Initialization Errors**

   * Symptom: `indexer-security-init.sh` fails with 503 or `NoNodeAvailableException`.
   * Fixes:

     * Ensure indexer is running:

       ```bash
       journalctl -u wazuh-indexer -f
       tail -f /var/log/wazuh-indexer/wazuh-indexer.log
       ```

     * Fix certs ownership:

       ```bash
       chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs
       chmod 400 /etc/wazuh-indexer/certs/*
       ```

     * Run init only once per cluster.

3. **Authentication Failures (401)**

   * Wrong credentials (default: `admin:admin`).
   * Security not initialized → rerun `indexer-security-init.sh`.
   * Missing CA trust → use `root-ca.pem` with clients.

4. **Node Won’t Start / Service Down**

   * Check status/logs:

     ```bash
     systemctl status wazuh-indexer
     journalctl -xeu wazuh-indexer
     ```

   * Fixes:

     * Port conflict (9200/9300).

     * Heap too small → edit `/etc/wazuh-indexer/jvm.options`:

       ```
       -Xms2g
       -Xmx2g
       ```

     * If corrupted (lab only):

       ```bash
       systemctl stop wazuh-indexer
       rm -rf /var/lib/wazuh-indexer/*
       systemctl start wazuh-indexer
       ```

5. **Useful Debug Commands**

```bash
curl -k -u admin:admin https://<node_ip>:9200/_cluster/health?pretty
curl -k -u admin:admin https://<node_ip>:9200/_cat/nodes?v
curl -k -u admin:admin https://<node_ip>:9200/_cat/indices?v
tail -f /var/log/wazuh-indexer/wazuh-indexer.log
```

---

### Security Checklist (d3 + d4)

To avoid the common 503 “security not initialized” error, confirm the following:

1. **Certificates OK**

   * CN matches node name (d3 or d4).
   * `root-ca.pem` present on both nodes.

2. **OpenSearch Config OK**

   * `network.host = node IP`
   * `node.name = d3/d4` accordingly
   * `cluster.initial_master_nodes` includes both
   * `plugins.security.nodes_dn` matches cert CNs

3. Start services

4. `systemctl daemon-reload`

5. `systemctl enable wazuh-indexer`

6. `systemctl start wazuh-indexer`

7. Check cluster

8. ```bash
   curl -k --cert /etc/wazuh-indexer/certs/admin.pem \
        --key /etc/wazuh-indexer/certs/admin-key.pem \
        https://192.168.0.223:9200/_cluster/health?pretty
   ```

✅ Expect `status: green` and `number_of_nodes: 2`.

9. Initialize security (once)

```bash
/usr/share/wazuh-indexer/bin/indexer-security-init.sh
```

10. Test access

```bash
curl -k -u admin:admin https://192.168.0.223:9200/_cat/nodes?v
```

✅ Both d3 and d4 should be listed.

---
