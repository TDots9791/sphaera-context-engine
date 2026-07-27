# Контракт полного отчёта графа

Статус: **локальная development verification, не заявление о подписанном релизе**. Этот документ описывает формат артефактов полного построения, а не обещает одинаковую скорость на другой машине или corpus.

## Границы прохода

Полное построение передаёт весь выбранный scope в `index --full` без фильтра по расширениям. Штатные adapters разбирают исходники, PDF, CSV, XLSX, JSON, Markdown, TXT и другие поддерживаемые форматы.

Защищённые секреты, известные binary, недоступные и превышающие заданный лимит файлы не маскируются как обработанные: их количество и причины остаются в `index.json` и сводке. Это защищает карту от ложной полноты.

## Набор артефактов

Полный отчёт сохраняется внутри проекта, в `.sphaera/reports/full-graph-<timestamp>/`.

| Артефакт | Содержание |
|---|---|
| `graph-full.json` | Полный portable snapshot: `sources`, `nodes`, `edges`, adapters, fingerprint, graph run и validation. |
| `graph-sources.csv` | Все источники графа: locator, SHA-256, размер, adapter, validation, расширение и категория. |
| `graph-nodes.csv` | Все nodes: id, kind, code, source locator, fingerprint, materialization, validation и metadata. |
| `graph-edges.csv` | Все edges: `from`, `to`, relation, provenance source, validation и metadata. |
| `graph-file-types-category.csv`, `graph-file-types-extension.csv` | Разрез только источников, вошедших в граф: code, document, data/config и расширения. |
| `graph-node-kinds.csv`, `graph-edge-relations.csv`, `graph-edges-by-source-type.csv` | Сводка структуры и связей. |
| `file-read-probe.csv` | Пофайловое потоковое чтение: размер, SHA-256, время и MiB/s. Поле `in_graph_source` показывает, вошёл ли файл в snapshot. |
| `index.json`, `graph-update.json`, `graph-status.json` | Машиночитаемые результаты фаз Sphaera. |
| `summary.json`, `report.md` | Единая сводка времени, состава и validation. |

## Время и интерпретация

`file-read-probe.csv` измеряет чтение и fingerprint файла. Это не искусственный одиночный `index` для каждого файла и не внутреннее время извлечения Sphaera.

Внутренние фазы остаются отдельными: `scan_ms`, `extract_ms`, `sync_ms`, wall time graph update и graph export. Это позволяет отличить скорость диска и fingerprint от разбора формата, SQLite и построения связей.

`state=current`, `validation.valid=true` и `stale_sources=0` означают, что snapshot согласован с выбранным scope на момент проверки. Любое другое состояние сохраняется в отчёте как неопределённость, а не скрывается.

## Воспроизводимость

Сравнивайте прогоны на одинаковом scope, лимите размера файла, policy `.gitignore`, настройке workers и версии Sphaera. В отчёте сохраняются эти параметры, project fingerprint и SHA-256 источников. Числа относятся к конкретному corpus, диску и машине; они не являются универсальной гарантией производительности.
