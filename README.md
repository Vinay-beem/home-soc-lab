# 🛡️ Home SOC Lab

A hands-on cybersecurity home lab built to practice **Security Operations Center (SOC)** workflows, endpoint monitoring, threat detection, investigation, and incident response.

This project documents my learning journey as I build and improve a small SOC environment using real security tools.

---

## 🎯 Objectives

- Build a functional home SOC environment
- Collect and analyze endpoint telemetry
- Practice threat detection and investigation
- Understand SIEM workflows
- Work with Windows security events
- Map detections to MITRE ATT&CK
- Create and test custom detection rules
- Practice incident response techniques
- Document real troubleshooting and lessons learned

---

## 🏗️ Current Architecture

```text
                    ┌─────────────────────────┐
                    │       SOC SERVER        │
                    │        PC A             │
                    │                         │
                    │   Wazuh Manager         │
                    │   Wazuh Indexer         │
                    │   Wazuh Dashboard       │
                    └────────────▲────────────┘
                                 │
                              TCP 1514
                                 │
                    ┌────────────┴────────────┐
                    │       ENDPOINT          │
                    │        PC B             │
                    │      Windows 10         │
                    │                         │
                    │   ┌─────────────────┐   │
                    │   │     Sysmon      │   │
                    │   └────────┬────────┘   │
                    │            ↓            │
                    │   Windows Event Logs    │
                    │            ↓            │
                    │   ┌─────────────────┐   │
                    │   │  Wazuh Agent    │   │
                    │   └─────────────────┘   │
                    └─────────────────────────┘
