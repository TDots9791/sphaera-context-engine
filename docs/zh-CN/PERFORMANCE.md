# 性能与安全的完整索引

状态：**本地开发验证，不是发布声明**。正式可用性仅由已签名发行包及其 release notes 决定。

## 完整索引如何扩展

- 常规 `index` 与 `update` 只重新提取已变化的 fingerprint。
- 完整重建需要显式执行：`sphaera index --root <project> --full --format json`。
- 当待处理文件达到 256 个时，下一本地 revision 使用自适应 JSONL preindex：独立文件并行提取到经 fingerprint 验证的 shards，随后由**一个** SQLite writer 提交最多 512 个文件的有界 batch。不会使用多个并发 SQLite writers，因为这会增加锁竞争而不能正确提速。
- `SPHAERA_PREINDEX=auto|force|off`：`auto` 使用 256 文件阈值，`force` 用于小 corpus 验证，`off` 保留直接路径。
- Shards 位于项目内 `.sphaera/tmp/preindex/<digest>/`。只有 SQLite commits 成功且 `graph validate` 有效后才删除；中断或无效 run 会隔离保留以便安全 retry，并且不会进入 source scan 或发行包。

## GPU：明确边界

`sphaera acceleration --format json` 通过 `nvidia-smi` 检测 NVIDIA GPU，并报告单独 semantic worker 是否可使用 CUDA host。图构建不宣称 GPU 加速：文件遍历、hashing、有界 parsing 和 SQLite 都是 CPU/IO 工作。GPU 适用于单独配置的 llama.cpp embeddings、OCR、ASR 或 vision，而不是 SQLite merge。

## 本地测量 — 2026-07-23

Windows mixed-document corpus：71 scanned / 70 selected files，292 evidence records。相同 corpus 上两次顺序执行 `index --full`；结果受 corpus 限制，不保证适用于其他磁盘、CPU 或数据。

| Path | Wall time | Scan | Extract | SQLite sync | Result |
|---|---:|---:|---:|---:|---|
| Direct `SPHAERA_PREINDEX=off` | 8,362 ms | 12 ms | 939 ms | 6,614 ms | 70 files, 292 records |
| Staged `SPHAERA_PREINDEX=force` | 7,162 ms | 12 ms | 761 ms | 5,592 ms | 70 shards, 1 SQLite batch, shards removed |

Staged run 后：`graph validate valid=true`、`stale_sources=0`、`issues=0`，剩余 shard runs 为 `0`。这是本地开发验证；没有为此创建 archive、signature 或 publication。
