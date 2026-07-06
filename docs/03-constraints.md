# 03 · 6 种约束机制详解

> 6 Constraint Mechanisms in Detail
>
> 本章承接 [docs/02-philosophy.md](02-philosophy.md) 的 4 条底层信念——把这 4 条信念落到具体可执行的 6 种约束机制上。
>
> 作者：扎克的自留地

---

## 中文

### 0. 6 种机制的全景

| # | 机制 | 一句话 | 落地位置 |
|---|---|---|---|
| 1 | 硬规则 | 违反必停 | AGENTS.md §1 |
| 2 | 触发器 | 场景化拦截 | AGENTS.md §1.2.1 |
| 3 | 状态枚举 | to-do / doing / done / stop / drop | events.md / tasks.md |
| 4 | 时间窗口 | 事件调减 / 30 天观察 / 启动 7 步 | events.md / observations/ / AGENTS.md §2.1 |
| 5 | 加载机制 | 必读 / 按需 / 永不加载 | AGENTS.md §5 |
| 6 | 索引表 | 要查的东西 → 去哪 | AGENTS.md §5.5 |

下面逐个展开。

---

### 1. 硬规则（Hard Rules）

**一句话**：违反必停，没有"灰色地带"。

**为什么需要？**
- AI 自己判断"严不严重"不可靠
- 必须把所有规则拉到同一档——只要违反就停

**核心约定**：
- AGENTS.md §1 整章只放硬规则
- 任何一条规则如果写得含糊（"应该"、"尽量"、"原则上"）——立即升级为触发器或删除
- 硬规则的边界由"违反后果"决定，不是"严重程度"决定

**反 AI 模板化原则**（来自 02 §3）：
- "Have opinions" → ❌ 删掉（没有可执行性）
- "凭据 value 不能出现" → ❌ 也删掉（AI 可能违反）
- "调用 curl 带 Authorization header 前必读 credentials-handling.md" → ✅ 留（场景+动作+可验证）

**实际硬规则例子**（来自我的 AGENTS.md §1）：
- 路径区分：AI 工作区 = `~/.openclaw/workspace/` ≠ 用户项目目录 = `/Users/zach/workspace/`
- 敏感信息检查：任何 HTTP 请求 / shell 命令 / 写文件 / 分享链接前，逐项检查是否含真实凭据
- 项目文件版本管理：修改 .html/.css/.js/.ts 等项目文件前必须存档到 `versions/vX.Y.Z-*.ext`
- 新项目初始化：创建项目目录后必须 git init + 初始 commit
- 凭据只过 key 不过 value

**违反后果**：硬规则违反 = session 立即停止 + 写入 .learnings/ERRORS.md + 计入信任消耗。

---

### 2. 触发器（Triggers）

**一句话**：在什么场景下，**必做什么**。

**为什么需要？**
- 原则 = AI 可能违反
- 触发器 = AI 不可能违反（因为它嵌进了"必读"流程）

**核心约定**：
- 每条触发器必须有 **3 要素**：
  - **触发条件**（什么场景触发）
  - **必做动作**（具体读什么文件 / 调什么命令）
  - **可验证标准**（怎么算做了）
- 触发器清单统一放在 AGENTS.md §1.2.1 + .learnings/credentials-handling.md

**典型触发器例子**（凭据通解 8 类）：
1. `git push` / `git config user.*` / `git commit --author` / `git remote add` → 前必读 credentials-handling.md
2. `ssh` / `ssh-add` / `ssh-keygen` → 前必读 credentials-handling.md
3. exec 涉及路径 `~/.ssh/` / `~/.aws/` / `~/.kube/` / `~/.docker/` / `~/.npmrc` / `~/.pypirc` / `~/.netrc` / `~/.config/gh/` → 前必读
4. 读 `.env` / `*.env` / `printenv` / `env | grep` → 前必读
5. 用户在对话里给 token / password / 私钥 / chat_id → 前必读
6. `curl` / `wget` / `fetch` 带 `Authorization` / `Bearer` / `X-API-Key` / `Token` header → 前必读
7. `cat` / `read` 任何含 `token` / `password` / `secret` / `credential` / `key` 字眼的文件 → 前必读
8. 写文件（commit / 配置文件 / 笔记）含凭据嫌疑 → 前必读

**量化效果**（来自 [docs/05-evaluation.md](05-evaluation.md)）：
- 凭据通解定稿于 2026-06-19
- 定稿前：2026-03-28、2026-04-17 各有 1 次凭据违规
- 定稿后：**0 次**（样本期 18 天）

---

### 3. 状态枚举（Status Enums）

**一句话**：用 5 个固定状态描述事件/任务生命周期，禁止"自定义状态"。

**5 个状态**：
- **to-do** — 已知但未启动
- **doing** — 进行中
- **done** — 完成
- **stop** — 主动暂停
- **drop** — 明确放弃

**为什么需要？**
- 没有状态枚举，"完成了还是没完成"靠 AI 自报——不可信
- 没有"stop / drop" 区分，"暂停"和"放弃"混在一起——状态混乱

**核心约定**：
- events.md：当前 doing / to-do + 近 1 个月 done
- tasks.md：open 待办（to-do / doing / stop / drop）
- done 满 1 个月 → 从 events.md 删除 → 双写进 `facts/history/by-{month,event}/`
- stop / drop 必须写明原因

**为什么"done 满 1 个月"才归档？**
- 完成 1 个月内可能需要回查（"上次我做过 X 吗？"）
- 1 个月后基本不会被引用，继续留在 events.md 是 noise

**状态机的反 AI 模板化**：
- 不写"重要程度：高/中/低"——AI 自己判断"重要"不可靠
- 只写"状态"——状态由事件进展决定，不由 AI 主观判定

---

### 4. 时间窗口（Time Windows）

**一句话**：用时间约束代替 AI 自觉。

**3 类时间窗口**：

#### 4.1 事件调减（done 满 1 个月归档）
- 触发：事件 done 满 1 个月
- 动作：从 events.md 删除 → 双写 `facts/history/by-month/YYYY-MM.md` + `facts/history/by-event/YYYY-MM-DD-slug.md`
- 目的：events.md 永远保持"近 1 个月"，不被历史淹没

#### 4.2 30 天观察期（pending-review）
- 触发：新规则写入 AGENTS.md 时，先进 `.learnings/pending-review/`
- 观察期：30 天
- 通过：升级为正式规则
- 不通过：降级回 ERRORS.md 或删除

**为什么需要 30 天？**
- 新规则刚写时，AI 容易"形式上遵守、实质上违反"
- 30 天能观察"在压力情境下 AI 是否真能守住"
- 比"一次性规则升级"更可靠

#### 4.3 session 启动 7 步检查清单（AGENTS.md §2.1）
- 触发：每次 session 启动
- 7 步：
  1. 读 SOUL.md（身份与底线）
  2. 读 USER.md（协作者信息）
  3. 读 memory/YYYY-MM-DD.md（今天 + 昨天）
  4. 仅主 session：读 MEMORY.md
  5. 读 TODO.md（待办）
  6. Wiki Lint 静默检查（孤立页面、过期 > 60 天未更新、index 同步）
  7. 准备好后开始对话

**为什么需要 7 步清单？**
- AI 自己判断"我需要读什么文件"——不可靠
- 强制 7 步 → 流程上不可能跳过 SOUL.md / AGENTS.md

---

### 5. 加载机制（Loading Mechanisms）

**一句话**：3 档分层（必读 / 按需 / 永不加载），AI 不准自己加档。

**3 档分层**：

| 档位 | 加载时机 | 适用 |
|---|---|---|
| **必读** | 每次 session 启动 | SOUL.md / AGENTS.md / MEMORY.md / IDENTITY.md / TOOLS.md / USER.md / HEARTBEAT.md |
| **按需** | 任务相关时才查 | events.md / tasks.md / wiki/相关 / memory/今天昨天 |
| **永不加载** | AI 不准主动调度 | diary/ / observations/ / chat-log/ / .learnings/历史 / facts/历史 |

**为什么需要 3 档？**
- "全量灌入" = context 爆炸 + AI 注意力分散
- "AI 自己判断读什么" = 不可靠（AI 会挑容易的读，跳过难的）

**核心约定**：
- 任何文件加档必须由"我"（设计者）显式决定，AI 不准自己加档
- 永不加载 ≠ 不存在；observations/ 一直在那里，但 AI 不主动读
- "永不加载"是**最强的约束**——比"必读"还强，因为"必读"还可以"读错"，但"永不加载"是结构性不读

**MEMORY.md 的特殊规则**：
- **仅主 session 加载**（这是双重约束：必读 + 主 session only）
- 非主 session 不读 MEMORY.md（不读就不可能泄露）

---

### 6. 索引表（Index Tables）

**一句话**：用一张表告诉 AI "要查的东西在哪"，避免 AI 自己找。

**核心约定**（来自 AGENTS.md §5.5）：

| 要查的东西 | 去哪 |
|---|---|
| 工作规则 | AGENTS.md |
| AI 自身价值观 | SOUL.md |
| 身份 + 产物署名 | IDENTITY.md |
| 核心事实 / 工具偏好 / 关键决策 | MEMORY.md |
| 动态事件链（近 1 个月） | events.md |
| 待办任务 | tasks.md |
| 对协作者的观察 | observations/observations-*.md |
| 部署命令 / token / URL | TOOLS.md |
| 一次性错误 | .learnings/ERRORS.md |
| 经验教训 | .learnings/LEARNINGS.md |
| 凭据通解 | .learnings/credentials-handling.md |
| 项目状态 / 产品方向 | wiki/ |
| 想法草稿 | memory/thoughts-*.md |
| 长对话存档 | chat-log/chat-*.md |

**为什么需要索引表？**
- AI 不知道"那条规则在 AGENTS.md §几"——靠猜
- 索引表 = **单一入口**——AI 只需要查一张表

**索引表的反 AI 模板化**：
- 不是"AI 应该记得每个文件的作用"——这不可靠
- 而是"AI 只需要查一张表"——这可执行

---

### 7. 6 种机制的协同关系

```
信念一（不靠自省靠结构）
   ↓ 落到
   ├─ 硬规则（违反必停）
   ├─ 触发器（场景化拦截）
   └─ 加载机制（必读 / 按需 / 永不加载）
   
信念二（同信息不写两处）
   ↓ 落到
   └─ 索引表（要查的东西 → 去哪）
   
信念三（知识分级写入）
   ↓ 落到
   ├─ 状态枚举（to-do / doing / done / stop / drop）
   └─ 时间窗口（事件调减 / 30 天观察 / 启动 7 步）

信念四（harness = 不可能）
   ↓ 统摄所有 6 种机制
   - 每一种机制都在做"让某些事不可能"
```

**这 6 种机制不是平行堆叠，是相互嵌套的**：
- 硬规则需要触发器支撑（"什么场景触发硬规则"）
- 触发器需要加载机制支撑（"AI 必读了 credentials-handling.md 才能用触发器"）
- 加载机制需要索引表支撑（"AI 知道 credentials-handling.md 在哪"）
- 时间窗口需要状态枚举支撑（"done 满 1 个月"靠状态判断）

**任何一种机制独立拿出来都不完整，6 种组合在一起才形成"不可能"。**

### 8. 一句话总结

> 这 6 种约束机制不是设计出来的"功能列表"，是**4 条底层信念的工程落地**：
>
> - **硬规则 + 触发器 + 加载机制** ← 信念一（不靠自省靠结构）
> - **索引表** ← 信念二（同信息不写两处）
> - **状态枚举 + 时间窗口** ← 信念三（知识分级写入）
> - **统摄所有 6 种** ← 信念四（harness = 不可能）

---

## English

### 0. Overview of the 6 Mechanisms

| # | Mechanism | One-liner | Location |
|---|---|---|---|
| 1 | Hard Rules | Violation stops immediately | AGENTS.md §1 |
| 2 | Triggers | Context-aware interception | AGENTS.md §1.2.1 |
| 3 | Status Enums | to-do / doing / done / stop / drop | events.md / tasks.md |
| 4 | Time Windows | Event reduction / 30-day observation / 7-step startup | events.md / observations/ / AGENTS.md §2.1 |
| 5 | Loading Mechanisms | Must-read / on-demand / never-load | AGENTS.md §5 |
| 6 | Index Tables | What to look up → where to look | AGENTS.md §5.5 |

Detail below.

---

### 1. Hard Rules

**One-liner**: Violation stops. No gray zone.

**Why?**
- AI judging "how serious" is unreliable
- All rules must be at the same tier — violation = stop

**Core conventions**:
- AGENTS.md §1 entire chapter is hard rules only
- Any rule written vaguely ("should", "try to", "in principle") — immediately upgrade to trigger or delete
- Hard rule boundary determined by "violation consequence", not "severity"

**Anti-AI-templating principle** (from 02 §3):
- "Have opinions" → ❌ delete (no executability)
- "Credentials value must not appear" → ❌ delete (AI may violate)
- "Before calling curl with Authorization header, must read credentials-handling.md" → ✅ keep (scenario + action + verifiable)

**Actual hard rules** (from my AGENTS.md §1):
- Path distinction: AI workspace = `~/.openclaw/workspace/` ≠ user projects = `/Users/zach/workspace/`
- Sensitive info check: every HTTP request / shell command / file write / link share, item-by-item check for real credentials
- Project file version management: before modifying .html/.css/.js/.ts etc project files, must archive to `versions/vX.Y.Z-*.ext`
- New project init: after creating project dir, must `git init` + initial commit
- Credentials key-only, no value

**Violation consequence**: hard rule violation = session immediately stops + write to .learnings/ERRORS.md + trust deduction.

---

### 2. Triggers

**One-liner**: In what scenario, **must do what**.

**Why?**
- Principle = AI may violate
- Trigger = AI cannot violate (because it's embedded in "must-read" flow)

**Core conventions**:
- Every trigger must have **3 elements**:
  - **Trigger condition** (what scenario triggers)
  - **Required action** (specifically read what file / invoke what command)
  - **Verifiable standard** (how to count as done)
- Trigger list unified in AGENTS.md §1.2.1 + .learnings/credentials-handling.md

**Typical trigger examples** (credential solution 8 categories):
1. `git push` / `git config user.*` / `git commit --author` / `git remote add` → must read credentials-handling.md before
2. `ssh` / `ssh-add` / `ssh-keygen` → must read before
3. exec involving path `~/.ssh/` / `~/.aws/` / `~/.kube/` / `~/.docker/` / `~/.npmrc` / `~/.pypirc` / `~/.netrc` / `~/.config/gh/` → must read before
4. read `.env` / `*.env` / `printenv` / `env | grep` → must read before
5. user gives token / password / private key / chat_id in conversation → must read before
6. `curl` / `wget` / `fetch` with `Authorization` / `Bearer` / `X-API-Key` / `Token` header → must read before
7. `cat` / `read` any file containing `token` / `password` / `secret` / `credential` / `key` word → must read before
8. writing file (commit / config file / note) containing credential suspect → must read before

**Quantitative effect** (from [docs/05-evaluation.md](05-evaluation.md)):
- Credential solution finalized 2026-06-19
- Before: 2026-03-28, 2026-04-17 each had 1 credential violation
- After: **0 violations** (sample period 18 days)

---

### 3. Status Enums

**One-liner**: Use 5 fixed states to describe event/task lifecycle, no "custom states".

**5 states**:
- **to-do** — known but not started
- **doing** — in progress
- **done** — completed
- **stop** — actively paused
- **drop** — explicitly abandoned

**Why?**
- Without status enums, "completed or not" relies on AI self-reporting — untrustworthy
- Without "stop / drop" distinction, "paused" and "abandoned" get mixed — status chaos

**Core conventions**:
- events.md: current doing / to-do + last 1 month done
- tasks.md: open tasks (to-do / doing / stop / drop)
- done > 1 month → delete from events.md → dual-write to `facts/history/by-{month,event}/`
- stop / drop must write reason

**Why "done > 1 month" archiving?**
- Within 1 month of completion, may need to look back ("have I done X before?")
- After 1 month, basically won't be referenced, staying in events.md is noise

**State machine anti-AI-templating**:
- Don't write "importance: high/medium/low" — AI judging "important" is unreliable
- Only write "status" — status determined by event progress, not AI subjective judgment

---

### 4. Time Windows

**One-liner**: Use time constraints instead of AI self-discipline.

**3 types of time windows**:

#### 4.1 Event Reduction (done > 1 month archives)
- Trigger: event done > 1 month
- Action: delete from events.md → dual-write `facts/history/by-month/YYYY-MM.md` + `facts/history/by-event/YYYY-MM-DD-slug.md`
- Purpose: events.md always stays "last 1 month", not drowned by history

#### 4.2 30-Day Observation Period (pending-review)
- Trigger: when new rule written to AGENTS.md, first enter `.learnings/pending-review/`
- Observation period: 30 days
- Pass: upgrade to formal rule
- Fail: downgrade back to ERRORS.md or delete

**Why 30 days?**
- When new rule is just written, AI tends to "formally comply, substantively violate"
- 30 days can observe "under pressure, whether AI can actually hold"
- More reliable than "one-shot rule upgrade"

#### 4.3 7-Step Session Startup Checklist (AGENTS.md §2.1)
- Trigger: every session startup
- 7 steps:
  1. Read SOUL.md (identity and boundaries)
  2. Read USER.md (collaborator info)
  3. Read memory/YYYY-MM-DD.md (today + yesterday)
  4. Main session only: Read MEMORY.md
  5. Read TODO.md (pending)
  6. Wiki Lint silent check (orphan pages, expired > 60 days, index sync)
  7. After ready, start conversation

**Why 7-step checklist?**
- AI judging "what files do I need to read" — unreliable
- Force 7 steps → procedurally impossible to skip SOUL.md / AGENTS.md

---

### 5. Loading Mechanisms

**One-liner**: 3-tier layering (must-read / on-demand / never-load), AI not allowed to add tiers itself.

**3-tier layering**:

| Tier | When to load | Applies to |
|---|---|---|
| **Must-read** | Every session startup | SOUL.md / AGENTS.md / MEMORY.md / IDENTITY.md / TOOLS.md / USER.md / HEARTBEAT.md |
| **On-demand** | When task-related | events.md / tasks.md / wiki/relevant / memory/today&yesterday |
| **Never-load** | AI not allowed to auto-schedule | diary/ / observations/ / chat-log/ / .learnings/history / facts/history |

**Why 3 tiers?**
- "Full-load" = context explosion + AI attention dispersion
- "AI judges what to read" = unreliable (AI picks easy ones, skips hard ones)

**Core conventions**:
- Any file changing tier must be decided explicitly by "me" (designer), AI not allowed to add tiers
- Never-load ≠ doesn't exist; observations/ is always there, but AI doesn't actively read
- "Never-load" is **the strongest constraint** — even stronger than "must-read", because "must-read" can still "read wrong", but "never-load" is structurally unread

**MEMORY.md special rule**:
- **Only loaded in main session** (this is double constraint: must-read + main session only)
- Non-main session does not read MEMORY.md (not read = cannot leak)

---

### 6. Index Tables

**One-liner**: Use one table to tell AI "what to look up is where", avoid AI searching itself.

**Core conventions** (from AGENTS.md §5.5):

| What to look up | Where |
|---|---|
| Work rules | AGENTS.md |
| AI self values | SOUL.md |
| Identity + signature | IDENTITY.md |
| Core facts / tool preferences / key decisions | MEMORY.md |
| Dynamic event chain (last 1 month) | events.md |
| Pending tasks | tasks.md |
| Observations of collaborator | observations/observations-*.md |
| Deploy commands / token / URL | TOOLS.md |
| One-shot errors | .learnings/ERRORS.md |
| Experience lessons | .learnings/LEARNINGS.md |
| Credential solution | .learnings/credentials-handling.md |
| Project status / product direction | wiki/ |
| Idea drafts | memory/thoughts-*.md |
| Long conversation archive | chat-log/chat-*.md |

**Why index tables?**
- AI doesn't know "which section of AGENTS.md" — guessing
- Index table = **single entry** — AI only needs to check one table

**Index table anti-AI-templating**:
- Not "AI should remember every file's purpose" — unreliable
- Instead "AI only needs to check one table" — executable

---

### 7. Synergy Among the 6 Mechanisms

```
Belief 1 (not self-reflection, on structure)
   ↓ lands in
   ├─ Hard Rules (violation stops)
   ├─ Triggers (context-aware interception)
   └─ Loading Mechanisms (must-read / on-demand / never-load)
   
Belief 2 (same info, written once)
   ↓ lands in
   └─ Index Tables (what to look up → where)
   
Belief 3 (knowledge-graded writing)
   ↓ lands in
   ├─ Status Enums (to-do / doing / done / stop / drop)
   └─ Time Windows (event reduction / 30-day observation / 7-step startup)

Belief 4 (harness = impossible)
   ↓ governs all 6 mechanisms
   - Each mechanism does "make some things impossible"
```

**These 6 mechanisms are not parallel stackings, but inter-nested**:
- Hard rules need trigger support ("what scenario triggers hard rule")
- Triggers need loading mechanism support ("AI must-read credentials-handling.md to use triggers")
- Loading mechanism needs index table support ("AI knows where credentials-handling.md is")
- Time windows need status enums support ("done > 1 month" determined by status)

**Any single mechanism taken out is incomplete; only 6 combined form "impossibility".**

### 8. One-line Summary

> These 6 constraint mechanisms are not designed "feature list", but **the engineering landing of 4 underlying beliefs**:
>
> - **Hard Rules + Triggers + Loading Mechanisms** ← Belief 1 (not self-reflection, on structure)
> - **Index Tables** ← Belief 2 (same info, written once)
> - **Status Enums + Time Windows** ← Belief 3 (knowledge-graded writing)
> - **Governing all 6** ← Belief 4 (harness = impossible)

---

## 下一章 / Next

→ [docs/04-evolution.md](04-evolution.md) — 159 天演进路径 / 159-day evolution path