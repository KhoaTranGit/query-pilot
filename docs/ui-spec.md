# QueryPilot UI specification — calm, friendly, evidence-first

**Design reference:** `../ui.png` (approved concept refresh, 2026-11-14). **Product identity:** QueryPilot — Self-Auditing AI Analytics. This is illustrative artwork only: any metric, file count, progress status, name, or investigation result in the image is *fictional*, not a completed feature or computed result. The running product must derive all displays from the actual workspace state.

## Experience goal

A first-time user should understand what to do in seconds: **bring files → understand the data → ask a question → inspect the answer and its evidence.** Treat QueryPilot as an approachable data workbench, not a dense enterprise monitoring dashboard. The app must remain suitable for serious analytical work without exposing all of its complexity at once.

## Design direction: the visual hierarchy is a requirement

1. **Warm, soft canvas.** Use a tinted warm-neutral/very pale lavender background, not a pure-white full-page canvas. Reserve brighter card surfaces for focus and contrast. Maintain WCAG-compliant contrast for body text, controls, and chart labels.
2. **More breathing room.** Use generous inter-section separation and card padding; prefer two large intentional regions over six tiny dashboard widgets. Avoid nested card-in-card-in-card layouts.
3. **Friendly without becoming childish.** Muted indigo, soft lilac/blue and pale green or peach accents, rounded corners, small welcoming illustration in onboarding/empty states, simple friendly copy. Decorative illustration must not displace evidence or decision controls.
4. **One primary action.** The question input and Investigate action should be the clearest interaction on a prepared workspace. The first-time empty workspace should instead emphasize file upload. Do not make multiple competing CTA buttons.
5. **Charts that explain themselves.** Start with one well-labeled, readable chart, a concise natural-language takeaway, and a short “How this was checked” summary. Use honest axes, visible units, comparisons and denominators. Prefer straightforward line/bar charts to fancy or ambiguous visualizations.
6. **Progressive disclosure.** Compact data summary and analysis trail on the landing page; reveal the full relationship graph, field profiles, SQL, row-level evidence, deep diagnostics, and metric approval in dedicated views or on-demand drawers.
7. **Approachable data language.** Prefer “Your data,” “Data at a glance,” “Ask about your data,” “How this answer was checked.” Explain terms such as join cardinality or metric grain when users expand technical details.
8. **No visual pretense.** Don't show checked steps, approved relationships, revenue figures, percentages, time series, or file badges without actual supporting state. Empty/loading/demo modes must be clearly identified.

## Layout blueprint — desktop

- **Sidebar:** slender, low-noise navigation rail: Workspaces, Data Sources, Investigations, Reports, Settings. Advanced Data Studio can appear contextually under workspace understanding; don't force every item into the top-level menu.
- **Header:** active workspace name, subtle readiness indicator based on real status, an optional create-workspace action. Skip global KPI clutter.
- **Welcome/empty area:** human heading with a short promise, a compact friendly data illustration, and a prominent upload affordance. Minimize illustration once an investigation is open.
- **Dataset overview:** one large, airy region combining an upload/drop area and a small number of truthful data-summary facts (files, proposed/approved relationships, columns). If the same view supports both upload and overview, show its actual current state rather than a contrived simultaneously empty-and-populated mock.
- **Question input:** full-width focused input with clearly labeled Investigate action and contextual scope (files, approved definitions, timeframe where present).
- **Result area:** one dominant chart/visual explanation on the left; one concise **Integrity Summary** on the right explaining reproduced result, applicable challenges, significant alternatives and whether anything remains unresolved. Put deeper charts behind expansion or a follow-up tab.
- **On-demand detail:** side sheet/drawer for metric confirmation, join inspection, SQL, query results, provenance and limitations. Do *not* show a permanent wall of technical details.

## Progressive states and transitions

- **No files:** display upload invitation and privacy summary; hide charts and invented insights. Example questions may be prompts only, marked examples.
- **Uploaded, profiling:** stateful progress and accessible error recovery; don't claim relationships discovered until calculations finish.
- **Needs review:** show small amber banner/inline prompt with one action to review proposed relationships or business metric assumptions; allow safe, unrelated analyses to proceed.
- **Ready:** show data overview and focused question input; optional non-fabricated exploratory prompts based on profiled columns.
- **Investigating:** show bounded milestones in human terms (understand question → query data → check arithmetic → explain findings). Never mark pending or failed steps complete.
- **Complete:** offer a headline finding, accessible chart, **non-misleading integrity status**, limitations, evidence trail and “How this was checked”/“Inspect SQL” secondary actions.
- **Insufficient evidence:** say what cannot be answered and which table, field, or definition is missing. No deceptive placeholder chart.
- **Error or partial result:** explain partial scope and recovery action rather than presenting complete-looking output.

## Interaction design and trust

- Candidate joins/metrics must show distinct `Proposed`, `Approved`, `Rejected`, and `Needs clarification` states; an automated inference is never represented as user-confirmed truth.
- A compact explanation panel shows what the system learned about files and what still needs confirmation.
- One metric/relationship approval drawer at a time: natural-language definition, key filters, grain, units, source fields, join cardinality, consequences, and confirm/edit/reject choices.
- Evidence links expose AST-validated SQL, dataset fingerprint, semantic profile version, result values and verification method; technical depth accessible through explicit user action.
- Do not invent model confidence percentages. Show observed checks, caveats, remaining unknowns and **explicitly whether arithmetic was merely reproduced or the conclusion survived tested alternatives**. Do not use a universal green verified badge.

## Design proportions and tokens (guidelines, not executable code)

| Element | Guidance |
|---|---|
| Canvas | Warm ivory/pale lavender tint, never stark pure white across the whole viewport |
| Primary ink | Dark navy with comfortably readable contrast |
| Accent | Desaturated indigo, used for active navigation and primary action |
| Supporting colors | Soft lavender/blue, muted mint verification, quiet amber review, reserved coral/red errors |
| Spacing | Base 8px rhythm; cards approximately 24–32px internal padding; sections approximately 28–40px apart on desktop |
| Corners | Soft rounding, roughly 16–24px for large cards; consistent, not pill-shaped everywhere |
| Shadows | Minimal and diffuse; subtle border/tonal separation, no dense grids of outlined boxes |
| Typography | Clear large headline, readable medium section headings, 14–16px body text, concise labels |
| Charts | One primary plot at a time; plain-language title; legible units, labels, legends and comparisons |
| Illustrations | Friendly abstract/robot companion only for welcome or empty moments, not every panel |
| Motion | Subtle state feedback; respect reduced-motion preference |

Do not treat these illustrative values as a pixel-perfect command if that reduces accessibility or responsiveness.

## Responsive behavior

- **Desktop ≥1280px:** narrow sidebar, wide main column, chart + evidence card side by side. Do not squeeze three equal-width analytics columns into the viewport.
- **Tablet:** collapsible sidebar; upload and data summary stack; chart above evidence; drawers become sheets.
- **Phone:** single column, accessible sticky question action if needed, readable chart or table alternative, no tiny clickable graph nodes, user-controlled horizontal scroll only for data tables.
- Keyboard navigation, focus styles, screen reader labels, reduced motion, loading skeletons and readable chart/table alternatives required.

## Usability and QA acceptance

1. In a 1440px desktop screenshot, the viewer can identify workspace, data readiness, primary action, main chart, and key finding **without searching through a wall of panels**.
2. The visual canvas feels warm and restrained; no white-on-white dashboard overexposure and no more than two prominent result panels in the initial investigation view.
3. Unknown dataset names and arbitrary CSV fields do not break layout or generate static retail KPI cards.
4. A user can locate SQL/provenance within two deliberate interactions, but these details do not clutter default view.
5. Empty, profiling, review-needed, investigating, success, failure and insufficient-data states are all designed and tested.
6. Test at desktop/tablet/mobile widths; keyboard-only completion for upload, relationship approval, question submission and evidence inspection.
7. In usability walkthroughs, a new user can explain the next action without written tutorial and distinguish a verified result from an inferred/needs-review item.

## Illustration vs software contract

`ui.png` expresses **visual tone, hierarchy and composition**; it is not a specification for hard-coded data. The future application should keep the look and feel while dynamically rendering only observed, approved, or properly labeled demo state. Advanced Data Studio, full provenance graph and report export remain fully in scope; they are simply not all shown simultaneously on the home screen.
