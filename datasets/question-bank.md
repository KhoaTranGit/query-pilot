# Seed questions for a cross-domain gold set (answers not prefilled)

Create independent reference queries and calculations **after downloading data**. The questions below are benchmark *ideas*; some are intentionally unanswerable and should trigger clarifications or refusal.

## Retail / Olist

- Which product categories contributed the most to quarter-over-quarter change in order-item gross amount?
- How does late delivery share vary by state and month? Define lateness against estimated delivery date.
- How many unique orders are represented in item and payment tables? What join grain prevents duplicate counting?
- What was the company's actual net profit? **Expected**: not answerable without additional cost/refund policies/data.

## Transport / UCI Bike Sharing

- How does hourly ridership vary by weekday versus weekend?
- Compare total counts across seasons and explain the impact of normalized weather field interpretation.
- Do hourly and daily aggregates reconcile? Do not sum both into one total.
- Did a new marketing promotion cause an increase in rides? **Expected**: no experiment/promotion data.

## Marketing / UCI Bank Marketing

- What is the subscription proportion by campaign contact category?
- Which cohorts have the highest observed subscription rates, with denominators?
- What is the financial ROI of the campaigns? **Expected**: missing revenue/cost values.
- Can call duration be used as a feature available before the call? **Expected**: no.

## Unknown-schema resilience

Rename all tables and all columns while preserving source contents. Upload in different order. Ensure manual correction of one join is honored. Ask unrelated questions and require principled refusal, not hallucinated SQL.
