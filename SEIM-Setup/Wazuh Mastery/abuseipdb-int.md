# 🚨 Integrating AbuseIPDB with Wazuh

Wazuh can be extended with custom integrations to enrich alerts with external threat intelligence.  
This guide shows how to integrate **AbuseIPDB** using the provided script `custom-abuseipdb.py`.

---

## ⚙️ 1. Prerequisites

- A working **Wazuh Manager** installation  
- Python dependencies inside Wazuh framework:

```bash
/var/ossec/framework/python/bin/pip install requests
```

- A valid **AbuseIPDB API key** → [Get one here](https://www.abuseipdb.com/account/api)

---

## 📂 2. Integration Script

Your Wazuh includes the script at:

```
/var/ossec/integrations/custom-abuseipdb.py
```

It queries AbuseIPDB whenever an alert contains a `srcip` field.  
If the IP has reports, extra fields are added to the Wazuh event.

---

## ⚙️ 3. Configure Wazuh

Edit Wazuh configuration:

```bash
nano /var/ossec/etc/ossec.conf
```

Add the integration block:

```xml
<integration>
  <name>custom-abuseipdb.py</name>
  <hook_url>https://api.abuseipdb.com/api/v2/check</hook_url>
  <api_key>YOUR_ABUSEIPDB_API_KEY</api_key>
  <alert_format>json</alert_format>
</integration>
```

📌 Replace `YOUR_ABUSEIPDB_API_KEY` with your real API key.

---

## 🔄 4. Restart Wazuh Integrator

```bash
systemctl restart wazuh-manager
tail -f /var/ossec/logs/ossec.log | grep abuseipdb
```

You should see messages like:

```
wazuh-integratord: INFO: Enabling integration for: 'custom-abuseipdb.py'.
```

---

## 🧪 5. Testing the Integration

1. Create a sample alert JSON file `/tmp/test_alert.json`:

```json
{
  "id": "12345",
  "rule": { "id": "100001" },
  "agent": { "id": "001", "name": "test-agent" },
  "data": { "srcip": "8.8.8.8" }
}
```

2. Run the script manually (for debugging):

```bash
/var/ossec/integrations/custom-abuseipdb.py /tmp/test_alert.json YOUR_ABUSEIPDB_API_KEY https://api.abuseipdb.com/api/v2/check
```

3. Check logs:

```bash
cat /var/ossec/logs/integrations.log
```

---

## 📊 6. Viewing Results in Wazuh

If AbuseIPDB reports data, your alerts will include extra fields such as:

```json
"abuseipdb": {
  "found": 1,
  "abuseConfidenceScore": 75,
  "countryCode": "US",
  "isp": "Example ISP",
  "domain": "example.com",
  "totalReports": 42,
  "lastReportedAt": "2025-09-10T15:00:00+00:00"
}
```

---

## ⚠️ Notes

- Make sure you use the **App key** from AbuseIPDB.  
- Consider adjusting your rules so only suspicious alerts trigger AbuseIPDB lookups (to save API quota).  
- Debug mode can be enabled by setting `debug_enabled = True` inside the script.

---

✅ Now Wazuh will automatically enrich alerts with **AbuseIPDB threat intelligence**!
