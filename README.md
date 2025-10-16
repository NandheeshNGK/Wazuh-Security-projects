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
```
sudo systemctl restart wazuh-agent
```

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

## 4. VirusTotal Integration

Add  **VirusTotal integration** to the manager `ossec.conf.manager`:
```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_VT_API_KEY</api_key>
  <rule_id>100200</rule_id>
  <alert_format>json</alert_format>
</integration>
```

**VirusTotal API Setup**

- **To get your API key, first sign up for an account with VirusTotal.**  
- **After signing up, you can access your API key from the VirusTotal portal.**

**Restart Manager**:
```
sudo systemctl restart wazuh-manager

```

## 5.Configure email notifications on the manager

---

### ⚠️ Important Note Before Configuring Email Alerts

If you plan to use **Gmail SMTP** for email notifications, keep in mind that Gmail requires **additional configuration steps**, such as enabling App Passwords or adjusting SMTP settings.  
These steps are **not included in this project**, so please refer to the **official Wazuh or Gmail SMTP documentation** for proper setup before proceeding.

---


Add or edit the email block in manager `ossec.conf`:
```xml
<global>
  <email_notification>yes</email_notification>
  <email_to>your-email@example.com</email_to>
  <smtp_server>smtp.gmail.com</smtp_server>
  <smtp_port>587</smtp_port>
  <email_from>wazuh-alerts@example.com</email_from>
</global>

  <alerts>
    <log_alert_level>3</log_alert_level>
    <email_alert_level>3</email_alert_level>
  </alerts>

  <email_alerts>
    <email_to>mail@gmail.com</email_to>
    <rule_id>502,87105</rule_id>
    <do_not_delay />
  </email_alerts>
```
##**Note:**

- log_alert_level 3: Logs alerts from level 3 and above.

- email_alert_level 3: Sends email for alerts of level 3 and above.

- rule_id: Specific rule IDs for which you want email notifications.
  
- Replace mail@gmail.com with your real email.
