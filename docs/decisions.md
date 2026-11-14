# Architecture decisions and open questions

## Accepted in specification

- Product is data-agnostic, CSV-first, rather than retail-dataset-bound.
- Data semantics are proposed and approved, not declared true by model intuition.
- DuckDB is a safe analytical engine **only when combined with process isolation and permissions**.
- Deterministic computation is source of numerical truth. RAG indexes metadata, not bulk transactional values.
- LangGraph is a bounded orchestrator; no free-form autonomous external side effects.
- No application code included in this starter.

## Decide during Milestone 0 (with evidence)

1. Local vs managed LLM provider and privacy implications.
2. Actual CSV upload limit and compute quota for target deployment.
3. Storage backend and snapshot/garbage collection policy.
4. Retrieval threshold for Chroma/embedding introduction; prove need with tests.
5. Default relation approval and privacy consent UX.
6. Report formats and retention; whether public demo accepts arbitrary personal data.

Record each decision with date, options, rationale, consequences and rollback path. Never call unresolved assumptions implemented features.
