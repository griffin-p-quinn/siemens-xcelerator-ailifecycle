# Siemens Industrial Edge & Insights Hub — Agent Skill

## What It Is
Siemens Industrial Edge (IE) deploys containerized apps directly on shop-floor edge devices (PLCs, panels, gateways); Insights Hub is the connected IIoT/analytics cloud. Together they cover shop-floor telemetry and edge compute.

**Agentic-readiness: 🟢 Native (REST/MQTT) — 9/10.**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| Insights Hub REST | HTTP | Asset/timeseries/event APIs |
| **MindConnect MQTT** | MQTT | Device → cloud ingestion |
| **OPC UA** | PubSub over MQTT/JSON | PLC connectivity standard |
| IE Databus | Internal MQTT broker | On-device pub/sub |
| Docker | Containers | Edge apps (OPC UA Connector, Databus, IIH Essentials) |
| IEM | Cloud portal | Fleet deployment/management |

### Architecture
```
PLC (S7-1500) --OPC UA--> IE OPC UA Connector --MQTT--> IE Databus --> Mendix Edge App / IIH --> Insights Hub
```

---

## Deployment & Server Architecture
- Already headless/edge-native. Scale pattern: **broker / edge cluster**; agents subscribe to MQTT topics or call Insights Hub REST.
- Griffin's stack: `Mendix_On_Edge` (bottling-line dashboard) + `HyperV` provisioning for IEVD/IEM VMs.

---

## Agent Prompts
```
"Subscribe to the IE Databus MQTT topics for the bottling line and summarize live state"
"Query Insights Hub timeseries REST for asset X over the last 24h"
"Add a new PLC tag to the OPC UA Connector config"
"Provision an IEVD VM using the Hyper-V scripts"
```

---

## Integration Points
- **Mendix:** runtime containerized as an IE app.
- **OPC UA:** PLC tag/node config.
- **Opcenter/MES:** shop-floor execution context.

---

## Sources
- Insights Hub / Industrial Edge APIs — https://developer.siemens.com/insights-hub/docs/apis/index.html
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 12.
