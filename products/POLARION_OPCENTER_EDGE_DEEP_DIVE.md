# Polarion, Opcenter & Industrial Edge — Deep Architecture & Integration Guide

> **Scope:** Polarion ALM Systems Requirements, Opcenter MOM/MES Shop-Floor Execution, Siemens Industrial Edge IoT Telemetry Ingestion, and Closed-Loop Quality for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. Polarion ALM & Requirements Ingestion

**Polarion ALM** governs system requirements, software specifications, and Verification & Validation (V&V) test cases.

```
+---------------------------------------------------------------------------------------------------+
|                                 POLARION REQUIREMENT SYNC PIPELINE                                |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [RFP PDF Ingestion]  -->  Polarion NLP Parsing Agent                                             |
|                                    |                                                              |
|                                    v                                                              |
|  +---------------------------------------------------------------------------------------------+  |
|  | POLARION WORK ITEM TREES                                                                    |  |
|  | * WorkItem: REQ-SYS-101 (Airframe Structural Margin >= 1.5)                               |  |
|  | * WorkItem: REQ-SYS-102 (Max Electrical Voltage Drop <= 3%)                                |  |
|  | * WorkItem: TEST-SYS-501 (Vibration Spectrum Test)                                        |  |
|  +---------------------------------------------------------------------------------------------+  |
|                                    |                                                              |
|                                    v                                                              |
|  [Teamcenter Requirements Manager]  <-- Synchronized via Teamcenter Polarion Integration (TPI)     |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. Opcenter MOM/MES & Electronic Work Instructions (EWI)

In **Stage 2 (Realize)**, Teamcenter Manufacturing pushes the Manufacturing BOM (MBOM) and process routing to **Opcenter MOM/MES**:

### 2.1 Opcenter Execution Responsibilities
1. **Order Dispatching:** Releases shop-floor work orders (`WO-2026-0041`) to specific assembly stations (`FINAL ASSY 1`).
2. **Electronic Work Instructions (EWI):** Displays step-by-step 3D visual assembly instructions to operators, including required fastener torque specs ($N \cdot m$) and component part numbers.
3. **Data Collection:** Records technician digital signatures, tool torque values, and scan IDs for every installed flight component.

---

## 3. Industrial Edge Telemetry & MindSphere Ingestion

In **Stage 3 (Optimize)**, **Siemens Industrial Edge** devices deployed at assembly stations capture high-frequency sensor streams:

### 3.1 Industrial Edge Sensor Data Pipeline
- **Smart Tool Telemetry:** Ingests digital torque wrench readings over OPC-UA / MQTT (`Topic: plant/line1/station3/torque`).
- **3D Laser Scanners:** Captures 3D point cloud scans of fastener hole locations and skin flushness.
- **Teamcenter Quality Sync:** If laser scan deviates from 3D CAD profile by $> 0.15 \text{ mm}$, Industrial Edge triggers a Non-Conformance (`NC`) item in Teamcenter automatically.

---

## 4. Technical Gaps & Roadmap Solutions

1. **Gap:** Polarion to Teamcenter sync can lag by up to 10 minutes when requirements are edited concurrently.
   - **Solution:** Configure real-time REST webhooks between Polarion and `https://tc.xcelerator.us/`.
2. **Gap:** Industrial Edge sensor telemetry streams produce high-volume raw data ($> 100 \text{ MB/min}$) that can overload PLM.
   - **Solution:** Perform edge analytics filtering on Industrial Edge devices, forwarding only out-of-spec anomaly telemetry to Teamcenter Quality.
