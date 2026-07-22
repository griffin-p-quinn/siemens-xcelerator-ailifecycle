# Altair RapidMiner AI Studio — Agent Skill

## What It Is
Altair RapidMiner AI Studio (formerly RapidMiner Studio) is a low-code data-science/ML platform for ETL, feature engineering, model training, and deployment. In the Siemens Intelligence Center X framing it is the **ML ("analyze") layer**. Griffin's `AIStudioMCPAgent` wraps its Java API as an MCP server.

**Agentic-readiness: 🟡 Scriptable via JAR plugin → MCP.**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **AIStudioMCPAgent** (Griffin) | JAR plugin running *inside* AI Studio → MCP | Tools: `run_process`, `get_results`, `list_processes`, `import_data`, `get_model_metrics` |
| AI Studio Java API | JVM | What the plugin wraps |
| Process (.rmp) | Visual workflow file | Drag-drop operator pipeline |
| AutoModel (.ioo) | Model artifact | Automated ML result; deploy via Deployment Service |
| Deployment Service | REST endpoint | Wraps a trained model |

### MCP pattern
```
LLM Agent --MCP--> AIStudioMCPAgent (JAR in AI Studio) --Java API--> Process/AutoModel --> prediction
```

---

## Deployment & Server Architecture
- AI Studio is a **desktop app**; the MCP plugin runs *inside* it, so headless use requires an AI Studio process kept alive (server/session host) or the RapidMiner **AI Hub** server edition for scheduled/served processes.
- Trained models (`.ioo`) can be deployed as REST endpoints (Deployment Service / AI Hub) for stateless agent calls without a live Studio.

---

## Agent Prompts
```
"Use AIStudioMCPAgent to run the cost-prediction process and return model metrics"
"List available AI Studio processes via the MCP plugin"
"Import a CSV dataset into the AI Studio repository and train an AutoModel"
"Deploy the .ioo cost model as a REST endpoint"
```

---

## Integration Points
- **Siemens GenAI:** AutoModel results explained via a hosted LLM.
- **Teamcenter:** quality/cost predictions linked to TC change records.
- **MCP ecosystem:** same pattern as nx-mcp / capitalMCP.

---

## Sources
- Altair RapidMiner AI Studio — https://altair.com/rapidminer
- Griffin's plugin: `D:\projectsD\AIStudioMCPAgent` (JAR → MCP).
- Siemens Intelligence Center X (AI Studio = ML layer) — https://news.siemens.com/en-us/siemens-intelligence-center-x/
