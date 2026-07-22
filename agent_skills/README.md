# Agent Skills — Siemens Xcelerator Digital Fabric

Downloadable agent/skill Markdown docs that let an LLM agent work with each product's **integration layer(s)**. Every doc follows: **What it is → Integration layer(s) → Deployment & headless/server architecture** (for desktop apps) → **Agent prompts → Integration points → Sources**. Grounded in the in-repo source of truth [`../AGENTIC_READINESS_VERIFIED.md`](../AGENTIC_READINESS_VERIFIED.md).

Download any doc individually, or grab everything as [`siemens-agent-skills.zip`](siemens-agent-skills.zip). The 16 Teamcenter SOA sub-skills are bundled in [`teamcenter-soa-skills.zip`](teamcenter-soa-skills.zip).

## Manifest

| Doc | Product / Layer | Lifecycle stage(s) | Agentic tier | Desktop → server needed? |
|-----|-----------------|--------------------|--------------|--------------------------|
| [polarion-alm.md](polarion-alm.md) | Polarion ALM | 01 RFP, 02 Requirements/RFLP | 🟢 Native-ready (8) | No (REST v1) |
| [phm-made.md](phm-made.md) | PHM Technology MADe | 03 Reliability/PhSM | 🔴 Hard wall (3) | **Yes — no headless run; RPA handoff** |
| [simcenter-amesim.md](simcenter-amesim.md) | Simcenter Amesim | 04 Systems Sim | 🟡 Scriptable (7) | **Yes — precompiled model + worker pool** |
| [capital.md](capital.md) | Siemens Capital (EWIS) | 05 Electrical/EWIS | 🔴 Hard wall (2) | **Yes — no ECAD authoring API** |
| [nx.md](nx.md) | Siemens NX / NX Open | 06 Mechanical CAD | 🟡 Scriptable (6) | **Yes — NX X Server-User license pool** |
| [simcenter-3d-nastran.md](simcenter-3d-nastran.md) | Simcenter 3D / Nastran | 07 Structural CAE | 🟡 Scriptable (7) | **Partial — solver headless; pre/post desktop** |
| [siemens-eda-questa.md](siemens-eda-questa.md) | Siemens EDA Questa/ModelSim | 08 Embedded/FPGA | 🟢 Native-ready (9, best) | No (CLI/batch) |
| [teamcenter.md](teamcenter.md) | Teamcenter PLM | 09/13 Program, V&V | 🟡 Scriptable (6) | No (server; Pool Manager) |
| [tecnomatix.md](tecnomatix.md) | Tecnomatix Process/Plant Sim | 10 Mfg/Assembly Sim | 🟢 Native COM (8) | **Yes — COM instance pool (Windows)** |
| [opcenter.md](opcenter.md) | Opcenter MES/MOM | 11 Production Execution | 🟢 Native REST (8) | No (REST/OData) |
| [industrial-edge.md](industrial-edge.md) | Industrial Edge + Insights Hub | 12 Shop-floor Telemetry | 🟢 Native (9) | No (edge/REST/MQTT) |
| [mendix.md](mendix.md) | Mendix Digital Fabric | Orchestration | 🟢 Native MCP (9) | No (container-native) |
| [ai-lifecycle-harness.md](ai-lifecycle-harness.md) | AI Lifecycle Harness | 14 Delivery + all | Steward | No (meta-layer) |
| [ai-studio-rapidminer.md](ai-studio-rapidminer.md) | Altair RapidMiner AI Studio | AI & Data Fabric (ML) | 🟡 Scriptable | **Yes — JAR plugin / AI Hub** |
| [graph-neo4j.md](graph-neo4j.md) | Graph / Neo4j | AI & Data Fabric (data) | 🟢 Native | No (Bolt/HTTP) |
| [siemens-genai.md](siemens-genai.md) | Siemens GenAI / Model Catalog | AI & Data Fabric (LLM) | 🟢 Native REST | No (OpenAI-compatible) |
| [digital-twin.md](digital-twin.md) | Digital Twin Composer (DTC) | Digital Factory | 🟢 Native | No (FastAPI) |
| [siemens-ix.md](siemens-ix.md) | Siemens iX Design System | UI platform | 🟢 Native (docs MCP) | No (client library) |

## Bundles
- [siemens-agent-skills.zip](siemens-agent-skills.zip) — all skill `.md` docs + this README.
- [teamcenter-soa-skills.zip](teamcenter-soa-skills.zip) — 16 Teamcenter SOA `SKILL.md` sub-skills (bom_describe, change_flow_orchestration, part_3d_examine, …).

> **Honesty note on desktop apps:** NX, Simcenter Amesim/3D, Tecnomatix, MADe, and Capital are desktop applications. Their docs include a **Deployment & headless/server architecture** section describing exactly what server/license infrastructure is required for agent use — including the two hard walls (Capital ECAD authoring, MADe headless run/results) where no programmatic path exists today.
