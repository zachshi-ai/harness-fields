# 05 · 量化佐证与效果评价

> Quantitative Evidence and Evaluation
>
> 本章用具体数字回答一个问题：**这套方法论有效吗？**
>
> 我把所有可量化的指标归到 3 个角度：容量控制、错误下降、协作流畅度。每个数字都附数据来源。
>
> 作者：扎克的自留地

---

## 中文

### 0. 评价框架

我用什么标准评价这套方法论"有效"？

| 评价角度 | 衡量指标 | 数据来源 |
|---|---|---|
| **容量控制** | 核心文件行数 / 文件数 | 文件状态 + git log |
| **错误下降** | 凭据违规次数 / 重复错误升级 | ERRORS.md / LEARNINGS.md |
| **协作流畅度** | cron 任务数 / 自动同步状态 | `openclaw cron list` |

下面按这 3 个角度展开。

---

### 1. 容量控制

#### 1.1 MEMORY.md 行数变化（核心指标）

| 时间 | MEMORY.md 行数 | 阶段 |
|---|---|---|
| 2026-02-20 | **3362 行** | 服务器版起点 |
| 2026-03-27 | ~1000 行 | 本地化后 |
| 2026-06-26 | **152 行** | 核心改造后 |

**变化**：3362 → 152 = **缩水 95.5%**。

**为什么这是核心指标？**
- MEMORY.md 是"AI 长期记忆"——AI 每次 session 必读
- 它臃肿 = AI 注意力分散 / context 占用大
- 它精简 = AI 注意力集中 / context 利用高效

**缩水路径**：
- 2026-02-20 → 2026-03-27：本地化迁移（删 70%）—— 把分散到多个文件的内容砍掉
- 2026-03-27 → 2026-06-26：核心改造（再删 85%）—— 用索引表 + 加载机制替代"塞进 MEMORY.md"

#### 1.2 核心文件数变化

| 时间 | 核心文件数 | 阶段 |
|---|---|---|
| 2026-02-20 | 11+ | 服务器版（AIZACH_ENTITY / AIZACH_RULES / EVOLUTION / BASIC_RULES 等多个） |
| 2026-03-27 | 6 | 本地化后（删繁就简） |
| 2026-06-26 | **9** | 核心改造后（重新分层 + 加 IDENTITY / events / tasks） |

**关键观察**：
- 11 → 6：**-45%**（本地化迁移砍冗余）
- 6 → 9：**+50%**（核心改造加分层）

**为什么从 6 涨到 9 是好事？**
- 之前 6 个文件里有些是"复合文件"——一个文件装多种功能
- 核心改造后变成"分层文件"——每个文件职责单一
- 文件数变多但每个文件更小、更聚焦

#### 1.3 各核心文件行数变化（2026-02-20 → 2026-07-07）

| 文件 | 服务器版（2026-02-20） | 当前（2026-07-07） | 变化 |
|---|---|---|---|
| SOUL.md | 58 | 39 | -33% |
| AGENTS.md | 275 | 478 | +74% |
| **MEMORY.md** | **3362** | **152** | **-95.5%** |
| TOOLS.md | 206 | 253 | +23% |
| USER.md | 15 | 11 | -27% |
| HEARTBEAT.md | 70 | 29 | -59% |
| IDENTITY.md | ❌ 不存在 | 46 | 新增 |
| events.md | ❌ 不存在 | 1 行 + 归档 | 新增 |
| tasks.md | ❌ 不存在 | 简短 | 新增 |

**关键观察**：
- **MEMORY.md 缩 95.5%**——核心改造最大单一变化
- **AGENTS.md 涨 74%**——把分散在多个文件的规则合并进来
- **新文件都"小而专"**——IDENTITY.md 46 行 / HEARTBEAT.md 29 行

**总行数对比**：
- 服务器版：58 + 275 + 3362 + 206 + 15 + 70 = **3986 行**
- 当前：39 + 478 + 152 + 253 + 11 + 29 + 46 + 1 + 简短 ≈ **1009 行**
- 总行数缩水 **74.7%**

### 2. 错误下降

#### 2.1 凭据违规次数（最关键的可量化指标）

| 时间段 | 凭据违规次数 | 说明 |
|---|---|---|
| 2026-03-28 | 1 | API token 泄露进 git 记录 |
| 2026-04-17 | 1 | 敏感信息未检查 |
| 2026-04-17 ~ 2026-06-19 | 0 | 暂未违规 |
| 2026-06-19 ~ 2026-07-07 | **0** | 凭据通解定稿后 |

**凭据通解定稿前**：2 次违规 / 约 80 天
**凭据通解定稿后**：0 次违规 / 18 天

**结论**：
- ✅ 凭据违规次数从 80 天 2 次 → 18 天 0 次
- ⚠️ 样本期 18 天，统计意义有限——需要更长周期验证

**为什么这条最重要？**
- 凭据违规 = 安全事故，不是"风格问题"
- 凭据违规从"反复失败"到"0 次"——这是 6 大约束机制协同生效的可量化证据

#### 2.2 错误库结构（ERRORS.md / LEARNINGS.md）

| 文件 | H2 条目数 | 总行数 | 状态 |
|---|---|---|---|
| .learnings/ERRORS.md | 2 条 | 50 行 | 已规范化 |
| .learnings/LEARNINGS.md | 8 条 | 213 行 | 持续维护 |
| .learnings/credentials-handling.md | — | 99 行 | 凭据通解 |

**关键观察**：
- **ERRORS.md 规范化**——之前错误散落在 SOUL.md / AGENTS.md / LEARNINGS.md 各处
- **升级路径明确**——1 次 → ERRORS.md / 2 次 → LEARNINGS.md / 3 次+ → AGENTS.md 强制规则
- **30 天观察期**——pending-review/ 目录追踪"待观察规则"

#### 2.3 关键失败事件（git log 摘选）

| 时间 | commit | 说明 |
|---|---|---|
| 2026-03-28 | `fix: 记录 API token 泄露错误并追加安全铁律到 SOUL.md` | 首次凭据违规 |
| 2026-04-17 | `docs: 敏感信息检查（重要）` | 加入凭据检查规则 |
| 2026-06-15 | `chore: 删除 6-04 日记（扎克的自留地 2026-06-15 05:18 指令）` | 删除未经认可的产物 |
| 2026-06-15 | `fix: 删除 ERRORS.md 里未经许可增加的'反讽关键词'规则` | 删除 AI 私自加的规则 |
| 2026-06-15 | `revert: 删除 §2.3.1（未经许可擅自增加）` | 删除 AI 私自加的章节 |

**关键观察**：
- **2026-06-15 一天 3 次"删除 AI 私自加的规则"**——这是 AGENTS.md §1.6 的真实触发
- **规则不是 AI 能加的**——必须由设计者（我）显式决定

### 3. 协作流畅度

#### 3.1 cron 任务数

**当前生效 cron 任务：11 条**（来自 `openclaw cron list`）

| cron | 时间 | 状态 |
|---|---|---|
| `1aad694f` Memory Dreaming Promotion | 每天 03:00 | ok |
| `daily-ai-topics` 每日 AI 选题 | 每天 09:00 | ok |
| `46df0a62` daily-noon-thought | 每天 12:00 | ok |
| `8ab43079` Daily Diary & Observations | 每天 23:50 | ok |
| `348fc40e` daily-diary-retry-1 | 每天 23:55 | ok |
| `e9df9f53` daily-errors-pdca | 每天 23:58 | ok |
| `a1fa3dd6` daily-diary-retry-2 | 每天 00:00 | ok |
| `ea96dc15` daily-diary-retry-3 | 每天 00:05 | ok |
| `0747f3d9` daily-noon-thought-retry | 每天 12:30 | ok |
| `f920e7a0` daily-noon-thought-retry | 每天 13:00 | ok |
| `8e55d383` daily-errors-pdca (retry) | 每天 23:59 | ok |

**11 条全部状态 ok**——这是协作流畅度的核心指标。

#### 3.2 自动同步状态

- **飞书 Base 同步**：日记 + 观察记录自动同步，2026-04-15 验证通过
- **daily-diary-retry**：3 次重试机制，确保失败时也能补齐
- **pending-review**：30 天观察期自动化追踪

#### 3.3 工作区活跃度（git log）

- **git 总提交数**：60 次（截至 2026-07-07）
- **首次 commit**：2026-03-27
- **平均 commit 频率**：60 次 / 102 天 ≈ **0.6 次/天**
- **关键 commit 集中在 5 个时间点**：3-28 / 4-05 / 6-19 / 6-26 / 7-04

**说明**：commit 频率不高，但**每次 commit 都伴随具体可验证的产物**——这是"质量 > 效率"的实践证据。

### 4. 没法量化的部分（诚实说明）

| 维度 | 为什么没法量化 |
|---|---|
| **6 条硬规则的执行效果** | 行为规则，无可观察的可测量指标；只能从"扎克的自留地 push back 次数"间接观察 |
| **session 启动 7 步是否真执行** | 启动过程对 AI 不可见；只能从"是否漏读某文件"间接推断 |
| **"写到 50% 停下来自检"实际触发率** | 没有 hook / log 记录"中途停下来的对话" |
| **"重大任务先推演再动手"命中率** | 同上，没有结构化字段记录 |
| **observations 是否真"永不主动加载"** | 加载策略对 AI 不透明——设计意图清楚，实际机制不透明 |
| **30 天观察期的规则升级率** | 机制刚建立（2026-07-04），没跑完完整周期 |
| **wiki lint 静默检查是否真跑了** | 需手动触发——是否触发、触发后处理了什么，无记录 |
| **archive 调减是否真生效** | `facts/` 目录存在但空——机制未触发过 |

**共同根因**：所有"行为类规则"都依赖 AI 自觉 + 设计者观察，没有 telemetry 层。

### 5. 可借鉴 / 不可借鉴

#### 5.1 可借鉴（已验证有效）

- **"同信息不写两处"原则**——用索引表落地，避免双源同步问题
- **"触发器式规则"**——把"该做什么"变成"什么场景下必做什么"，比"原则上要注意 X"可执行 10 倍
- **"凭据只过 key 不过 value"**——一条原则 + 一个文件 + 一份触发器列表，三件套
- **"状态枚举 + 调减规则"**——用 5 个状态枚举 + 时间窗口，让记忆不会无限膨胀
- **"层级化的文件加载机制"**——必读 / 按需 / 永不加载，三档分层
- **MEMORY.md 长期记忆"主 session 限定"加载**——从 2-20 沿用至今的安全底线

#### 5.2 不可借鉴（有强上下文依赖）

- **"永不主动加载 observations"**——依赖具体平台支持 + AI 工作区物理隔离机制
- **"飞书 Base 同步"**——依赖特定工具 + 特定账号
- **"heartbeat 静默检查"**——依赖具体平台 cron 工具；2026-06-30 已发生过 cron 工具挂掉事故
- **"所有规则的中文表述 + 扎克的自留地原话引用"**——风格极度个人化，**不可移植**

#### 5.3 仍待验证（处于观察期）

- 凭据通解的长期有效性（当前样本期 18 天）
- "30 天观察期" 升级机制是否真能跑通
- 调减规则（done 满 1 个月归档）是否真会触发
- 6 条硬规则在压力情境下是否能守住

### 6. 一句话总结

> 这套方法论的**可量化效果**集中在 3 个角度：
>
> - **容量控制**：MEMORY.md 缩水 95.5%（3362 → 152 行）；核心文件总行数缩水 74.7%
> - **错误下降**：凭据违规 80 天 2 次 → 18 天 0 次（样本期有限）
> - **协作流畅度**：11 条 cron 任务全部 ok / 60 次 git commit / 飞书同步稳定
>
> **没法量化的部分**明确标注（8 个维度）——共同根因是"行为规则没有 telemetry 层"。
>
> 这套方法论**仍在观察期**——核心改造只跑了 11 天，长期效果需要在未来 3-6 个月持续验证。

---

## English

### 0. Evaluation Framework

What standards do I use to judge this methodology as "effective"?

| Evaluation Angle | Metric | Data Source |
|---|---|---|
| **Capacity Control** | Core file lines / file count | File status + git log |
| **Error Decline** | Credential violation count / repeated error upgrade | ERRORS.md / LEARNINGS.md |
| **Collaboration Fluency** | Cron task count / auto sync status | `openclaw cron list` |

Detailed below by these 3 angles.

---

### 1. Capacity Control

#### 1.1 MEMORY.md Line Count Change (Core Metric)

| Time | MEMORY.md Lines | Phase |
|---|---|---|
| 2026-02-20 | **3362 lines** | Server-version starting point |
| 2026-03-27 | ~1000 lines | After local migration |
| 2026-06-26 | **152 lines** | After core refactor |

**Change**: 3362 → 152 = **95.5% reduction**.

**Why is this the core metric?**
- MEMORY.md is "AI long-term memory" — AI must-read every session
- Bloated = AI attention dispersion / context occupancy large
- Concise = AI attention focus / context utilization efficient

**Reduction path**:
- 2026-02-20 → 2026-03-27: local migration (cut 70%) — cut content scattered across multiple files
- 2026-03-27 → 2026-06-26: core refactor (cut another 85%) — use index table + loading mechanism instead of "stuff into MEMORY.md"

#### 1.2 Core File Count Change

| Time | Core File Count | Phase |
|---|---|---|
| 2026-02-20 | 11+ | Server version (AIZACH_ENTITY / AIZACH_RULES / EVOLUTION / BASIC_RULES etc) |
| 2026-03-27 | 6 | After local migration (simplification) |
| 2026-06-26 | **9** | After core refactor (re-layering + add IDENTITY / events / tasks) |

**Key observations**:
- 11 → 6: **-45%** (local migration cut redundancy)
- 6 → 9: **+50%** (core refactor add layering)

**Why is 6 → 9 a good thing?**
- The previous 6 files had some "composite files" — one file holding multiple functions
- After core refactor became "layered files" — each file with single responsibility
- File count grew but each file smaller, more focused

#### 1.3 Per Core File Line Count Change (2026-02-20 → 2026-07-07)

| File | Server (2026-02-20) | Current (2026-07-07) | Change |
|---|---|---|---|
| SOUL.md | 58 | 39 | -33% |
| AGENTS.md | 275 | 478 | +74% |
| **MEMORY.md** | **3362** | **152** | **-95.5%** |
| TOOLS.md | 206 | 253 | +23% |
| USER.md | 15 | 11 | -27% |
| HEARTBEAT.md | 70 | 29 | -59% |
| IDENTITY.md | ❌ doesn't exist | 46 | new |
| events.md | ❌ doesn't exist | 1 line + archive | new |
| tasks.md | ❌ doesn't exist | short | new |

**Key observations**:
- **MEMORY.md shrink 95.5%** — biggest single change of core refactor
- **AGENTS.md grew 74%** — merged rules scattered across multiple files
- **New files all "small and focused"** — IDENTITY.md 46 lines / HEARTBEAT.md 29 lines

**Total line count comparison**:
- Server version: 58 + 275 + 3362 + 206 + 15 + 70 = **3986 lines**
- Current: 39 + 478 + 152 + 253 + 11 + 29 + 46 + 1 + short ≈ **1009 lines**
- Total lines shrink **74.7%**

### 2. Error Decline

#### 2.1 Credential Violation Count (Most Critical Quantifiable Metric)

| Time Period | Credential Violation Count | Note |
|---|---|---|
| 2026-03-28 | 1 | API token leak into git record |
| 2026-04-17 | 1 | Sensitive info not checked |
| 2026-04-17 ~ 2026-06-19 | 0 | No violation yet |
| 2026-06-19 ~ 2026-07-07 | **0** | After credential solution finalized |

**Before credential solution**: 2 violations / ~80 days
**After credential solution**: 0 violations / 18 days

**Conclusion**:
- ✅ Credential violation count from 80 days 2 times → 18 days 0 times
- ⚠️ Sample period 18 days, statistical significance limited — needs longer cycle verification

**Why is this most important?**
- Credential violation = security incident, not "style issue"
- Credential violation from "repeated failure" to "0 times" — this is quantifiable evidence of 6 constraint mechanisms working in synergy

#### 2.2 Error Library Structure (ERRORS.md / LEARNINGS.md)

| File | H2 Entry Count | Total Lines | Status |
|---|---|---|---|
| .learnings/ERRORS.md | 2 | 50 | Normalized |
| .learnings/LEARNINGS.md | 8 | 213 | Continuously maintained |
| .learnings/credentials-handling.md | — | 99 | Credential solution |

**Key observations**:
- **ERRORS.md normalized** — previously errors scattered in SOUL.md / AGENTS.md / LEARNINGS.md
- **Upgrade path explicit** — 1 time → ERRORS.md / 2 times → LEARNINGS.md / 3+ times → AGENTS.md hard rule
- **30-day observation period** — pending-review/ directory tracks "rules under observation"

#### 2.3 Key Failure Events (git log excerpts)

| Time | commit | Note |
|---|---|---|
| 2026-03-28 | `fix: record API token leak error and add safety iron law to SOUL.md` | First credential violation |
| 2026-04-17 | `docs: sensitive info check (important)` | Add credential check rule |
| 2026-06-15 | `chore: delete 6-04 diary (扎克的自留地 2026-06-15 05:18 directive)` | Delete unauthorized product |
| 2026-06-15 | `fix: delete 'irony keyword' rule added without permission in ERRORS.md` | Delete rule AI added by itself |
| 2026-06-15 | `revert: delete §2.3.1 (added without permission)` | Delete section AI added by itself |

**Key observation**:
- **2026-06-15 three "delete AI-added-by-itself rules" in one day** — this is AGENTS.md §1.6 real trigger
- **Rules cannot be added by AI** — must be explicitly decided by designer (me)

### 3. Collaboration Fluency

#### 3.1 Cron Task Count

**Current active cron tasks: 11** (from `openclaw cron list`)

| cron | Time | Status |
|---|---|---|
| `1aad694f` Memory Dreaming Promotion | daily 03:00 | ok |
| `daily-ai-topics` Daily AI Topic Selection | daily 09:00 | ok |
| `46df0a62` daily-noon-thought | daily 12:00 | ok |
| `8ab43079` Daily Diary & Observations | daily 23:50 | ok |
| `348fc40e` daily-diary-retry-1 | daily 23:55 | ok |
| `e9df9f53` daily-errors-pdca | daily 23:58 | ok |
| `a1fa3dd6` daily-diary-retry-2 | daily 00:00 | ok |
| `ea96dc15` daily-diary-retry-3 | daily 00:05 | ok |
| `0747f3d9` daily-noon-thought-retry | daily 12:30 | ok |
| `f920e7a0` daily-noon-thought-retry | daily 13:00 | ok |
| `8e55d383` daily-errors-pdca (retry) | daily 23:59 | ok |

**All 11 status ok** — this is the core metric of collaboration fluency.

#### 3.2 Auto Sync Status

- **Feishu Base Sync**: Diary + observation records auto-sync, 2026-04-15 verified pass
- **daily-diary-retry**: 3 retry mechanisms to ensure backfill on failure
- **pending-review**: 30-day observation period auto-tracking

#### 3.3 Workspace Activity (git log)

- **Git total commits**: 60 (as of 2026-07-07)
- **First commit**: 2026-03-27
- **Average commit frequency**: 60 / 102 days ≈ **0.6 times/day**
- **Key commits concentrated in 5 time points**: 3-28 / 4-05 / 6-19 / 6-26 / 7-04

**Note**: Commit frequency is not high, but **each commit is accompanied by concrete verifiable products** — this is practical evidence of "quality > efficiency".

### 4. Parts That Cannot Be Quantified (Honest Disclosure)

| Dimension | Why Cannot Be Quantified |
|---|---|
| **6 hard rules execution effect** | Behavior rules, no observable measurable metrics; can only indirectly observe from "扎克的自留地 push back times" |
| **Session startup 7 steps actually executed** | Startup process opaque to AI; can only indirectly infer from "did I miss reading some file" |
| **"Stop at 50% to self-check" actual trigger rate** | No hook / log recording "mid-way stopped conversations" |
| **"Major tasks: state plan before acting" hit rate** | Same as above, no structured field recording |
| **observations really "never auto-load"** | Loading strategy opaque to AI — design intent clear, actual mechanism opaque |
| **30-day observation period rule upgrade rate** | Mechanism just established (2026-07-04), no complete cycle yet |
| **wiki lint silent check really ran** | Requires manual trigger — whether triggered, what processed after trigger, no record |
| **archive reduction really works** | `facts/` directory exists but empty — mechanism not triggered |

**Common root cause**: All "behavior-type rules" depend on AI self-discipline + designer observation, no telemetry layer.

### 5. Borrowable / Not Borrowable

#### 5.1 Borrowable (Verified Effective)

- **"Same info, written once" principle** — use index table to land, avoid dual-source sync issue
- **"Trigger-style rules"** — convert "what to do" to "in what scenario must do what", 10x more executable than "in principle be careful about X"
- **"Credentials key-only, no value"** — one principle + one file + one trigger list, three-piece set
- **"Status enums + reduction rules"** — use 5 status enums + time windows, memory won't infinitely expand
- **"Hierarchical file loading mechanism"** — must-read / on-demand / never-load, three-tier layering
- **MEMORY.md long-term memory "main session limited" loading** — security baseline since 2-20

#### 5.2 Not Borrowable (Strong Context Dependency)

- **"Never auto-load observations"** — depends on specific platform support + AI workspace physical isolation mechanism
- **"Feishu Base sync"** — depends on specific tool + specific account
- **"Heartbeat silent check"** — depends on specific platform cron tool; 2026-06-30 already had cron tool crash incident
- **"All rules Chinese expression + 扎克的自留地 original quote"** — extremely personal style, **not portable**

#### 5.3 Still Pending Verification (In Observation Period)

- Credential solution long-term effectiveness (current sample period 18 days)
- Whether "30-day observation period" upgrade mechanism really works
- Whether reduction rules (done > 1 month archive) really triggers
- Whether 6 hard rules hold under pressure

### 6. One-line Summary

> This methodology's **quantifiable effect** concentrates in 3 angles:
>
> - **Capacity control**: MEMORY.md shrink 95.5% (3362 → 152 lines); core file total lines shrink 74.7%
> - **Error decline**: credential violation 80 days 2 times → 18 days 0 times (limited sample period)
> - **Collaboration fluency**: 11 cron tasks all ok / 60 git commits / Feishu sync stable
>
> **Parts that cannot be quantified** explicitly marked (8 dimensions) — common root cause is "behavior rules have no telemetry layer".
>
> This methodology **is still in observation period** — core refactor only ran 11 days, long-term effect needs continuous verification in next 3-6 months.

---

## 完结 / End

→ 回到 [README.md](../README.md) 查看完整目录结构