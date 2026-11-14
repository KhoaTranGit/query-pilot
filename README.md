# QueryPilot — Self-Auditing AI Analytics

**Status: PLAN-ONLY REPOSITORY · UI refreshed 2026-11-14 · NO APPLICATION CODE**

QueryPilot is a dataset-agnostic analytics workspace with a **self-auditing integrity engine**. Users upload unfamiliar CSVs, approve ambiguous joins/metrics, ask business questions, and receive findings that are independently challenged for join fanout, alternate approved definitions, data-quality sensitivity and numerical reconciliation—plus a reproducible evidence trail.

> Design principle: the application adapts to the dataset; the application must **not invent the dataset's meaning**. “Data agnostic” is an architectural goal, not a promise that every arbitrary file supports every question.

## What you receive

- `objective.md` — product intent, capabilities, scope, non-goals, and measurable outcomes.
- `plan.md` — **authoritative** incremental implementation contract for OpenCode, milestones, gates, dependency choices and tests.
- `AGENTS.md` — strict, persistent OpenCode instructions.
- `ui.png` — **refreshed calm, welcoming UI mockup** with warm tinted canvas, more breathing room, a simple chart, and compact investigation evidence (all example figures are fictional).
- `docs/` — architecture, data onboarding, relation inference, semantics, RAG, agent design, **integrity-engine contract**, security, UI interactions, evaluation, API contracts, and decisions.
- `datasets/` — verified download sources, licensing notes, scenario questions, and evaluation design.
- `evals/` — golden-set specification, **integrity traps and A/B/C/D ablations**, failure taxonomy (**specifications only**).
- `prompts/` — design guidance for future prompt templates (**not executable prompt implementations**).
- `backend/`, `frontend/`, `tests/`, `data/`, `reports/`, `artifacts/` — empty **future development** areas, with `.gitkeep` only.

**No `.py`, `.ts`, `.tsx`, `.js`, `.json`, `.yaml`, `.toml`, `.sql`, generated samples, app code, or dependency lockfiles are included.** The source-code directories are intentionally empty. This is a specification handoff, not a runnable app.

## UI design direction

The revised `ui.png` is the **approved visual starting point**: airy layout, soft warm background, friendly onboarding, one dominant understandable chart, and optional technical depth. Follow `docs/ui-spec.md` rather than restoring the previous dense dashboard. Data profile, approvals, SQL and evidence remain core features but are revealed progressively. The illustration is not a functioning application or a source of real computed data.

## Product journey

1. Create a workspace and upload 1–N CSV files (no fixed names, domain, or predetermined tables).
2. Review inferred file formats, column types, data profiling and quality warnings.
3. Inspect *proposed* joins and semantic metrics; approve or correct anything ambiguous.
4. Ask a question such as “Which segments explain the decline in orders this quarter?”
5. QueryPilot retrieves workspace metadata, plans bounded analyses, executes constrained read-only SQL, and verifies arithmetic.
6. Inspect the **How this was checked** card: reproduced calculations, meaningful alternative results, audit findings, and checks not yet completed.
7. Explore the evidence graph, query/result references, SQL, data limitations and downloadable **Integrity Report**; receive a precise refusal if required information is missing.

## Suggested reading order for OpenCode

1. `AGENTS.md` → `objective.md` → `plan.md`
2. `docs/architecture.md` → `docs/data-ingestion.md` → `docs/relationships-and-semantics.md`
3. `docs/security-and-privacy.md` → `docs/integrity-engine.md` → `docs/agent-and-rag.md` → `docs/evaluation.md`
4. `docs/ui-spec.md` and `ui.png` → `docs/progress.md`

## First OpenCode request

> Read AGENTS.md, objective.md, plan.md, and docs/integrity-engine.md. Begin **only Milestone 0**. Treat self-auditing as the signature requirement; implement deterministic integrity checks before the LLM workflow, using established libraries. Do not hard-code datasets, claim an audit passed without executing checks, or advance a milestone until its gate passes.

## Public data and attribution

See `datasets/catalog.md`. Suggested external tests: Olist Brazilian E-Commerce (Kaggle; CC BY-NC-SA 4.0; download may need a Kaggle account), UCI Bike Sharing (CC BY 4.0), UCI Bank Marketing (CC BY 4.0). None of their data files are redistributed here. Respect source terms.

## Project boundary

The repo contains **no runtime functionality** today. The UI image is an aspirational design rather than a tested application screenshot. The metrics and SQL illustrated in the image are fictional design examples, not calculations on the listed datasets.
