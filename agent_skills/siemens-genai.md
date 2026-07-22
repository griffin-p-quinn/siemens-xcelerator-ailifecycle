# Siemens GenAI / Model Catalog — Agent Skill

## What It Is
Siemens operates an internal GenAI platform / model catalog hosting open-weight and proprietary models behind a unified **OpenAI-compatible REST API** with a Siemens-issued key. Griffin's `LLMS` CLI + React WebUI is the benchmark workbench for this catalog.

**Agentic-readiness: 🟢 Native (OpenAI-compatible REST).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| OpenAI-compatible REST | `/openai/v1` | `chat.completions`, embeddings; Siemens key |
| `llms` CLI (Griffin) | Python (uv) | `llms models list`, `llms chat`, `llms bench`, `llms serve` |
| WebUI workbench | React + iX | Multi-model benchmark |

### API pattern
```python
from openai import OpenAI
client = OpenAI(base_url="https://<siemens-genai-host>/openai/v1",
                api_key=os.environ["SIEMENS_GENAI_KEY"])
r = client.chat.completions.create(model="<siemens-model-id>",
    messages=[{"role":"user","content":"Diagnose this anomaly..."}])
```

---

## Deployment & Server Architecture
- Fully server-hosted; stateless REST — no headless concerns. Agents call it as the LLM backend for other bridges (DTC Co-Pilot, priceBook, GraphRAG).
- Provider is swappable (Siemens GenAI ↔ Anthropic) via env config in downstream apps.

---

## Agent Prompts
```
"List available Siemens GenAI models and their context windows"
"Benchmark Siemens models on a TC BOM analysis prompt via the LLMS CLI"
"Configure DTComposer to use Siemens GenAI as its LLM backend"
```

---

## Integration Points
- **DTC:** DTComposer anomaly diagnosis / recovery simulation.
- **priceBook / GraphRAG:** chat + embeddings over graphs.
- **AI Studio:** predictions explained via a hosted LLM.

---

## Sources
- OpenAI-compatible client — https://platform.openai.com/docs/api-reference
- Griffin's `LLMS` CLI/WebUI (`D:\projectsD\LLMS`).
