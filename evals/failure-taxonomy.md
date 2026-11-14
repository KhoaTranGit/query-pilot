# Failure taxonomy and required counterexamples

- Input: delimiter/encoding/header confusion, truncation, bad quoting, malformed type, unsafe filename.
- Join: wrong key, missing foreign key, N:M fanout, duplicate key, asymmetric null loss, wrong grain.
- Semantics: invented revenue definition, wrong unit/currency, missing denominator, undefined time scope, ambiguous status.
- Query: wrong dialect, alias clash, blocked external reads, unbounded scan, unsafe function, nested SQL bypass.
- Analytics: wrong comparison period, arithmetic hallucination, percent-vs-percentage-point error, spurious causation, invented confidence.
- Integrity: reproduced arithmetic mislabeled robust, untested check reported pass, metric sensitivity hidden, materiality threshold absent, bad additive reconciliation, audit budget exhaustion marked success.
- Retrieval: relevant schema omitted, unrelated workspace retrieved, stale snapshots, examples falsely treated as metrics.
- Agent: infinite retries, hidden tool call, wrong result reused, unreported budget exhaustion.
- Privacy: raw PII in logs or LLM requests, row leakage in exports, cross-workspace content.
- UX: false 'verified' indicators, fabricated chart values, inaccessible drawer or keyboard graph.

Every discovered failure receives minimal reproduction, severity, affected benchmark cases, fix, and passing regression evidence.
