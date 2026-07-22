# Graph / Neo4j — Agent Skill

## What It Is
The graph layer models product structures, supply chains, ontologies, and knowledge bases. Stack: **Neo4j** (graph DB) + **Python** (GraphRAG / GraphStudio) + **FastAPI**, with a React+iX frontend. In the Intelligence Center X framing this is the **data/ontology (Graph Studio) layer**. GraphRAG adds LLM-augmented retrieval over the graph.

**Agentic-readiness: 🟢 Native (Cypher/Bolt REST + MCP-friendly).**

---

## Integration Layer(s)

| Interface | Form | Notes |
|-----------|------|-------|
| Cypher over Bolt | Query protocol | Primary graph query interface |
| Neo4j HTTP API | REST | Query without a driver |
| GraphRAG | Python (`graphrag_core.py`) | Entity extraction → graph → retrieval+LLM |
| GraphStudio | Python | Builds ontology from TC BOM (`build_ontology_model.py`) |
| FastAPI backend | REST | Fronts the graph for the iX viewer |

### Cypher patterns
```cypher
MATCH (p:Product {category:"Software"}) RETURN p.name, p.listPrice ORDER BY p.listPrice DESC;
MATCH path=(a:Assembly)-[:HAS_CHILD*]->(c:Part) WHERE a.name=$name RETURN path;
MATCH path=(prod:Product)-[:REQUIRES*1..5]->(raw:Material) WHERE raw.riskScore>0.7 RETURN prod.name, raw.name;
```

---

## Deployment & Server Architecture
- Neo4j runs as a server/container (`docker-compose up -d`); agents connect over Bolt/HTTP — fully headless.
- GraphRAG index built offline, then served via `graphrag_web.py` / FastAPI for query-time retrieval.

---

## Agent Prompts
```
"Write a Cypher query to find all software products over $10,000"
"Run GraphRAG over the Teamcenter BOM and answer a shared-component question"
"Build the ontology model from Teamcenter via GraphStudio"
"Traverse supply-chain risk paths in the priceBook graph"
```

---

## Integration Points
- **Teamcenter:** GraphStudio / tcSupplyChainStory build graphs from TC BOM.
- **Siemens GenAI:** GraphRAG / priceBook use LLMs for chat over the graph.
- **Siemens iX:** graph viewer UI.

---

## Sources
- Neo4j — https://neo4j.com/docs/
- Griffin's graph projects: GraphStudio, GraphRAG, priceBook.
- Siemens Intelligence Center X (Graph Studio = data/ontology layer) — https://news.siemens.com/en-us/siemens-intelligence-center-x/
