# Nmap Port Scan Detection

## Overview

An Nmap port scan was performed from the Kali Linux virtual machine against the Windows 10 endpoint to simulate reconnaissance activity within the SOC home lab.

The objective of this simulation was to:
- Generate network reconnaissance traffic
- Monitor endpoint activity
- Detect suspicious behavior using Wazuh
- Analyze security alerts in the SIEM dashboard



## Attack Scenario

In real-world cyber attacks, attackers commonly perform port scanning during the reconnaissance phase to identify:
- Open ports
- Running services
- Potential vulnerabilities
- Accessible systems

This simulation recreates that activity in a controlled lab environment.



## Environment

| Machine | Role |
|---|---|
| Kali Linux | Attacker |
| Windows 10 | Target Endpoint |
| Windows 10 Server + Wazuh | SIEM Monitoring Server |



## Tools Used

- Nmap
- Kali Linux
- Wazuh SIEM
- Windows 10
- Sysmon



## Attack Execution

### Step 1 — Identify Target IP

Verified the Windows 10 virtual machine IP address.

Example: 192.168.x.x


### Step 2 — Perform Nmap Scan

Executed the following command from the Kali Linux machine:

```bash
nmap -sS <target-ip>
```

Example:

```bash
nmap -sS 192.168.x.x
```


## What the Scan Does

The SYN scan (`-sS`) attempts to identify open ports on the target system by sending TCP SYN packets.

This technique is commonly used for:
- Reconnaissance
- Network enumeration
- Service discovery



## Detection and Monitoring

The Windows endpoint generated logs related to:
- Incoming connection attempts
- Network activity
- Suspicious scanning behavior

These logs were forwarded to Wazuh through the Wazuh agent and analyzed by the SIEM platform.



## Wazuh Alerts

Wazuh generated alerts related to:
- Network scanning activity
- Suspicious connection attempts
- Reconnaissance behavior

The alerts were visible in the Wazuh dashboard.



## Investigation Process

The following information was analyzed:
- Source IP address
- Target endpoint
- Timestamp
- Alert severity
- Associated logs



## Security Impact

Port scanning is an important indicator of early-stage attacker activity and can help security analysts identify:
- Reconnaissance attempts
- Unauthorized scanning
- Potential intrusions
