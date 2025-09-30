## Wazuh Integration for Direct URL Threat Intelligence Check

### Objective: Automated URL Scanning via Wazuh and VirusTotal

The core concept of this method is to configure Wazuh to automatically send the specific URL **"coindex-income.com"**, found within Agent logs, to the VirusTotal **URL Report API** for immediate threat confirmation.

-----

### Step 1: Create a Log File for Simulation (Log Simulation)

On the **Ubuntu Agent (Ubuntu\_VM)**, we simulate a typical web access log entry containing the malicious URL.

Run the following command in the Agent's terminal to inject the log into `/var/log/my-access.log`:

```bash
echo "Sep 29 14:30:00 Ubuntu_VM user: Suspicious access to https://coindex-income.com/ detected." | sudo tee -a /var/log/my-access.log
```

-----

### Step 2: Configure the Wazuh Agent for Log Collection (Agent - `ossec.conf`)

The Agent needs to be configured to monitor the newly created log file (`/var/log/my-access.log`) and forward its contents to the Manager.

Add the following configuration block to the Agent's `/var/ossec/etc/ossec.conf` file:

```xml
<ossec_config>
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/my-access.log</location>
  </localfile>
</ossec_config>
```

**Action:** Restart the Agent to apply changes: `sudo systemctl restart wazuh-agent`

-----

### Step 3: Write a Custom Detection Rule (Manager - `local_rules.xml`)

On the **Wazuh Manager**, a custom rule is required to specifically match the injected log message.

Add the following **Custom Rule** to the Manager's `/var/ossec/etc/rules/local_rules.xml` file:

```xml
<group name="custom_url_check,">
  <rule id="100003" level="9">
    <program_name>user</program_name>
    <match>Suspicious access to https://coindex-income.com/ detected</match>
    <description>HIGH: Phishing URL 'coindex-income.com' accessed (External Report Required).</description>
    <group>phishing,mitre_initial_access,virustotal_scan,</group>
  </rule>
</group>
```

-----

### Step 4: Integrate VirusTotal with the New Rule (Manager - `ossec.conf`)

The final step is to instruct the Manager's VirusTotal integration module to use the **URL API** whenever **Rule ID 100003** is triggered.

Add or modify the following block in the Manager's `/var/ossec/etc/ossec.conf`.

***Note:*** *This configuration was heavily validated to avoid XML Tag Ordering errors that previously prevented the Manager from starting.*

```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_VIRUSTOTAL_API_KEY</api_key>  
  <hook_url>https://www.virustotal.com/vtapi/v2/url/report</hook_url>  
  <group>syscheck</group>
  <rule_id>100003</rule_id>   
  <alert_format>json</alert_format>  
</integration>
```

-----

### Step 5: Final Verification

**Action:** Restart the Manager: `sudo systemctl restart wazuh-manager`

**Expected Outcome:**

1.  The Agent sends the simulated log to the Manager.
2.  The Manager triggers **Rule ID 100003**.
3.  The **Wazuh Integrator** module queries VirusTotal.
4.  The resulting Alert on the Dashboard (under the **JSON** tab) will include the **VirusTotal data**, confirming **`positives: 7`**.