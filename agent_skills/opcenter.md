# Siemens Opcenter — Agent Skill

## What It Is
Opcenter is Siemens' manufacturing operations management (MOM/MES) suite — Execution, APS (advanced planning & scheduling), and Intelligence (analytics). It owns **Production Execution / MES** (stage 11). Server product, REST/OData-native.

**Agentic-readiness: 🟢 Native (REST) — 8/10.**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **REST** | HTTP | Execution Core REST services |
| **OData v4.1** | Query protocol | **Opcenter Intelligence API** — analytics/query |
| Execution Core REST | HTTP | Work orders, operations, NCRs |
| Events/webhooks | HTTP | Shop-floor event streaming |

### OData / REST pattern
```http
GET /intelligence/odata/v4/ProductionOrders?$filter=status eq 'Released'
GET /execution/rest/workorders/{id}/operations
POST /execution/rest/nonconformances
```

---

## Deployment & Server Architecture
- Server product; scale pattern: **HTTP load balancer** across Opcenter instances. Fully headless — no desktop dependency.

---

## Agent Prompts
```
"Query released production orders via the Opcenter Intelligence OData API"
"Create a non-conformance record (NCR) via the Execution Core REST API"
"Correlate an NCR with a Teamcenter ECO for the change graph"
"Pull OEE / throughput analytics from Opcenter Intelligence"
```

---

## Integration Points
- **Teamcenter:** NCR ↔ ECO/CAPA (changeGraphApp).
- **Industrial Edge / Insights Hub:** shop-floor telemetry context.
- **SAP:** cost/material signals.

---

## Sources
- Opcenter Intelligence OData API — https://developer.siemens.com/insights-hub/docs/apis/advanced-opcenter-intelligence/api-opcenter-intelligence-api.html
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 11.
