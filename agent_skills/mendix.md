# Mendix Low-Code Platform — Agent Skill

## What It Is
Mendix is Siemens' low-code application platform (acquired 2018). In Griffin's landscape it is the **orchestration layer** — the **Digital Fabric** queue dispatcher / license-token pool that fronts all the FastMCP servers. Concepts: Studio Pro (IDE), packages (.mpk), custom React/TS widgets, Mendix Runtime (Docker-deployable).

**Agentic-readiness: 🟢 Native MCP — both directions (9/10).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **MCP Server module** | Studio Pro 11.8+ | Microflows → MCP tools (Mendix acts as an MCP server) |
| **MCP Client + Maia MCP Client** | Studio Pro 11.8+ | Mendix consumes external MCP servers |
| Published REST / OData / GraphQL | HTTP | App-level service endpoints |
| Java actions | JVM | Custom server-side logic |
| Custom widgets | React/TypeScript → `.mpk` | `siemens.TcBOM`, newView, plmVisWeb |

### Widget toolchain
```bash
npm install
npm run build      # TSX → widget bundle
npm run release    # → .mpk for Studio Pro
```

---

## Deployment & Server Architecture
Mendix runtime is container-native (Docker); this is what makes it the **Digital Fabric dispatcher**:
- **Queue dispatcher + license-token pool + orchestration** across the NX/Capital/Teamcenter/Simcenter bridges.
- Official MCP **both directions** means the Fabric can *expose* microflows as agent tools and *call* other MCP servers (including Griffin's `nx-mcp`, `capitalMCP`).
- Deployable on Industrial Edge as a containerized IE app.

---

## Agent Prompts
```
"Expose a Mendix microflow as an MCP tool via the MCP Server module"
"Configure the Maia MCP Client to call griffin-p-quinn/nx-mcp"
"Explain how the Digital Fabric dispatcher pools NX license tokens"
"Build the TcBOM widget and describe its Teamcenter REST integration"
```

---

## Integration Points
- **Teamcenter:** widgets + Fabric consume TC SOA/REST.
- **Industrial Edge:** Mendix runtime as a containerized IE app.
- **All bridges:** Digital Fabric orchestrates nx-mcp, capitalMCP, Simcenter/Nastran, Teamcenter SOA.
- **Siemens iX:** frontends use `@siemens/ix`.

---

## Sources
- Mendix MCP — https://docs.mendix.com/agents/mcp/
- Maia MCP Client — https://docs.mendix.com/refguide/maia-mcp/
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §2 & §6.
