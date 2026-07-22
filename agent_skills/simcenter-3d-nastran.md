# Simcenter 3D / Simcenter Nastran — Agent Skill

## What It Is
Simcenter 3D is Siemens' integrated CAE pre/post environment; Simcenter Nastran is the structural solver. Together they own **Structural CAE** (stage 07). Pre/post is a **desktop app** on the NX platform; the **solver is fully headless via CLI**.

**Agentic-readiness: 🟡 Scriptable + bridge (7/10).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **`NXOpen.CAE`** | C# / Python | Pre/post automation (mesh, BCs, results extraction) |
| **Nastran solver CLI** | `nastran deck.dat` | **Fully headless** — SOL 101 (linear static), SOL 106 (nonlinear), etc. |
| HEEDS | DSE driver | Design-space exploration / optimization |
| BDF/DAT/OP2/F06 | Files | Input deck / results interchange |

### Headless solve
```bash
nastran model.dat scr=yes           # runs the deck non-interactively
# results in model.op2 / model.f06
```

---

## Deployment & Headless / Server Architecture
- **Solver:** genuinely headless — the `nastran deck.dat` CLI needs no GUI; ideal for a **solver farm**.
- **Pre/post (Simcenter 3D):** desktop app on the NX platform — automate via `NXOpen.CAE` under the same **NX X Server-User headless license** and license-token pool as NX CAD.
- Scale pattern: **solver farm + dual-license management** (NX platform license for pre/post + Nastran solver tokens).

---

## Agent Prompts
```
"Automate mesh + BC setup via NXOpen.CAE, then export a Nastran BDF"
"Run a SOL 101 linear static solve headlessly with the nastran CLI and parse the .f06"
"Drive a HEEDS design-space exploration over a thickness parameter"
"Extract max von Mises stress from the .op2 via NXOpen.CAE post"
```

---

## Integration Points
- **NX:** shares the NX platform + Server-User license pool.
- **Amesim / HEEDS:** multi-disciplinary optimization.
- **V&V:** structural margins feed verification.

---

## Sources
- Simcenter 3D — https://plm.sw.siemens.com/en-US/simcenter/3d-simulation/
- Simcenter Nastran — https://plm.sw.siemens.com/en-US/simcenter/mechanical-simulation/nastran/
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 07.
