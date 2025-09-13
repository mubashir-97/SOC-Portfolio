# 📧 Integrating Gmail with Wazuh for Email Alerts

Wazuh can send security alerts via email. To relay messages through **Gmail**, we’ll use **Postfix** as a mail relay (SMTP client).  

---

## ⚙️ 1. Install Postfix and Dependencies

```bash
apt-get update
apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules -y
```

Choose **Internet Site** when prompted, or press Enter and configure later.

---

## 📝 2. Configure Postfix

Edit the main configuration file:

```bash
nano /etc/postfix/main.cf
```

Add/replace the following lines:

```ini
# Relay through Gmail
relayhost = [smtp.gmail.com]:587

# SASL authentication
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_sasl_tls_security_options = noanonymous
smtp_sasl_mechanism_filter = plain, login

# TLS
smtp_use_tls = yes
smtp_tls_security_level = encrypt
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
```

---

## 🔑 3. Set Gmail Credentials

Create the authentication file:

```bash
nano /etc/postfix/sasl_passwd
```

Add your Gmail account (⚠️ use a **Google App Password**, not your normal password):

```
[smtp.gmail.com]:587 your-email@gmail.com:yourapppassword
```

Now secure and hash it:

```bash
postmap /etc/postfix/sasl_passwd
chmod 600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
chown root:root /etc/postfix/sasl_passwd*
```

---

## 🔄 4. Restart Postfix

Apply changes:

```bash
systemctl restart postfix
systemctl status postfix
```

---

## 📤 5. Test Email Delivery

Send a test message:

```bash
echo "Test mail from postfix" | mail -s "Test Postfix" -r "wazuh@example.wazuh.com" your-email@gmail.com
```

Check logs if you don’t receive it:

```bash
tail -f /var/log/mail.log
```

---

## ⚡ 6. Configure Wazuh to Use Email

Edit Wazuh’s main config:

```bash
nano /var/ossec/etc/ossec.conf
```

Inside `<global> ... </global>`, set your email settings:

```xml
<global>
  <email_notification>yes</email_notification>
  <email_to>your-email@gmail.com</email_to>
  <smtp_server>localhost</smtp_server>
  <email_from>wazuh@example.wazuh.com</email_from>
  <email_maxperhour>12</email_maxperhour>
</global>
```

Save and restart Wazuh:

```bash
systemctl restart wazuh-manager
```

---

## 🧪 7. Trigger a Test Alert

For example, simulate a failed SSH login:

```bash
echo "Sep 13 12:34:56 myhost sshd[1234]: Failed password for root from 203.0.113.50 port 4242 ssh2" >> /var/log/auth.log
```

Check your Gmail inbox for the alert.

---

## ✅ Done!

Now Wazuh is integrated with Gmail and can send real-time alerts.  

---

## ⚠️ Notes

- Always use a **Google App Password** (generated from your Google account security settings). Normal Gmail passwords won’t work.  
- Consider tuning Wazuh rules to only send **high-severity alerts**, otherwise your inbox can get spammed.  
