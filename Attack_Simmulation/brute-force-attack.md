# Brute Force Attack Simulation

## Objective
Simulate a brute force login attack from Kali Linux against Windows 10
and detect it using Wazuh SIEM.

## Tools Used
- Hydra (Kali Linux) — attack tool 
- Wazuh SIEM — detection
- Windows 10 — target machine

## Attack Command Used
```bash
hydra -l administrator -P /usr/share/wordlists/Passwords.txt rdp://192.168.x.x
```

## What Happened
- Hydra sent rapid repeated login attempts to Windows 10 over RDP
- Windows logged each failure as Event ID 4625
- Wazuh agent forwarded logs to Wazuh manager
- Wazuh triggered alert after detecting multiple failures in short time

## Wazuh Alert Details
- Rule ID: 5710
- Alert Level: 10 (High)
- Description: Multiple failed login attempts detected
- Source IP: 192.168.x.x (Kali Linux)

## Key Windows Event IDs Triggered
| Event ID | Description |
|----------|-------------|
| 4625 | Failed login attempt |
| 4624 | Successful login (if any) |
| 4740 | Account lockout triggered |


## Lessons Learned
- Default RDP with no lockout policy is easily brute-forced
- Wazuh detects the pattern within seconds
- Account lockout policy should be enforced in production
