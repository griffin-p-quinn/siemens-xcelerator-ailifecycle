# AI Lifecycle Harness — Agent Skill

## What It Is
The AI Lifecycle Harness is the **meta-orchestration / WBS steward** layer of Griffin's Digital Fabric. It is not a Siemens product — it is the governing engine that decomposes work into **Deliverables → Criteria → Accomplishment → Milestones** and drives the RFP→Delivery lifecycle across all 14 stages, coordinating the per-product bridges. It also owns the **Human-in-the-Loop (HITL) approval gate** at Delivery / Airworthiness (stage 14).

**Agentic-readiness: Steward / meta-layer (orchestration, not a scored tool).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| WBS engine | Deliverables→Criteria→Accomplishment→Milestones | Steward data model |
| Mendix Digital Fabric | Queue dispatcher | Executes the WBS against product bridges |
| HITL gate | Approval workflow | Certification sign-off (stage 14) |
| FastMCP bridges | MCP | Delegates to nx-mcp, capitalMCP, Teamcenter SOA, Simcenter/Nastran, etc. |

### Role in the thread
```
RFP → [Harness WBS steward] → dispatch stage tasks → product bridges (MCP) → V&V → HITL gate → Delivery
```

---

## Deployment & Server Architecture
- Runs as the orchestration meta-layer over the **Mendix Digital Fabric** dispatcher (container-native), which in turn pools licenses and load-balances the desktop-app bridges (NX, Amesim, Tecnomatix).
- The HITL gate ensures a human approves airworthiness/certification before Delivery — an intentional non-autonomous checkpoint.

---

## Agent Prompts
```
"Decompose this RFP into a WBS of Deliverables, Criteria, Accomplishment, and Milestones"
"Dispatch stage-06 (NX CAD) work to the nx-mcp bridge via the Digital Fabric"
"Show the HITL approval gate state for the airworthiness sign-off"
"Report lifecycle coverage across all 14 stages with per-stage agentic tier"
```

---

## Integration Points
- **All 14 lifecycle stages** — the harness is cross-cutting.
- **Mendix Digital Fabric:** execution substrate.
- **Teamcenter:** certification sign-off objects.

---

## Sources
- In-repo `AGENTIC_READINESS_VERIFIED.md` §2 (orchestration layer) & stage 14.
- In-repo `INDEX.md` (Master Control & Orchestration row).
