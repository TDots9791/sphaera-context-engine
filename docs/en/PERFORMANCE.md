# Performance and safe full indexing

Status: **locally verified in development, not a release claim**. Official availability is defined only by a signed distribution and its release notes.

## Full-index scaling

- Normal `index` and `update` re-extract only changed fingerprints.
- A full rebuild is explicit: `sphaera index --root <project> --full --format json`.
- At 256+ pending files, the next local revision uses adaptive JSONL preindexing: independent files are extracted in parallel into fingerprint-verified shards, then **one** SQLite writer commits a bounded batch of up to 512 files. Multiple concurrent SQLite writers are deliberately not used: they increase locking without a correct speed-up.
- `SPHAERA_PREINDEX=auto|force|off`: `auto` uses the 256-file threshold, `force` validates staging on a small corpus, and `off` keeps the direct path.
- Shards live inside the project at `.sphaera/tmp/preindex/<digest>/`. They are deleted only after SQLite commits succeed and `graph validate` is valid; an interrupted or invalid run remains isolated for a safe retry and is excluded from source scanning and distribution.

## GPU: an explicit boundary

`sphaera acceleration --format json` detects an NVIDIA GPU through `nvidia-smi` and reports whether a CUDA host is available for a separate semantic worker. Graph construction is not claimed as GPU-accelerated: filesystem walking, hashing, bounded parsing, and SQLite are CPU/IO work. GPU is relevant to separately configured llama.cpp embeddings, OCR, ASR, or vision work, not to SQLite merge.

## Local measurement — 2026-07-23

Windows mixed-document corpus: 71 scanned / 70 selected files, 292 evidence records. Two sequential `index --full` runs on the same corpus; this is corpus-bound, not a performance guarantee for other disks, CPUs, or data.

| Path | Wall time | Scan | Extract | SQLite sync | Result |
|---|---:|---:|---:|---:|---|
| Direct `SPHAERA_PREINDEX=off` | 8,362 ms | 12 ms | 939 ms | 6,614 ms | 70 files, 292 records |
| Staged `SPHAERA_PREINDEX=force` | 7,162 ms | 12 ms | 761 ms | 5,592 ms | 70 shards, 1 SQLite batch, shards removed |

After the staged run: `graph validate valid=true`, `stale_sources=0`, `issues=0`, and `0` shard runs remained. This is local development verification; no archive, signature, or publication was created for it.

## Retrieval quality: Chinese, 2026-07-26

Index speed says nothing about whether the right material is found. For Chinese this matters especially: the language is written without spaces, so retrieval rests entirely on segmentation — and when segmentation fails, a whole sentence becomes a single token and there are no matches at all.

Corpus: 12 documents across three domains — PRC statutes (zh.wikisource, public domain under Art. 5 of the PRC Copyright Law), medicine and science (zh.wikipedia, CC BY-SA). 62 questions, `top_k=12`, engine version 1.0.1.

| Metric | Value |
|---|---:|
| recall@12 | 0.935 |
| precision@1 | 0.903 |
| MRR | 0.919 |
| nDCG@12 | 0.924 |
| P95 latency | 22 ms |

**How question correctness is established.** 46 of the 62 questions are generated mechanically: substrings that occur in exactly one document of the corpus. The claim "a string occurring in exactly one document must return that document" is verifiable without knowing the language. Selection happens **before** the engine runs and does not depend on its answers: discarding questions the engine got wrong would fit the benchmark to the engine and always yield a perfect score. The remaining 16 questions are human-written and marked separately; on those precision@1 is 0.938, on generated ones 0.891.

**What this set does not measure.** Semantic correspondence and paraphrase: a question here is a discriminating substring, not a natural formulation. Paraphrases require a native speaker, and we did not substitute generation for one.

The measurement is corpus-bound and is not a guarantee for another corpus, another domain, or another machine.
