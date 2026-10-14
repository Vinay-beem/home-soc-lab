# Suspicious PowerShell Activity Detection

## Overview

A suspicious PowerShell execution was simulated on the Windows 10 virtual machine to generate process creation events and test threat detection capabilities within the SOC home lab environment.

The objective of this simulation was to:
- Generate suspicious PowerShell activity
- Monitor process execution events
- Detect encoded PowerShell commands
- Analyze Sysmon logs using Wazuh SIEM
- Investigate generated security alerts


## Attack Scenario

PowerShell is commonly abused by attackers for:
- Remote command execution
- Malware delivery
- Persistence
- Credential dumping
- Defense evasion

This simulation recreates suspicious PowerShell activity in a controlled environment for security monitoring and detection practice.


## Environment

| Machine | Role |
|---|---|
| Windows 10 | Target Endpoint |
| Windows 10 + Wazuh | SIEM Monitoring Server |


## Tools Used

- PowerShell
- Sysmon
- Wazuh SIEM
- Windows 10


## Attack Execution

### Step 1 — Open PowerShell

Opened PowerShell on the Windows 10 virtual machine with administrative privileges.


### Step 2 — Execute Encoded Command

Executed a Base64-encoded PowerShell command:

```powershell
powershell -enc ZQBjAGgAbwAgAHQAZQBzAHQA
```


## What the Command Does

The encoded PowerShell command simulates suspicious activity commonly observed during:
- Malware execution
- Script obfuscation
- Command hiding
- Post-exploitation activity

Encoded PowerShell commands are frequently flagged by security monitoring solutions because attackers often use them to evade detection.


## Detection and Monitoring

Sysmon generated detailed logs related to:
- Process creation
- Parent-child process relationships
- PowerShell execution activity
- Command-line arguments

These logs were forwarded to Wazuh through the Wazuh agent for analysis.


## Wazuh Alerts

Wazuh generated alerts related to:
- Encoded PowerShell execution
- Suspicious command-line activity
- Potential malicious scripting behavior

Alerts were monitored through the Wazuh dashboard.


## Investigation Process

The following information was analyzed:
- Executed command
- Timestamp
- Process name
- Parent process
- Alert severity
- Associated Sysmon logs


## Security Impact

Suspicious PowerShell activity may indicate:
- Malware execution
- Living-off-the-land attacks
- Script-based intrusion activity
- Post-exploitation behavior

Monitoring PowerShell activity is important for detecting advanced threats in enterprise environments.
