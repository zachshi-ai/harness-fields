# 04 · 159 天演进路径

> 159-Day Evolution Path
>
> 本章记录这套方法论在 159 天里经过的关键节点。每个节点都有具体可验证的产物（commit / 文件 / cron），不是叙事性回顾。
>
> 作者：扎克的自留地

---

## 中文

### 0. 全景时间线

```
2026-01-29              2026-02-20              2026-03-27              2026-06-26              2026-07-07
   │                        │                       │                       │                      │
   ▼                        ▼                       ▼                       ▼                      ▼
 moltbot              Server Backup           Local Migration         Memory Architecture     Today
（产品代号 1）         （产物起点）              （git 起点）              Refactor              （体系成型）
```

| 阶段 | 时间 | 关键产物 |
|---|---|---|
| **moltbot 阶段** | 2026-01-29 ~ 2026-02-16 | 服务器版，产物已丢失 |
| **服务器版备份** | 2026-02-17 ~ 2026-02-20 | 最老的本地备份（含 SOUL.md / AGENTS.md / MEMORY.md / TOOLS.md） |
| **本地化迁移** | 2026-03-27 | git init + 重写核心文件 |
| **质量规则强化** | 2026-04 ~ 2026-06-07 | SOUL.md 6 条硬规则 + AGENTS.md 工作流规则 |
| **凭据通解** | 2026-06-19 | credentials-handling.md 99 行 + AGENTS.md §1.2.1 触发器 |
| **核心改造** | 2026-06-26 | 记忆架构整体方案落地 |
| **错误 PDCA** | 2026-07-04 | ERRORS.md 规范化 + 30 天观察期 |

### 1. moltbot 阶段（2026-01-29 ~ 2026-02-16）

**起点**：2026-01-29。

**当时的产品形态**：
- moltbot 是当时的产品代号
- 服务器版——所有产物在服务器上
- 我和我的 AI 助手开始长期协作

**这一阶段的产物状态**：
- ❌ **永久丢失**——服务器已下线，1-29 到 2-16 之间的所有产物无法找回
- 我能记得的只有"那一阶段确实存在、确实跑通过"
- 这是整套方法论的**真正的起点**，但**没有可对比的产物**

**这一阶段的特征**：
- AI 每次 session 全新启动的问题暴露
- 我开始反复失败、反复承认（详见 [docs/01-architecture.md §0](01-architecture.md#0-这套系统的起源为什么会有它)）
- 但当时的"约束"主要是原则性的话——"凭据不能出现"、"改完要验证"

### 2. 服务器版备份（2026-02-17 ~ 2026-02-20）

**关键节点**：2026-02-17、2026-02-19、2026-02-20 三次备份。

**当时的核心文件**（来自 `emergency-backups/20260220-020803/`）：

| 文件 | 行数 | 内容 |
|---|---|---|
| SOUL.md | 58 行 | 美式 AI 人格化模板（"Be resourceful"、"Earn trust"、"Have opinions"） |
| AGENTS.md | 275 行 / 10 H2 | 含 First Run / Every Session / Memory / Safety / External vs Internal / Group Chats / Tools / Heartbeats 等 |
| MEMORY.md | **3362 行** / 3 H2 | 臃肿——把一切记忆塞进一个文件 |
| TOOLS.md | 206 行 / 8 H2 | 工具路径 + 凭据 + 部署命令 |
| USER.md | 15 行 | 用户信息 |
| HEARTBEAT.md | 70 行 / 4 H2 | 心跳检查项 |

**额外文件**：
- `AIZACH_ENTITY.md`（199 行）
- `AIZACH_RULES.md`（157 行）
- `EVOLUTION.md`（103 行）
- `BASIC_RULES.md`（51 行）
- `MEMORY_STDE_SKILLS.md`
- `ZERO_DEPENDENCY_COMMERCIALIZATION.md`

**这一阶段的特征**：
- **方法论不成熟**——11 个分散文件，单 MEMORY.md 3362 行
- **AI 模板化痕迹**——SOUL.md 是美式人格化模板
- **没有"加载机制"概念**——文件基本是"全量灌入"
- **没有"凭据通解"**——靠原则性的话约束

**这一阶段的产物**：
- `/Users/zach/workspace/server-backup/emergency-backups/` 下的 4 个备份目录
- `server-backup/memory/archive/2026-02-MEMORY.md`（72 行精简版）
- 我目前能在本地对比的**最老的产物**

### 3. 本地化迁移（2026-03-27）

**关键节点**：2026-03-27，git 首次 commit。

**这次迁移做了什么**（根据 git log）：
- `git init` + 创建工作区
- 重写 4 个核心文件（AGENTS.md / SOUL.md / MEMORY.md / TOOLS.md）
- 删掉 AIZACH_ENTITY.md / AIZACH_RULES.md / EVOLUTION.md / BASIC_RULES.md 等冗余文件
- 删除多个 "before-fix" 备份

**本地化后 vs 服务器版对比**：

| 指标 | 服务器版（2026-02-20） | 本地化后（2026-03-27） | 变化 |
|---|---|---|---|
| 核心文件数 | 11+ | 6 | **-45%** |
| SOUL.md 行数 | 58 | 39 | -33% |
| AGENTS.md 行数 | 275 | 478 | **+74%**（重写 + 加规则） |
| MEMORY.md 行数 | **3362** | ~1000 | **-70%** |
| TOOLS.md 行数 | 206 | 253 | +23% |
| HEARTBEAT.md 行数 | 70 | 29 | -59% |

**这次迁移的本质**：
- ✅ **删繁就简**——把 11 个分散文件砍到 6 个核心
- ✅ **重写哲学层**——SOUL.md 从"美式人格模板"改成"扎克的自留地的硬规则"
- ✅ **MEMORY.md 缩 70%**——从 3362 行砍到 1000 行
- ⚠️ **AGENTS.md 加 74%**——把分散在多个文件的规则合并进来

### 4. 质量规则强化（2026-04 ~ 2026-06-07）

**关键节点**：2026-06-07，SOUL.md 6 条硬规则定稿。

**这一阶段的关键 commit**（git log 摘选）：
- `add self-review rule to AGENTS.md before writing diary/observations`（2026-03-28）
- `fix: 记录 API token 泄露错误并追加安全铁律到 SOUL.md`（2026-03-28）
- `chore: clean up duplicate cron jobs, update daily diary cron`（2026-04-04）
- `feat: add daily diary cron job + backfill Lark Base entries`（2026-04-05）
- `feat: add art deployment docs, fix nginx permissions for static assets`（2026-04-06）
- `chore: 删除 6-04 日记（扎克的自留地 2026-06-15 05:18 指令）`（2026-06-15）
- `fix: 删除 ERRORS.md 里未经许可增加的'反讽关键词'规则`（2026-06-15）

**SOUL.md 6 条硬规则**：
1. 质量 > 效率
2. 重大任务先推演再动手
3. 写到 50% 停下来自检
4. 不会做就说不会做
5. 改完必须 force run / 看到产物
6. 重复错误消耗信任

**这一阶段的特征**：
- **反复试错**——3-28 的 API token 泄露教训、4-17 的敏感信息检查、6-15 两次"删除未经许可的规则"——都是失败循环的具体事件
- **规则逐步收敛**——从原则性的话 → 触发器式规则 → 硬规则

### 5. 凭据通解（2026-06-19）

**关键节点**：2026-06-19，credentials-handling.md 定稿。

**这次定稿做了什么**（git log）：
- `fix(agents): §1.2.1 凭据通解（指纹/token/JWT 模式表 + 路径禁令 + 输出过滤）`
- `fix(agents): §1.2.1 凭据通解改写为触发器 + 新增 .learnings/credentials-handling.md`

**关键文件**：
- `.learnings/credentials-handling.md`（99 行）—— 5 条规则 + 调用形式区分 + 模式表 + 案例
- `AGENTS.md §1.2.1`——触发器清单

**凭据通解的核心**：
- **"只过 key，不过 value"**——核心原则
- **8 类触发器**——什么场景下必读 credentials-handling.md
- **违反后果**——"信任归零，同类错每犯一次耐心 -10"

**量化效果**：
- 通解定稿前：2026-03-28、2026-04-17 各有 1 次凭据违规
- 通解定稿后：**0 次**（样本期 18 天，2026-06-19 ~ 2026-07-07）

### 6. 核心改造（2026-06-26）

**关键节点**：2026-06-26，记忆架构整体方案落地。

**关键 commit**（git log）：
- `c19d256 feat: 记忆架构整体方案落地（2026-06-26凌晨）`
- `32ad422 feat: 补完架构剩余文件（identity 三场景 + facts 归档目录）`（2026-06-27）

**这次改造做了什么**：
- **新增 IDENTITY.md**（46 行）——对话身份 + 产物署名映射
- **新增 events.md**（1 行 + 归档）——近 1 个月动态事件链
- **新增 tasks.md**——open 待办
- **新增 identity/**（特殊身份子目录）
- **新增 facts/**（事件归档目录）
- **MEMORY.md 从 1000 行砍到 152 行**——核心改造最大的单一数字变化（缩水 85%）

**核心改造前后对比**：

| 指标 | 本地化后（2026-03-27） | 核心改造后（2026-06-26） | 变化 |
|---|---|---|---|
| 核心文件数 | 6 | **9** | +50% |
| MEMORY.md 行数 | ~1000 | **152** | **-85%** |
| IDENTITY.md | ❌ | 46 行 | 新增 |
| events.md | ❌ | 1 行 + 归档 | 新增 |
| tasks.md | ❌ | 简短 | 新增 |
| 加载机制 | 隐式 | 显式（必读 / 按需 / 永不加载） | 强化 |
| 索引表 | ❌ | AGENTS.md §5.5 | 新增 |

**这次改造的本质**：
- ✅ **重新分层**——从 6 个文件扩到 9 个（IDENTITY / events / tasks / identity/ / facts/）
- ✅ **加载机制显式化**——必读 / 按需 / 永不加载 三档分层
- ✅ **MEMORY.md 极致精简**——从 1000 行砍到 152 行（缩水 85%）

### 7. 错误 PDCA（2026-07-04）

**关键节点**：2026-07-04，ERRORS.md 规范化。

**关键 commit**（git log）：
- `651dfb9 docs: add pending-review 30-day observation tracker`
- `60b855c fix: AGENTS.md §2.3.1 + review_errors.py + ERRORS规范化`
- `4c1395b revert: 删除 §2.3.1（未经许可擅自增加）`
- `d943c5e fix: 删除 ERRORS.md 里未经许可增加的'反讽关键词'规则`
- `e6c53be chore: snapshot workspace state before ERRORS PDCA setup`

**这次规范化做了什么**：
- **ERRORS.md 独立成文**——2 条 H2 标题 / 50 行
- **LEARNINGS.md 持续维护**——8 条 H2 / 213 行
- **pending-review/ 目录**——30 天观察期
- **daily-errors-pdca cron**——每天 23:58 自动 review

**关键事件**：
- **2026-06-15 05:18**——删除未经许可增加的'反讽关键词'规则
- **2026-06-15 05:15**——删除 §2.3.1（未经许可擅自增加）
- 这两次"删除"事件说明：**规则不是 AI 自己能加的，必须由我显式决定**

### 8. 一句话总结

> 这套方法论在 159 天里经历了 **5 个关键节点**：
>
> 1. **2026-01-29 moltbot 阶段**——产物已丢失（起点）
> 2. **2026-02-20 服务器版备份**——最老的可对比产物（11 文件 / MEMORY 3362 行）
> 3. **2026-03-27 本地化迁移**——6 文件 / MEMORY 1000 行（删繁就简）
> 4. **2026-06-26 核心改造**——9 文件 / MEMORY 152 行（重新分层 + 加载机制显式）
> 5. **2026-07-04 错误 PDCA**——30 天观察期 + daily cron
>
> 每次演进都伴随具体可验证的产物（commit / 文件 / 行数），不是叙事性回顾。

---

## English

### 0. Panoramic Timeline

```
2026-01-29              2026-02-20              2026-03-27              2026-06-26              2026-07-07
   │                        │                       │                       │                      │
   ▼                        ▼                       ▼                       ▼                      ▼
 moltbot              Server Backup           Local Migration         Memory Architecture     Today
(Code Name 1)         (Earliest Products)     (Git Starting Point)    Refactor                (System Formed)
```

| Phase | Time | Key Products |
|---|---|---|
| **moltbot Phase** | 2026-01-29 ~ 2026-02-16 | Server-only, products lost |
| **Server Backup** | 2026-02-17 ~ 2026-02-20 | Earliest local backups (with SOUL.md / AGENTS.md / MEMORY.md / TOOLS.md) |
| **Local Migration** | 2026-03-27 | git init + rewrite core files |
| **Quality Rule Reinforcement** | 2026-04 ~ 2026-06-07 | SOUL.md 6 hard rules + AGENTS.md workflow rules |
| **Credential Solution** | 2026-06-19 | credentials-handling.md 99 lines + AGENTS.md §1.2.1 triggers |
| **Core Refactor** | 2026-06-26 | Memory architecture landing |
| **Error PDCA** | 2026-07-04 | ERRORS.md normalization + 30-day observation |

### 1. moltbot Phase (2026-01-29 ~ 2026-02-16)

**Start**: 2026-01-29.

**Product form at that time**:
- moltbot was the product code name
- Server version — all products on the server
- I started long-term collaboration with my AI assistant

**Product state of this phase**:
- ❌ **Permanently lost** — server went offline, all products from 1-29 to 2-16 cannot be recovered
- All I can remember is "this phase did exist, did run"
- This is the **true starting point** of the methodology, but **has no comparable products**

**Characteristics of this phase**:
- AI every-session-fresh problem exposed
- I started repeatedly failing, repeatedly admitting (see [docs/01-architecture.md §0](01-architecture.md#0-the-origin-of-this-system-why-it-exists))
- But "constraints" at the time were mainly principled words — "credentials must not appear", "must verify after changes"

### 2. Server Backup (2026-02-17 ~ 2026-02-20)

**Key nodes**: 2026-02-17, 2026-02-19, 2026-02-20 three backups.

**Core files at that time** (from `emergency-backups/20260220-020803/`):

| File | Lines | Content |
|---|---|---|
| SOUL.md | 58 lines | American AI persona template ("Be resourceful", "Earn trust", "Have opinions") |
| AGENTS.md | 275 lines / 10 H2 | Includes First Run / Every Session / Memory / Safety / External vs Internal / Group Chats / Tools / Heartbeats |
| MEMORY.md | **3362 lines** / 3 H2 | Bloated — stuffing all memory into one file |
| TOOLS.md | 206 lines / 8 H2 | Tool paths + credentials + deploy commands |
| USER.md | 15 lines | User info |
| HEARTBEAT.md | 70 lines / 4 H2 | Heartbeat checks |

**Extra files**:
- `AIZACH_ENTITY.md` (199 lines)
- `AIZACH_RULES.md` (157 lines)
- `EVOLUTION.md` (103 lines)
- `BASIC_RULES.md` (51 lines)
- `MEMORY_STDE_SKILLS.md`
- `ZERO_DEPENDENCY_COMMERCIALIZATION.md`

**Characteristics of this phase**:
- **Immature methodology** — 11 scattered files, single MEMORY.md 3362 lines
- **AI templating traces** — SOUL.md was American persona template
- **No "loading mechanism" concept** — files basically "full-load"
- **No "credential solution"** — relied on principled words for constraint

**Products of this phase**:
- 4 backup directories under `/Users/zach/workspace/server-backup/emergency-backups/`
- `server-backup/memory/archive/2026-02-MEMORY.md` (72 lines simplified version)
- **Oldest comparable product** I can compare locally

### 3. Local Migration (2026-03-27)

**Key node**: 2026-03-27, git first commit.

**What this migration did** (per git log):
- `git init` + create workspace
- Rewrite 4 core files (AGENTS.md / SOUL.md / MEMORY.md / TOOLS.md)
- Delete AIZACH_ENTITY.md / AIZACH_RULES.md / EVOLUTION.md / BASIC_RULES.md etc redundant files
- Delete multiple "before-fix" backups

**Local-after vs Server-version comparison**:

| Metric | Server (2026-02-20) | Local-after (2026-03-27) | Change |
|---|---|---|---|
| Core file count | 11+ | 6 | **-45%** |
| SOUL.md lines | 58 | 39 | -33% |
| AGENTS.md lines | 275 | 478 | **+74%** (rewrite + add rules) |
| MEMORY.md lines | **3362** | ~1000 | **-70%** |
| TOOLS.md lines | 206 | 253 | +23% |
| HEARTBEAT.md lines | 70 | 29 | -59% |

**Essence of this migration**:
- ✅ **Simplification** — cut 11 scattered files to 6 core
- ✅ **Rewrite philosophy layer** — SOUL.md changed from "American persona template" to "扎克的自留地's hard rules"
- ✅ **MEMORY.md shrink 70%** — from 3362 lines to 1000
- ⚠️ **AGENTS.md grew 74%** — merge rules scattered across multiple files

### 4. Quality Rule Reinforcement (2026-04 ~ 2026-06-07)

**Key node**: 2026-06-07, SOUL.md 6 hard rules finalized.

**Key commits of this phase** (git log excerpts):
- `add self-review rule to AGENTS.md before writing diary/observations` (2026-03-28)
- `fix: record API token leak error and add safety iron law to SOUL.md` (2026-03-28)
- `chore: clean up duplicate cron jobs, update daily diary cron` (2026-04-04)
- `feat: add daily diary cron job + backfill Lark Base entries` (2026-04-05)
- `feat: add art deployment docs, fix nginx permissions for static assets` (2026-04-06)
- `chore: delete 6-04 diary (扎克的自留地 2026-06-15 05:18 directive)` (2026-06-15)
- `fix: delete 'irony keyword' rule added without permission in ERRORS.md` (2026-06-15)

**SOUL.md 6 hard rules**:
1. Quality > Efficiency
2. Major tasks: state plan before acting
3. Stop at 50% to self-check
4. Can't do = say can't do
5. After changes must force run / see artifact
6. Repeated errors consume trust

**Characteristics of this phase**:
- **Repeated trial and error** — 3-28 API token leak lesson, 4-17 sensitive info check, 6-15 two "delete without permission" events — all specific events of the failure loop
- **Rules gradually converge** — from principled words → trigger-style rules → hard rules

### 5. Credential Solution (2026-06-19)

**Key node**: 2026-06-19, credentials-handling.md finalized.

**What this finalization did** (git log):
- `fix(agents): §1.2.1 credential solution (fingerprint/token/JWT pattern table + path ban + output filter)`
- `fix(agents): §1.2.1 credential solution rewrite to trigger + add .learnings/credentials-handling.md`

**Key files**:
- `.learnings/credentials-handling.md` (99 lines) — 5 rules + invocation form distinction + pattern table + cases
- `AGENTS.md §1.2.1` — trigger list

**Core of credential solution**:
- **"key-only, no value"** — core principle
- **8 types of triggers** — what scenarios must read credentials-handling.md
- **Violation consequence** — "trust to zero, same type of error -10 patience each time"

**Quantitative effect**:
- Before solution: 2026-03-28, 2026-04-17 each had 1 credential violation
- After solution: **0 violations** (sample period 18 days, 2026-06-19 ~ 2026-07-07)

### 6. Core Refactor (2026-06-26)

**Key node**: 2026-06-26, memory architecture overall solution landed.

**Key commits** (git log):
- `c19d256 feat: 记忆架构整体方案落地（2026-06-26凌晨）`
- `32ad422 feat: 补完架构剩余文件（identity 三场景 + facts 归档目录）` (2026-06-27)

**What this refactor did**:
- **New IDENTITY.md** (46 lines) — dialogue identity + signature mapping
- **New events.md** (1 line + archive) — last 1 month dynamic event chain
- **New tasks.md** — open tasks
- **New identity/** (special identity subdirectory)
- **New facts/** (event archive directory)
- **MEMORY.md from 1000 lines cut to 152 lines** — biggest single number change of core refactor (85% reduction)

**Core refactor before/after comparison**:

| Metric | Local-after (2026-03-27) | Core-after (2026-06-26) | Change |
|---|---|---|---|
| Core file count | 6 | **9** | +50% |
| MEMORY.md lines | ~1000 | **152** | **-85%** |
| IDENTITY.md | ❌ | 46 lines | New |
| events.md | ❌ | 1 line + archive | New |
| tasks.md | ❌ | Short | New |
| Loading mechanism | Implicit | Explicit (must-read / on-demand / never-load) | Strengthened |
| Index table | ❌ | AGENTS.md §5.5 | New |

**Essence of this refactor**:
- ✅ **Re-layering** — expanded from 6 files to 9 (IDENTITY / events / tasks / identity/ / facts/)
- ✅ **Loading mechanism explicit** — 3-tier layering: must-read / on-demand / never-load
- ✅ **MEMORY.md extreme simplification** — from 1000 lines to 152 lines (85% reduction)

### 7. Error PDCA (2026-07-04)

**Key node**: 2026-07-04, ERRORS.md normalization.

**Key commits** (git log):
- `651dfb9 docs: add pending-review 30-day observation tracker`
- `60b855c fix: AGENTS.md §2.3.1 + review_errors.py + ERRORS normalization`
- `4c1395b revert: delete §2.3.1 (added without permission)`
- `d943c5e fix: delete 'irony keyword' rule added without permission in ERRORS.md`
- `e6c53be chore: snapshot workspace state before ERRORS PDCA setup`

**What this normalization did**:
- **ERRORS.md as independent document** — 2 H2 / 50 lines
- **LEARNINGS.md continuously maintained** — 8 H2 / 213 lines
- **pending-review/ directory** — 30-day observation period
- **daily-errors-pdca cron** — auto review daily at 23:58

**Key events**:
- **2026-06-15 05:18** — delete 'irony keyword' rule added without permission
- **2026-06-15 05:15** — delete §2.3.1 (added without permission)
- These two "delete" events explain: **rules cannot be added by AI itself, must be explicitly decided by me**

### 8. One-line Summary

> This methodology has gone through **5 key nodes** in 159 days:
>
> 1. **2026-01-29 moltbot Phase** — products lost (starting point)
> 2. **2026-02-20 Server Backup** — oldest comparable product (11 files / MEMORY 3362 lines)
> 3. **2026-03-27 Local Migration** — 6 files / MEMORY 1000 lines (simplification)
> 4. **2026-06-26 Core Refactor** — 9 files / MEMORY 152 lines (re-layering + loading mechanism explicit)
> 5. **2026-07-04 Error PDCA** — 30-day observation + daily cron
>
> Each evolution accompanied by concrete verifiable products (commit / file / line count), not narrative review.

---

## 下一章 / Next

→ [docs/05-evaluation.md](05-evaluation.md) — 量化佐证与效果评价 / Quantitative evidence and evaluation