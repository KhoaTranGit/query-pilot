# Threat model & required protections

## Never trust

Uploaded file paths, CSV cells, column names, file names, user text, LLM outputs, retrieved documents or reports. All are untrusted input. The model is not an authorization system.

## CSV and workspace protections

- Validate file signatures and limits before storage; sanitize paths, quarantine uploads and forbid remote file reads.
- Strict workspace isolation for files, database objects, embeddings, traces and caches; session auth added before hosted multi-user use.
- Explicit privacy notice; local-only parsing default, remote LLM sees schema/aggregates only if configured and consented.
- PII detection is best-effort, never guaranteed; mask row previews and default report exports to aggregates.
- Retention, deletion, data migration and redaction requirements before public deployment.

## Generated SQL execution protections

- Parse and validate with SQLGlot AST using approved dialect; permit only constrained SELECT queries, deny arbitrary functions, file readers, external integrations, attach/copy/load/export, extensions and stacked statements.
- AST filtering is **not a sufficient security boundary**. Enforce filesystem/network isolation at process/container level, a restricted read-only connection, per-workspace file restrictions, memory/CPU limits, row limits and timeouts.
- Parameterize user-supplied literals. Quote/escape mapped identifiers correctly via vetted libraries.
- Never grant LLM direct execution of database DDL/DML, SQL macros, Python code or OS commands.
- Test nested statements, unions, comments, CTE, subqueries, sensitive functions, path traversal and prompt injection attempts.

## Output integrity

Escaped UI rendering. Prevent formula execution when exporting CSV (`=`, `+`, `-`, `@`, etc. prefixes). Downloads must be authorized and scoped; sanitize filenames and strip sensitive sample values. A generated recommendation is not a license to perform external actions.

## Operational requirements

Security regression tests are a hard gate before public access. The local MVP is not production multi-tenant-safe. Threat model updates are required for adding external warehouses, remote LLM provider or shared workspaces.
