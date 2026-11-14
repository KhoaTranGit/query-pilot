# Relationship inference and semantic metric contracts

## Relationship candidates are not facts

Score candidate joins using column semantic similarity, type compatibility, uniqueness, overlap of sampled values, null frequency, name similarity and expected multiplicity. Use deterministic statistics first, LLM only to explain/rank candidate options. Display suggested direction, key pairs, potential null loss, cardinality, estimated row-count change, duplicate impact and example edge cases.

## Approval gates

- Require approval before a proposed join is eligible for agent SQL.
- Block unsafe many-to-many combinations by default; present a path to aggregate to a common grain or use user-certified bridge tables.
- Joined measures must be compatible with requested grain. Avoid summing the same order payment over multiple order lines.
- Present 1:1, 1:N, N:1 and N:M relationship labels with measured counts, not arbitrary percentages masquerading as confidence.
- Invalidation: changing relationship definitions invalidates prior derived metric claims and future agent cache for the affected workspace version.

## Metric contracts

Each metric: stable ID; display name; business definition; source tables; field expressions; filters; approved joins; grain; aggregations; unit/currency; timestamp and timezone basis; handling of missing/duplicate/refund/cancellation values; approval owner, status, version, and lineage. QueryPilot may propose metric definitions but must not assert they are company policy.

## Clarification examples

- `price` may mean list price, net price or unit price. Ask rather than label it net revenue.
- `status = delivered` might be a reasonable completed order candidate, but must be explicitly confirmed.
- A daily aggregate and hourly aggregate are alternative grains: never casually join them and sum both.
- Conversion rate requires exposure/visitor denominators; do not calculate it from orders alone.

## Worked joins example (conceptual, not executable SQL)

An e-commerce dataset contains order headers (one/order), items (many/order), payments (many/order). The naive header-to-items-to-payments join multiplies both sides. Correct design first aggregates items to order grain, aggregates payments separately to order grain, then combines at order grain. Require reconciliation against independent source totals and show discrepancies when present.
