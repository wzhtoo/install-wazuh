# Wazuh Lab Setup & Advanced Threat Detection Guide

This repository contains the full installation, troubleshooting, and advanced configuration steps for setting up a Wazuh All-in-One server and implementing **real-time threat intelligence (VirusTotal) integration**.

This setup is ideal for learning unified security monitoring (XDR & SIEM), file integrity monitoring (FIM), and log data analysis.

---

## Comprehensive Project Documentation

The contents of this repository are structured to provide a complete, step-by-step guide for recreating this lab environment and the advanced detection workflow.

Each numbered file corresponds to a major stage of the project:

| File Name | Project Phase Covered | Key Outcomes Demonstrated |
| :--- | :--- | :--- |
| **01\_wazuh\_initial\_setup.md** | Initial Wazuh Central Component Installation | Successful All-in-One deployment. |
| **03\_wazuh\_agent\_dev.md** | Windows Agent Deployment | Agent enrollment commands and service start-up. |
| **04-05\_Troubleshoot\_wazuh\_agent(...).md** | Agent Enrollment Troubleshooting | Resolving connection failures by opening **port 1515** on the server firewall. |
| **06\_VirusTotals.md** | Initial Threat Intelligence | Confirming the malicious nature of the URL (`coindex-income.com`) via **VirusTotal**. |
| **07\_DirectURLThreatIntellig...** | **Advanced SIEM Integration** | Writing **Custom Rule 100003** and configuring the VT URL API integration. |
| **README.md** (This file) | Full Project Summary & Report | Summarizes all phases and highlights the final validated alert and remediation action. |

