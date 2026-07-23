# Tecnomatix & Digital Twin Composer (DTC) — Deep Architecture & Integration Guide

> **Scope:** Assembly Line Realization, Robotic Fastening Kinematics, Ergonomics (RULA Index), Digital Twin Composer (DTC) Production Co-Pilot, NVIDIA Omniverse USD Scenes, and AMR Material Dispatching for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. Tecnomatix & DTC Architecture Overview

The **REALIZE Stage** transforms the verified CAD/FEA digital model into a manufacturable assembly line across physical stations (`LARGE ASSY 2-3` and `FINAL ASSY 1-7`).

```
+---------------------------------------------------------------------------------------------------+
|                                TECNOMATIX & DTC INTEGRATION ARCHITECTURE                          |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [Verified 3D CAD Assembly from Stage 1]                                                          |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 1. Tecnomatix Process Simulate    |  (Simulates robotic arm reachability, rivet tool paths)    |
|  |     Kinematics & Collision Check  |                                                            |
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 2. Ergonomics & RULA Assessment   |  (Evaluates technician reach, posture & fatigue limits)   |
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 3. Digital Twin Composer (DTC)    |  (FastAPI + ReAct Agent over Siemens LLM)                  |
|  |     Production Co-Pilot Execution |  (Monitors station bottlenecks & dispatches AMRs)          |
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 4. NVIDIA Omniverse 3D View       |  (Renders 3D USD plant scene with real-time station KPIs)  |
|  +-----------------------------------+                                                            |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. DTC Production Co-Pilot System Architecture

Rooted in the local codebase implementation (`d:/projectsD/DTComposer`), the Digital Twin Composer (DTC) Co-Pilot provides real-time operator chat, tool calling, and station recovery:

### 2.1 DTC Co-Pilot Backend Architecture (`app/backend/dtc_copilot/`)
- **FastAPI + SSE Stream Engine:** Exposes `POST /api/copilot/run` to stream real-time tool calls (`load_production_line`, `get_station_diagnostics`, `propose_recovery_strategy`).
- **LangGraph ReAct Agent:** Uses Siemens Internal LLM (`deepseek-v4-flash`) for reliable tool calling.
- **Scenario State:** Manages stations `LARGE ASSY 2–3` + `FINAL ASSY 1–7`. Resolves bottleneck at **`FINAL ASSY 1`** (building 2 units/day vs target 7 units/day).

---

## 3. Station Diagnostics & Ergonomics Matrix

| Station ID | Station Name | Primary Task | Target Units/Day | Current Status | Key Bottleneck / Ergonomics Risk |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `1` | `LARGE ASSY 2` | Airframe Main Spar Sub-Assembly | 7 | GREEN | Normal operation (RULA = 1) |
| `2` | `LARGE ASSY 3` | Composite Wing Skin Bonding | 7 | GREEN | Normal operation (RULA = 2) |
| `3` | **`FINAL ASSY 1`** | Avionics & Wiring Harness Installation | 7 | **RED** | **2 units/day (Behind)**; Component mismatch on Bracket B-12 |
| `4` | `FINAL ASSY 2` | Propulsion & Motor Mounting | 7 | YELLOW | AMRs queued waiting for `FINAL ASSY 1` |
| `5` | `FINAL ASSY 3` | Flight Surface Control Rigging | 7 | GREEN | Normal operation (RULA = 2) |

---

## 4. Technical Gaps & Roadmap Solutions

1. **Gap:** Scene synchronization between Tecnomatix assembly changes and NVIDIA Omniverse 3D USD files requires manual export passes.
   - **Solution:** Embed OpenUSD Python scripts (`usd_scene_exporter.py`) within Tecnomatix to stream CAD pose changes live into Omniverse.
2. **Gap:** Operator ergonomics failures (RULA score > 3) do not automatically adjust fixture geometry in CAD.
   - **Solution:** Create an agent hook connecting DTC ergonomics scores directly to NX fixture CAD expressions (`fixture_height_mm`).
