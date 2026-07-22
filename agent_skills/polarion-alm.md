# Siemens Polarion ALM — Agent Skill

## What It Is
Polarion is Siemens' Application Lifecycle Management platform — requirements, work items, RFLP traceability, test management, and V&V. In the RFP→Delivery lifecycle it owns **Requirements & RFLP** (stage 02) and anchors RFP intake (stage 01).

**Agentic-readiness: 🟢 Native-ready — the closest in PLM (8/10).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **REST API v1** | `/polarion/rest/v1` (OpenAPI) | Primary programmatic interface — CRUD work items, documents, links |
| **OSLC** | Linked-data | Cross-tool traceability |
| SOAP / WSDL | Legacy web services | Full coverage |
| **Copilot API (2606)** | First-party AI | Polarion-native assistant API |
| **`peakflames/PolarionMcpServers`** | Community MCP | Built on REST v1 — usable agent MCP today |

### REST v1 pattern
```http
GET  /polarion/rest/v1/projects/{projectId}/workitems?query=type:requirement
POST /polarion/rest/v1/projects/{projectId}/workitems
PATCH /polarion/rest/v1/projects/{projectId}/workitems/{id}
```

---

## Deployment & Server Architecture
- Server product; agent scale pattern: **multi-instance cluster behind an HTTP load balancer**.
- Fully headless — REST v1 + OSLC need no GUI. This is why Polarion scores highest among the PLM/ALM tools for agent-readiness.

---

## Agent Prompts
```
"Query all requirements in project X via Polarion REST v1 and summarize coverage"
"Create a work item linking an RFP clause to a derived requirement"
"Use the peakflames/PolarionMcpServers MCP to trace a requirement to its tests"
"Parse a 41-page RFP spec and write requirements into Polarion via REST v1"
```

---

## Integration Points
- **RFP intake:** LLM parses spec → writes requirements via REST v1.
- **Teamcenter:** SysML/RFLP handoff for systems engineering.
- **V&V:** trace links to sim/test results.

---

## Sources
- Polarion REST v1 — https://testdrive.polarion.com/polarion/rest/v1/definition
- Copilot (2606) — https://blogs.sw.siemens.com/polarion/
- Community MCP — https://github.com/peakflames/PolarionMcpServers
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 02.
