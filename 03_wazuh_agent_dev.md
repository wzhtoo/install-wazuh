
# Deploying a New Wazuh Agent on Windows

This guide outlines the steps for configuring and deploying a new Wazuh Agent on a Windows endpoint, based on the selections shown in the deployment screen.

<img src="./Image/1_deploy-new-agent.png">

## 1\. Select the Package

First, select the appropriate installer package for your operating system.

  * **Operating System:** Select **Windows**.
  * **Architecture:** Choose the architecture of your Windows machine. The most common option is usually selected by default.
      * **MSI 32/64 bits**

## 2\. Configure the Server Address

The agent needs the IP address of the central **Wazuh Manager** to connect and send security events.

  * **Server Address:** Enter the IP address or fully qualified domain name (FQDN) of your Wazuh Manager.
  * **Example from Image:** `10.0.0.198`

## 3\. Optional Settings (Agent Name & Group)

Configure optional settings, which include assigning a unique name to the agent and grouping it for easier management.

  * **Assign an agent name:** It is a best practice to assign a descriptive and **unique** name to the agent. This name cannot be changed after the agent is enrolled.
      * **Example from Image:** `Windows`
  * **Select one or more existing groups:** Assign the agent to a group. Agents in the same group share configuration policies.
      * **Example from Image:** `default`

-----

## 4\. Run Commands to Install and Start the Agent

After making the selections above, the Wazuh interface generates the specific commands needed for installation and starting the agent on the Windows endpoint.

### Installation Command

This command downloads the MSI package, installs the agent, and pre-configures it with the manager IP and agent name you provided.

  * **Requirement:** This command **must** be run in a Windows **PowerShell** terminal with **administrator privileges**.

<!-- end list -->

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.x-1.msi -OutFile wazuh-agent.msi ; \
msiexec.exe /i wazuh-agent.msi /qn WAZUH_MANAGER='10.0.0.198' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='Windows'
```

*Note: The exact version number in the URL (`4.x.x`) will vary depending on your Wazuh version.*

### Start the Agent

After the installation is complete, run the following command to start the Wazuh Agent service.

```powershell
NET START Wazuh
```

<img src="./Image/2_deploy-new-agent.png">
   
   

