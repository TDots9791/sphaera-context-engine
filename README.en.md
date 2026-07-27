# Sphaera context engine

[![Russian](https://img.shields.io/badge/LANGUAGE-RU-D7A94B?style=flat-square&labelColor=101918)](README.md)
[![English](https://img.shields.io/badge/LANGUAGE-EN-4EA9A5?style=flat-square&labelColor=101918)](README.en.md)
[![中文](https://img.shields.io/badge/LANGUAGE-ZH-0C5A58?style=flat-square&labelColor=101918)](README.zh-CN.md)

[![Architecture: local-first](https://img.shields.io/badge/ARCHITECTURE-LOCAL--FIRST-0C5A58?style=flat-square&labelColor=101918)](README.en.md#architecture)
[![Context: evidence-first](https://img.shields.io/badge/CONTEXT-EVIDENCE--FIRST-4EA9A5?style=flat-square&labelColor=101918)](README.en.md#evidence-context)
[![Protocol: MCP](https://img.shields.io/badge/PROTOCOL-MCP-176D69?style=flat-square&labelColor=101918)](README.en.md#mcp-integration)
[![Local AI: llama.cpp](https://img.shields.io/badge/LOCAL--AI-LLAMA.CPP-4EA9A5?style=flat-square&labelColor=101918)](README.en.md#llama-cpp)
[![Access: source available](https://img.shields.io/badge/ACCESS-SOURCE--AVAILABLE-D7A94B?style=flat-square&labelColor=101918)](docs/en/SOURCE-AVAILABILITY.md)

Local, evidence-first context engine for AI agents.

Sphaera builds a verifiable local working map of code and documents. AI receives the materials relevant to its task, while important conclusions retain a path back to the original source. One map works across agents and models; the delivered context adapts to the available context window.

The foundation is on-device work: a local database and CLI, with no mandatory server or external services. Sphaera understands **29 code modes** (Rust, Python, JS/TS, Go, C/C++, C#, Java, Kotlin, Swift, PHP, Ruby, SQL, Bash, PowerShell, Dockerfile, and more) and **11 document formats** (DOCX, PDF, XLSX, ODS, PPTX, XML, CSV/TSV, Markdown, HTML, JSON) — 40 structured adapters in total. Russian is a native mode with morphology awareness; English is supported; for Chinese, retrieval quality is measured on a dedicated corpus rather than asserted.

## What is in this repository

This is Sphaera's public product repository: documentation, examples, roadmap, discussions, and issue reporting. Official signed distributions, licenses, updates, and support are issued only through the website.

- Product page: https://iustitia.tech/academy/sphaera-context-engine/
- Installation: [docs/en/INSTALLATION.md](docs/en/INSTALLATION.md)
- Source availability: [docs/en/SOURCE-AVAILABILITY.md](docs/en/SOURCE-AVAILABILITY.md)
- Performance and full indexing: [docs/en/PERFORMANCE.md](docs/en/PERFORMANCE.md)
- Connected graph (code, documents, project context) and work resumption: [docs/en/CONNECTED-GRAPH.md](docs/en/CONNECTED-GRAPH.md)
- Full graph-report contract: [docs/en/FULL-GRAPH-REPORT.md](docs/en/FULL-GRAPH-REPORT.md)
- Security reporting: [SECURITY.en.md](SECURITY.en.md)

## Who it is for

- teams working with large repositories and document collections;
- legal, financial, research, and engineering professionals who need to trace an AI conclusion to its source;
- users of compatible AI platforms.

## What Sphaera does

Sphaera lets AI work from a current map of a project rather than an endless folder of files. It finds task-relevant materials, preserves their connection to the original source, and delivers an appropriate amount of context to each model. This reduces repeated reading, speeds up verification of important conclusions, and gives a team one project memory instead of disconnected histories across AI tools.

<a id="architecture"></a>

### Architecture: local project memory

AI should not have to rediscover a project for every task, agent, or model. Sphaera maintains a durable, evidence-backed map on the device: each fragment has a precise locator, source fingerprint, and dependency links. When a file changes, the affected part of the map changes instead of the team's accumulated working knowledge being rebuilt.

<a id="evidence-context"></a>

### Context delivery: evidence, not an archive

The model receives neither a "zip archive" nor an uncontrolled wall of text. It receives a `ContextPack` with required sources. Under a tight window, `capsule` splits evidence into compact expandable frames: route and key facts first, then the needed fragments and relations. A compact local model can therefore progress through a complex task without losing the path to source.

<a id="mcp-integration"></a>
<a id="llama-cpp"></a>

### Models, integrations, and the working contour

| Contour | How it works | Practical result |
|---|---|---|
| **Models without a name catalog** | Sphaera resolves provider rules and the connected model's available window; unknown IDs do not need a brittle exception list | One project context for cloud AI and local `llama.cpp`; the 6B/9B compact-model path is verified, with a variable window up to 2M tokens |
| **Context delivery** | `ContextPack` and capsule frames retain required evidence, constraints, and exact locators | The model sees less noise but can expand proof step by step rather than guess from a summary |
| **Code, documents, and provenance** | 40 structured adapters (29 code modes + 11 document formats) and a graph of sources, transformations, and dependent results keep facts, inferences, conflicts, and gaps distinct | One working memory joins code with PDFs, agreements, tables, slides, and AI output |
| **Links across code, documents, and reasoning** | Three graph orientations — code, documents, and project context — are stored separately but joined by verifiable edges: a document clause → code file by path mention, a specification requirement ↔ code by shared identifier (REQ-042 and similar), agreement ↔ addendum ↔ invoice by shared number, a reasoning step → the nodes it depends on. Edge integrity is validated separately | Move from a document clause to code and back, find related documents across formats by a shared entity, and see which decision rests on what. Requirements with no trace in code are listed by name — a traceability gap is visible individually instead of disappearing into an aggregate |
| **Resuming long work** | `resume` assembles a compact skeleton of a reasoning thread sized to the model window: open questions first, then decisions and facts, each with the addresses of the nodes it touches. When a source changes, dependent steps are flagged for recheck | A small local model continues a complex multi-day task from a skeleton with addresses instead of re-reading the whole dialogue; notes do not go stale silently |
| **Fast full indexing** | On a large change, adaptive pre-index creates verified JSONL shards in parallel and commits through one SQLite writer; shards disappear only after commit and graph validation | Faster full passes without losing resumable work or corrupting durable storage |
| **Reliable local storage** | Compaction makes a verified backup; locks and watchers are designed for independent Sphaera instances | Multiple agents can maintain different projects and databases at the same time without contour conflicts |
| **Acceleration without marketing substitution** | Sphaera detects CUDA-capable GPU; graphing, structural extraction, and SQLite remain CPU-bound while external local `llama.cpp` workers can use GPU | Acceleration status stays honest: GPU is not presented as a graph accelerator, yet remains available to local semantic components |
| **Integration** | The foundation is local work through the CLI and an on-device database; skills for supported AI platforms use the same map. The local MCP server (`mcp-stdio`) is an optional surface, not a requirement | An agent receives verifiable context in its normal workflow, without a separate server and without mandatory MCP |
| **Host model detected, not declared** | The active model is read from the vendor's own metadata; the context window is taken only where the vendor publishes it and is never inferred from a model name. The answer states where the budget came from: measured from the vendor, declared by the host, taken from the registry, or chosen as a safety minimum | Switching models needs no skill reinstall, and a safety minimum cannot be mistaken for a measured window |
| **Public output contract** | The `capabilities` command declares schema versions, the list of public outputs with their required fields, and the invocation form; a conformance test runs every command against the built binary | Integrators learn about a shape change from the contract rather than by breaking in production |

### Scale: measured, not promised

Full indexing should not turn into hours of waiting. In local validation on a document corpus of 70 selected files and 292 evidence records, the staged pass took **7,162 ms** versus **8,362 ms** for the direct pass; after commit all 70 temporary shards were removed and the graph remained valid. This is a corpus-and-machine-specific validation, not a universal guarantee. Method, limits, and later measurements are in the [performance documentation](docs/en/PERFORMANCE.md).

### AI economics

- **Less paid re-reading.** A new task or model does not have to reload an already familiar project. Sphaera reuses its local working map and adds only what changed or matters now.
- **Expensive models spend effort on reasoning, not search.** For models with costly input tokens, Sphaera suppresses material that is not needed for the task. Actual savings can be checked on a customer's own corpus and against the selected AI provider's usage data; no universal ROI percentage is promised.
- **Small models get context sized for large tasks.** Instead of an unmanageable archive, they receive a concise guide, key fragments, exact source locations, and sequential context. This enables economical models to work effectively with complex projects, documents, and tables within their context window.
- **Long work does not fall apart.** For multi-day, multi-step tasks Sphaera holds a reasoning skeleton — open questions, decisions, and facts with the addresses of the code and documents they touch — and delivers it compactly within the model window. A small local model resumes from that skeleton rather than the entire prior dialogue; when a source changes, dependent steps are flagged for recheck.
- **Predictable Sphaera cost.** The subscription does not add a markup to your AI provider's tokens and does not limit the number of supported AI agents, models, IDEs, or platforms within licensed users and devices.

### Technical value without extra infrastructure

- **One map for different AI tools.** Compatible AI tools use one local map. Changing a model changes the amount and form of delivery, not the project memory.
- **Context matched to each model.** A smaller window gets a shorter, guiding package; a larger window receives more connections and evidence. No separate data preparation is needed for each model.
- **Works at scale.** Large repositories, long PDFs, tables, and document chains do not have to be read in full each time. The map updates affected data and guides AI to the relevant location without losing its link to the source.
- **Code and documents in one working contour.** Sphaera handles common programming languages plus DOCX, PDF, XLSX/ODS, PPTX, XML, CSV/TSV, Markdown, HTML, and JSON.
- **Russian language is a first-class case.** Sphaera is designed for Russian documents and Russian morphology: it accounts for word forms, Cyrillic, and professional abbreviations while preserving the original quotation. Morphology is backed by a signed dictionary component derived from OpenCorpora (CC BY-SA) with 103,152 lemmas; its integrity is verified on load, and a mismatch is reported by diagnostics rather than passed over in silence.
- **Chinese: measured, not promised.** Chinese is written without spaces, so retrieval depends on segmentation. Measured on 2026-07-26 over a corpus of 12 documents across three domains (PRC statutes, medicine, science) and 62 questions: recall@12 **0.935**, precision@1 **0.903**, MRR **0.919**, P95 **22 ms**. Question correctness is mechanically verifiable — ground truth comes from statistics of the text itself, not from engine output. This set does not measure semantic paraphrase, and we do not claim that it does.
- **Context remains tied to evidence.** Important conclusions can be traced back to a line of code, page, paragraph, cell, or slide. After a source changes, related results can be identified for review.

### How the approach differs

- many tools map code, retrieve similar fragments, or store one agent's memory; Sphaera combines code, documents, tables, and result provenance in one local working contour;
- no separate server, graph database, or external Sphaera AI model is required; the working map remains on the user's device;
- rather than creating a new memory for every IDE or model, Sphaera maintains one verifiable foundation for different AI tools;
- the product distinguishes original material, model-derived output, conflicts, and items that require review.

Sphaera does not replace professional review or make an incorrect source correct. It makes the path from an AI question to original material shorter, more visible, and less costly to reuse.

### Engineering depth and verification

Quality does not come from a count of isolated commands. It comes from one connected chain: Sphaera understands the material, preserves its relationship to the source, sizes delivery for the task and model, and makes the result reviewable.

| Layer | Verified coverage | What the user gets |
|---|---|---|
| Materials | **40 structured adapters:** 29 code modes and 11 document formats | One working contour for repositories, tables, agreements, PDFs, and processing results |
| Relationships | One local map of sources, transformations, and dependent results; three orientations (code, documents, project context) joined by verifiable edges | A path from conclusion to source, from a document clause to code and back, and visibility into what a change affects |
| Context | One data foundation with adaptive delivery for different AI models | Small models receive connected working context; large models do not spend their window on already-known material |
| Quality control | Compatibility matrices, automated checks, and benchmark suites for advertised capabilities | Claims can be checked against a stated version and corpus rather than taken on trust |

This combination makes Sphaera a working contour rather than a collection of disconnected features. For a customer, that means more predictable AI work, fewer repeated actions, and faster review before a financial, legal, or technical decision.

## Get started

1. Open the official product page and obtain a Trial or license.
2. Copy your one-command online installer from the Sphaera account page.
3. The installer verifies the signed release, installs the license on this device, and connects your selected AI tools.

See [installation details](docs/en/INSTALLATION.md).

## Contribute and give feedback

Open an Issue with a clear example, expected result, and actual result. Requests for integrations, document formats, and adapter improvements are especially useful.

Before the source code is opened, external contribution is accepted through Issues and architectural discussion; pull requests containing derivative code are not accepted yet.

## Status

`1.0.0` is the public product baseline. Availability of a capability in an installed build and availability of a distribution are determined by signed release notes; this public repository remains a transparent space for documentation, feedback, and compatibility.
