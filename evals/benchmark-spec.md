# Golden benchmark specification (no executable test code)

## Case schema

Each case records: case ID, dataset+version, uploaded file set, question, authorized tables, relevant time basis, required metric contract, optional expected clarification, expected join policy, approved reference computation, numerical output with tolerance and unit, expected evidence references, answerability label, risk class and notes.

## Design and scoring

Target at least 60 curated questions split across three domains; 20% intentionally missing/ambiguous, 20% join/grain errors, 20% timeseries, remainder aggregates/segments. A human reviewer must verify ground truth. Score SQL validity separately from correctness, explanation faithfulness separately from chart quality. Add curated plausible-but-wrong claims for fanout, ambiguous semantics, missingness reversal, time-window drift, and decomposition mismatch. Record error detection, false alarm and **undetected material error** rates separately. Record bootstrap confidence intervals where practical. Keep gold calculations isolated from retrieval documents.

## Baseline arms

A plain text-to-SQL; B same model with retrieved metadata; C bounded governed investigation plus arithmetic verification; **D=C plus the deterministic integrity challenge engine**. Preserve the same answerable/unanswerable gold cases and model settings. Equalize budgets where feasible, or separately report added query calls, latency and costs.

## Acceptance and stop conditions

Zero unauthorized writes/path access in enumerated security probes. Known N:M joins require approval or refusal. Unsafe generated SQL cannot reach execution. Numeric claims must reconcile to stored evidence. Never obscure partial completion.
