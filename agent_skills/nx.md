# Siemens NX — Agent Skill

## What It Is
NX is Siemens' flagship CAD/CAM/CAE system. It is a **desktop application**; agent automation is achieved through the **NX Open** programming interface (.NET / C++ / Python `NXOpen`), **NX Journals** (recordable/replayable scripts), and batch/headless execution. Griffin's **`griffin-p-quinn/nx-mcp`** FastMCP server wraps NX Open for LLM-agent consumption.

**Agentic-readiness: 🟡 Scriptable — needs bridge (6/10).** World-class scripting API, but no official MCP and every batch run consumes a license token.

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **NX Open** | .NET (C#/VB), C++, Python (`import NXOpen`) | The primary programmatic API — all NX automation goes through here |
| **Journaling** | Recorded .cs/.vb/.py | Record UI actions → replay/parameterize as a script |
| **`run_journal`** | CLI | `run_journal <script>` executes a journal against a part |
| **`-batch`** | CLI flag | Non-interactive execution |
| **`griffin-p-quinn/nx-mcp`** | FastMCP server (primary agent interface) | Wraps NX Open; tools: `build_part`, `open_model`, `run_simulation`, `export_jt` |
| Design Copilot NX (Summer 2025) | In-app assistant | **Non-programmatic** — learning/documentation aid, not an agent API |

> **Credit:** Use Griffin's `griffin-p-quinn/nx-mcp` as the primary MCP. The third-party `DreamEnding/NX_MCP` exists but is not the reference implementation here.

### NX Open pattern (C#)
```csharp
using NXOpen;
public class MyJournal {
    public static void Main(string[] args) {
        Session theSession = Session.GetSession();
        Part workPart = theSession.Parts.Work;
        // ... NXOpen API calls
    }
}
```

---

## Deployment & Headless / Server Architecture
NX is a **desktop app** — to use it agentically at scale you must run it server-side, non-interactively:

- **NX X "Server User" headless license** (since **NX2506.4000**) is the real enabler for non-interactive sessions. See community.sw.siemens.com "Running local NX X session with Server-User license."
- **License-token pool + dispatcher:** every `-batch` / `run_journal` invocation burns a license token. Griffin's pattern is an **NX X Server-User license-token pool** orchestrated by the **Mendix Digital Fabric** worker queue, so concurrent agent requests are load-balanced against a finite license pool rather than blocking.
- **Teamcenter Dispatcher** can also queue NX-TC batch jobs server-side (translations, JT export).
- Pattern: agent → `nx-mcp` tool call → dispatcher acquires token → headless NX runs journal → JT/STEP artifact returned → token released.

---

## Agent Prompts
```
"Use the nx-mcp server (griffin-p-quinn/nx-mcp) to create a new part parametrically"
"Run a headless NX build via the license-token pool and export the result to JT"
"Generate an NX Open C# journal that creates a 100x50x25mm box"
"Explain how the NX X Server-User license enables non-interactive batch runs"
```

---

## Integration Points
- **Capital:** CapitalNXIntegration links NX mechanical geometry ↔ Capital wire lengths.
- **Teamcenter:** NX models managed in TC; JT exports feed the AWC/plmVisWeb viewer.
- **Mendix:** nx-mendix + Digital Fabric surface NX data/actions and pool licenses.

---

## Sources
- NX Open / NXOpen API — https://docs.sw.siemens.com/en-US/product/209349590/
- NX X Server-User non-interactive license — https://community.sw.siemens.com/s/article/Running-local-NX-X-session-with-Server-User-license
- Design Copilot NX (Summer 2025) — https://news.siemens.com/en-us/siemens-designcenter-nx-summer-2025/
- Griffin's MCP — `griffin-p-quinn/nx-mcp`
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 06.
