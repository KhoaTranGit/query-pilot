# Architecture contract — domain-independent self-auditing analytic engine

## System boundaries

- **Client:** Next.js + React + TypeScript, Tailwind and shadcn/ui; React Flow for inferred relationships / evidence graphs; Apache ECharts for charts. No analytical truth computed in the browser.
- **API:** FastAPI + Pydantic v2 schemas; workspace, upload, profiling, confirmation, question, investigation, result, feedback, and export endpoints.
- **Workspace service:** File ingestion, immutable file manifests, content hashes, data-preview controls, workspace version, and scoped data dictionaries.
- **Analytic warehouse:** Local DuckDB connections scoped to each workspace; read-only analysis role/isolated worker for generated queries; Polars for profiles and derived result checks.
- **Metadata registry:** Persist table/column metadata, candidate relationships, user-approved joins, metric contracts and versions. SQLite for local single-user MVP; plan PostgreSQL for multiuser production.
- **Integrity service:** a domain-neutral deterministic auditor recomputes claims and runs applicable join, metric, quality, decomposition and evidence checks; returns a typed Integrity Report and an auditable status before publication.
- **Agent:** LangGraph explicit state machine with limits. It chooses tools and proposes counterchecks but **cannot override the Integrity service verdict**; it never directly operates the filesystem or constructs unchecked DB connections.
- **Retrieval:** LangChain deterministic table lookup first; Chroma and sentence-transformers only if metadata volume justifies vector retrieval. Every retrieval result has workspace + dataset-version provenance.
- **Observability:** Langfuse tracing (opt-in for private inputs), OpenTelemetry and structured logs; avoid raw CSV rows/PII in traces by default.

## Data lifecycles

1. POST files -> temporary quarantine -> format/type validation -> stable snapshots -> DuckDB-safe import -> metadata profile -> workspace manifest.
2. Inferred metadata -> user approval/correction -> versioned semantic contracts.
3. Question + pinned snapshot -> retrieve definitions -> answerability check -> bounded plan -> SQL policy -> constrained investigation -> draft claim -> **integrity challenge plan + deterministic counterchecks** -> status (robust/sensitive/unresolved/blocked) -> evidence graph -> report.
4. New uploads or schema changes -> new workspace version; mark old investigations stale. Never quietly reuse SQL/metrics from a different snapshot.

## Dependency policy

Use mature libraries for primitives: DuckDB CSV auto-detection and query engine; Polars profiling; SQLGlot SQL AST; Pydantic schemas; LangGraph execution and persistence; LangChain retrieval plumbing; sklearn/statsmodels for statistical operations; React Flow/ECharts for graphs. **Never hand-write your own tokenizer, ORM, SQL parser, vector index, data frame engine, workflow runtime, statistical test or charting engine.** No PyTorch model training is needed for MVP; pretrained sentence-transformers uses PyTorch internally if selected.

## Design anti-patterns

No hard-coded `order_id`, `revenue`, named dataset or join assumptions in reusable layers. No dataset rows embedded as vector search documents for numeric aggregation. No accidental one-to-many multiplication. No free-form LLM-to-SQL execution. No implicit claim of root cause. No proof-by-second-LLM alone. **A reproduced arithmetic result is not automatically a robust business conclusion.** All tool outputs typed and version-linked.

## Portability

Standardize workspace contracts, not industry-specific tables. Hold fixed orchestration graph while dataset schema, relationships, metric registry, and question all vary. Use dependency inversion for LLM and metadata index providers. Verify unknown schema tests before calling the platform data-agnostic.
