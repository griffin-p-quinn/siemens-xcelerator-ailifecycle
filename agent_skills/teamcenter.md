# Siemens Teamcenter — Agent Skill

## What It Is
Teamcenter is Siemens' PLM platform: product structure (BOM), engineering change (ECR/ECO), items, documents, workflows, and the digital thread across design ↔ manufacturing ↔ supply chain. It is **server-based** and agent-accessible via SOA services, ITK, and Active Workspace REST.

**Agentic-readiness: 🟡 Scriptable — needs bridge (6/10).** Rich SOA/ITK, but Copilot/AI-BOM-Agent are closed UX, not open MCP.

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **SOA** | .NET (`Teamcenter.Soa.Client`) / Java | Primary programmatic API — `StructureManagementService`, `DataManagementService`, etc. |
| **ITK** | C API | Server-side customization/batch |
| **TcXML** | File | Bulk import/export |
| **Active Workspace (AWC)** | REST/JSON gateway + RDS data service | Web UI + data services; public GraphQL contract **UNVERIFIED** — RDS is REST |
| SOA REST (JsonRestServices) | HTTP + cookie session | `Core-2006-03-Session/login`, `Query-2006-03-Finder/performSearch`, `Cad-2007-01-StructureManagement/getBOMsFromRevisionRules` |
| Teamcenter Copilot / AI BOM Agent (TC 2606) | Siemens-hosted UX | Closed — **not an open MCP** |

### Auth + BOM (SOA REST)
```python
import requests
s = requests.Session()
s.post("https://<tc-host>/tc/JsonRestServices/Core-2006-03-Session/login",
       json={"credentials":{"user":"...","password":"...","group":"","role":"","locale":""}})
s.post(".../Cad-2007-01-StructureManagement/getBOMsFromRevisionRules",
       json={"inputs":[{"clientId":"1","product":{"uid":"<itemRev_uid>"},"rule":{"uid":"<rule_uid>"}}]})
```

---

## Deployment & Server Architecture
- Teamcenter is already a server product. Agent scale pattern: **Pool Manager (4-tier `tcserver` pool)** load-balances SOA sessions.
- **Teamcenter Dispatcher** provides a native server-side job queue (translations, batch, NX-TC).
- Griffin's bridge: **SOA C# DataManagement wrapper + AWC RDS data service**, plus 16 packaged SOA agent skills (see `teamcenter-soa-skills.zip`).

---

## Key TC Concepts for Agents
| Concept | TC Term | Notes |
|---------|---------|-------|
| Part | Item + ItemRevision | UIDs are the primary key |
| BOM | BOMWindow / BOMLine | Recursive structure |
| Change | ECR (request) / ECO (order) | Change workflow |
| Document | Dataset attached to Item | JT, PDF, CAD |
| Workflow | Process Template | Approval flows |

---

## Agent Prompts
```
"Use the SOA DataManagementService wrapper to search all ECOs in 'Released' status"
"Traverse a BOMWindow via getBOMsFromRevisionRules and return the indented BOM"
"Explain the 16 Teamcenter SOA skills in teamcenter-soa-skills.zip"
"Push a Capital EBOM into a Teamcenter ECO for change management"
```

---

## Integration Points
- **Mendix:** BOM/newView/plmVisWeb widgets + Digital Fabric consume TC SOA/REST.
- **NX:** NX models managed in TC; JT generated from NX.
- **Capital:** Capital EBOM syncs to TC change management.
- **Opcenter/SAP:** changeGraphApp bridges TC ECOs ↔ Opcenter NCRs ↔ SAP cost.

---

## Sources
- Teamcenter — https://www.siemens.com/en-us/products/teamcenter/
- SOA usage refs (`recs12/TcSOAMasterForm`, `jio-kim/PDM`).
- Bundled SOA skills: `teamcenter-soa-skills.zip` (16 `SKILL.md`).
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stages 09/13.
