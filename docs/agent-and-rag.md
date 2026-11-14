# LLM/RAG and bounded self-auditing investigation

## Goal

Retrieve **metadata and governed business definitions**, not arbitrary raw transactional rows. Produce bounded reproducible analytical evidence instead of a plausible-sounding chat response.

## Retrieval stages

1. Deterministic registry lookup for matched table/column identifiers, approved joins, metric names and versions.
2. BM25 / vector search only where useful for verbose workspace descriptions or reviewed examples. LangChain retrievers and optional Chroma provide the index; do not implement retrieval from scratch. Namespace all documents by workspace and snapshot.
3. Optional reranking via a library-backed cross-encoder model if retrieval benchmarks justify it.
4. Source filters: no cross-workspace retrieval; verified exemplars only; invalidate stale docs when snapshot changes.

## LangGraph node outline

User question -> determine scope/time -> retrieve approved metadata -> answerability -> (clarify if needed) -> decompose -> generate SQL -> policy check -> bounded investigation -> draft findings -> propose relevant counterchecks -> call **deterministic Analytical Integrity Engine** -> label claim status -> evidence-linked synthesis -> persist Integrity Report. A failure or exhausted budget enters an explicit safe-stop state.

## Tools (proposed)

`list_tables`, `describe_table`, `get_metric_contract`, `get_approved_joins`, `query_approved_sql`, `profile_result`, `compare_groups`, `run_integrity_audit`, `get_integrity_report`, `get_evidence`, `save_report`. Every tool has a typed request/response contract and verifies workspace-scoped permissions. The model may not freely access filesystem, credentials, networks, Python REPL or unapproved DB tables.

## Explicit budgets

Initial target <=12 total SQL executions (<=6 investigative and <=6 integrity checks), <=2 SQL repair attempts, <=2 user clarifications, a fixed server-side execution deadline, capped data return sizes and token budgets. Report when analysis stops due to budget. Agent memory and report state require the exact data snapshot; do not let earlier analysis be treated as current after data changes.

## Claim verifier

Numerical findings must be recomputed (DuckDB/Polars), cross-check totals and attached source/query/metric IDs, then challenged via the test families in `docs/integrity-engine.md`. The deterministic auditor returns `arithmetic_reproduced`, `robust_within_tested_assumptions`, `assumption_sensitive`, `unresolved` or `blocked`; the LLM cannot override this. No claim of causation or universal correctness. If narrative conflicts, block or regenerate it; no second-model-only proof.

## Unanswerable cases

Return typed `insufficient_data`, `ambiguous_metric`, `unapproved_relationship`, `unsafe_query`, `timeout`, `budget_exceeded`, `no_evidence`, or `partially_answered`, with clear next steps and never fabricated assumptions.
