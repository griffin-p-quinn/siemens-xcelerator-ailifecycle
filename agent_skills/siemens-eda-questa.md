# Siemens EDA — Questa / ModelSim — Agent Skill

## What It Is
Questa (and ModelSim) is Siemens EDA's HDL simulation/verification tool for embedded/FPGA/ASIC logic. It owns **Embedded / FPGA** (stage 08). It is the **best-native agent target** in the lifecycle — CLI-first, batch-native, CI-friendly.

**Agentic-readiness: 🟢 Native-ready — the best (9/10).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| **`vsim -c`** | Console (no GUI) mode | Batch simulation |
| **`-do <file>.do`** | TCL script | Full scripted control of compile/sim/wave |
| **`qrun`** | Unified flow driver | Compile + optimize + sim in one command |
| **`-batch`** | Flag | Non-interactive regression |
| TCL | Scripting language | Native automation surface |

### Batch run
```bash
vlib work
vlog design.sv testbench.sv
vsim -c -do "run -all; quit" testbench      # console, batch
# or:
qrun -batch design.sv testbench.sv
```

---

## Deployment & Server Architecture
- Already fully headless. Scale pattern: **CI regression farm** — each `vsim`/`qrun` invocation takes a license per invocation; regressions fan out across the farm.
- No desktop dependency for agent use — this is why it scores highest.

---

## Agent Prompts
```
"Compile and run a SystemVerilog testbench headlessly with vsim -c and report pass/fail"
"Write a .do TCL script that runs a regression and dumps a waveform"
"Use qrun -batch to run the full FPGA regression suite in CI"
"Parse the transcript for assertion failures"
```

---

## Integration Points
- **CI/CD:** regression farm integration.
- **Program & Build:** verification gates feed Teamcenter V&V.

---

## Sources
- Questa / ModelSim — https://eda.sw.siemens.com/en-US/ic/questa/
- Grounded in in-repo `AGENTIC_READINESS_VERIFIED.md` §1 stage 08 (best-native).
