# PHM Technology MADe — Agent Skill

## What It Is
MADe (Maintenance Aware Design environment) by PHM Technology is a reliability / physics-of-failure (PhSM) and RAMS analysis tool covering FMEA, fault propagation, diagnostics, and safety analysis. In the lifecycle it owns **Reliability / PhSM** (stage 03).

**Agentic-readiness: 🔴 Custom bridge required — a hard wall (3/10).**

---

## Integration Layer(s) — the honest picture

| Capability | Status |
|-----------|--------|
| SysML v2 model ingest | ✅ **Import-only API** |
| Headless / batch run | ❌ **None** — no non-interactive execution path |
| Results extraction API | ❌ **None** — analysis results cannot be pulled programmatically |
| MCP / REST / CLI | ❌ None shipped or announced |

> **Hard wall:** MADe accepts SysML v2 *in* but offers **no way to run an analysis headlessly and no way to extract results programmatically**. It is the least agent-ready tool in the lifecycle after Capital-authoring.

---

## Deployment & Server Architecture
- No server/headless mode exists. The only viable pattern is **manual export + RPA handoff**:
  - Agent generates/updates the SysML v2 model → imports into MADe (import API) → **human runs** the reliability analysis in the GUI → results manually exported → agent consumes the exported file.
- This is an **open gap** in the digital thread; closing it would require either a PHM Technology API or screen-automation RPA around the desktop GUI.

---

## Agent Prompts
```
"Generate a SysML v2 model of the failure modes and import it into MADe"
"Explain why MADe results cannot be extracted programmatically today"
"Draft an RPA handoff plan for running a MADe analysis around its GUI"
```

---

## Integration Points
- **Systems models:** consumes SysML v2 from the requirements/systems stage.
- **V&V:** reliability results (once manually exported) feed verification.

---

## Sources
- PHM Technology MADe — https://www.phmtechnology.com/made
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 03 & §4 (hard wall).
