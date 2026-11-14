# Scientific evaluation plan

## Experiments

Compare A) direct text-to-SQL, B) metadata-RAG text-to-SQL, C) governed investigator with arithmetic verification, and **D) full governed investigator plus deterministic Analytical Integrity Engine**. Use the same datasets/questions/model settings; document any unequal tool budgets, latency and cost rather than hiding them.

## Primary metrics

- Ingestion pass rate across clean/messy CSVs; data-type and quote/delimiter correctness.
- Relationship inference precision and abstention accuracy; approved join cardinality safety; metric definition correctness.
- Executable SQL ratio vs **true** answer accuracy (not the same thing).
- Numeric accuracy with documented tolerance and units; valid claim-to-query evidence links; unsupported assertion rate.
- **Primary differentiator:** undetected material analytical error rate = materially wrong findings presented as reliable / materially wrong candidate findings; report denominator, precision/recall for caught errors, false alarms, check coverage, appropriate abstentions and sensitivity-driven changed conclusions.
- Answerability: accurate refusal on missing data, unapproved joins and undefined metrics.
- Completion time, token usage, per-run monetary cost, steps/queries, error recovery rate.
- User experience: time to correctly approve semantics and find the supporting SQL.

## Split design

Separate datasets by domain (retail, bike sharing, marketing). Include withheld renamed-column variants and zero-shot unseen schemas. Keep gold reference SQL/logic from test generation out of RAG index. Stratify simple, join, aggregate, time, ambiguous, unanswerable, malicious. Do not use test questions as agent prompt demonstrations.

## Acceptance targets (not measured)

Target >=90% safe SQL validity; >=90% numeric accuracy on answerable gold cases; **>=25% relative reduction in undetected material errors for D versus C** (hypothesis, not expected result); 100% rejection in enumerated write/path-read security probes; 100% material published claims evidence-linked; >=95% check coverage including explicit not-tested reasons; 0 *known* unsafe joins approved automatically. These are directional targets requiring actual evaluation and transparent reporting.

## Reporting

Ship an evaluation report listing denominator, error cases, dataset license, hardware, model/provider/version, sample size, prices, and bootstrap confidence intervals where practical. No fabricated accuracy plots or claims about production reliability.
