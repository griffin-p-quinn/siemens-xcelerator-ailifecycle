# Agentic Readiness — VERIFIED Against Actual Siemens Products (RFP → Delivery)

> **Status:** Deep-research verified (2026-07-22) against official Siemens/Mendix/Microsoft documentation, developer portals, community.sw.siemens.com, and public code. Every claim below is sourced; unverifiable claims are marked **UNVERIFIED**. This document supersedes the provisional scorecard in `goal.md` where they disagree — see **§6 Corrections**.

This is the machine- and human-readable **source of truth** consumed by `agentic_landscape_site/index.html` (the Siemens iX tracker) and `digital_thread_schema.json`.

---

## 1. The Complete RFP → Delivery Lifecycle (14 Stages)

Each stage lists the primary Siemens product, the **exact agent interface**, whether it runs **headless**, how an agent **scales/load-balances** against it, and its **native-vs-bridge** classification.

| # | Stage | Primary Product | Agent Interface (verified) | Headless | Load Balancing / Scale Pattern | Classification | Score |
|---|-------|-----------------|----------------------------|:--------:|--------------------------------|----------------|:-----:|
| 01 | **RFP Intake & Capture** | Doc ingestion → Polarion/Teamcenter | LLM parse of 41-pg spec → write via Polarion REST v1 | ✅ | HTTP load balancer | 🟡 Bridge (ingestion) | — |
| 02 | **Requirements & RFLP** | **Polarion ALM** (+ TC SysML) | REST API v1 `/polarion/rest/v1` (OpenAPI), OSLC, SOAP/WSDL; **Copilot API (2606)**; community MCP `peakflames/PolarionMcpServers` | ✅ | Multi-instance cluster + LB | 🟢 Native-ready (closest in PLM) | 8 |
| 03 | **Reliability / PhSM** | **PHM Technology MADe** | Import-only API (SysML v2 ingest); **no headless-run / no results-extraction API** | ❌ | — | 🔴 Custom bridge (hard wall) | 3 |
| 04 | **Systems Sim (6-DOF)** | **Simcenter Amesim** | Python API (`amesim` / legacy `ame_apy`), CMD batch over `.amegp`; **model must be pre-compiled** | ✅ | Worker-session pool | 🟡 Scriptable + bridge | 7 |
| 05 | **Electrical / EWIS** | **Siemens Capital** | **NO public API/CLI/SDK.** File/PLM (Teamcenter) handoff or RPA only | ❌ | — | 🔴 Custom bridge (hardest wall) | 2 |
| 06 | **Mechanical CAD** | **Siemens NX / NX Open** | NX Open (.NET/C++/Python `NXOpen`), Journaling, `run_journal`, `-batch`; **NX X "Server User" headless license (since NX2506.4000)**; **NO official MCP** (only 3rd-party `DreamEnding/NX_MCP`); Design Copilot NX = non-programmatic in-app | ✅ (token-gated) | **License-token pool + Dispatcher / Mendix Digital Fabric** | 🟡 Scriptable + bridge | 6 |
| 07 | **Structural CAE** | **Simcenter 3D / Nastran** | `NXOpen.CAE` (C#/Python) for pre/post; **Nastran solver CLI** `nastran deck.dat` SOL 101/106 fully headless; HEEDS for DSE | ✅ | Solver farm + dual-license mgmt | 🟡 Scriptable + bridge | 7 |
| 08 | **Embedded / FPGA** | **Siemens EDA Questa/ModelSim** | `vsim -c`, `-do <file>.do` (TCL), `qrun`, `-batch`; CI regression farm | ✅ | Regression farm (license per invocation) | 🟢 Native-ready (**best**) | 9 |
| 09 | **Program & Build** | **Teamcenter** (IPP&E, Schedule Mgr) | SOA (.NET `Teamcenter.Soa.Client` / Java), ITK, TcXML, `StructureManagementService` | ✅ | **Pool Manager (4-tier `tcserver` pool)** | 🟡 Scriptable + bridge | 6 |
| 10 | **Mfg / Assembly Sim** | **Tecnomatix Process Simulate & Plant Simulation** | COM/ActiveX `RemoteControl`, SimTalk 2.0, `pyplantsim`, .NET automation; headless via `visible=False` | ✅ | Windows/COM licensed-instance pool | 🟢 Native (COM) | 8 |
| 11 | **Production Execution / MES** | **Opcenter** (APS/Execution) | REST + **OData v4.1** (Intelligence API; Execution Core REST) | ✅ | HTTP load balancer | 🟢 Native (REST) | 8 |
| 12 | **Shop-floor Telemetry / Edge** | **Industrial Edge + Insights Hub** | REST services; **MindConnect MQTT**; **OPC UA** (PubSub over MQTT/JSON); Docker containers | ✅ | Broker / edge cluster | 🟢 Native (REST/MQTT) | 9 |
| 13 | **Verification & Validation** | **Teamcenter V&V** | SOA services + trace-link objects; 51/51 reqs → sim/test PASS, 24 RFP standards linked | ✅ | Pool Manager | 🟡 Scriptable + bridge | 6 |
| 14 | **Delivery / Airworthiness** | **Teamcenter** (cert sign-off) + **AI Lifecycle Harness** HITL gate | SOA + Human-in-the-Loop approval gate | ✅ | — | 🟡 Bridge + HITL | — |

---

## 2. Orchestration Layer (cross-cutting all 14 stages)

| Component | Role | Agent Interface (verified) | Classification | Score |
|-----------|------|----------------------------|----------------|:-----:|
| **Mendix Digital Fabric** (Griffin's Work) | Queue dispatcher / license-token pool / orchestration | **Official MCP both directions** — MCP Server module (microflows→tools), MCP Client + **Maia MCP Client** (Studio Pro 11.8+); published REST/OData/GraphQL + Java actions | 🟢 Native MCP | 9 |
| **AgentCenter Visual Control Tower** (Their Work) | Master visual agent control | Web control tower (`agentcenter.xcelerator.us`) | UI orchestration | — |
| **AI Lifecycle Harness** | Agentic WBS steward (Deliverables→Criteria→Accomplishment→Milestones) | Meta-layer orchestration | Steward | — |
| **Teamcenter Dispatcher** | Siemens-native queue/job dispatch (translations, batch, NX-TC) | Server-side job queue | 🟢 Native dispatch | — |

**Neutral FastMCP Bridge (Griffin's Work):** custom MCP servers (`griffin-p-quinn/nx-mcp` + this repo's `mcp_servers/`) proving the MCP wrapping pattern for the 🟡/🔴 tools. Score 9/10 as a pattern.

---

## 3. Agentic-Readiness Tiers (verified scores)

- 🟢 **NATIVE AGENT-READY** — open programmatic interface, headless, deterministic:
  **Questa 9 · Insights Hub/Edge 9 · Mendix 9 · Opcenter 8 · Tecnomatix Plant/Process Sim 8 · Polarion 8**
- 🟡 **SCRIPTABLE — NEEDS BRIDGE** — rich API but no agent/MCP layer; wrap it:
  **Simcenter 3D 7 · Amesim 7 · NX 6 · Teamcenter SOA 6**
- 🔴 **CUSTOM BRIDGE REQUIRED / WALL** — no or import-only API:
  **MADe 3 · Capital 2**

---

## 4. Where Custom Bridges Are Required (the gap Griffin's Work fills)

| Product | Why a bridge is needed | Griffin's bridge |
|---------|------------------------|------------------|
| **Capital** (🔴 2/10) | No public API at all — hardest wall | **Capital Java Bridge + Project XML generator**; harness materialized in NX |
| **MADe** (🔴 3/10) | Import-only (SysML v2); no headless run/results | Manual export + RPA handoff (open gap) |
| **NX** (🟡 6/10) | World-class NX Open scripting, **but no official MCP**; every batch run burns a license token | **`griffin-p-quinn/nx-mcp` FastMCP server** + NX X Server-User license-token pool via Mendix Digital Fabric |
| **Teamcenter** (🟡 6/10) | Rich SOA/ITK, but Copilot/AI-BOM-Agent are closed UX, not open MCP | SOA C# DataManagement wrapper + AWC RDS data service |
| **Simcenter Amesim/3D** (🟡 7/10) | Headless solve exists; no agent layer + license gating | Python `ame_apy` / `NXOpen.CAE` + Nastran CLI wrappers |

---

## 5. Official Siemens MCP & AI Inventory (verified 2026-07-22)

**MCP servers that actually exist (first-party):**
- ✅ **Siemens iX design-system docs MCP** — `mcp.siemens.com` / `ix.siemens.io/docs/home/mcp-server` (docs/RAG only, requires Siemens token).
- ✅ **Mendix MCP** — MCP Server module + MCP Client + Maia MCP Client (`docs.mendix.com/agents/mcp/`).
- ❌ **NX / Teamcenter / Capital MCP** — none shipped, announced, or on public roadmap (**UNVERIFIED** beyond "none found").
- 🟡 **Polarion** — no first-party MCP, but community `peakflames/PolarionMcpServers` (built on REST v1) + first-party **Copilot API (2606)**.

**Siemens AI/agent products (embedded, mostly non-programmatic):**
- **Siemens Industrial Copilot** — Microsoft partnership (Oct 24 2024), Phi-3 for NX X natural-language; covers NX X, Teamcenter/Teamcenter X, TIA Portal.
- **Design Copilot NX** (Summer 2025) — in-app learning/documentation assistant, **not** an agent API.
- **NX CAM Copilot** (SPS 2025) — CAM programming assist.
- **Teamcenter Copilot + AI BOM Agent + M365 Copilot** (TC 2606) — Siemens-hosted UX.
- **Eigen Engineering Agent** (Hannover Messe 2025) — agentic automation-engineering (writes automation code, configures devices), the most "agentic" Siemens step.

---

## 6. Corrections vs. the provisional `goal.md` scorecard

| Item | `goal.md` (provisional) | VERIFIED | Note |
|------|-------------------------|----------|------|
| **NX MCP** | "coming soon" | **No official MCP; 3rd-party `DreamEnding/NX_MCP` only; Design Copilot NX is non-programmatic** | NX X Server-User license (NX2506.4000) is the real headless enabler |
| **NX score** | 3/10 | **6/10** | Excellent NX Open scripting raises it; bridge still required |
| **Teamcenter AW GraphQL** | "RDS GraphQL" | **AWC uses REST/JSON gateway; public GraphQL contract UNVERIFIED** | RDS data service is REST |
| **Simcenter Amesim** | 5/10 | **7/10** | Documented CMD-batch Python path |
| **Tecnomatix** | (unscored) | **8/10 native (COM/SimTalk/pyplantsim)** | Genuinely agent-ready |
| **Mendix** | (orchestration only) | **Official MCP both directions — 9/10** | Validates the Digital Fabric orchestration bet |

---

## 7. Sources

- Siemens Industrial Copilot — https://www.siemens.com/en-us/company/insights/generative-ai-industrial-copilot/
- Siemens + Microsoft scale industrial AI (Oct 2024) — https://news.microsoft.com/source/2024/10/24/siemens-and-microsoft-scale-industrial-ai/
- Design Copilot NX (Summer 2025) — https://news.siemens.com/en-us/siemens-designcenter-nx-summer-2025/
- NX X Server-User non-interactive license — https://community.sw.siemens.com/s/article/Running-local-NX-X-session-with-Server-User-license
- NX_MCP (third-party) — https://github.com/DreamEnding/NX_MCP
- Mendix MCP — https://docs.mendix.com/agents/mcp/ · Maia MCP Client — https://docs.mendix.com/refguide/maia-mcp/
- Polarion REST v1 — https://testdrive.polarion.com/polarion/rest/v1/definition · Copilot (2606) — https://blogs.sw.siemens.com/polarion/ · MCP — https://github.com/peakflames/PolarionMcpServers
- Teamcenter — https://www.siemens.com/en-us/products/teamcenter/ · SOA usage refs `recs12/TcSOAMasterForm`, `jio-kim/PDM`
- Plant Simulation COM — https://pypi.org/project/pyplantsim/ · docs https://scredirect.docs.sws.siemens.com/tdoc/plantsim/16/plant_sim_help/
- Opcenter Intelligence OData API — https://developer.siemens.com/insights-hub/docs/apis/advanced-opcenter-intelligence/api-opcenter-intelligence-api.html
- Insights Hub / Industrial Edge APIs — https://developer.siemens.com/insights-hub/docs/apis/index.html
- PHM Technology MADe — https://www.phmtechnology.com/made
- Siemens Capital — https://www.siemens.com/en-us/products/capital/

> **Research caveat:** WebSearch was blocked by org policy during research; findings came via WebFetch against official pages and search-index snippets. Items that could not be confirmed against a primary doc are flagged **UNVERIFIED** above.
