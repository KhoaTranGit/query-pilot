# QueryPilot — Self-Auditing AI Analytics | Master Implementation Plan

**Version 2.0 · 2026-11-14 · SPECIFICATION ONLY — zero application code**

## 0. Product thesis and immutable boundaries

QueryPilot — **Self-Auditing AI Analytics** — is a dataset-agnostic, CSV-first investigative workspace. Users upload one or many unfamiliar CSVs, review inferred structure and business meaning, ask analytical questions, and receive **findings that the system actively challenges before publishing**. The engine adapts to user data, not the other way around.

**Product-defining capability: Analytical Integrity Engine.** Every material conclusion must pass deterministic reproduction plus applicable attempts to falsify it: join/fanout checks, alternative *approved or explicitly hypothetical* metric definitions, data-quality sensitivity, and contribution reconciliation. Show what survives, what changes, and what remains unknowable. A plausible LLM narrative is never proof.

**Novelty positioning:** This is a focused, reproducible implementation of existing ideas, *not* a claim to invent text-to-SQL, agentic analysis, or verification. Portfolio differentiation is measured by reduced **undetected analytical errors** on unseen data compared with simpler systems.

**Do not build:** (a) a retail-specific dashboard, (b) a simple upload-and-chat wrapper, (c) an agent that merely self-critiques with another LLM, (d) unbounded SQL execution, (e) provenance without adversarial verification, or (f) invented metrics/causal narratives.

**Critical constraints:** zero mandatory paid APIs for the deterministic core; local-first one-user MVP; strict read-only data plane; no mandatory GPU; no silent join approvals; always distinguish **reproduced arithmetic** from **robust interpretation**; gracefully abstain; documentation-only current repo.

## 1. Chosen stack and responsibilities

| Layer | Prefer | Owned responsibility | Never reimplement |
|---|---|---|---|
| Data ingestion, query engine | **DuckDB** | CSV detection, imported workspace tables, analytic SQL | CSV parser or SQL execution engine |
| Frame profiling/transforms | **Polars** (lazy where practical) | Column profile, nulls, distinctness, derived computations | General-purpose data frame engine |
| Data contracts | **Pydantic** + optional **Pandera** | Typed APIs, validation; data-quality assertions | Schema validator |
| API + async tasks | **FastAPI** + existing background job library as needed | Uploads, workspace versions, analysis lifecycle | Async scheduler or upload implementation |
| Agent controller | **LangGraph** | Bounded investigate/clarify/execute/verify workflow | Workflow state machine infrastructure |
| Model/tool adapters | **LangChain** integrations; provider via supported model API | Tool binding, retriever adapters, messages | LLM client, tool loop |
| Retrieval store | **Chroma** optional, start with metadata search; use supported BM25/FTS if needed | Index schema/semantic docs, not all data rows | Vector DB or text index |
| Embeddings | **sentence-transformers** via LangChain; optional local PyTorch dependency | Embed metadata text | Embedding encoder from scratch |
| SQL governance | **SQLGlot** plus strict database/process sandbox | Parse AST, restrict operations/tables/functions | SQL parser; security solely by prompting |
| Numeric/statistics | **DuckDB / Polars / NumPy / SciPy / statsmodels** | Recompute claims, stratify data, quantify perturbation effects, optional statistics | Numeric reasoning delegated to LLM |
| Database metadata | DuckDB + SQLite/SQLModel only if needed | User choices, contracts, query evidence | Custom database framework |
| Frontend | **Next.js or React/Vite** + TypeScript + Tailwind + shadcn/ui | Responsive application and accessible UI | Reusable UI kit |
| Charts + evidence graph | **Apache ECharts** + **React Flow** | Plots and query/evidence relationships | Hand-drawn charting / graph engine |
| Monitoring/evaluation | **pytest, Playwright, Langfuse or LangSmith (optional)** | Gold cases, regressions, traces | Evaluation/observability platform |
| Development | **uv**, standard JS package manager, GitHub Actions | Reproducible tools | Package/dependency manager |

**Selection guidance:** defer Chroma and heavy local embeddings until workspace schemas exceed deterministic search; keep provider-agnostic LLM interface; do not deploy PyTorch-based models unless needed. Libraries are selections, not a requirement to install all on day one. Verify current versions and compatibility at build time.

## 2. Architecture at a glance

### Data plane (deterministic)

**Upload → quarantine/validate → CSV sniff and preview → profile → workspace version → inferred relationships (approval gate) → semantic metric contracts (approval gate) → approved DuckDB views → constrained SQL execution → typed result tables → deterministic verification.**

### Intelligence plane

**Question → scope/answerability → retrieve approved metadata (selective RAG) → clarify if necessary → bounded LangGraph plan → SQLGlot policy check → governed DuckDB results → draft findings → adversarial audit plan → deterministic integrity checks → label each finding → evidence graph → answer/report.**

### Integrity plane (signature product layer)

**Candidate finding → recompute → challenge join assumptions → test approved/clearly labeled metric alternatives → assess missing/duplicate/outlier sensitivity → reconcile segment and total changes → publish with status or abstain.** Verification must use independent deterministic computations and versioned queries, not a second LLM agreeing with the first. The plane is reusable across domains and separately testable without an LLM.

These planes communicate through typed contracts; LLMs never gain raw arbitrary file-system paths, SQL execution privileges, or permissions to edit uploads. See `docs/architecture.md`.

## 3. Core data-agnostic invariants

1. Every CSV becomes a versioned table with immutable upload provenance and explicit physical/semantic schema; names are sanitized for SQL without losing original labels.
2. Support files regardless of domain, ordering, names, capitalization, delimiters, BOM, quoted strings, encoding (with errors reported), missing values and date formats; allow user override.
3. Table relationships are candidates, not ground truth. Evaluate uniqueness, type compatibility, overlap, NULLs and prospective cardinality/fanout. Confirm joins that matter for an answer. Never silently join on names alone.
4. Define the **grain** of each table and each measure; show multiplication risk on 1:N and N:M joins. Pre-aggregate before joins where required; log assumption and lineage.
5. Business concepts (sales, churn, conversion, “active”, margin) are user-defined or validated, not guessed from suggestive column names.
6. SQL may only read approved workspace data through explicitly allowed tables/views; deny external I/O, extension loading, DDL/DML, mutations, multiple statements and unsafe functions. Enforce OS/container limits as well as AST validation.
7. All operations carry dataset snapshot ID; changed files invalidate associated retrieval context, cached queries, metrics, and evidence.
8. Low-data or semantically unsupported requests return typed `needs_clarification`, `insufficient_data`, `unsafe_query`, or `analysis_failed`—not fabricated numbers.
9. The model describes insights; DuckDB and statistical libraries produce numbers; an independent verifier recomputes key totals, denominators, time windows and changes.
10. Every **material finding** receives a documented falsification attempt appropriate to its assumptions, or an explicit `not_tested` reason. An audit must never relabel untested work as verified.
11. Report separate statuses for `arithmetic_reproduced`, `robust_within_tested_assumptions`, `assumption_sensitive`, `unresolved`, and `blocked`; these are not probabilities of truth.
12. Tests must demonstrate unchanged shared product/agent/integrity code across unrelated benchmark datasets.

## 4. Data ingestion specification

- Upload: 1–N `.csv` (optionally `.zip` containing CSV later); configurable limits for size, row/column count, concurrent jobs, and total workspace quota. Stream upload; no full-file browser memory load; validate file signatures, nesting, path traversal and decompression bombs.
- Parsing: DuckDB `sniff_csv`/`read_csv`; support manual overrides for delimiter/header/encoding/date/type/decimal; present percentage of failed rows, examples and reason. Preserve raw file outside public directory and keep original filename/provenance.
- Profiling: row count, columns, inferred and override types, distinct/non-null ratios, frequent values, min/max, date ranges, duplicate/near-duplicate identifiers, PII-like fields (advisory), sparse/constant columns.
- Discovery: candidate semantic roles (date, entity ID, amount, category, status) using metadata and profiles with **inferred** labels. Do not transmit sensitive samples without consent.
- Store: source snapshot and approved analytical views in workspace-scoped DuckDB, metadata/versions separately; build compact searchable metadata documents for the RAG tier.
- User gate: review field types before allowing high-confidence calculations; request confirmation for ambiguous relationships and KPI meanings; allow “keep separate”.

**Failure handling:** empty CSV, malformed quote, column type mixed, duplicates headers, ambiguous locale `1,234`, timezone mismatches, missing keys, accidental PII upload; show actionable correction, no partial silent data loss.

## 5. Relationship and semantic intelligence

- Propose candidate joins from token similarity, compatible physical types, approximate key uniqueness, overlap and referential support; attach rationale and candidate cardinality.
- Show an interactive relationship graph; accept only approved edges in SQL planning. Flag possible N:M joins, duplicate keys and estimated row explosion.
- Detect table grain (transaction, line-item, customer, daily aggregate) as **candidate**; users can define primary keys, relationships and aggregate grain explicitly.
- Propose reusable metric contracts: human description, SQL definition, population filter, timestamp/timezone, grain, units/currency, safe aggregations, lineage, review status/version.
- Never infer financial concepts merely because column names include `price`, `amount`, `profit` or `revenue`. “Net revenue” requires an explicitly approved returns/discount/tax policy where relevant.
- Ambiguity example: `orders` + `order_items` + `payments` may produce payment × item multiplication. Never do a naive three-way join; aggregate payments and items to compatible order grain before approved joining.

See `docs/relationships-and-semantics.md` and `docs/data-ingestion.md`.

## 6. Agent/RAG integration

- **Small datasets:** deterministic schema/metric lookup before vector search; embeddings are not needed for one tiny schema.
- **Metadata RAG:** index dataset overview, approved data dictionary, join contracts, verified SQL examples, column roles, and past **verified** investigations. Use LangChain document loaders/retrievers and optional Chroma; metadata index namespaced by workspace and dataset version.
- **Never store all transactional rows as vectors** simply for arithmetic; query analytical data in DuckDB.
- **LangGraph:** nodes: scope → retrieve → answerability/clarification → investigative plan → SQL policy + resource gate → execute → draft findings → **select counterchecks** → deterministic integrity service → reconcile/challenge → evidence-linked narrative/report. Deterministic auditor owns verdicts; the LLM only proposes testable candidates and descriptions.
- **Budgets:** starting maximum **12 read-only SQL executions per request** (≤6 investigative + ≤6 audit), ≤2 user clarifications, ≤2 SQL repair attempts, capped result sizes and wall-clock time. Audits cannot run beyond the authorized snapshot and query budget; truncated checks produce an honest `partially_audited` state.
- **Proof:** each material claim links to query/result IDs, source snapshot, versioned metrics and the audit tests run. Causation requires a separate research design and is outside MVP.
- **Analytical memory:** only store reviewed examples, with stale-data warning and re-execution requirement.

See `docs/agent-and-rag.md`.

## 7. Investigations that work across domains

Supported patterns for first release:

- Summaries: count, sum, mean, median, proportion, distinct entities and distributions.
- Time trends: properly sorted date/time and explicitly stated time basis.
- Segment comparisons: region/product/channel/cohort if such columns exist.
- Contribution attribution: group-level contribution to additive metric deltas; note offsetting growth and denominator conventions.
- Correlations/outliers: exploratory and appropriately qualified; do not call these causal drivers.
- Data-quality analysis: missingness, duplicates, outliers, suspect keys, validity warnings.
- Follow-up analysis: preserve snapshot, filters and agreed metric definition with visible scope badges.

Defer user-facing forecasting, causal inference and optimization until separately validated. The agent must decline unsupported requests, e.g. “Why did profit decline?” with order counts only.

## 7A. Analytical Integrity Engine — mandatory product differentiator

This is a deterministic service with typed inputs/outputs and no dependency on a specific industry schema. Full contract: `docs/integrity-engine.md`.

**Per-finding challenge protocol (applicability is determined, never fabricated):**

1. **Reproduce the result.** Independently recompute numerator, denominator, aggregation grain, comparison periods, and percent versus percentage-point change; tie to source query and snapshot.
2. **Join stress test.** Inspect approved join cardinality, unmatched key rates, duplicate keys, and row/measure inflation. Where a join is uncertain, compare allowed safe alternatives or abstain; never auto-adopt a different relationship.
3. **Semantic sensitivity.** Compare only *approved* alternative metric definitions or clearly tagged hypothetical definitions (e.g., include vs exclude cancelled orders); show resulting range and ask for approval before finalizing financial interpretations.
4. **Data-quality sensitivity.** Quantify impact of duplicate rows, missingness, invalid timestamps, relevant outliers and filter/coverage shifts. Never silently alter the source or remove valid extremes.
5. **Contribution reconciliation.** For additive metrics, independently confirm category/segment deltas sum to the parent delta within an explicit tolerance; explain offsets, incomplete coverage, rounding and nonadditive metrics.
6. **Evidence-based verdict.** Assign to each claim: `arithmetic_reproduced`, `robust_within_tested_assumptions`, `assumption_sensitive`, `unresolved`, or `blocked`. List executed checks, materially changing alternatives, untested checks and next user action. There is **no synthetic confidence score**.

**Materiality:** At M0 define documented absolute/relative tolerances per metric type; never use one arbitrary percent across currencies, counts and rates. Use explicit denominator safeguards and state when a meaningful sensitivity test is infeasible.

**Example:** “Sales fell 12%” may be reproducible under an approved gross-sales contract, but differ materially if cancelled orders are excluded. It must remain *assumption-sensitive* until that business definition is resolved; it is not ‘verified’ merely because the SQL executed.

**Required artifact — Integrity Report:** original finding; measure definition/version; snapshot and query IDs; baseline result; checks table (method, tested assumption, numerical effect, pass/fail/not-applicable); unresolved issues; final verdict; reproducibility details. Users can inspect it via an approachable “How this was checked” card rather than a dense default dashboard.

**Acceptance trap tests:** misleading 1:N/N:M joins, wrong metric filters, missing-data-driven trend reversal, period misalignment, offsetting segments, nonadditive rate decompositions, and plausible-looking but unsupported conclusions. The engine must detect or explicitly abstain for each curated trap; falsification is not a guarantee of discovering every error.

## 8. Product UX and visual requirements

Follow `ui.png` and `docs/ui-spec.md`; treat the image as **concept art with fictional figures**. Primary desktop navigation: Workspaces, Data Sources, Data Studio, Investigations, Reports, Settings. Core page states:

- Workspace setup: drag-drop files, preview, detected types, upload limits, delete.
- Data Studio: graph of tables and suggested relationships, key/cardinality badges, field-profile drawers, metric approvals.
- Investigation: persistent question bar, scoped files/metrics, meaningful progress trail, a simple chart and an **Integrity Summary** with `reproduced / robust within tested assumptions / sensitive / unresolved` language; reveal SQL, assumptions and audit checks via side panel.
- Report: export summaries and source/caveat manifest; default aggregate-only.

**Approved UI refresh (2026-11-14):** prioritize an approachable, breathable workspace over a dense enterprise dashboard. Use a warm tinted canvas rather than a stark white page; generous whitespace, fewer visible panels, soft restrained colors, a friendly onboarding illustration, and a focused natural-language question area. The initial result view should show **one dominant, easily interpreted chart plus one compact evidence/finding card**. Put relationships, SQL, metric approvals, and complex provenance in dedicated views or on-demand drawers (progressive disclosure), without losing discoverability or auditability. Avoid permanently visible grids of KPI cards, unexplained graphics, excessive badges, and multiple competing CTAs.

Follow `docs/ui-spec.md` for responsive layouts, design proportions, accessible chart alternatives, honest states, and user-centered QA acceptance. The reference image is illustrative and must never result in hard-coded uploaded-file counts, retail KPIs, or fake verified status in the running UI.

## 9. Evaluation plan and anti-shortcut checks

**Dataset trials:**

1. Multi-CSV e-commerce (Olist) — join discovery, table grain, aggregate payments, translation table; external data download requires user respecting license.
2. Hourly transport demand (UCI Bike Sharing `hour.csv`) — time-series aggregation, normalized weather inputs, no default join to daily rollups.
3. Bank marketing (UCI Bank Marketing `bank-full.csv`) — semicolon delimiter, categorical encodings, class imbalance, misleading `duration` for pre-call prediction.
4. Adversarial CSV fixtures created **during implementation** — renamed headers, duplicate keys, blank columns, quoted delimiters, mixed types, non-ASCII, malicious payloads, incorrect inferred associations.

**Metrics:** ingestion success; inferred relation precision/abstention; SQL validity; numerical correctness; **undetected material-error rate** (incorrect claims published as reliable), error-detection recall/false-alarm rate, sensitivity-test coverage, appropriate abstention, evidence coverage, retrieval recall, latency, SQL calls and LLM cost. Report counts and denominators, not only percentages.

**Ablations:** (A) direct text-to-SQL, (B) metadata-RAG SQL, (C) bounded investigative agent + arithmetic verification, **(D) full Self-Auditing QueryPilot with integrity challenges**. Hold input snapshots, questions and model settings fixed; document compute/query-budget differences. Report whether D reduces undetected errors compared with C, including extra cost and false alarms. No invented performance charts.

See `docs/evaluation.md` and `evals/benchmark-spec.md`.

## 10. Phased milestones: strictly sequential

### M0 — Documentation, skeleton, scope confirmation

**Input:** This documentation-only repo. **Build later:** choose exact package versions, create Python/TS development scaffold, local development scripts, CI baseline and manual architecture review. **Gate:** README runs clean, design consistency review, security review, empty/default state works, no dataset-specific code in shared layers. Do not claim this gate passed today.

### M1 — Dataset-agnostic upload and profile, NO LLM

Implement streamed multi-CSV import, detected dialects/types, immutable workspace versions, preview, data-quality statistics, recoverable parse failures. **Gate:** all three externally obtained dataset families ingest without hard-coded column names; messy fixture error states tested; per-workspace isolation and privacy checks pass.

### M2 — Relationship Studio and semantic contracts, NO LLM required

Implement candidate joins, uniqueness/cardinality checks, interactive graph, user approval/reject, metric-definition creation and grain policy. **Gate:** fanout traps are flagged and blocked; approved-only joins; metric versions pinned; wrong joins cannot produce published verified claims.

### M3 — Governed SQL, arithmetic verifier and independent integrity checks, NO LLM REQUIRED

Implement SQLGlot AST inspection, isolated read-only DuckDB execution, approved identifier/function/operation policy, query/result lineage and the **deterministic Analytical Integrity Engine** (`docs/integrity-engine.md`). Implement all applicable challenge categories with transparent `not_applicable`/`not_tested` states, typed Integrity Reports, configurable materiality thresholds, and reproducible test fixtures. **Gate:** unsafe SQL blocked by layered controls; numeric results reproducible; curated fanout/definition/missingness/decomposition traps detected or explicitly flagged; false-green verdicts rejected; no source-file read through SQL.

### M4 — Retrieval + LangGraph investigator and falsification planner

Implement LangChain schema/metric retrieval and optional Chroma; use bounded LangGraph to propose investigations, produce candidate findings, **request allowed counterchecks**, and call the independent M3 auditor before synthesis. Audits consume a reserved portion of the total query budget. **Gate:** cross-domain gold cases show no retail special cases; unsafe/unanswerable requests refused; all material claims have audit status and evidence; the model cannot overwrite an auditor verdict or present partial audits as robust.

### M5 — Polished interactive workspace, integrity report and exports

Implement the approved calm UI in `ui.png` and `docs/ui-spec.md`: upload/onboarding, airy overview, one clear primary chart, small plain-language **“How this was checked”** card, context-aware relationship editor, integrity challenge detail, SQL/evidence drawer, and reproducible reports. **Gate:** reviewer can distinguish reproduced from assumption-sensitive claims in seconds; no green ‘verified’ badge for merely executed SQL; screenshots remain breathable; controls accessible; exports preserve baseline, alternatives, checks, assumptions and audit verdict.

### M6 — Cross-domain benchmark, ablations and public portfolio demo

Create verified gold answers and **known wrong-but-plausible counterexamples**; run A/B/C/D ablations on fixed unseen schemas and measure undetected errors, error-detection recall, false alarms, abstention, accuracy, latency and cost. **Gate:** report D-versus-C effect with denominators and confidence intervals when feasible; meet `objective.md` targets or disclose gaps; safety checks pass; demo works on unfamiliar files without edits to shared code.

### M7 (optional) — Constrained scenarios, saved semantic memory, extra file types

Only after M6: forecasting with time-aware validation, assumption-based what-if calculations, more data formats, collaborative workspaces, external warehouses. **Gate:** explicit model/assumptions and uncertainty; no unverified prescriptive financial advice.

## 11. Completion / handoff rules

At each milestone OpenCode must: (1) list files changed and library usage, (2) state tested requirements, (3) run and log real checks, (4) log exceptions in `docs/progress.md`, (5) stop if a gate fails. Never claim a feature exists because it appears in the plan or mockup. Use only documented source URLs from `datasets/catalog.md`; never commit third-party datasets to this repository. For new libraries or datasets, verify maintainers, licenses and current docs.

## 12. Explicit unresolved decisions

- Choose which local/provider LLM to support first and whether to offer an offline stub mode by default.
- Set concrete upload limits, query audit budgets, and deployment isolation based on host capacity.
- Choose local persistence layout and semantic-profile approvals UX.
- Decide whether metadata retrieval needs Chroma at MVP scale; start deterministic, measure retrieval recall first.
- Define metric-specific materiality thresholds and adjudication process for an `assumption_sensitive` verdict.
- Confirm public demo privacy policy and report export defaults.

Record choices in `docs/decisions.md` as part of M0. No question blocks reading this repo or starting M0.
