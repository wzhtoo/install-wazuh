# Initial Setup Issues on Ubuntu VM for Wazuh Lab

When starting a new Wazuh lab setup on a fresh **Ubuntu VM (especially a minimal installation or server version)**, two common command-line utility issues are often encountered immediately after installation: **`curl`** , **`ifconfig`** and **`Fixing Copy/Paste and Clipboard Sharing on Ubuntu VM`** are usually missing or not found in the default system PATH for all users.

This document outlines the problems and provides the quick fixes necessary to proceed with the Wazuh installation process.

## 1\. Issue: `curl: command not found`

The `curl` command is essential for downloading the Wazuh installation scripts and GPG keys from the official repositories. Since a minimal Ubuntu installation typically does not include it by default, attempting to run any Wazuh installation command that starts with `curl` will result in an error.

### The Problem

```bash
$ curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
Command 'curl' not found, but can be installed with:
sudo apt install curl
```

### The Solution

You must first update your package list and then install `curl` using the `apt` package manager.

```bash
# Update the local package index
sudo apt update

# Install the curl utility
sudo apt install curl -y
```

Once installed, you can proceed with using `curl` to download necessary files for Wazuh.

## 2\. Issue: `ifconfig: command not found`

`ifconfig` is a traditional command used to view and configure network interfaces, often necessary to quickly check the VM's **IP address** before proceeding with installation or firewall configuration. In modern Ubuntu versions, this command is part of the deprecated **`net-tools`** package and has been superseded by the **`ip`** command.

### The Problem

```bash
$ ifconfig
Command 'ifconfig' not found, but can be installed with:
sudo apt install net-tools
```

### The Solution (Recommended: Use `ip a`)

The best practice on modern Linux is to use the **`ip address`** (or **`ip a`** for short) command, which is always available by default:

```bash
# View network interface details using the modern command
ip a
```

### Alternative Solution (If `ifconfig` is preferred or required)

If you specifically need to use `ifconfig`, you can install the legacy package:

```bash
# Install the net-tools package
sudo apt install net-tools -y
```

After installation, the `ifconfig` command will be fully functional.

-----

This initial setup should resolve the two most immediate obstacles and allow you to continue with the main steps of your Wazuh deployment\!

## 3\. Issue: Fixing Copy/Paste and Clipboard Sharing on Ubuntu VM

One of the most common productivity issues when setting up a **Wazuh Lab** on a **Graphical Ubuntu VM (Virtual Machine)**, especially in VMware or VirtualBox, is the inability to **copy and paste (clipboard sharing)** between the host machine (your main computer) and the guest VM (the Ubuntu instance).

This feature is typically provided by a set of drivers and utilities often called "VMware Tools" or "Guest Additions." For modern Ubuntu systems, the easiest way to install this functionality is by installing the `open-vm-tools-desktop` package.

## The Problem

Out of the box, a fresh Ubuntu VM with a desktop environment may not have the necessary software components running to synchronize the clipboard between the Host and the Guest.

**Symptom:** You copy text from your Windows/macOS/Linux host machine, but pressing `Ctrl+V` or right-click $\rightarrow$ Paste inside the Ubuntu VM does nothing.

## The Solution: Installing `open-vm-tools-desktop`

The `open-vm-tools-desktop` package provides the complete set of tools required for a seamless desktop experience, including clipboard sharing, dynamic screen resizing, and better performance.

### Required Command

You should run this command inside your Ubuntu VM's terminal:

```bash
# Update the package index
sudo apt update

# Install the desktop version of the Open VM Tools for clipboard and graphics
sudo apt install open-vm-tools-desktop -y
```

### Final Step

After the installation is complete, it is crucial to **reboot the VM** (or at least log out and log back in) for the new service to start and fully take effect.

```bash
# Reboot the VM to activate the installed tools
sudo reboot
```

Once the system restarts, you should be able to copy and paste text seamlessly between your host machine and your Ubuntu Wazuh VM, significantly improving your installation workflow.