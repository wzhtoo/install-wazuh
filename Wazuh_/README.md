# Wazuh Lab Setup & Advanced Threat Detection Guide

This repository documents the complete process of deploying, configuring, and hardening a Wazuh security monitoring environment. It serves as a hands-on guide for learning enterprise-level security operations, from initial setup to advanced threat intelligence integration.

---

## Project Overview

This lab demonstrates a real-world scenario of building a security monitoring infrastructure:

- **Deployed Wazuh All-in-One** server (Manager, Indexer, Dashboard)
- **Integrated Windows 11 endpoint** for security monitoring
- **Implemented advanced threat detection** with VirusTotal integration
- **Performed system hardening** using CIS benchmarks
- **Resolved critical infrastructure issues** (RAM failures, certificate problems, agent connectivity)

---

## Complete Project Documentation

The repository is organized chronologically to guide you through the entire security operations workflow:

| File Name | Phase | Key Outcomes |
| :--- | :--- | :--- |
| **01_wazuh_initial_setup.md** | Infrastructure Deployment | Successful All-in-One Wazuh deployment |
| **03_wazuh_agent_dev.md** | Endpoint Security | Windows agent deployment and configuration |
| **04-05_Troubleshoot_wazuh_agent.md** | Connectivity Resolution | Fixed firewall rules (port 1515) and agent-server communication |
| **06_VirusTotals.md** | Threat Intelligence | Malicious URL analysis (`coindex-income.com`) via VirusTotal |
| **07_DirectURLThreatIntelligence.md** | **Advanced SIEM Integration** | Custom Rule 100003 creation and VT API integration |
| **08_SystemHardening_CIS.md** | Security Hardening | CIS benchmark implementation and score improvement from 26% to 24% |
| **09_Infrastructure_Recovery.md** | Disaster Recovery | RAM failure diagnosis and hardware remediation |

---

## Technical Achievements

### Security Monitoring
- **Real-time Threat Detection**: Configured automated VirusTotal lookups for suspicious URLs
- **Custom Alerting**: Created specialized rules for high-fidelity threat detection
- **Compliance Monitoring**: Implemented CIS Windows 11 Benchmark tracking

### Infrastructure Management
- **Docker Environment**: Managed single-node Wazuh deployment with containers
- **Certificate Management**: Resolved SSL/TLS issues between Wazuh components
- **Update Procedures**: Established safe upgrade processes for Wazuh components

### Endpoint Security
- **Agent Management**: Deployed and maintained Wazuh endpoint protection
- **Policy Enforcement**: Configured password policies, audit policies, and UAC settings
- **Health Monitoring**: Established continuous agent status monitoring

---

## Let's Learn Together!

This repository represents a comprehensive journey through modern security operations. Each document contains:

- **Step-by-step configuration guides**
- **Troubleshooting methodologies** 
- **Real problem-solving scenarios**
- **Best practices for enterprise security**

Whether you're new to security monitoring or looking to enhance your SIEM/XDR skills, these materials provide practical, hands-on experience with a production-ready security platform.

**Explore the files above to follow our complete security operations journey!**