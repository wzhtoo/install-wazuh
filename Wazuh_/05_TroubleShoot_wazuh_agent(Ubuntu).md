## The Ultimate Fix for Wazuh Agent Installation Conflict on Debian/Ubuntu

### 1\. The Initial Problem

The user was attempting to install a new **Wazuh Agent** on an Ubuntu system but repeatedly encountered a conflict error, despite previously attempting to remove the old Wazuh components.

**The Error Message:**

```
dpkg: regarding .../wazuh-agent_4.13.1-1_amd64.deb containing wazuh-agent:
 wazuh-agent conflicts with wazuh-manager
 wazuh-manager (version 4.13.1-1) is present and installed.

dpkg: error processing archive ./wazuh-agent_4.13.1-1_amd64.deb (--install):
 conflicting packages - not installing wazuh-agent
Errors were encountered while processing:
 ./wazuh-agent_4.13.1-1_amd64.deb
```

**The Analysis:** The system was rejecting the **Wazuh Agent** installation because its package manager (**`dpkg`**) still recorded the **Wazuh Manager** package as being "present and installed." This is due to an internal conflict: you cannot install the Agent and the Manager on the same machine.

-----

### 2\. Attempted Solutions (and Why They Failed)

The user first attempted the standard removal commands for both the old Agent and the Manager, which failed to clear the package's metadata:

| Attempted Command | Purpose | Result & Failure Point |
| :--- | :--- | :--- |
| `sudo apt-get remove --purge wazuh-manager` | Standard uninstallation to remove the package and configuration files. | **Failed with exit status 127.** The package's internal **pre-removal script** was either missing or corrupted, preventing the cleanup process from completing successfully. |
| `sudo dpkg --remove --force-remove-reinstreq wazuh-manager` | Force removal by ignoring "reinstallation required" errors. | **Failed again with exit status 127.** The corrupted script blocked even this forceful method. |
| `sudo dpkg --remove --force-all wazuh-manager` | The most aggressive force removal command. | **Failed once more with exit status 127.** The stubborn, corrupted script could not be bypassed using conventional package manager tools. |

-----

### 3\. The Final, Successful Solution: Manual Database Cleanup

Since the `dpkg` package manager could not execute its removal scripts, the only way to solve the conflict was to **manually edit the `dpkg` database file** and remove the corrupted entry for `wazuh-manager`.

**The Issue Resolved:** We successfully bypassed the stubborn, corrupted pre-removal script by directly manipulating the `dpkg` database file to mark the `wazuh-manager` package as "not installed."

#### Step 1: Open the `dpkg` Status File

Use the `nano` editor with `sudo` privileges to open the file that stores the installation status of all packages.

```bash
sudo nano /var/lib/dpkg/status
```

#### Step 2: Delete the Corrupted Entry

1.  In the `nano` editor, press **Ctrl + W** and search for `wazuh-manager`.
2.  Find the entry starting with **`Package: wazuh-manager`**.
3.  **Delete every line** of this entry—from the `Package: wazuh-manager` line down to the blank line just before the next package entry (e.g., `Package: [another package name]`).
4.  This action ensures that `dpkg` no longer has any record of the conflicting Manager being installed.

#### Step 3: Save and Exit

1.  Press **Ctrl + O** and then **Enter** to save the changes.
2.  Press **Ctrl + X** to exit the editor.

