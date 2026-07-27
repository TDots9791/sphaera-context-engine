# Full graph-report contract

Status: **local development verification, not a signed-release claim**. This document defines full-build artifacts; it does not promise identical speed on another machine or corpus.

## Scope

A full build passes the selected scope to `index --full` without an extension filter. Native adapters handle source code, PDF, CSV, XLSX, JSON, Markdown, TXT, and other supported formats.

Protected secrets, known binaries, inaccessible files, and files above the configured limit are not presented as processed. Their counts and reasons remain in `index.json` and the summary.

## Artifacts

A full report is written inside the project at `.sphaera/reports/full-graph-<timestamp>/`.

| Artifact | Contents |
|---|---|
| `graph-full.json` | Complete portable snapshot: `sources`, `nodes`, `edges`, adapters, fingerprint, graph run, and validation. |
| `graph-sources.csv` | Every graph source with locator, SHA-256, size, adapter, validation, extension, and category. |
| `graph-nodes.csv` | Every node with id, kind, code, source locator, fingerprint, materialization, validation, and metadata. |
| `graph-edges.csv` | Every edge with `from`, `to`, relation, provenance source, validation, and metadata. |
| `graph-file-types-*.csv` | Only sources included in graph, grouped by code/document/data-config category and extension. |
| `graph-node-kinds.csv`, `graph-edge-relations.csv`, `graph-edges-by-source-type.csv` | Structural and relationship summaries. |
| `file-read-probe.csv` | Per-file streaming read: size, SHA-256, elapsed time, and MiB/s; `in_graph_source` identifies snapshot membership. |
| `index.json`, `graph-update.json`, `graph-status.json`, `summary.json`, `report.md` | Phase outputs and consolidated report. |

## Timing and validation

The file probe measures file reading and fingerprinting. It is neither one artificial `index` run per file nor Sphaera's internal extraction time. Internal `scan_ms`, `extract_ms`, `sync_ms`, graph-update wall time, and export wall time stay separate.

`state=current`, `validation.valid=true`, and `stale_sources=0` mean the snapshot matched its selected scope at verification time. Any other state remains explicit in the report.

Compare runs only with the same scope, file-size limit, `.gitignore` policy, worker setting, and Sphaera version. Measurements are corpus-, disk-, and machine-bound, not universal performance guarantees.
