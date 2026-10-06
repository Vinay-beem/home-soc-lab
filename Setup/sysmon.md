# Sysmon Setup

## Overview

Microsoft Sysmon was installed on the Windows 10 endpoint **FA_Laptop** to provide detailed endpoint telemetry for the SOC lab.

Sysmon records security-relevant activity such as:

- Process creation
- Process termination
- Process hashes
- System activity

These events are collected from Windows Event Logs and forwarded to Wazuh.

---

## 1. Install Sysmon

Sysmon was downloaded from the official Microsoft Sysinternals website and extracted to:

```text
C:\Sysmon
```

PowerShell was opened as **Administrator** and Sysmon was installed with:

```powershell
cd C:\Sysmon
.\Sysmon64.exe -accepteula -i
```

---

## 2. Verify Sysmon

Check the Sysmon service:

```powershell
Get-Service Sysmon*
```

Expected:

```text
Status   Name
------   ----
Running  Sysmon64
```

---

## 3. Verify Sysmon Events

Sysmon events are stored in:

```text
Microsoft-Windows-Sysmon/Operational
```

Check recent events with:

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 5
```

Important events observed:

| Event ID | Description |
|---|---|
| `1` | Process Creation |
| `5` | Process Termination |

---

## 4. Wazuh Integration

The Wazuh Agent was configured to collect the Sysmon Operational event channel:

```xml
<localfile>
    <location>Microsoft-Windows-Sysmon/Operational</location>
    <log_format>eventchannel</log_format>
</localfile>
```

Telemetry flow:

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

## 5. Verification

The Wazuh Agent confirmed that it was analyzing the Sysmon event channel:

```text
Analyzing event log: 'Microsoft-Windows-Sysmon/Operational'
```

Sysmon-related events were also observed in the Wazuh Dashboard.

---

## Current Status

| Component | Status |
|---|---|
| Sysmon | ✅ Installed |
| Sysmon Service | ✅ Running |
| Event Collection | ✅ Working |
| Wazuh Integration | ✅ Working |
| Dashboard Detection | ✅ Working |

---

## References

- [Microsoft Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Wazuh Documentation](https://documentation.wazuh.com/)
