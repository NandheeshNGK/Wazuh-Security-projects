# Malicious File Detection + Email Alert System Using Wazuh and VirusTotal

## Introduction
This project demonstrates endpoint detection and response (EDR) capabilities using Wazuh, an open-source security monitoring platform. The objective is to monitor critical directories for malicious or suspicious files, integrate VirusTotal to automatically check file hashes for threats, and send email alerts when a potentially malicious file is detected. For this project, Ubuntu virtual machines were used for both the Wazuh Manager and Agent.

<img width="870" height="400" alt="image" src="https://github.com/user-attachments/assets/1124549b-46c5-475f-b25c-c634f4a43cb3" />

##Project Setup and Implementation Steps
## 1. Wazuh Setup on Ubuntu VMs
This project uses two **Ubuntu virtual machines**:
- **Wazuh Manager VM**: collects events from agents, applies rules, triggers VirusTotal checks, and sends alerts.
- **Wazuh Agent VM**: monitors critical directories for file changes and sends events to the manager.

**Installation Steps:**
1. Deploy Ubuntu VM for the manager.
2. Install Wazuh manager on the manager VM.
3. Deploy Ubuntu VM for the agent.
4. Install Wazuh agent and configure it to communicate with the manager.

## 2. Configuring Wazuh Agent for File Monitoring
The agent monitors files and directories for changes using **File Integrity Monitoring (FIM)**.

**Sample `ossec.conf.agent` snippet:**
```xml
<syscheck>
  <disabled>no</disabled>
  <frequency>43200</frequency>
  <scan_on_start>yes</scan_on_start>

  <!-- Directories to monitor -->
  <directories>/etc,/usr/bin,/usr/sbin</directories>
  <directories>/bin,/sbin,/boot</directories>
  <directories realtime="yes">/root</directories> -- Add the following line to define the directories that will be monitored

  <!-- Exclude system directories -->
  <ignore>/proc</ignore>
  <ignore>/sys</ignore>
  <ignore>/dev</ignore>
</syscheck>
```

![wazuh1](https://github.com/user-attachments/assets/b1b75a0c-1bb4-45c9-9b5f-a62a1762fc9b)


**Restart agent**:
sudo systemctl restart wazuh-agent

## 3. Adding Local Rules on Wazuh Manager

**Create a custom rule on the manager to detect changes in /root**.
**Sample `local_rules.xml` example:**
```xml

<group name="local,">
  <rule id="100200" level="7">
    <if_sid>550</if_sid>
    <match>/root</match>
    <description>File modified inside /root directory.</description>
    <group>fim,vt-alert</group>
  </rule>
</group>

```
<img width="1100" height="544" alt="image" src="https://github.com/user-attachments/assets/a9dba24d-af89-47db-b628-c007a8afe50a" />
