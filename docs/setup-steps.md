# Wazuh SOC Home Lab – Setup Guide

This document provides step-by-step instructions to set up the **Multi-Endpoint Wazuh SOC Home Lab** environment.

---

## Prerequisites

### Hardware / Systems

- 1 System for Wazuh Server (Ubuntu)
- 1 Windows 11 system
- 1 Kali Linux system (physical)
- 1 Kali Linux Virtual Machine (inside Windows or any hypervisor)

---

### Network Requirements

- All systems must be connected to the same network
- Note the IP address of the Wazuh server

```bash
ip a
```

---

## Step 1: Install Wazuh Server (Ubuntu)

Update system packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Download the official installation script:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```


This installs:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

---

## Step 2: Access Wazuh Dashboard

After installation, access:

```
https://<WAZUH_SERVER_IP>:443
```

Default credentials (shown in terminal):

- Username: `admin`
- Password: `<generated>`

> Note: You may see a self-signed certificate warning. This is normal for local setups.

---

## Step 3: Deploy Wazuh Agent (Windows)

1. Open Wazuh Dashboard
2. Navigate to **Agents → Deploy New Agent**
3. Select:
   - Operating System: Windows
   - Server Address: `<WAZUH_SERVER_IP>`
   - Agent Name: e.g., `windows-11`
4. Copy the generated PowerShell command
5. Run it in **PowerShell (Administrator)**
6. Start the agent service:

```powershell
net start wazuh
```

---

## Step 4: Deploy Wazuh Agent (Kali Linux)

From Dashboard → Deploy New Agent:

- OS: Linux
- Server IP: `<WAZUH_SERVER_IP>`
- Agent Name: `kali-primary`

Run the generated command on Kali:

```bash
curl -so wazuh-agent.sh <generated_link>
sudo bash wazuh-agent.sh
```

Start the agent:

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

Repeat the same process for the **Kali VM**.

---

## Step 5: Verify Agent Connection

In Wazuh Dashboard:

```
Agents → Agent List
```

Status should show:

```
Active
```

If not active:

### Linux

```bash
sudo systemctl restart wazuh-agent
```

### Windows

```powershell
Restart-Service wazuh
```

Wait 1–2 minutes and refresh the dashboard.

---

## Step 6: Enable Vulnerability Detection

Wazuh automatically scans installed packages and software.

To verify:

```
Dashboard → Vulnerabilities → Select Agent
```

You should see:

- CVE ID
- Severity
- Affected package

---

## Step 7: Remediation Process (Example)

### Windows

1. Identify vulnerable software (e.g., Firefox)
2. Update to latest version
3. Restart system if required
4. Wait for next Wazuh scan

---

### Kali Linux

Update all packages:

```bash
sudo apt update && sudo apt upgrade -y
```

For specific package:

```bash
sudo apt install --only-upgrade <package_name>
```

---

## Step 8: Capture Evidence

For documentation:

- Dashboard overview
- Agent list
- Vulnerability (before)
- Vulnerability (after)

Store screenshots inside:

```
screenshots/
```

---

## Environment Summary

| Component | Details |
|----------|---------|
| SIEM | Wazuh |
| Server OS | Ubuntu |
| Endpoints | Windows 11, Kali Linux, Kali VM |
| Features Used | Vulnerability Detection, Agent Monitoring |

---

## Troubleshooting

### Agent not showing

- Check network connectivity
- Verify server IP
- Restart agent service

---

### Dashboard not accessible

Check services:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-dashboard
```

---

## Learning Outcomes

- Wazuh SIEM deployment
- Multi-endpoint monitoring
- CVE-based vulnerability management
- Patch validation
- SOC workflow simulation

---

## Author

Santhoshkumar  
SOC / Blue Team Enthusiast

