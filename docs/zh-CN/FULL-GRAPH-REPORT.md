# 完整图谱报告契约

状态：**本地开发验证，不构成已签名发行版声明**。本文定义完整构建的产物，不承诺在其他机器或语料上获得相同速度。

## 范围

完整构建将选定范围交给不带扩展名过滤的 `index --full`。原生 adapters 处理源代码、PDF、CSV、XLSX、JSON、Markdown、TXT 及其他受支持格式。

受保护的机密、已知二进制文件、无法读取的文件和超过配置限制的文件不会被伪装为已处理；其数量和原因保留在 `index.json` 和汇总中。

## 产物

完整报告写入项目内的 `.sphaera/reports/full-graph-<timestamp>/`。

| 产物 | 内容 |
|---|---|
| `graph-full.json` | 完整 portable snapshot：`sources`、`nodes`、`edges`、adapters、fingerprint、graph run 和 validation。 |
| `graph-sources.csv` | 所有图谱来源：locator、SHA-256、大小、adapter、validation、扩展名和分类。 |
| `graph-nodes.csv` | 所有节点：id、kind、code、source locator、fingerprint、materialization、validation 和 metadata。 |
| `graph-edges.csv` | 所有边：`from`、`to`、relation、provenance source、validation 和 metadata。 |
| `graph-file-types-*.csv` | 仅统计已进入图谱的来源，按 code/document/data-config 分类和扩展名分组。 |
| `graph-node-kinds.csv`、`graph-edge-relations.csv`、`graph-edges-by-source-type.csv` | 结构与关系汇总。 |
| `file-read-probe.csv` | 按文件流式读取：大小、SHA-256、耗时、MiB/s；`in_graph_source` 表示是否进入 snapshot。 |
| `index.json`、`graph-update.json`、`graph-status.json`、`summary.json`、`report.md` | 阶段输出与汇总报告。 |

## 时间与验证

文件 probe 测量读取和 fingerprint，不是每个文件一次人工 `index`，也不是 Sphaera 内部提取时间。`scan_ms`、`extract_ms`、`sync_ms`、graph-update wall time 和 export wall time 保持独立。

`state=current`、`validation.valid=true` 和 `stale_sources=0` 表示 snapshot 在验证时与所选范围一致；其他状态会在报告中明确保留。

仅在相同范围、文件大小限制、`.gitignore` policy、worker 设置和 Sphaera 版本下比较运行。测量结果受语料、磁盘和机器约束，不是通用性能保证。
