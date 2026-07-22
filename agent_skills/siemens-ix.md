# Siemens iX Design System — Agent Skill

## What It Is
Siemens iX is the official Siemens design system and web component library (`@siemens/ix`, `@siemens/ix-icons`) — React, Angular, and framework-agnostic web components matching the Siemens design language. This tracker itself is built on iX.

**Agentic-readiness: 🟢 Native — first-party docs MCP exists.**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **iX docs MCP** | `mcp.siemens.com` / `ix.siemens.io/docs/home/mcp-server` | Docs/RAG only; requires Siemens token |
| `@siemens/ix` | Web components | `ix-application`, `ix-card`, `ix-dropdown`, `ix-menu`, etc. |
| `@siemens/ix-react` | React wrappers | `IxTable`, `IxTree`, `IxWorkflowSteps`, … |
| `@siemens/ix-icons` | Icon set | Validate names via `ix-icon-search` |

### Setup
```bash
npm install @siemens/ix @siemens/ix-icons
```
```ts
import '@siemens/ix/dist/siemens-ix/siemens-ix.css';
import { defineCustomElements } from '@siemens/ix/loader';
defineCustomElements();
```

### Theming
```ts
document.documentElement.setAttribute('data-ix-theme', 'classic');
document.documentElement.setAttribute('data-ix-color-schema', 'dark');
```

---

## Deployment & Server Architecture
- Pure client-side component library — no server needed. The **iX docs MCP** is the agent-facing service (documentation retrieval), token-gated.

---

## Agent Prompts
```
"Search the iX docs MCP for the ix-dropdown component API"
"Search iX icons for 'maintenance'"
"Use ix-pane-layout to build a two-pane detail view"
"Which iX theme tokens control the accent color?"
```

---

## Integration Points
- All modern Siemens web apps (MRO, changeGraphApp, priceBook, LLMS, this tracker).
- Always validate icons with `ix-icon-search` before use.

---

## Sources
- iX docs + MCP — https://ix.siemens.io/docs/home/mcp-server
- iX component library — https://ix.siemens.io/
