# Troubleshooting Wazuh: Resolving "wazuh-manager.service failed" and API Connection Issues

In this lab, I encountered a situation where the **Wazuh Manager** service failed to start, causing the Wazuh Dashboard to show an "API Connection Error." Below is the documentation of how I diagnosed and fixed the issue.

## 1. The Symptoms

- The Wazuh Dashboard showed an **API connection error**.
- Running `sudo systemctl status wazuh-manager` returned `Active: failed`.
- The system logs (`/var/ossec/logs/ossec.log`) indicated that `wazuh-apid` (Wazuh API Daemon) failed to start correctly, often showing "Killed" or "Non-existent process" errors.

## 2. Diagnosis

The issue was primarily caused by **stale PID (Process ID) files**. When the Wazuh Manager service is not shut down cleanly, it leaves behind lock files in the `/var/ossec/var/run/` directory. When the service tries to restart, it detects these "ghost" processes and prevents the API daemon from starting.

Additionally, ensure your system has sufficient RAM (at least 4GB recommended), as low memory can cause the Linux kernel to "kill" the API daemon.

## 3. The Fix

Follow these steps to clean up the environment and restart the service:

### Step 1: Remove Stale Lock Files

Clean the directory containing the stale process identifiers:

```bash
sudo rm -rf /var/ossec/var/run/*

```

### Step 2: Fix File Permissions

Ensure the Wazuh service has the correct ownership of its runtime directory:

```bash
sudo chown -R wazuh:wazuh /var/ossec/var/run/

```

### Step 3: Restart the Service

Now, restart the Wazuh manager service to initialize all components correctly:

```bash
sudo systemctl restart wazuh-manager

```

### Step 4: Verify the Fix

Check the status of the service to ensure it is active:

```bash
sudo systemctl status wazuh-manager

```

Also, verify that the API port (55000) is now listening:

```bash
sudo ss -tunlp | grep 55000

```

## Conclusion

This troubleshooting process taught me the importance of checking the `/var/ossec/logs/ossec.log` file when a service fails. Understanding how to manage service processes and clean up stale lock files is a critical skill in maintaining a stable security monitoring environment.

---
