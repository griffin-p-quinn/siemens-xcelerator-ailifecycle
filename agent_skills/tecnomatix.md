# Tecnomatix — Process Simulate & Plant Simulation — Agent Skill

## What It Is
Tecnomatix is Siemens' digital manufacturing suite. **Process Simulate** does robotics/assembly simulation; **Plant Simulation** does discrete-event (DES) material-flow simulation. Together they own **Mfg / Assembly Sim** (stage 10). Desktop apps, but genuinely automatable via COM.

**Agentic-readiness: 🟢 Native (COM) — 8/10.**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **COM / ActiveX `RemoteControl`** | Windows COM | Drive Plant Simulation from any COM client |
| **SimTalk 2.0** | Built-in scripting | In-model automation language |
| **`pyplantsim`** | Python wrapper | Pythonic COM control of Plant Simulation |
| .NET automation | Process Simulate | Programmatic robotics/assembly ops |
| `.visible = False` | COM property | **Runs headless** (no visible window) |

### pyplantsim / COM pattern
```python
from pyplantsim import Plantsim
ps = Plantsim(version="16.0", visible=False)   # headless
ps.load_model("line.spp")
ps.set_value("Model.throughput_target", 120)
ps.start_simulation("Model.Frame")
kpi = ps.get_value("Model.throughput")
```

---

## Deployment & Headless / Server Architecture
- Desktop apps, but COM `RemoteControl` + `.visible=False` gives **true headless operation**.
- Scale pattern: **Windows/COM licensed-instance pool** — a pool of Windows hosts each running a licensed instance; the Digital Fabric dispatches DES jobs to them.
- COM is Windows-bound, so the pool is Windows-based (unlike the Linux-friendly Nastran/Questa farms).

---

## Agent Prompts
```
"Load a Plant Simulation model headlessly via pyplantsim and return throughput KPI"
"Sweep a buffer size parameter across DES runs and find the bottleneck"
"Drive Process Simulate robotics via .NET automation to validate reach"
"Explain how .visible=False enables headless Plant Simulation"
```

---

## Integration Points
- **DTC:** DES results feed the digital-twin production model.
- **Opcenter/MES:** validated process → execution.
- **Digital Fabric:** COM licensed-instance pool.

---

## Sources
- Plant Simulation COM / pyplantsim — https://pypi.org/project/pyplantsim/
- Plant Simulation help — https://scredirect.docs.sws.siemens.com/tdoc/plantsim/16/plant_sim_help/
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 10 & §6.
