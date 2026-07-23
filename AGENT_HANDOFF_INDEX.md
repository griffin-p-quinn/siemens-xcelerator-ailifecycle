# Siemens Xcelerator AI Lifecycle — Agent Handoff & Architecture Index
**Document Version:** 1.1.0  
**Target Codebase:** `griffin-p-quinn/siemens-xcelerator-ailifecycle`  
**Deployment URL:** `https://griffin-p-quinn.github.io/siemens-xcelerator-ailifecycle/`  
**Purpose:** Universal technical handoff specification for AI agents and human developers. Use this index to understand the site's underlying theory, CI/CD deployment pipeline, system architecture, data structures, lookup tables, and modification procedures.

---

## 1. Theory & Architectural Philosophy of the Site

### 1.1 Core Mission & Purpose
The **Siemens Xcelerator AI Lifecycle Agentic Readiness Tracker** was created to answer a fundamental industry question:  
> *"Can a single autonomous AI agent drive an entire aerospace & defense enterprise product lifecycle — from RFP intake to airworthiness delivery — across the Siemens Xcelerator software portfolio?"*

Using the **FlyNow QX-250 Quadcopter** (a 41-page USAF solicitation RFP, 51 trace-linked requirements, 650g mass, 6-DOF Amesim flight dynamics) as the ground-truth benchmark, this site systematically audits, measures, and visualizes the programmatic interface readiness of **14 Siemens Digital Industries Software products**.

### 1.2 The Dual-Track Theory: Griffin's Work vs. Chris's Work
The site is built around a comparative dual-track framework:
- **Griffin's Work (Local / Workspace Implementation):** Focused on native **FastMCP JSON-RPC servers** (`griffin-p-quinn/nx-mcp`), the **Mendix Digital Fabric Queue Dispatcher** (`workflows/mendix_digital_fabric_dispatcher.py`) which solves the critical **headless CAD license token bottleneck**, the **Digital Twin Composer (DTC) Production Co-Pilot** (`d:\projectsD\DTComposer`), and pluggable Mendix 3D widgets.
- **Their Work / Chris's Work (`xcelerator.us` Cloud Endpoints):** Focused on cloud REST endpoints (`https://agentcenter.xcelerator.us/`, `nx.xcelerator.us`, `capital.xcelerator.us`, `tc.xcelerator.us`) and static web demonstrators (`flynow.xcelerator.us`).

### 1.3 Zero-Build, Pure Static Design Philosophy
To ensure total reliability, instant loading, and zero deployment friction, the site strictly follows a **Zero-Build, Pure Static Architecture**:
- **No Node.js / NPM Build Step:** No Webpack, Vite, Rollup, or Babel compilation. The code runs as raw native HTML5, CSS3, and ES Modules.
- **Self-Hosted Siemens IX Design System:** All Siemens IX CSS (`assets/siemens-ix/siemens-ix.css`), JS module runtimes (`assets/siemens-ix/siemens-ix.esm.js`), and icons (`assets/ix-icons/ix-icons.esm.js`) are self-hosted in `assets/`. This eliminates third-party CDN dependencies and subresource integrity failures.
- **Lazy-Loaded Visualization Engine:** Apache ECharts (`assets/echarts.min.js`) is dynamically loaded on demand (`ensureECharts()`) only when the user opens View 4 (Scorecard), preserving initial load performance.
- **Dual Compatibility:** Runs identically when served from a web server (`https://...` or `http://localhost`) or opened directly from disk (`file:///...`).

### 1.4 The 3 Agentic Readiness Tiers
Every product in the Siemens portfolio is classified into one of three readiness tiers:
1. 🟢 **Native Agent-Ready (Scores 8–10):** Open programmatic interfaces, headless batch CLI execution, deterministic JSON/OData endpoints (e.g., Mendix, Industrial Edge, EDA Questa, Opcenter, Tecnomatix, Polarion).
2. 🟡 **Bridge-Required (Scores 4–7):** Rich API suites exist (C#, Java, Python `ame_apy`, C++ NX Open), but lack native OpenMCP wrappers. Requires Griffin's custom FastMCP bridges (e.g., Simcenter 3D, Amesim, Teamcenter SOA, NX CAD, Capital EWIS).
3. 🔴 **Hard Wall (Scores 1–3):** GUI-only, PDF/file-export-only interfaces with 0% headless execution path. Complete barrier for autonomous agents (e.g., PHM Technology MADe).

---

## 2. CI/CD & GitHub Pages Deployment Pipeline

### 2.1 Hosting Architecture
The site is hosted on **GitHub Pages** under the origin:
`https://griffin-p-quinn.github.io/siemens-xcelerator-ailifecycle/`

```
 [ Local Workspace ]                      [ GitHub Repository ]                  [ GitHub Pages CDN ]
 d:\projectsD\AILifecycle  --- git push --> griffin-p-quinn/            --- auto-deploy --> https://griffin-p-quinn.
 index.html, assets/, etc.                 siemens-xcelerator-ailifecycle          github.io/siemens-xcel...
```

### 2.2 Branch & Directory Deployment Rules
- **Source Branch:** The primary default repository branch (`main` / `master`).
- **Publishing Source:** Root directory (`/`) of the repository.
- **Static Assets:** GitHub Pages serves `index.html` as the default root document and `DIGITAL_THREAD_VISUALIZER.html` as the subpage.
- **Relative Path Guarantee:** All asset imports (`assets/siemens-ix/...`), markdown links (`agent_skills/...`, `products/...`), and script sources use strict **relative paths** (`href="assets/..."`, NOT `/assets/...` or `file:///...`), ensuring 100% path resolution under GitHub Pages' subpath routing (`/siemens-xcelerator-ailifecycle/`).

### 2.3 Agent Deployment Workflow (Pre-Push & Post-Push)
When an agent or developer makes code or content updates:
1. **Local Preview:** Test changes by opening `index.html` directly in the browser or via a local HTTP server (`python -m http.server 8080`).
2. **Pre-Push Validation:**
   - Verify Python AST and JSON schemas: `python -c "import json, ast; print('OK')"`
   - Verify relative markdown links (`md_link_checker.py`).
   - Confirm score synchronization between `index.html` and `DIGITAL_THREAD_VISUALIZER.html`.
3. **Automated Live Deployment:**
   - Commit and push changes:
     ```bash
     git add .
     git commit -m "feat: update readiness score for NX FastMCP bridge"
     git push origin main
     ```
   - GitHub Pages automatically builds and deploys the root directory within **30–60 seconds**.

---

## 3. System Architecture Overview

```
                  +-------------------------------------------------------------+
                  |                      WEB APPLICATION                        |
                  |  index.html (Main Tracker)   DIGITAL_THREAD_VISUALIZER.html|
                  +-------------------------------------------------------------+
                                                 |
         +---------------------------------------+---------------------------------------+
         |                                       |                                       |
+------------------+                    +------------------+                    +------------------+
|  ASSETS ENGINE   |                    | KNOWLEDGE BASE   |                    | EXECUTION ENGINE |
| assets/          |                    | agent_skills/    |                    | workflows/       |
| - siemens-ix/    |                    | - 21 .md skills  |                    | - dispatcher.py  |
| - ix-icons/      |                    | - 2 .zip bundles |                    | - dag.py         |
| - echarts.min.js |                    | products/        |                    | mcp_servers/     |
| - favicon.svg    |                    | - 7 deep dives   |                    | - 4 FastMCP py   |
+------------------+                    +------------------+                    +------------------+
```

---

## 4. Web Application Structure (`index.html`)

### 4.1 View Router & Layout Containers
The main app is a single-page application governed by `showView(idx)` (lines ~1157-1175). Selecting a tab or clicking a card updates `showView()` and hides/shows `.ail-vpane` elements.

| View ID | Label / Section | DOM Selector | Code Landmark Line Range | Primary Function |
| :--- | :--- | :--- | :--- | :--- |
| **`v1`** | **Overview & Pipeline Cards** | `#v1` | Lines 410 – 460 | Stage cards grid (14 stages), status indicators, and summary KPIs. |
| **`v2`** | **Architecture Diagram** | `#v2` | Lines 461 – 470, 1039 – 1056 | Layered orchestration diagram, license token pools, and API matrix table (`#connMatrix`). |
| **`v3`** | **Tiers & Bridge Tracker** | `#v3` | Lines 471 – 480, 1057 – 1072 | Readiness tier cards (`#tierCols`), custom bridge table (`#bridgeTable`), and MCP inventory grid (`#mcpGrid`). |
| **`v4`** | **Scorecard Chart** | `#v4` | Lines 481 – 490, 1073 – 1090 | Lazy-loaded ECharts horizontal bar chart rendering 0–10 readiness scores (`#scoreChart`). |
| **`v5`** | **AI & Data Fabric** | `#v5` | Lines 491 – 500, 1091 – 1150 | Siemens Intelligence Center X (ICX) overview, agentic app thread, and skill download catalog. |

### 4.2 Global Data Structures & State (In-Memory JS)
All application state and content are driven by five central JavaScript data structures defined in `index.html`:

| Data Structure Variable | Line Range | Purpose & Schema |
| :--- | :--- | :--- |
| **`STAGES`** | Lines 508 – 549 | Array of 14 objects mapping product stages: `{ no: '01', prod: 'ingest', tier: 'native', score: 8, auth: '...', lb: '...', api: '...', headless: '...' }`. |
| **`BRIDGES`** | Lines 551 – 556 | Array of tuples for custom bridges: `[Product, Score, Tier, Reason, GriffinBridgeRepo]`. |
| **`MCP_INV`** | Lines 558 – 773 | Array of MCP inventory objects: `{ id: '...', nm: '...', tier: '...', st: 'yes|no|part', txt: '...' }`. |
| **`DOWNLOAD`** | Lines 775 – 804 | Map connecting stage numbers to downloadable skill `.md` files and `.zip` archives in `agent_skills/`. |
| **`FAB_DL`** | Lines 806 – 820 | Map connecting Data Fabric feature keys to corresponding `.md` skill files in `agent_skills/`. |

---

## 5. "Change This Here" Handoff Lookup Matrix

Use this dictionary to instantly find the exact files, functions, and lines to edit for common tasks:

| Goal / User Request | Primary Target File | Exact Line Range / Function | Secondary Files to Update |
| :--- | :--- | :--- | :--- |
| **Change a Product's Score (e.g. NX or Amesim)** | [index.html](index.html) | `STAGES` array (Lines 508–549) | [DIGITAL_THREAD_VISUALIZER.html](DIGITAL_THREAD_VISUALIZER.html) Table 3 (Line 415), [SIEMENS_AUTHENTIC_PORTFOLIO_MAP.md](SIEMENS_AUTHENTIC_PORTFOLIO_MAP.md) |
| **Edit Product Deep Dive Modal Content** | [index.html](index.html) | `openProdModal()` (Lines 945–990) & `#prodModal` | Corresponding `.md` file in `products/` |
| **Add a New Siemens Product to Pipeline** | [index.html](index.html) | Add object to `STAGES` (Line 508) | Add skill `.md` in `agent_skills/`, update `DOWNLOAD` map (Line 775) |
| **Modify ECharts Colors, Padding, or Labels** | [index.html](index.html) | `drawChart()` (Lines 1081–1089) | N/A (ECharts options configured inline) |
| **Edit Skill File Download Mappings** | [index.html](index.html) | `DOWNLOAD` dictionary (Lines 775–804) | Create/modify corresponding `.md` file in `agent_skills/` |
| **Change Header Drawer Text or KPIs** | [index.html](index.html) | `#ctxBody` container (Lines 383–405) | N/A |
| **Edit Landscape Comparison Table** | [DIGITAL_THREAD_VISUALIZER.html](DIGITAL_THREAD_VISUALIZER.html) | Tab 1 `#tab-landscape` (Lines 302–349) | [GRIFFIN_VS_CHRIS_LANDSCAPE_ANALYSIS.md](GRIFFIN_VS_CHRIS_LANDSCAPE_ANALYSIS.md) |
| **Edit QX-250 7-Step Digital Thread Table** | [DIGITAL_THREAD_VISUALIZER.html](DIGITAL_THREAD_VISUALIZER.html) | Tab 2 `#tab-thread` (Lines 350–401) | [DIGITAL_THREAD_VISION.md](DIGITAL_THREAD_VISION.md) |
| **Modify Mendix Dispatcher Worker Queue** | [workflows/mendix_digital_fabric_dispatcher.py](workflows/mendix_digital_fabric_dispatcher.py) | Python script methods | `workflows/agentcenter_workflow_dag.py` |
| **Update FastMCP Server Specs (NX, Capital, etc.)** | [mcp_servers/](mcp_servers/) | Target server `.py` file | Corresponding skill `.md` in `agent_skills/` |

---

## 6. Subpage Architecture (`DIGITAL_THREAD_VISUALIZER.html`)

The subpage visualizer is an independent, responsive component comparing **Griffin's Work** (local FastMCP servers, Mendix Digital Fabric worker queue) vs. **Their Work** (Chris's cloud endpoints at `xcelerator.us`).

### 6.1 Tab Switching Engine
Governed by `switchTab(tabId)` (Lines 436–443). Clicking `.tab-btn` elements toggles `.active` classes on `.tab-pane` containers:

1. **Tab 1: `#tab-landscape` (Lines 302–349):** 4-column breakdown of Master Orchestration, NX CAD, Capital EWIS, and Low-Code UI.
2. **Tab 2: `#tab-thread` (Lines 350–401):** QX-250 7-step engineering tool ground-truth results.
3. **Tab 3: `#tab-scorecard` (Lines 402–433):** Product-by-product headless status, MCP-native support, scores, and critical takes.

---

## 7. Directory & Asset Catalog

### 7.1 `assets/` (Design System & Third-Party Engines)
- **`assets/siemens-ix/`**: Self-hosted Siemens IX styles (`siemens-ix.css`), ESM runtime (`siemens-ix.esm.js`), theme tokens, and custom SVG favicon (`favicon.svg`).
- **`assets/ix-icons/`**: Siemens IX icon web components (`ix-icons.esm.js`).
- **`assets/echarts.min.js`**: Apache ECharts 5.x library (lazy-loaded via `ensureECharts()` when opening View 4).

### 7.2 `agent_skills/` (21 Skill Instructions & Packages)
Contains individual `.md` skill definitions for LLM agents:
`nx.md`, `teamcenter.md`, `capital.md`, `opcenter.md`, `mendix.md`, `industrial-edge.md`, `siemens-eda-questa.md`, `simcenter-3d-nastran.md`, `simcenter-amesim.md`, `phm-made.md`, `polarion-alm.md`, `graph-neo4j.md`, `ai-studio-rapidminer.md`, `siemens-genai.md`, `siemens-ix.md`, `ai-lifecycle-harness.md`, `digital-twin.md`, `tecnomatix.md`, `teamcenter-soa-skills.zip`, `siemens-agent-skills.zip`.

### 7.3 `products/` (7 Technical Deep Dives)
Markdown specifications rendered inside `#prodModal`:
`NX_CAD_DEEP_DIVE.md`, `CAPITAL_EWIS_DEEP_DIVE.md`, `TEAMCENTER_DEEP_DIVE.md`, `SIMCENTER_CAE_DEEP_DIVE.md`, `MENDIX_DIGITAL_FABRIC_DEEP_DIVE.md`, `TECNOMATIX_DTC_DEEP_DIVE.md`, `POLARION_OPCENTER_EDGE_DEEP_DIVE.md`.

### 7.4 `workflows/` & `mcp_servers/` (Python Implementation Code)
- **`workflows/mendix_digital_fabric_dispatcher.py`**: License token pool manager & worker queue.
- **`workflows/agentcenter_workflow_dag.py`**: End-to-end execution DAG.
- **`workflows/dtc_copilot_engine.py`**: Production Co-Pilot engine.
- **`mcp_servers/nx_mcp_server.py`**, `capital_mcp_server.py`, `simcenter_mcp_server.py`, `teamcenter_mcp_server.py`: FastMCP server scripts.

---

## 8. Agent Modification & Verification Protocol

When an agent is instructed to make changes ("change X here"), follow this 3-step verification workflow:

1. **Perform Targeted Edits:** Update the designated file and line range according to Section 5.
2. **Run Syntax & Link Validation:** Execute validation scripts via Python:
   ```bash
   # Validate Python AST & JSON schemas
   python -c "import json, ast; print('JSON & AST OK')"
   ```
3. **Confirm Visual & Score Alignment:** Re-verify that score changes in `index.html` `STAGES` are reflected in `DIGITAL_THREAD_VISUALIZER.html` Table 3.
