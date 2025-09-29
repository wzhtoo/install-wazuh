# Wazuh + VirusTotal API Integration Guide  

## Step 1: Create Test Directory  

```bash
mkdir /opt/wazuh
chmod 777 /opt/wazuh
````

➡️ **Note**:

* Normally, for testing Wazuh FIM, you can use a temporary path such as `/tmp/malware`.
* In this setup, we’re using `/opt/wazuh` because the server is configured to restart every 5 minutes, and using `/opt/wazuh` ensures the test directory is available for monitoring.
* When testing with this folder, Wazuh will generate an alert with **Rule ID 554 (File added to the system)**.
* If the folder disappears after a system restart, that’s just the **normal cleanup process** — not an error.

👉 Do you also want me to add a **warning** that `/opt/wazuh` should only be used for **testing purposes**, and in production they should avoid using system paths?


## Step 2: Configure Agent for FIM

Add the following to your **agent configuration** to enable real-time monitoring:

```xml
<agent_config os="linux">
  <syscheck>
      <directories realtime="yes" check_all="yes">/opt/wazuh</directories>
  </syscheck>
</agent_config>
```

Navigate to:
**Agents Management > Groups** in the Wazuh Dashboard to apply this config.

---

## Step 3: Restart Agent & Test File Creation

**On the Agent Machine:**

```bash
systemctl restart wazuh-agent
echo "hello" >> /opt/wazuh/hello.txt
```

✅ At this point, Wazuh should detect the new file `hello.txt`.

---

## Step 4: Verify in Wazuh Dashboard

* Go to **Wazuh Dashboard**
* Navigate: `File Integrity Monitor > Events`

📷 Example: <img src="./image/01_wazuh.png" width="600">

🔗 [Official Wazuh - VirusTotal Integration Docs](https://documentation.wazuh.com/current/user-manual/capabilities/malware-detection/virus-total-integration.html)

## Step 5: Configure VirusTotal API

**On the Wazuh Server (Ubuntu):**

```bash
sudo nano /var/ossec/etc/ossec.conf
```

**Insert the integration:**

```xml
<integration>
  <name>virustotal</name>
  <api_key>API_KEY</api_key> <!-- Replace with your VirusTotal API key -->
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```

📷 Example: <img src="./image/api.png" width="600">

Restart Wazuh Manager:

```bash
systemctl restart wazuh-manager
```
## Step 6: Test with Malware File

**On the Agent Machine (Ubuntu):**

```bash
curl https://secure.eicar.org/eicar.com -o /opt/wazuh/eicar
```

➡️ This is the **EICAR test malware file** (safe for testing) used to check antivirus/malware detection systems.

## Step 7: Validate Results in Wazuh Dashboard

Return to the **Wazuh Dashboard** on the server:

Example Screenshots:

* VirusTotal Integration <img src="./image/virustotal.png" width="600">

* Detection Detail <img src="./image/Detail.png" width="600">

---

✅ **Conclusion**:
With this setup, every file change detected by Wazuh’s FIM will be automatically sent to **VirusTotal** for analysis, helping you identify potential malware quickly.