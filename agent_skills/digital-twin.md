# Digital Twin Composer (DTC) — Agent Skill

## What It Is
Digital Twin Composer (DTC) builds and orchestrates digital twins of industrial assets and production systems, connecting real-time IoT data, simulation, and AI reasoning. Griffin's **DTC Production Co-Pilot** (`D:\projectsD\DTComposer`) adds LLM-driven diagnosis, recovery simulation, and execution (Aircraft Final Assembly Line scenario).

**Agentic-readiness: 🟢 Native (FastAPI + LangGraph, LLM-configurable).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| FastAPI backend | REST / WebSocket | DTC API client + LLM agent |
| LLM agent | Siemens GenAI or Claude | `diagnose(anomaly)` → `simulate(recovery)` → `execute(action)` |
| React frontend | TypeScript + iX + React Three Fiber | 3D twin visualization |
| demo_mode | Scripted playback | Runs without a live DTC |

### Architecture
```
React (iX + R3F) --HTTP/WS--> FastAPI
  ├─ DTC API client (twin state, telemetry)
  ├─ LLM agent (diagnose → simulate → execute)
  └─ demo_mode (scripted)
```

---

## Deployment & Server Architecture
- Backend is already server-side (uvicorn); the twin/telemetry source is DTC or a scripted feed.
- Provider-swappable LLM (Siemens GenAI ↔ Anthropic) via `.env`.

---

## Agent Prompts
```
"Explain the DTC Co-Pilot diagnose→simulate→execute loop"
"Add a recovery action that reroutes parts to a backup station"
"Run the co-pilot in demo_mode (no live DTC)"
```

---

## Integration Points
- **Siemens GenAI:** LLM backend for diagnosis.
- **Teamcenter / Opcenter:** changeGraphApp reads ECOs/CAPAs / NCRs.
- **Siemens iX:** consistent UI.

---

## Sources
- Griffin's `D:\projectsD\DTComposer` (FastAPI + LangGraph + R3F).
- Grounded in in-repo INDEX.md (Digital Factory & Assembly Sim row).
