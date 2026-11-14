# OpenCode Development Contract — QueryPilot Self-Auditing AI Analytics

**READ THIS FIRST. Status: NO CODE IMPLEMENTED.** This file controls subsequent coding work. The current repository intentionally contains no application source code.

## Authority and required order

1. `objective.md` defines product behavior; `plan.md` defines execution sequence and gates.
2. `docs/security-and-privacy.md` is non-negotiable for SQL and upload behavior.
3. `docs/relationships-and-semantics.md` is non-negotiable for correct joins/metrics.
4. **`docs/integrity-engine.md` is the signature functional contract**; every published material claim must have a truthfully recorded check/verdict.
5. `docs/progress.md` tracks real progress; update on each completed milestone with proof.
6. Resolve conflicts by choosing safer, simpler, scope-limited behavior; record tradeoffs in `docs/decisions.md`.

## Non-negotiable rules

- **Do not implement important functionality from scratch when a maintained library exists.** Use DuckDB for CSV import/SQL, Polars for dataframe operations, Pydantic for validation, SQLGlot for SQL parsing, LangChain for retrieval integrations, LangGraph for stateful tool orchestration, scikit-learn/statsmodels/SciPy for statistical operations, React Flow for graphs, ECharts for charts, and appropriate preexisting upload/UI components.
- PyTorch is **optional only** for existing embedding or pretrained ML integrations; do not build or train neural networks for core MVP. No custom tokenizer, vector index, SQL parser, embedding engine, workflow engine, CSV parser, chart renderer or statistical optimizer.
- **No hard-coded columns, fixed tables, Olist conditionals, retail KPIs, prewired joins, static dataset assumptions or demo-number UI masquerading as computed results** in runtime business logic.
- Keep all source data private by default. Never commit real uploaded datasets, API keys, exports containing rows, logs containing PII, or proprietary contents.
- No unvalidated model-generated SQL execution, no arbitrary SQL file reads, no privileged database access, no unbounded agent loops, no query results passed indiscriminately to the LLM.
- Distinguish inferred/proposed/approved/rejected joins and metrics. Block calculations that require unresolved semantics; favor clarification over fabrication.
- Audit every material finding: dataset snapshot/version, table/column provenance, metric definition, SQL policy outcome, query IDs/result cells, **challenge checks and statuses**. Never conflate arithmetic reproduction with robustness to assumptions or invent a completed check.
- Do not let the LLM perform arithmetic when it can be computed deterministically. The audit verdict is produced by deterministic services, **not a second LLM**. Never claim causation from correlations or contribution analyses.
- UI must follow the **2026-11-14 refreshed** `docs/ui-spec.md` and `ui.png`: warm tinted background, generous spacing, friendly accessible graphics, one primary chart + concise **How this was checked** summary by default, advanced audit/SQL details progressively disclosed. `ui.png` is conceptual and contains intentionally illustrative values. Do not recreate a cluttered, stark-white KPI wall.
- Do not add optional frameworks just to make the stack larger. Start with the smallest maintainable MVP.

## Implementation discipline

- Work on **exactly one milestone at a time**, in `plan.md` order. Do not proceed until acceptance checks and regression tests for the current milestone pass.
- Before edits: summarize planned files, libraries used, dependencies, risks, and acceptance criteria.
- After edits: run targeted tests, type/lint checks as applicable, record actual pass/fail results in `docs/progress.md`, add known gaps and next milestone.
- Keep imports/services/agents isolated by responsibility; design APIs with typed contracts; pin dependency versions after validation.
- Request user input only where unavoidable: model credentials, unclear semantics, source-license decisions or safety-sensitive deployment settings.
- Maintain offline deterministic fake/stub mode for testing. Offline mode must not pretend to produce genuine LLM investigations.
- Use tests for messy CSVs, schema drift, alias collisions, Unicode headers, N:M fanout, NULLs, zero denominators, alternative metric definitions, missingness-driven reversals, decomposition mismatches, misleading LLM claims and invalid SQL. Compare **C without challenges vs D with integrity challenges** on identical cases.
- When reviewing any requirement, prefer official library docs and avoid relying on out-of-date code examples.

## Execution entrypoint

First read `objective.md`, `plan.md`, `docs/integrity-engine.md`, `docs/architecture.md`, and `docs/progress.md`. Start with **M0 only**. Treat this repository as *a specification, not a partially built program*. Do not quietly restore any source code from older QueryPilot scaffolds.
