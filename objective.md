# QueryPilot — Self-Auditing AI Analytics | Product Objective

**Version:** 2.0 · **Updated:** 2026-11-14 · **Status:** specification only, no application code

## One-sentence product

A **dataset-agnostic, CSV-first AI analytics investigator** that not only answers questions using governed SQL but **independently challenges its own conclusions** for join errors, ambiguous metric definitions, data-quality distortions and incomplete numerical reconciliation before presenting them as trustworthy.

## The problem

Conventional upload-and-chat analytics can produce convincing but incorrect business explanations: joins multiply records, a metric excludes the wrong statuses, missing values distort trends, or subgroup changes do not reconcile with the headline. Displaying SQL is useful but does **not** prove that the analytical interpretation is reliable.

## Our differentiation (carefully scoped)

**The Analytical Integrity Engine is the flagship**, not CSV upload, text-to-SQL, RAG, multi-agent orchestration or the dashboard. It generates a *challenge record* for each material finding: what was measured, what was rechecked, which plausible approved assumptions were tested, how much the answer changed, and what the user should trust or clarify. These techniques are not individually research-novel; differentiation must be demonstrated through reliability benchmarks against baselines.

## Who uses it

- **Business/operations analyst:** discover why an operational measure changed without unknowingly double-counting data.
- **Manager:** read a concise finding and understand whether it is stable enough to act on, or depends on unresolved assumptions.
- **Data scientist:** inspect calculations, approved joins, test variants, evidence lineage and reproducibility.

## Core user promise

**Upload unfamiliar CSVs → confirm meaning where needed → ask a question → see both the result and how QueryPilot tried to disprove it.** No hard-coded industry schema, table names, preconfigured datasets or source-code edits. Support both single and multiple CSV files.

Data-agnostic does **not** mean omniscient: the system must not infer missing business policy or required fields as facts. Users must be able to approve/edit join and metric semantics. Unsupported questions return an explicit limitation or clarification, never a fabricated analysis.

## Required end-to-end experience

1. **Upload:** Isolate and version an arbitrary set of CSVs with source provenance, resource limits and privacy guidance.
2. **Understand:** Profile columns, types, nulls, possible keys/grain, dates, duplicates and quality risks; allow correction.
3. **Connect:** Suggest relationships with key overlap/cardinality/fanout diagnostics; require approval before material joins.
4. **Define:** Propose domain-neutral measures, units, filters and grain; require explicit approval for ambiguous financial or operational definitions.
5. **Investigate:** Use metadata retrieval + bounded LangGraph tooling to query approved DuckDB views, compare periods/segments and draft evidence-backed findings.
6. **Challenge:** Run independent deterministic integrity tests for arithmetic, join sensitivity, approved/hypothetical metric variants, missingness/duplicate/outlier effects, and additive contribution reconciliation **where applicable**.
7. **Decide what can be said:** Clearly label each finding as `arithmetic_reproduced`, `robust_within_tested_assumptions`, `assumption_sensitive`, `unresolved` or `blocked`. Avoid numerical 'AI confidence' scores and unsupported causal assertions.
8. **Explain and export:** Present one understandable chart, a concise **How this was checked** summary, and expandable SQL, test outcomes, alternative results, snapshot IDs and caveats in a reproducible Integrity Report.

## Mandatory MVP scope

- Arbitrary single- and multi-CSV workspaces with dynamic schema discovery; no domain-specific business logic in shared services.
- Profile/review of keys, physical types, grain, join risk and metric meaning; no silent N:M joins.
- Read-only, workspace-isolated DuckDB with SQLGlot policy enforcement **plus** OS/process-level limits; no model-generated arbitrary file/network access.
- Deterministic metric computation and a separately testable **Analytical Integrity Engine** that can run with **no LLM configured**.
- Retrieval of relevant workspace metadata and approved definitions, bounded tool-enabled LangGraph investigations (not embeddings of transaction rows).
- Per-claim provenance: dataset fingerprint, approved schema/metric versions, SQL/query IDs, result cells, check IDs, assumptions, verdicts and untested checks.
- Truthful refusal/clarification when values or definitions are missing, results are unsafe, or material checks cannot complete.
- A warm, spacious, accessible UI consistent with existing `ui.png`; start with a simple chart and readable integrity summary, then progressive disclosure for technical details.
- Cross-domain benchmark and adversarial integrity tests; no retrospective change to the application to accommodate a benchmark dataset.

## Explicit non-goals for MVP

- Claiming novel research, universal analytical correctness, causal discovery or infallible error detection.
- Automatically approving joins/metric definitions; inventing revenue, cost, refund, profit or currency semantics.
- Full causal inference, unrestricted forecasting, optimization or speculative what-if recommendations.
- Auto-editing source CSVs, sending emails, paying invoices, controlling live business systems or unrestricted execution.
- Training foundation models; writing homegrown CSV parsers, SQL engines, vector DBs, LangGraph equivalents, numeric libraries or charting frameworks.
- Enterprise authentication, multi-tenant SaaS, connectors beyond CSV, or mandatory large local models.

## Product-level integrity standard

**Arithmetic validation is not the same as interpretive robustness.** A claim may reproduce under one selected definition yet change materially with plausible alternatives. The system must reveal this, not silently award a green check. An auditor must attempt the checks relevant to the question and show when a check is impossible, unapproved, irrelevant or budget-constrained. Only versioned, approved definitions can support final governed business metrics; hypothetical alternatives remain labeled scenarios.

Audits are deterministic, bounded and reproducible. The LLM can suggest what to investigate but cannot approve an audit, invent a completed check, or change a verifier verdict. Statistical associations are described as associations; attribution of an additive change is not proof of causal mechanism.

## Planned success criteria — targets, **not measured results**

| Goal | Proposed acceptance evidence |
|---|---|
| Domain independence | Same unchanged core processes **3 unrelated datasets**, including a multi-table case and held-out renamed schemas |
| Query correctness | ≥90% correct numeric answers on answerable human-verified questions; valid permitted SQL measured separately |
| Integrity effectiveness | **≥25% relative reduction in undetected material analytical errors** for full system D versus investigator C on curated adversarial gold cases; report false alarms, denominators and uncertainty |
| Challenge coverage | ≥95% of material published findings have applicable checks executed **or an explicit not-tested reason**; no 'robust' verdict if critical checks failed/skipped |
| Security | 0 successful unauthorized writes/external reads in the defined adversarial probe suite; disclose scope of suite, not universal security |
| Explainability | 100% of published material numerical claims link to dataset, definition, query, result and audit outcome |
| Honesty | 100% of clearly unanswerable/unsafe curated cases refuse, ask for missing semantics, or stop with a typed reason |
| Usability | New user can reach an answer or a concrete limitation on a new dataset without editing code; audit details accessible without front-page clutter |

The engineering target for D-versus-C differentiation is **a goal to test**, not a predicted or guaranteed advantage. If missed, document exactly why and report results honestly.

## Definition of done

On a previously unseen domain dataset, an unfamiliar user can upload files, resolve important semantics, investigate a question and inspect a result **alongside systematic attempts to falsify it**. The application can demonstrate why some answers remain stable and why others cannot responsibly be trusted. No implementation or benchmarks are completed in this repository.
