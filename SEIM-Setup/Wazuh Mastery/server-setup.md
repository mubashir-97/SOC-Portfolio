---

# ✅ Wazuh Multinode Deployment Checklist

## Part 2 – Server (Manager) Setup

---

### 1. System Preparation

#### 1.1 Prepare the OS

* Update repositories and upgrade packages:

  ```bash
  apt-get update && apt-get upgrade -y
  ```
* Set hostname and update `/etc/hosts` with node names (d1, d2, etc.).
* Sync system time (important for cluster communication & cert validation):

  ```bash
  apt-get install -y chrony
  timedatectl set-ntp true
  ```
* Disable swap (recommended for performance):

  ```bash
  swapoff -a
  sed -i '/ swap / s/^/#/' /etc/fstab
  ```
* Open firewall for Wazuh Manager ports (default 1514/1515 for agents, 55000 for API):

  ```bash
  ufw allow 1514/tcp
  ufw allow 1515/tcp
  ufw allow 55000/tcp
  ```
* Reboot if kernel updates were applied.

#### 1.2 OS Hardening & Prereqs

* Create a dedicated non-root admin user for maintenance.
* Ensure `curl`, `tar`, `unzip`, `gpg`, `lsb-release` are installed:

  ```bash
  apt-get install curl tar unzip gpg lsb-release -y
  ```

---

### 2. Wazuh Manager Installation

* Add repository & key:

  ```bash
  curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring \
  --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import
  chmod 644 /usr/share/keyrings/wazuh.gpg

  echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" \
  | tee /etc/apt/sources.list.d/wazuh.list
  apt-get update
  ```
* Install manager:

  ```bash
  apt-get -y install wazuh-manager=4.4.11-1
  ```

---

### 3. Certificate Deployment

```bash
NODE_NAME=<SERVER_NODE_NAME>   # Example: d1 or d2

mkdir -p /etc/filebeat/certs
tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ \
    ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem

mv -n /etc/filebeat/certs/$NODE_NAME.pem /etc/filebeat/certs/filebeat.pem
mv -n /etc/filebeat/certs/$NODE_NAME-key.pem /etc/filebeat/certs/filebeat-key.pem

chmod 500 /etc/filebeat/certs
chmod 400 /etc/filebeat/certs/*
chown -R root:root /etc/filebeat/certs
```

---

### 4. Wazuh Manager – Indexer Connection

* Store credentials securely:

  ```bash
  echo 'admin' | /var/ossec/bin/wazuh-keystore -f indexer -k username
  echo '<ADMIN_PASSWORD>' | /var/ossec/bin/wazuh-keystore -f indexer -k password
  ```
* Edit `/var/ossec/etc/ossec.conf`:

```xml
<indexer>
  <enabled>yes</enabled>
  <hosts>
    <host>https://192.168.0.223:9200</host>
    <host>https://192.168.0.224:9200</host>
  </hosts>
  <ssl>
    <certificate_authorities>
      <ca>/etc/filebeat/certs/root-ca.pem</ca>
    </certificate_authorities>
    <certificate>/etc/filebeat/certs/filebeat.pem</certificate>
    <key>/etc/filebeat/certs/filebeat-key.pem</key>
  </ssl>
</indexer>
```

---

### 5. Filebeat Setup

* Install Filebeat:

  ```bash
  apt-get -y install wazuh-filebeat
  ```
* Configure `/etc/filebeat/filebeat.yml`:

```yaml
output.elasticsearch:
  hosts: ["https://192.168.0.223:9200", "https://192.168.0.224:9200"]
  protocol: https
  username: admin
  password: <ADMIN_PASSWORD>
  ssl.certificate_authorities: ["/etc/filebeat/certs/root-ca.pem"]
  ssl.certificate: "/etc/filebeat/certs/filebeat.pem"
  ssl.key: "/etc/filebeat/certs/filebeat-key.pem"

setup.template.json.enabled: true
setup.template.json.path: '/etc/filebeat/wazuh-template.json'
setup.template.json.name: 'wazuh'
setup.ilm.overwrite: true
setup.ilm.enabled: false

filebeat.modules:
  - module: wazuh
    alerts:
      enabled: true
    archives:
      enabled: false
```

---

### 6. Service Management

```bash
systemctl daemon-reload
systemctl enable wazuh-manager filebeat
systemctl start wazuh-manager filebeat
systemctl status wazuh-manager -l
systemctl status filebeat -l
```

---

### 7. Validation & Security Check

#### 7.1 Filebeat output test

```bash
filebeat test output
```

Expected Output (sample):

```
elasticsearch: https://192.168.0.223:9200...
  parse url... OK
  connection...
    dial up... OK
  TLS...
    handshake... OK
  talk to server... OK
  version: 7.10.2
```

#### 7.2 Check indices in Indexer cluster

```bash
curl -k --cert /etc/wazuh-indexer/certs/admin.pem \
     --key /etc/wazuh-indexer/certs/admin-key.pem \
     https://192.168.0.223:9200/_cat/indices?v | grep wazuh
```

#### 7.3 Manager cluster health

```bash
/var/ossec/bin/cluster_control -i
```

---

### 8. Troubleshooting Guidelines

#### 8.1 Filebeat Issues

* **Error:** `x509: certificate signed by unknown authority`
  → Ensure `/etc/filebeat/certs/root-ca.pem` matches the Indexer’s CA.
* **Filebeat won’t start**
  → Check logs at `/var/log/filebeat/filebeat` for YAML errors.
* **Filebeat not shipping logs**
  → Run:

  ```bash
  filebeat test output
  tail -f /var/log/filebeat/filebeat
  ```

#### 8.2 Manager to Indexer Connectivity

* **Connection refused / timeout**
  → Verify firewall allows TCP/9200 between manager and indexer.
* **TLS handshake failed**
  → Certificates may not match hostname/IP. Regenerate with correct SAN entries.
* **Unauthorized errors**
  → Verify `wazuh-keystore` has correct credentials.

#### 8.3 Wazuh Manager Service Issues

* **Manager fails to start**
  → Check `/var/ossec/logs/ossec.log`.
* **Cluster sync not working**
  → Check `/var/ossec/bin/cluster_control -i` for sync errors.
  → Ensure both managers (d1, d2) can resolve each other’s hostname.

#### 8.4 Indexer Indices Missing

* Run:

  ```bash
  curl -k -u admin:<password> https://192.168.0.223:9200/_cat/indices?v
  ```

  → If no `wazuh-alerts-*` appear, Filebeat is not connected.
* Check Filebeat logs for rejected events due to mapping conflicts.

#### 8.5 General Debugging

* Use `journalctl -u wazuh-manager -f` for real-time manager logs.
* Use `journalctl -u filebeat -f` for real-time filebeat logs.
* Use `openssl s_client -connect 192.168.0.223:9200 -CAfile /etc/filebeat/certs/root-ca.pem` to validate TLS connectivity.

---
