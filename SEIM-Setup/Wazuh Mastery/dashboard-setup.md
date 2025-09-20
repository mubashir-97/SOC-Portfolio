# ✅ Wazuh Multinode Deployment Checklist

## Part 3 – Dashboard Setup

### 1. System Preparation

#### 1.1 Prepare the OS

```bash
apt-get update && apt-get upgrade -y
apt-get install curl tar unzip gpg lsb-release -y

# Set hostname and update /etc/hosts
hostnamectl set-hostname dashboard

# Sync system clock
apt-get install -y chrony
timedatectl set-ntp true

# Disable swap
swapoff -a
sed -i '/ swap / s/^/#/' /etc/fstab

# Open firewall for HTTPS
ufw allow 443/tcp
```

---

### 2. Dashboard Installation

```bash
# Add repository & GPG key
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring \
--keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import
chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" \
| tee /etc/apt/sources.list.d/wazuh.list
apt-get update

# Install Wazuh Dashboard
apt-get -y install wazuh-dashboard=4.4.11-1
```

---

### 3. Certificate Deployment

```bash
NODE_NAME=dashboard
mkdir -p /etc/wazuh-dashboard/certs
tar -xf ./wazuh-certificates.tar -C /etc/wazuh-dashboard/certs/ \
    ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem

mv -n /etc/wazuh-dashboard/certs/$NODE_NAME.pem /etc/wazuh-dashboard/certs/dashboard.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME-key.pem /etc/wazuh-dashboard/certs/dashboard-key.pem

chmod 500 /etc/wazuh-dashboard/certs
chmod 400 /etc/wazuh-dashboard/certs/*
chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
```

---

### 4. Wazuh Dashboard Configuration

Edit `/etc/wazuh-dashboard/wazuh-dashboard.yml`:

```yaml
server.host: 192.168.0.225
server.port: 443

opensearch.hosts: ["https://192.168.0.223:9200", "https://192.168.0.224:9200"]
opensearch.ssl.verificationMode: certificate
opensearch.ssl.certificateAuthorities: ["/etc/wazuh-dashboard/certs/root-ca.pem"]

# Indexer credentials
opensearch.username: "admin"
opensearch.password: "admin"

opensearch.requestHeadersAllowlist: ["securitytenant","Authorization"]
opensearch_security.multitenancy.enabled: false
opensearch_security.readonly_mode.roles: ["kibana_read_only"]

server.ssl.enabled: true
server.ssl.key: "/etc/wazuh-dashboard/certs/dashboard-key.pem"
server.ssl.certificate: "/etc/wazuh-dashboard/certs/dashboard.pem"

uiSettings.overrides.defaultRoute: /app/wz-home
```

---

### 5. Wazuh API Configuration

Edit `/usr/share/wazuh-dashboard/config/api.yaml`:

```yaml
hosts:
  - default:
      url: https://192.168.0.221
      port: 55000
      username: wazuh-wui
      password: wazuh-wui
      run_as: false
```

✅ Use master node (192.168.0.221) for API.
Ensure `wazuh-wui` user exists in `/var/ossec/etc/internal_users.yml` on the master server.

---

### 6. Service Management

```bash
systemctl daemon-reload
systemctl enable wazuh-dashboard
systemctl start wazuh-dashboard
systemctl status wazuh-dashboard -l
```

---

### 7. Validation

**7.1 Test Wazuh API connectivity from Dashboard node**

```bash
curl -u wazuh-wui:wazuh-wui -k https://192.168.0.221:55000
```

Expected: JSON response from Wazuh API.

**7.2 Test Indexer connectivity**

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cat/indices?v | grep wazuh
```

**7.3 Browser test**
Open: `https://192.168.0.225`
Login with `admin:admin` (or your configured credentials).

---

### 8. Troubleshooting Guidelines

#### 8.1 Dashboard won’t start

```bash
journalctl -u wazuh-dashboard -f
```

Common causes:

* YAML syntax/indentation errors in `wazuh-dashboard.yml`
* Cert permissions incorrect:

```bash
chmod 500 /etc/wazuh-dashboard/certs
chmod 400 /etc/wazuh-dashboard/certs/*
chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
```

* Wrong `server.host` → ensure it matches `192.168.0.225`.

---

#### 8.2 API connection error

* Confirm `api.yaml` points to master node IP (`192.168.0.221`)
* Test with curl (step 7.1)
* Check master API logs:

```bash
tail -f /var/ossec/logs/api.log
```

* Ensure `wazuh-wui` user exists on master.

---

#### 8.3 Indexer authentication / SSL issues

* Check `opensearch.username` / `opensearch.password` in `wazuh-dashboard.yml`
* Test connectivity:

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cat/nodes?v
```

* TLS errors → confirm `root-ca.pem` exists.

---

#### 8.4 Browser SSL errors

* Import `root-ca.pem` into trusted CAs or bypass warning for testing.
* Check certificate validity:

```bash
openssl x509 -in /etc/wazuh-dashboard/certs/dashboard.pem -noout -subject -dates
```

---

#### 8.5 Dashboard loads but no data

* Verify Filebeat → Indexer → Dashboard pipeline:

```bash
filebeat test output
```

* Check cluster health:

```bash
curl -k --cert /etc/wazuh-dashboard/certs/dashboard.pem \
     --key /etc/wazuh-dashboard/certs/dashboard-key.pem \
     https://192.168.0.223:9200/_cluster/health?pretty
```

---
