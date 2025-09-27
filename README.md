# Wazuh Lab Setup & Troubleshooting Guide

This repository contains the full installation and troubleshooting steps for setting up a Wazuh All-in-One server and deploying a Windows Agent in a lab environment.

This setup is ideal for learning unified security monitoring (XDR & SIEM), file integrity monitoring (FIM), and log data analysis.

-----

## Project Goal

The primary goal of this lab is to successfully deploy the **Wazuh Central Components** (Manager, Indexer, and Dashboard) and connect a **Windows Agent** to the manager, demonstrating key communication and troubleshooting techniques.

-----

## Hardware Requirements

The following minimum hardware is recommended for a lab environment supporting up to 50 protected endpoints with a 90-day alert retention.

| Agents | CPU (vCPU) | RAM (GiB) | Storage (90 days) |
| :----: | :--------: | :-------: | :---------------: |
| **1 – 50** | 8 vCPU | 8 GiB | 100 GB |

-----

## Installation Guide (Wazuh Central Components)

The central components are installed on a single **Ubuntu Server** using the official Wazuh installation assistant.

### 1\. Run the All-in-One Installation

The following command downloads the installer script and executes the all-in-one installation (`-a`) automatically setting up the Wazuh Manager, Indexer, and Dashboard.

```bash
curl -sO https://packages.wazuh.com/4.13/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

### 2\. Access the Dashboard

Upon successful completion, the script will output the access credentials.

  * **URL:** `https://<WAZUH_DASHBOARD_IP_ADDRESS>`
  * **Username:** `admin`
  * **Password:** `<ADMIN_PASSWORD>` (Found in the summary output)

### Recover Passwords

To retrieve the generated passwords at any time:

```bash
sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

-----

## Deploying the Windows Agent

The agent is deployed on a **Windows endpoint** and configured to connect to the Wazuh Manager IP (`192.168.100.122` in the lab example).

### 1\. Generate Deployment Commands

In the Wazuh Dashboard, navigate to **Agents** \> **Deploy new agent**.

  * **Select OS:** `Windows`
  * **Enter Manager IP:** `192.168.100.122` (or your server IP)
  * **Assign Agent Name:** `Windows`

### 2\. Run Installation on Windows

Execute the following commands in an **Administrator PowerShell** on the Windows endpoint.

**A. Installation & Enrollment:**

```powershell
# (Example command based on generated script)
msiexec.exe /i wazuh-agent-4.x.x-1.msi /qn WAZUH_MANAGER='192.168.100.122' WAZUH_AGENT_NAME='Windows'
```

**B. Start the Service:**

```powershell
NET START WazuhSvc
```

-----

## Troubleshooting Agent Enrollment (Key Fixes)

If the agent fails to connect and enroll, the following steps were crucial for diagnosis and resolution:

### 1\. Check Agent Log File (On Windows)

Always check the log file for the specific error code (e.g., `ERROR: (1208)`) which indicates a connection failure.

```powershell
Get-Content "C:\Program Files (x86)\ossec-agent\ossec.log" -Tail 50
```

### 2\. Confirm Network Blockage

The log error `Unable to connect to enrollment service at '[IP]:1515'` strongly indicates a firewall block. Use `Test-NetConnection` to confirm if port 1515 is accessible.

```powershell
# Run this on the Windows Agent
Test-NetConnection -ComputerName 192.168.100.122 -Port 1515

# FAILURE: If TcpTestSucceeded is 'False', a firewall is blocking the connection.
```

### 3\. Solution: Open Server Firewall (On Ubuntu Server)

To resolve the connection failure, open **TCP port 1515** (Wazuh Enrollment Service) on the server's firewall.

```bash
# Check UFW status
sudo ufw status

# Allow inbound traffic on port 1515
sudo ufw allow 1515/tcp
```

Once port 1515 is allowed, the Windows Agent should successfully enroll and connect to the Wazuh Manager.


---
## Next Steps

Now that your Wazuh environment is fully operational, you can proceed with:
1.  **Exploring the Dashboard:** Familiarize yourself with FIM alerts, vulnerability reports, and security events.
2.  **Deploying More Agents:** Connect additional endpoints (Linux, macOS) to practice group management.
3.  **Custom Rule Creation:** Write custom rules to detect specific events unique to your environment.

Thank you for following this guide!

Happy Monitoring! 🔍