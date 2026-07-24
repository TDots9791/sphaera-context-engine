# Connected graph: code, documents, and project context

Status: **description of a locally buildable capability, not a claim about a signed release**. Whether a feature is present in an installed build is determined by signed release notes. The numbers below are specific to a corpus and machine and are not a universal guarantee.

## Why

One project needs several ways to look at itself. Code answers "what calls what." Documents answer "which clause of which agreement governs this." Project context answers "why we decided this and what is still open." These three views have different structure and must not be mixed during retrieval: merging code and documents into one index measurably degrades relevance. Sphaera therefore keeps three orientations separate and expresses the link between them as **edges**, not as a merged index.

## Three orientations

| Orientation | Nodes | Answers |
|---|---|---|
| Code | files, symbols, imports, calls | what calls what, what a change affects |
| Documents | document version → fragment (clause) → entity | which clause of which document, where else the same thing appears |
| Project context | reasoning threads: question, decision, fact, conflict, gap | why it was decided, what is still open, what it rests on |

Each orientation is queried separately and keeps its own exact addresses (locators) to the source.

## Edges between orientations

Links are built deterministically and carry evidence — which node, by which signal, and at which address it is linked.

- **Document ↔ document** — by a shared identifying entity (agreement or case number, invoice, tax ID, a norm reference). The same number in an agreement, an invoice, and an act links those documents together, including across formats (PDF, DOCX, XLSX). Weak coincidences (a shared date or amount) are not treated as a link, so no false edges appear.
- **Document → code** — by an explicit file-path mention in the document text. A specification naming a source file is linked to that code node, keeping the fragment's address. File paths are matched as an unambiguous signal; matching by function names is deferred to a separate precision measurement.
- **Project context → code and documents** — a reasoning step is linked to the nodes it rests on: explicitly (the agent supplied a reference) and automatically (a code path or an existing document entity is mentioned in the step text). Reasoning never creates new sources — it references only what has already been extracted from real material.

Edge integrity is checked by a separate command: a reference to a vanished node (file deleted, graph rebuilt) is detected as dangling rather than left unnoticed.

## Resuming long work instead of a handwritten handoff

For a multi-day, multi-step task Sphaera holds a **reasoning skeleton** and delivers it compactly within the model window:

1. open questions and gaps first — what needs attention;
2. then decisions and conflicts — what is settled;
3. then facts and assumptions — the support;
4. each item carries the addresses of the code and documents it touches, so the model can pull exact evidence.

The skeleton is trimmed to a token budget but never emptied: even for a very tight window at least one item remains to continue from. Unlike a handwritten handoff file, the skeleton **self-invalidates**: when a linked source changes, dependent steps are flagged for recheck, so staleness is visible rather than silent.

The practical effect: a small local model (for example around 6–9B) continues a complex task from the skeleton with addresses, rather than from the entire prior dialogue.

## Boundaries and honesty

- Orientations are deliberately not mixed during retrieval: this preserves the relevance of each.
- Edges are built from reliable signals (a shared identifying entity, a file path); less reliable signals are added only after precision is measured on a corpus.
- Project context is a derived layer: it does not rewrite source facts and does not create sources.
- Performance and coverage numbers are corpus-bound; for methodology and follow-up measurements see the [performance documentation](PERFORMANCE.md) and the [full graph-report contract](FULL-GRAPH-REPORT.md).
