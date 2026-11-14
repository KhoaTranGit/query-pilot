# Multi-CSV ingestion and profiling specification

## Input policy

Accept N independent CSV files with arbitrary safe names and arbitrary column names. Do not infer trust or semantic meaning from filenames. File validation includes extension + actual format sniffing, encoding, delimiter, quoting, escapes, headers, invalid rows, empty files, duplicate names, and suspicious oversized fields. Implement configurable size/count/time limits, enforced on streamed bytes before parsing. Keep upload quotas explicit to users.

## Existing libraries

Use FastAPI `UploadFile` with chunked handling, DuckDB `read_csv` / `sniff_csv` and Polars `scan_csv` as appropriate. Do not invent CSV parsers, dialect sniffers, column inference algorithms or ad-hoc regex parsing. DuckDB inference can be wrong; show warnings and allow explicit type overrides. Avoid automatically coercing identifiers with leading zeros to integer; preserve original values and parsing choices.

## Required outputs per table

- File hash, source name, row count, byte size, delimiter/encoding/header decision, parse warnings.
- Column name, original type, suggested logical role, observed null fraction, sampled distinct count, example masked values, type-conflict summary, min/max where appropriate.
- Candidate single/composite keys, uniqueness estimates, duplicates, temporal columns and timezone ambiguity; proposed grain with uncertainty.
- Data quality report: missingness, out-of-range or malformed dates, invalid numeric values, duplicate rows and suspicious cardinalities.
- Preview of a bounded number of rows with sensitive fields masked by default.

## Security/privacy

File uploads are untrusted. Protect against path traversal, symlinks, decompression bombs, HTML injection in metadata, CSV formula injection on export, inference and prompt injection in text cells. Never let a model turn a CSV cell into a tool instruction. Temporary files must be safely deleted when workspaces expire. Never send private CSV rows to remote LLM by default; use schema and approved aggregates. Any external data transfer must require an explicit opt-in.

## Failure modes

Partial import does not silently mark workspace ready; report which files failed and allow retry. Duplicate columns get deterministic stable aliases while preserving originals. Explain when header/delimiter/type confidence is low. Write real acceptance tests for quoted delimiters, Latin1/non-ASCII, huge values, BOM, empty fields, strange filenames, duplicated keys and renamed columns.
