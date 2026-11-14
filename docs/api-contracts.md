# Planned API contracts — specification only

Every request/response must be modeled using Pydantic. Responses carry stable `workspace_id`, `snapshot_id` where relevant, status and error codes. Version API schemas and never expose local absolute paths.

## Endpoint sketch (not implemented)

| Resource | Proposed operations | Behavior |
|---|---|---|
| Workspaces | Create/list/get/delete | Isolate inputs and investigations |
| Uploads | Multi-file upload, status, retry | Stream, validate, profile; error by file |
| Tables | List/profile/preview | Respect privacy and row cap |
| Relationships | List proposals, approve/reject/edit | Recompute impacted summaries on changes |
| Metrics | List/propose/approve/revise | Pin versioned grain, units, filters |
| Investigations | Create/run/status/cancel | Bounded async job with structured events |
| Evidence | Retrieve query/result/claim lineage | Only authorized read-only provenance |
| Integrity reports | Retrieve per-finding checks, alternate results and verdicts | No phantom passes; stable snapshot references |
| Reports | Export/list | Redact raw sensitive rows by default |

## Typed output models

Upload: files and per-file parse success/warnings. Profile: field roles, nulls, samples and warnings. Relationship: candidate keys, direction, cardinality, fanout measure, approval status. Metric: formula/filters, grain, units, snapshot lineage, approval status. Investigation: question, scope, plan steps, SQL execution references, finding claim IDs, status and limitations. Evidence: query/result IDs, aggregates, proof checks and timestamp. Integrity report: claim ID, snapshot/metric versions, baseline result, typed checks with applicability/execution status, materiality thresholds, alternative results, status and untested limitations.

## Contract rules

Never map user column names directly to API paths without encoding. Revisions are immutable or versioned. Long-running tasks support cancellation and bounded retry; stream progress only from real states. HTTP failure modes align with `docs/agent-and-rag.md` error taxonomy.
