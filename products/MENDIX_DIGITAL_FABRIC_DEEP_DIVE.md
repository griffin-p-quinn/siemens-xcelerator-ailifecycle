# Mendix Low-Code & Digital Fabric — Deep Architecture & Integration Guide

> **Scope:** Low-Code Application Platform, Mendix + NX Digital Fabric (Martin Roy Scalable Architecture), Worker Queue Dispatcher, Headless Licensing Pools, and Edge Deployment for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. Mendix Digital Fabric Architecture Overview

Based on the **Mendix + NX Architecture Reference — Digital Fabric** (Martin Roy architecture pattern), Mendix serves as the enterprise orchestration layer that connects user requests, web agents, and heavy CAD/ECAD worker pools.

```
+---------------------------------------------------------------------------------------------------+
|                                 MENDIX DIGITAL FABRIC ARCHITECTURE                                |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [AgentCenter / User Request]  -->  https://agentcenter.xcelerator.us/                             |
|                                            |                                                      |
|                                            v                                                      |
|  +---------------------------------------------------------------------------------------------+  |
|  | MENDIX ENTERPRISE DIGITAL FABRIC APP                                                        |  |
|  | * Request Ingestion REST/GraphQL Endpoints                                                  |  |
|  | * Asynchronous Work Item Queue & Task Dispatcher                                            |  |
|  | * State Machine & Teamcenter OAuth Token Management                                         |  |
|  +---------------------------------------------------------------------------------------------+  |
|                                            |                                                      |
|         +----------------------------------+----------------------------------+                   |
|         |                                  |                                  |                   |
|         v                                  v                                  v                   |
|  +-----------------------+      +-----------------------+      +-----------------------+          |
|  | NX CAD WORKER POOL    |      | CAPITAL WORKER POOL   |      | SIMCENTER WORKER POOL |          |
|  | * Worker 1 (Headless) |      | * Worker 1 (Headless) |      | * Worker 1 (Headless) |          |
|  | * Worker 2 (Headless) |      | * Worker 2 (Headless) |      | * Worker 2 (Headless) |          |
|  | * Worker N (Headless) |      | * Worker N (Headless) |      | * Worker N (Headless) |          |
|  +-----------------------+      +-----------------------+      +-----------------------+          |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. Key Digital Fabric Components

1. **REST & GraphQL Ingestion Gateway:** Accepts high-level engineering payloads from `AgentCenter` or Chris's web endpoints (`nx.xcelerator.us`, `capital.xcelerator.us`, `tc.xcelerator.us`).
2. **Asynchronous Job Dispatcher:** Prevents HTTP gateway timeouts by converting synchronous requests into background job IDs (`JOB-2026-9812`).
3. **Headless Worker Pool Manager:** Pre-warms and reuses background NX Open and Capital execution sessions, avoiding cold-start overhead (saving 15–25 seconds per CAD invocation).

---

## 3. Mendix Data Model Schema for Job Queue

```
+---------------------------------------------------------------------------------------------------+
|                                 MENDIX DOMAIN MODEL ENTITIES                                      |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [Entity: EngineeringJobRequest]                                                                  |
|    |-- JobID (String, Unique)                                                                     |
|    |-- DomainType (Enum: NX_CAD, CAPITAL_EWIS, SIMCENTER_FEA)                                     |
|    |-- Priority (Integer)                                                                         |
|    |-- Status (Enum: QUEUED, IN_PROGRESS, COMPLETED, FAILED)                                      |
|    |-- PayloadJSON (String, Unlimited)                                                            |
|    |-- WorkerID (String)                                                                          |
|    |-- ExecutionTimeMs (Long)                                                                     |
|    |-- ResultArtifactURL (String)                                                                 |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 4. Executable Reference Runner

The dispatcher, license-token pool, and headless worker reuse described above are implemented and
verified in **`workflows/mendix_digital_fabric_dispatcher.py`**. It genuinely imports and dispatches
against the FastMCP server reference implementations in `mcp_servers/` (`NXMcpServer`,
`CapitalMcpServer`, `SimcenterMcpServer`) — not stubs.

- **`LicenseTokenPool`** — bounded token capacity per domain (the scarce headless CAD license), with backpressure accounting.
- **`HeadlessWorker`** — pre-warmed session; each reuse avoids a representative 18,000 ms cold GUI spawn.
- **`DigitalFabricDispatcher`** — REST/GraphQL-style ingestion → priority `heapq` queue → token-gated dispatch → result artifacts.

Verified run (PowerShell, Python 3.13.5, exit 0): 7 jobs dispatched in strict priority order
(9→8→7→6→5→5→4), all `COMPLETED`, **126,000 ms cold-start avoided** by warm pooling, clean JSON stdout.

---

## 5. Technical Gaps & Roadmap Solutions

1. **Gap:** Standard Mendix apps require custom UI widgets to render 3D CAD visualization inline.
   - **Solution:** Embed PLM Vis Web (Siemens 3D Web Viewer) widgets directly into Mendix pages to render step-by-step CAD snapshots.
2. **Gap:** Edge deployment to shop-floor devices requires offline capability.
   - **Solution:** Deploy Mendix on Edge containers using Docker to process local station telemetry when WAN connectivity to Teamcenter is degraded.
