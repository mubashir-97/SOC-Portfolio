# 💬 Integrating Slack with Wazuh for Real-Time Alerts

Wazuh can push alerts directly to a **Slack channel** using the built-in Slack integration.  
This allows SOC teams to receive **real-time notifications** about security events.

---

## ⚙️ 1. Create a Slack Incoming Webhook

1. Go to your Slack workspace → **Apps** → search for **Incoming Webhooks**.
2. Click **Add Configuration**.
3. Select the channel where you want Wazuh alerts delivered.
4. Copy the **Webhook URL** (looks like `https://hooks.slack.com/services/TXXX/BXXX/XXXXX`).

---

## 📝 2. Configure Wazuh Integration

Edit the Wazuh configuration file:

```bash
nano /var/ossec/etc/ossec.conf
```

Inside `<ossec_config>`, add:

```xml
<integration>
  <name>slack</name>
  <hook_url>https://hooks.slack.com/services/TXXX/BXXX/XXXXX</hook_url>
  <alert_format>json</alert_format>
  <level>10</level>
</integration>
```

🔹 **Parameters**:
- `<name>` → Always `slack`
- `<hook_url>` → Your Slack webhook URL
- `<alert_format>` → Use `json` (recommended for structured alerts)
- `<level>` → Minimum alert level to send (e.g., 10 = only high-priority alerts)

---

## 🔄 3. Restart Wazuh Manager

Apply changes:

```bash
systemctl restart wazuh-manager
systemctl status wazuh-manager
```

---

## 🧪 4. Test the Integration

Generate a fake alert (failed SSH login):

```bash
echo "Sep 13 12:34:56 myhost sshd[1234]: Failed password for root from 203.0.113.50 port 4242 ssh2" >> /var/log/auth.log
```

Check your Slack channel for the alert message.

---

## ✅ Done!

Now Wazuh will send alerts directly into Slack 🎉  

---

## ⚠️ Notes

- Tune the `<level>` setting to avoid spamming Slack with low-severity alerts.  
- You can add multiple `<integration>` blocks if you want different rules for Slack, Gmail, etc.  
- Integration scripts live in `/var/ossec/integrations/slack.py`.

---

📌 Example repo structure for GitHub:

```
wazuh-integrations/
├── gmail/README_GMAIL.md
├── slack/README_SLACK.md
└── screenshots/
    └── slack-alert-example.png
```
