# Wazuh Windows Agent Setup

## Overview

This document describes how I deployed and configured the **Wazuh Agent** on my Windows 10 endpoint and connected it to the Wazuh Manager running on another machine.

The endpoint is used as the monitored Windows system in my home SOC lab.

---

## Lab Environment

| Component | Details |
|---|---|
| Endpoint | FA_Laptop |
| Operating System | Windows 10 Home Single Language |
| Wazuh Agent | 4.7.5 |
| Wazuh Manager | Wazuh 4.7.5 |
| Wazuh Server IP | `192.168.1.6` |
| Endpoint IP | `192.168.1.21` |
| Agent Communication | TCP `1514` |
| Agent Enrollment | TCP `1515` |

### Architecture

```text
Windows 10 Endpoint
FA_Laptop
192.168.1.21
        |
        | Wazuh Agent
        |
        | TCP 1514
        v
Wazuh Manager
192.168.1.6
        |
        +--> Wazuh Indexer
        |
        +--> Wazuh Dashboard
```

---

## 1. Deploying the Wazuh Agent

The Wazuh Agent was deployed from the **Wazuh Dashboard**.

### Deployment process

1. Open the Wazuh Dashboard.
2. Navigate to:

```text
Agents → Deploy new agent
```

3. Select:

```text
Operating System: Windows
Package: MSI 32/64 bits
```

4. Enter the Wazuh Manager address:

```text
192.168.1.6
```

5. Assign the agent name:

```text
FA_Laptop
```

6. Select the required agent group.

7. Copy the generated PowerShell installation command from the Wazuh Dashboard.

8. Run the generated command in **PowerShell as Administrator** on the Windows endpoint.

The Wazuh Dashboard generates the appropriate installation and enrollment parameters for the Wazuh Manager.

---

## 2. Start the Wazuh Agent

After installation, the Wazuh Agent service was started using:

```powershell
NET START WazuhSvc
```

The service can also be checked with:

```powershell
Get-Service WazuhSvc
```

Expected result:

```text
Status   Name       DisplayName
------   ----       -----------
Running  WazuhSvc   Wazuh
```

---

## 3. Verify Network Connectivity

The endpoint must be able to communicate with the Wazuh Manager.

### Check agent communication port

```powershell
Test-NetConnection 192.168.1.6 -Port 1514
```

Expected:

```text
TcpTestSucceeded : True
```

### Check enrollment port

```powershell
Test-NetConnection 192.168.1.6 -Port 1515
```

Expected:

```text
TcpTestSucceeded : True
```

### Port roles

| Port | Purpose |
|---|---|
| `1514/TCP` | Wazuh Agent → Wazuh Manager communication |
| `1515/TCP` | Wazuh Agent enrollment |

---

## 4. Wazuh Agent Configuration

The main Wazuh Agent configuration file is:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

A backup of the original configuration was created before making changes:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf.backup
```

---

## 5. Sysmon Integration

To collect Sysmon telemetry, the following configuration was added inside the existing `<ossec_config>` section:

```xml
<localfile>
    <location>Microsoft-Windows-Sysmon/Operational</location>
    <log_format>eventchannel</log_format>
</localfile>
```

This tells the Wazuh Agent to monitor the Windows Sysmon Operational event channel.

The resulting telemetry flow is:

```text
Sysmon
   ↓
Windows Event Log
   ↓
Wazuh Agent
   ↓
Wazuh Manager
   ↓
Wazuh Dashboard
```

---

## 6. Restart the Agent After Configuration Changes

After modifying `ossec.conf`, the Wazuh Agent was restarted:

```powershell
Restart-Service WazuhSvc
```

Then verified:

```powershell
Get-Service WazuhSvc
```

---

## 7. Verify Agent Logs

The Wazuh Agent log is located at:

```text
C:\Program Files (x86)\ossec-agent\ossec.log
```

To view the latest entries:

```powershell
Get-Content "C:\Program Files (x86)\ossec-agent\ossec.log" -Tail 30
```

A successful connection produced:

```text
Connected to the server ([192.168.1.6]:1514/tcp)
```

The agent also reported:

```text
Agent is now online.
```

Most importantly, the agent confirmed that it was analyzing the Sysmon event channel:

```text
Analyzing event log: 'Microsoft-Windows-Sysmon/Operational'
```

These messages confirmed that the Windows endpoint was successfully connected to the Wazuh Manager and collecting Sysmon telemetry.

---

## 8. Verify Sysmon Events on Windows

Sysmon events were verified locally using:

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 5
```

The endpoint generated events including:

| Event ID | Description |
|---|---|
| `1` | Process Creation |
| `5` | Process Termination |

Example:

```text
ProviderName: Microsoft-Windows-Sysmon

TimeCreated              Id
-----------              --
05-10-2026 14:39:46       1
05-10-2026 14:39:30       1
05-10-2026 14:39:25       5
```

---

## 9. Verify Events in Wazuh

After successful agent enrollment and Sysmon integration, Sysmon events were visible in the Wazuh Dashboard.

The endpoint used for monitoring is:

```text
FA_Laptop
Agent ID: 003
```

A dashboard search for:

```text
data.win.system.providerName:"Microsoft-Windows-Sysmon"
```

returned Sysmon-related events.

This confirmed the complete telemetry pipeline:

```text
FA_Laptop
    ↓
Sysmon
    ↓
Windows Event Logs
    ↓
Wazuh Agent
    ↓
TCP 1514
    ↓
Wazuh Manager
    ↓
Wazuh Rules
    ↓
Wazuh Dashboard
```

---

## 10. Troubleshooting

### Problem: Agent could not initially connect

The Wazuh Agent initially reported connection errors for ports `1514` and `1515`.

Example:

```text
Unable to connect to '[192.168.1.6]:1514/tcp'
```

and:

```text
Unable to connect to enrollment service at '[192.168.1.6]:1515'
```

### Investigation

Connectivity was tested using:

```powershell
Test-NetConnection 192.168.1.6 -Port 1514
Test-NetConnection 192.168.1.6 -Port 1515
```

Both ports eventually returned:

```text
TcpTestSucceeded : True
```

After connectivity was restored, the Wazuh Agent successfully connected:

```text
Connected to the server ([192.168.1.6]:1514/tcp)
```

and reported:

```text
Agent is now online.
```

### Lesson Learned

When a Wazuh Agent cannot connect to the Manager, verify:

1. Manager IP address
2. Network connectivity
3. TCP port `1514`
4. TCP port `1515`
5. Wazuh Manager availability
6. Windows Firewall / network restrictions
7. Agent log messages

---

## 11. Current Status

| Component | Status |
|---|---|
| Wazuh Manager | ✅ Online |
| Windows Wazuh Agent | ✅ Online |
| Agent Communication | ✅ Working |
| Agent Enrollment | ✅ Working |
| Sysmon | ✅ Installed |
| Sysmon Event Collection | ✅ Working |
| Sysmon → Wazuh Integration | ✅ Working |
| Wazuh Dashboard Detection | ✅ Working |

---

## 12. Next Steps

Planned work includes:

- Investigate Sysmon Event ID 1 process creation events
- Analyze parent-child process relationships
- Investigate PowerShell activity
- Investigate suspicious `cmd.exe` execution
- Map detections to MITRE ATT&CK
- Create custom Wazuh detection rules
- Document incident investigations
- Test additional controlled attack scenarios

---

## References

- [Wazuh Documentation](https://documentation.wazuh.com/)
- [Microsoft Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
