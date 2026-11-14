# Analytical Integrity Engine — Mandatory Technical Contract

**Status:** specification only. This module is the product differentiator and must work without an LLM. It audits claims; it cannot guarantee truth or discover every hidden error.

## Goal and input/output contracts

Input: dataset `snapshot_id`, approved table/grain/join contracts, approved metric definition/version, user question and scope, candidate finding, governing query/result IDs, applicable sensitivity-test constraints and compute/query budget.

Output: an immutable **Integrity Report** containing `claim_id`, baseline value and units, calculated numerator/denominator, snapshot/definitions, all check IDs, alternative values, materiality decisions, unchecked limitations, and a final status. Each check has `status` (`pass`, `fail`, `not_applicable`, `not_tested`, `blocked`), why, and source/query references.

## Required challenge families

| Challenge | Deterministic method | Guardrail |
|---|---|---|
| Arithmetic + time | Independently compute numerators, denominators, date boundaries, sign, ratios and percent vs pp | No division by zero, silent timezone or period drift |
| Join/grain robustness | Inspect uniqueness, cardinality, unmatched keys and fanout; compare only safe, approved alternatives | No auto-approval of inferred joins; cannot call unsafe join 'valid' |
| Metric/semantic sensitivity | Re-run **approved** definitions; simulate distinct hypothetical definitions only with visible labeling | Never claim a hypothetical definition is official or choose one without human review |
| Data-quality sensitivity | Measure impact of duplicates, null handling, anomalous timestamps and predeclared reasonable exclusions | No silent destructive cleaning or arbitrary deletion of valid outliers |
| Contribution reconciliation | Compare sum of additive segment deltas to total delta with rounding tolerance and coverage flag | Never sum percentages, averages, rates or overlapping groups as if additive |
| Evidence linkage | Check snapshot IDs, results, metric versions, SQL metadata and that the narrative numbers appear in results | A cited SQL statement alone is not correctness proof |

A test may be marked `not_applicable` **only** with justification. A missing or budget-exceeded applicable check is `not_tested` and may prevent a robust verdict.

## Claim verdict taxonomy

- **`arithmetic_reproduced`:** computation reproduces under its approved selected definition; no broader stability implied.
- **`robust_within_tested_assumptions`:** reproduced, and every **applicable critical** challenge passed for the tested, disclosed alternatives. The label never implies universally correct or causally proven.
- **`assumption_sensitive`:** at least one reasonable explicitly approved or clearly hypothetical alternative yields a *materially* changed result; show variants and request semantics confirmation if necessary.
- **`unresolved`:** material missing definitions, unavailable alternatives, test budget or inadequate evidence prevents robustness judgment.
- **`blocked`:** unsafe, invalid, contradicted or unreproducible claim must not be published as a finding.

`arithmetic_reproduced` and `robust_within_tested_assumptions` are **different milestones** in verification. The UI may show the former without suggesting the latter.

## Materiality policy

M0 must define per-metric thresholds with both absolute and relative tolerances, units, rounding, and division-by-zero handling, with a rationale. No universal 5% cutoff. Findings cannot claim 'negligible' sensitivity until a threshold and evidence are available. Record test coverage, not an invented confidence percentage.

## Sample challenge workflow (illustrative only)

User asks 'Why did order revenue fall?' with orders, items and payments CSVs. The candidate SQL returns a −12% change. Independent total reconciliation agrees under approved gross-sales definition. A cardinality audit finds that a naive items × payments join would double-count. The engine blocks that join and recomputes using safe order-level aggregates. An alternative visibly labeled 'exclude cancelled' metric changes the result materially. Conclusion: a decline exists for the approved gross-sales measure, but the magnitude is **assumption-sensitive**; ask for metric policy. Never pretend the hypothetical alternative is approved.

## Fail-safe execution

All audit SQL passes the same SQLGlot policy and constrained read-only worker as investigation SQL. The audit worker may only query the pinned snapshot and approved/explicitly hypothetical views constructed by deterministic code. Do not grant the model arbitrary Python, files, network, extension loading or DB writes. Reserve query/time budget for checks; on exhaustion return `unresolved` or `partially_audited`, never a false pass.

## Testable acceptance cases

- 1:N and N:M row inflation, overlapping group totals, missing key coverage, mismatched date windows and invalid denominators.
- Excluding cancelled records changes headline result; duplicate/outlier handling changes trend direction; definition mismatch changes AOV.
- Aggregate contribution sum fails to equal total; rates/percentages inappropriately added.
- A model states a number absent from tool results, or sources are from a stale dataset snapshot.
- No data/definition needed for question exists; critical audit times out.

Expected: catch error or explicitly abstain, preserve original evidence, and never publish a robust green verdict. See `docs/evaluation.md` and `evals/benchmark-spec.md`.
