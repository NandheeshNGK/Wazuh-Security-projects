# Malicious File Detection + Email Alert System Using Wazuh and VirusTotal

## Introduction
This project demonstrates endpoint detection and response (EDR) capabilities using Wazuh, an open-source security monitoring platform. The objective is to monitor critical directories for malicious or suspicious files, integrate VirusTotal to automatically check file hashes for threats, and send email alerts when a potentially malicious file is detected. For this project, Ubuntu virtual machines were used for both the Wazuh Manager and Agent.

<img width="870" height="400" alt="image" src="https://github.com/user-attachments/assets/1124549b-46c5-475f-b25c-c634f4a43cb3" />

##Project Setup and Implementation Steps
## 1. Wazuh Setup on Ubuntu VMs
This project uses two **Ubuntu virtual machines**:
- **Wazuh Manager VM**: collects events from agents, applies rules, triggers VirusTotal checks, and sends alerts.
- **Wazuh Agent VM**: monitors critical directories for file changes and sends events to the manager.

**Installation Steps (high level):**
1. Deploy Ubuntu VM for the manager.
2. Install Wazuh manager on the manager VM.
3. Deploy Ubuntu VM for the agent.
4. Install Wazuh agent and configure it to communicate with the manager.
