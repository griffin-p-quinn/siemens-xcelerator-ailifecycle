# Simcenter Amesim — Agent Skill

## What It Is
Simcenter Amesim is Siemens' multi-domain systems simulation platform (1D physical modeling: hydraulic, thermal, mechanical, electrical, control). In the lifecycle it owns **Systems Simulation (6-DOF)** (stage 04). It is a **desktop application** with a documented scripting/batch path.

**Agentic-readiness: 🟡 Scriptable + bridge (7/10).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **Python API** | `amesim` (modern) / `ame_apy` (legacy) | Drive models, set params, run, read results |
| **CMD batch** | over `.amegp` | Command-line simulation runs |
| Model precompilation | required | **The model must be pre-compiled** before headless runs |
| FMU export | FMI | Co-simulation / model exchange |

### Python pattern
```python
import amesim
model = amesim.open("plant.amegp")   # pre-compiled
model.set_param("valve.diameter", 0.012)
model.run()
results = model.get_results("pressure")
```

---

## Deployment & Headless / Server Architecture
- Desktop app — for agent use it runs **headless via the Python API / CMD batch**, but only against a **pre-compiled model** (compilation is the gating step).
- Scale pattern: **worker-session pool** — a pool of Amesim solver sessions consumes queued simulation jobs; license-gated, so token management mirrors the NX pattern via the Digital Fabric dispatcher.
- Griffin's bridge: Python `ame_apy` wrappers + worker-session pool.

---

## Agent Prompts
```
"Open the pre-compiled Amesim plant model, sweep valve diameter, and return peak pressure"
"Run an Amesim batch simulation over .amegp from the command line"
"Export the Amesim model as an FMU for co-simulation"
"Explain why the Amesim model must be pre-compiled for headless runs"
```

---

## Integration Points
- **Systems models:** consumes system definition; feeds performance to V&V.
- **Simcenter 3D / HEEDS:** design-space exploration.
- **Digital Fabric:** license-token pooled worker sessions.

---

## Sources
- Simcenter Amesim — https://plm.sw.siemens.com/en-US/simcenter/systems-simulation/amesim/
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 04 & §6.
