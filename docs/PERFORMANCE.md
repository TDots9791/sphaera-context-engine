# Производительность и безопасный полный индекс

Статус: **проверено локально в разработке, не является заявлением о релизе**. Официальная доступность определяется только подписанным дистрибутивом и его release notes.

## Как масштабируется полный индекс

- Обычные `index` и `update` переизвлекают только изменившиеся fingerprints.
- Полный rebuild запускается явно через `sphaera index --root <project> --full --format json`.
- При 256+ ожидающих файлов следующий локальный revision использует адаптивный JSONL preindex: независимые файлы извлекаются параллельно в fingerprint-verified shards, затем **один** SQLite writer фиксирует bounded batch до 512 файлов. Несколько параллельных SQLite writers не используются: это ухудшает блокировки и не даёт корректного ускорения.
- `SPHAERA_PREINDEX=auto|force|off`: `auto` — порог 256, `force` — проверка на малом corpus, `off` — прямой path.
- Shards лежат внутри проекта в `.sphaera/tmp/preindex/<digest>/`; они удаляются только после успешных SQLite commits и `graph validate valid=true`. Прерванный или невалидный run остаётся изолированно для безопасного retry и не попадает в source scan или дистрибутив.

## GPU: честная граница

`sphaera acceleration --format json` определяет NVIDIA GPU через `nvidia-smi` и показывает, доступен ли CUDA-host для отдельного semantic worker. Сбор графа не заявляется GPU-ускоренной: обход файлов, hashing, bounded parsing и SQLite — CPU/IO path. GPU применим к отдельно настроенным llama.cpp embeddings, OCR, ASR или vision, а не к SQLite merge.

## Локальное измерение 23.07.2026

Windows local mixed-document corpus: 71 scanned / 70 selected files, 292 evidence records. Два последовательных `index --full` запуска на одном corpus; результат corpus-bound, не гарантия для других дисков, CPU или данных.

| Path | Wall time | Scan | Extract | SQLite sync | Result |
|---|---:|---:|---:|---:|---|
| Direct `SPHAERA_PREINDEX=off` | 8,362 ms | 12 ms | 939 ms | 6,614 ms | 70 files, 292 records |
| Staged `SPHAERA_PREINDEX=force` | 7,162 ms | 12 ms | 761 ms | 5,592 ms | 70 shards, 1 SQLite batch, shards removed |

После staged run: `graph validate valid=true`, `stale_sources=0`, `issues=0`, оставшихся shard-runs — `0`. Это локальная development verification; архив, подпись и публикация для неё не создавались.
