# Capital EWIS & Electrical Co-Design — Deep Architecture & Integration Guide

> **Scope:** Electrical Wiring Interconnect System (EWIS), Capital Logic Schematics, Wire Sizing Algorithms, Capital Harness 3D Spatial Routing, and ECAD/MCAD Cross-Verification for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. Capital Architecture & Electrical Pipeline

Electrical engineering in FlyNow connects 2D circuit logic with 3D physical airframe routing via **Capital Logic**, **Capital Harness Option**, and **https://capital.xcelerator.us/**.

```
+---------------------------------------------------------------------------------------------------+
|                                 CAPITAL EWIS INTEGRATION PIPELINE                                 |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [Power & Signal Requirements from Teamcenter / Polarion]                                         |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 1. Capital Logic Schematics       |  (Synthesizes 2D wiring, connectors, relays, pinouts)     |
|  |     Wire Gauge Sizing             |                                                            |
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 2. Capital Harness 3D Routing     |  (Imports NX 3D spatial keep-in zones & routes bundles)    |
|  |     Bundle Diameter Calculation   |                                                            |
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 3. ECAD / MCAD Verification       |  (Validates bundle bend radii, thermal standoff, & pinout) |
|  |     Teamcenter EDA Co-Design Sync |                                                            |
|  +-----------------------------------+                                                            |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. Automated Wire Sizing & Voltage Drop Rules

The Capital Agent calculates minimum required wire gauges (AWG) based on peak continuous current draw, ambient temperature, and maximum allowable voltage drop ($\le 3.0\%$ of nominal bus voltage).

### 2.1 Wire Sizing Reference Matrix

| Circuit ID | Subsystem | Nominal Voltage | Peak Current | Cable Length | Calculated Minimum AWG | Voltage Drop % |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `CKT-PWR-01` | Main Flight Motors | 48 VDC | 45.0 A | 1.8 m | AWG 8 | 1.42% (PASS) |
| `CKT-AV-04` | Flight Controller Power | 12 VDC | 3.5 A | 0.9 m | AWG 20 | 0.85% (PASS) |
| `CKT-SIG-12` | CAN Bus Telemetry | 5 VDC | 0.2 A | 2.4 m | AWG 24 (Shielded) | 0.31% (PASS) |
| `CKT-PAY-02` | Sensor Payload Power | 24 VDC | 8.0 A | 1.2 m | AWG 16 | 1.15% (PASS) |

---

## 3. Capital Harness 3D Spatial Routing in NX

Once wire gauges are assigned in Capital Logic, the 3D topology is routed through designated NX spatial keep-in pathways:

### 3.1 3D Routing Constraints
1. **Bundle Diameter Expansion:** Total bundle diameter $D_{bundle} = 1.15 \times \sqrt{\sum d_i^2}$.
2. **Minimum Bend Radius:** $R_{bend} \ge 5.0 \times D_{bundle}$. If $R_{bend} < 5.0 \times D_{bundle}$, the agent flags a **Bend Radius Violation** and adjusts spatial control points in NX.
3. **Clamping & Fixture Interval:** Maximum un-clamped harness span $L_{clamp} \le 250.0 \text{ mm}$.

---

## 4. Technical Gaps & Roadmap Solutions

1. **Gap:** Capital API integration requires custom Java/C++ Bridge handlers, creating friction for direct LLM tool calling.
   - **Solution:** Wrap Capital Bridge APIs in a standardized FastMCP server (`AILifecycle/mcp_servers/capital_mcp_server.py`) exposing JSON-RPC tool contracts.
2. **Gap:** Harness routing in dense avionics bays often collides with structural bulkheads.
   - **Solution:** Feed 3D CAD collision meshes from NX MCP directly into Capital Harness Option's pathfinder algorithm to enable real-time obstacle avoidance.
