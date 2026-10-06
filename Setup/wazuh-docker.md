# Wazuh Docker Deployment

## Overview

Wazuh was deployed as a **single-node Docker environment** on my SOC server using Docker Desktop with WSL2 integration.

### Environment

| Component | Details |
|---|---|
| Host | PC A |
| OS | Windows |
| Deployment | Docker Desktop + WSL2 |
| Wazuh | 4.7.5 |
| Server IP | `192.168.1.6` |

---

## 1. Verify Docker

Check that Docker and Docker Compose are available:

```powershell
docker --version
docker compose version
```

Docker Desktop must be running before starting the Wazuh containers.

---

## 2. Generate Wazuh Certificates

Wazuh's Docker deployment requires certificates for secure communication between its components.

The certificate generation service was run with:

```bash
docker compose -f generate-indexer-certs.yml run --rm generator
```

---

## 3. Start Wazuh

The Wazuh services were started in detached mode:

```bash
docker compose up -d
```

This runs the containers in the background.

---

## 4. Verify Containers

Check the running services:

```bash
docker compose ps
```

The main Wazuh components are:

- `wazuh.manager`
- `wazuh.indexer`
- `wazuh.dashboard`

---

## 5. Access the Dashboard

The Wazuh Dashboard is exposed through HTTPS.

From the SOC server:

```text
https://localhost
```

From another device on the same network:

```text
https://192.168.1.6
```

---

## 6. Troubleshooting

Useful commands when diagnosing the Wazuh Docker deployment:

```bash
docker compose ps
docker compose logs
docker compose logs -f
```

These help identify container failures, startup problems, and communication issues.

---

## Current Status

| Component | Status |
|---|---|
| Docker Desktop | ✅ Running |
| WSL2 Integration | ✅ Working |
| Wazuh Manager | ✅ Running |
| Wazuh Indexer | ✅ Running |
| Wazuh Dashboard | ✅ Running |

---

## Next Steps

- Connect Windows endpoints
- Collect Sysmon telemetry
- Investigate security alerts
- Build custom detections
- Document SOC investigations
