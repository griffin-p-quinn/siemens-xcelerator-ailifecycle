# Siemens Xcelerator Portfolio — Ground-Truth Product Taxonomy & Agentic-Readiness Scorecard

> **Authority Document:** Ground-truth analysis of the Siemens Digital Industries Software portfolio, exact product taxonomy, API surfaces, data models, licensing constraints, and the **Agentic-Readiness Scorecard (0–10)** derived directly from the **FlyNow QX-250 Quadcopter Digital Thread** (`https://flynow.xcelerator.us/`).

---

## 1. Ground-Truth QX-250 Quadcopter Digital Thread Architecture

The **FlyNow Digital Thread** demonstrates the complete end-to-end engineering of the **QX-250 Quadcopter** — a 5-inch freestyle-class quadcopter (500g design point, ~650g as-built with dual-battery redundancy, 51 SRS requirements, 53-part NX assembly) carried from a **U.S. Air Force solicitation RFP** to a **flight-verified design** inside live Siemens Xcelerator tools.

```
+-------------------------------------------------------------------------------------------------------------------+
|                                 QX-250 DIGITAL THREAD FLOW & TOOL CHAIN                                           |
+-------------------------------------------------------------------------------------------------------------------+
| DOMAIN / STAGE      | OFFICIAL SIEMENS & PARTNER TOOL      | CORE ARTIFACTS & DATA MODELS | OFFICIAL API & BINDING     |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| Customer Origin     | USAF Solicitation RFP (41-page spec) | System Specifications          | Unstructured RFP PDF          |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| System & Reqs (ALM) | Polarion ALM & Teamcenter RFLP       | 51 SRS Requirements, 13 Funcs, | OSLC REST API, Teamcenter SOA |
|                     |                                      | 13-Block Logical Architecture  | C# DataManagementService      |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| PhSM & Reliability  | PHM Technology MADe                  | FMECA, Fault Trees, RBD,       | GUI / PDF Reports             |
|                     |                                      | 24 Order-1 Cut Sets            | (Custom Agent Bridge Needed)  |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| 6-DOF Performance   | Simcenter Amesim                     | 6-DOF Flight Dynamic Model,    | Python Scripting API          |
|                     |                                      | Cascade Flight Controller      | (`ame_apy`)                   |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| Mechanical CAD      | Siemens NX CAD                       | 53-Part Parametric Assembly,   | NX Open (C#, Python),         |
|                     |                                      | Single Datum Frame             | `griffin-p-quinn/nx-mcp`      |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| Electrical EWIS     | Siemens Capital Suite                | Capital Logic Schematics,      | Capital Bridge API (Java),    |
|                     |                                      | Capital Harness 3D in NX       | Project XML Generator         |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| FPGA Compute (RTL)  | Siemens EDA Questa                   | 8 SystemVerilog RTL Blocks,    | Batch TCL CLI Scripting       |
|                     |                                      | Self-Checking Testbenches      | (`vsim -c` + `.do` scripts)   |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| Onboard Software    | PX4 Autopilot SITL                   | Software Home for RFLP Funcs,  | PX4 MAVLink / C++ SITL        |
|                     |                                      | Rate-Damping Control Laws      |                               |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| Program & Build     | Teamcenter IPP&E / Schedule Mgr      | 12-Task Schedule, 22-Item EBOM,| Teamcenter SOA / Active       |
|                     |                                      | Stage MBOM, 10-Op Process Plan | Workspace Data Service        |
+---------------------+--------------------------------------+--------------------------------+-------------------------------+
| Verification (V&V)  | Teamcenter V&V Engine                | 51/51 Target->Measured PASS,   | Teamcenter SOA Verification   |
|                     |                                      | 24 Section-Tied RFP Standards  | Management                    |
+-------------------------------------------------------------------------------------------------------------------+
```

---

## 2. Ground-Truth Agentic-Readiness Scorecard (0–10 Bar)

Each tool in the Siemens suite is scored against **four strict agentic criteria**:
1. **Headless:** Available by API/CLI with no screen at full functional parity.
2. **MCP / Agent-Native:** Discovers and calls capabilities via typed Model Context Protocol contracts.
3. **Agent-Legible UX:** Predictable DOM, stable under browser automation, no renderer freezes.
4. **Orchestratable:** Driven by a structured Work Breakdown Structure (WBS: Deliverable -> Criteria -> Accomplishment -> Milestone).

```
+-------------------------------------------------------------------------------------------------------------------+
|                                 SIEMENS AGENTIC-READINESS SCORECARD                                              |
+-------------------------------------------------------------------------------------------------------------------+
| TOOL / SUBSYSTEM    | HEADLESS | MCP-NATIVE | AGENT-LEGIBLE UX | ORCHESTRATABLE | SCORE | CRITICAL FINDINGS         |
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Siemens EDA Questa  |    YES   |     NO     |       N/A        |      YES       |  7/10 | Batch `vsim -c` + `.do`   |
|                     |          |            |                  |                |       | scripts run headlessly.   |
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Teamcenter SOA      |    YES   |     NO     |       N/A        |    PARTIAL     |  6/10 | SOA C# API is robust but  |
|                     |          |            |                  |                |       | requires BMIDE types.     |
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Simcenter Amesim    |    YES   |     NO     |       N/A        |    PARTIAL     |  5/10 | `ame_apy` runs 6-DOF, but |
|                     |          |            |                  |                |       | linker requires hand-fix. |
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Capital EWIS        | PARTIAL  |  CUSTOM*   |     PARTIAL      |    PARTIAL     |  4/10 | No native headless API;   |
|                     |          |            |                  |                |       | Griffin built Java bridge.|
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Siemens NX CAD      | PARTIAL  |  CUSTOM*   |        NO        |       NO       |  3/10 | NX Open batch exists;     |
|                     |          |            |                  |                |       | `nx-mcp` built by Griffin.|
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Teamcenter AW UI    |    NO    |     NO     |        NO        |    PARTIAL     |  3/10 | AW Web UI freezes under   |
|                     |          |            |                  |                |       | CDP automation (30s timeout)|
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| PHM MADe            |    NO    |     NO     |     PARTIAL      |       NO       |  2/10 | GUI-only, PDF exports only|
|                     |          |            |                  |                |       | No headless path at all.  |
+---------------------+----------+------------+------------------+----------------+-------+---------------------------+
| Griffin Neutral MCP |    YES   |    YES     |       N/A        |      YES       |  9/10 | Custom FastMCP bridge    |
| (griffin-p-quinn)   |          |            |                  |                |       | (Proof of MCP Pattern).   |
+-------------------------------------------------------------------------------------------------------------------+
* CUSTOM: Bridges built by Griffin Quinn (`griffin-p-quinn/nx-mcp`) represent evidence of product gaps.
```

---

## 3. Product-by-Product Analysis

### 3.1 PHM Technology MADe (Reliability & Physics-of-Failure)
- **Role in Digital Thread:** "Simulate before you cut metal." Runs FMECA, fault tree analysis, and Reliability Block Diagrams (RBD).
- **Key Finding:** MADe identified **24 order-1 cut sets** (single points of failure where any single fault drops the aircraft). This discovery forced the addition of a **2nd battery, 2nd flight controller, and BMS/OR-ing block** onto the QX-250 electronics before freezing mechanical CAD!
- **Agentic Gap:** 0% Headless API (GUI-only, PDF exports only). Rated 2/10.

### 3.2 Simcenter Amesim (6-DOF Flight Dynamics)
- **Role in Digital Thread:** 6-DOF performance simulation with cascade flight controller.
- **Key Finding:** Revealed attitude loop recovery ~0.86s, lightly damped ($\zeta \approx 0.15$). Proved that shrinking quadcopter arm length without re-tuning flight software destabilizes hover (roll diverges to 67°).
- **Agentic Gap:** `ame_apy` runs headlessly, but model compilation requires manual linker fixes. Rated 5/10.

### 3.3 Siemens NX CAD & `griffin-p-quinn/nx-mcp`
- **Role in Digital Thread:** Physical layer. Assembles 53 quadcopter components on one parametric datum in NX.
- **Key Finding:** Native NX Open C#/Python APIs exist, but lack official MCP servers. Griffin Quinn built `griffin-p-quinn/nx-mcp` to expose headless STEP/JT export, parametric expression updates, and shaded renders via FastMCP. Rated 3/10 out-of-the-box (9/10 with `nx-mcp`).

### 3.4 Siemens Capital EWIS & AI Architect
- **Role in Digital Thread:** Functional electrical schematics and 3D harness routing in NX CAD.
- **Key Finding:** AI-authored bundle materialized 13 components -> 13 devices with zero manual placement. Griffin built a custom Java Bridge & project XML generator to automate Capital Logic. Rated 4/10 out-of-the-box.

### 3.5 Teamcenter RFLP, IPP&E & V&V System of Record
- **Role in Digital Thread:** Single Source of Truth (SSOT). Holds 51 requirements, 13 functions, 13 logical blocks, 22 EBOM items, stage MBOM, 10-op process plan, and 51/51 PASS verification matrix.
- **Key Finding:** Teamcenter SOA C# API (`Teamcenter.Soa.Client`) is robust for backend headless sync (6/10), but Active Workspace Web UI freezes under browser automation (3/10).

### 3.6 Siemens EDA Questa (FPGA Compute Verification)
- **Role in Digital Thread:** Hardware timing and safety layer. 8 SystemVerilog RTL blocks with self-checking testbenches.
- **Key Finding:** Highly scriptable via batch TCL CLI (`vsim -c` + `.do` scripts). Rated 7/10 (highest native score in the suite).
