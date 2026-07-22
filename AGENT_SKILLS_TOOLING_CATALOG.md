# Agent Skills & Tooling Catalog — Siemens Xcelerator Portfolio

> **Scope:** Exhaustive catalog of Model Context Protocol (MCP) tools, official Siemens API method signatures (`NXOpen`, `Teamcenter.Soa`, `CapitalBridge`, `Simcenter3D`), parameters, and Human-in-the-Loop (HITL) gate contracts for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. Official Siemens API & MCP Method Catalog

```
+-------------------------------------------------------------------------------------------------------------------+
|                                  SIEMENS XCELERATOR AGENT TOOLING CATALOG                                         |
+-------------------------------------------------------------------------------------------------------------------+
| XCELERATOR DOMAIN | MCP TOOL NAME               | OFFICIAL SIEMENS API METHOD              | PRIMARY FUNCTION       |
+-------------------+-----------------------------+------------------------------------------+------------------------+
| Teamcenter PLM    | `tc_parse_rfp`              | Polarion OSLC REST API                   | Parses RFP to Reqs     |
|                   | `tc_create_item_revision`   | `Teamcenter.Soa.Client.DataManagement`   | Creates Design Items   |
|                   | `tc_submit_change_order`    | `Teamcenter.Soa.Client.Workflow`         | Submits Change Orders  |
+-------------------+-----------------------------+------------------------------------------+------------------------+
| NX CAD            | `nx_expression_set`         | `NXOpen.Part.Expressions.CreateExpression`| Modifies CAD parameters|
|                   | `nx_create_extrude`         | `NXOpen.Features.ExtrudeBuilder`         | Generates solid body   |
|                   | `nx_check_interferences`    | `NXOpen.UF.UFModl.AskInterference`       | 0.0mm clash inspection |
|                   | `nx_synchronous_move_face`  | `NXOpen.Features.MoveFaceBuilder`        | Synchronous face move  |
+-------------------+-----------------------------+------------------------------------------+------------------------+
| Capital EWIS      | `capital_verify_wire_sizing`| `CapitalBridge.CapitalWire.setAttribute` | Calculates wire AWG    |
|                   | `capital_route_harness_3d`  | `NX Electrical Routing / Capital 3D`     | Routes 3D wire bundle  |
+-------------------+-----------------------------+------------------------------------------+------------------------+
| Simcenter CAE     | `simcenter_solve_fea`       | `NXOpen.CAE.SimSolution.Solve` (Nastran) | Solves Sol 101 FEA     |
|                   | `simcenter_defeaturing_retry`| `NXOpen.CAE.DefeatureBuilder`            | Removes micro-fillets  |
+-------------------+-----------------------------+------------------------------------------+------------------------+
| Tecnomatix / DTC  | `dtc_get_station_diagnostics`| Tecnomatix Plant Sim SimTalk 2.0         | Line bottleneck scan   |
|                   | `dtc_propose_recovery`      | DTC ReAct Agent SSE Stream               | AMR dispatch & rebalance|
+-------------------+-----------------------------+------------------------------------------+------------------------+
| Opcenter / Edge   | `edge_ingest_sensor`        | Industrial Edge MQTT / OPC-UA Node       | Ingests torque & scans |
|                   | `opcenter_dispatch_wo`      | Opcenter Execution Discrete OData        | Dispatches shop order  |
+-------------------------------------------------------------------------------------------------------------------+
```

---

## 2. Detailed Method Signatures & Data Contracts

### 2.1 Teamcenter Active Workspace Tools (`https://tc.xcelerator.us/`)

#### `tc_create_item_revision`
- **Official API Binding:** `Teamcenter.Services.Strong.Core._2008_06.DataManagement.CreateItems`
- **Parameters:**
  - `item_id` *(string, required)*: Canonical item ID (e.g. `000892`).
  - `item_name` *(string, required)*: Part description.
  - `item_type` *(string)*: `Design`, `Assembly`, or `ElectricalHarness`.
- **Response Contract:**
  ```json
  {
    "status": "SUCCESS",
    "item_id": "000892",
    "revision_id": "A;1",
    "uid": "w3e89f81a70s_UID",
    "release_status": "IN_WORK"
  }
  ```

---

### 2.2 NX CAD MCP Tools (`griffin-p-quinn/nx-mcp`)

#### `nx_expression_set`
- **Official API Binding:** `NXOpen.Part.Expressions.CreateExpression`
- **Parameters:**
  - `part_name` *(string)*: Active NX model file (e.g. `wing_main_spar.prt`).
  - `expressions` *(object)*: Map of driving dimensions (e.g. `{"wing_span": "1250.0", "spar_web_thickness": "3.2"}`).
- **Response Contract:**
  ```json
  {
    "status": "SUCCESS",
    "part_name": "wing_main_spar.prt",
    "updated_expressions": {"wing_span": "1250.0", "spar_web_thickness": "3.2"},
    "rebuild_success": true,
    "calculated_volume_mm3": 128450.25,
    "calculated_mass_kg": 11.84
  }
  ```

---

### 2.3 Simcenter 3D Nastran Tools

#### `simcenter_solve_fea`
- **Official API Binding:** `NXOpen.CAE.SimSolution.Solve` (Nastran Sol 101 Static)
- **Parameters:**
  - `model_id` *(string)*: Simcenter `.sim` file ID.
  - `solution_type` *(string)*: `SOL101_STATIC` or `SOL106_NONLINEAR`.
- **Response Contract:**
  ```json
  {
    "model_id": "FEM_Main_Spar.sim",
    "solution_type": "SOL101_STATIC",
    "solver_status": "COMPLETED",
    "max_von_mises_stress_mpa": 184.2,
    "material": "Aluminum 7075-T6",
    "yield_strength_mpa": 300.0,
    "calculated_safety_factor": 1.63,
    "verification_status": "PASS"
  }
  ```

---

## 3. Human-in-the-Loop (HITL) Gate Sign-Off Protocol

Every critical automated stage transition in [AgentCenter](https://agentcenter.xcelerator.us/) generates an interactive HITL sign-off request:

```json
{
  "gate_id": "GATE-STAGE2-SIGN-OFF",
  "title": "Structural Safety Factor & Line Realization Gate",
  "agent_results": {
    "simcenter_nastran_stress": "PASS (Von Mises = 184.2 MPa, SF = 1.63 >= 1.50 threshold)",
    "tecnomatix_assembly_sim": "PASS (0 Fastener Robot Collisions)",
    "tecnomatix_jack_rula": "PASS (RULA Score = 2, Low Risk)"
  },
  "required_approver_role": "Chief Aerospace Manufacturing & Structural Engineer",
  "actions": ["APPROVE_AND_PROCEED_TO_STAGE_3", "REJECT_WITH_COMMENTS"]
}
```
