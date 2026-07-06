# 01 · 字段记忆系统的逻辑架构

> The Logical Architecture of the Field Memory System
>
> 本文是 harness-fields 方法论的核心章节。它回答一个问题：**一个完整的字段约束系统由哪些组件构成？它们之间如何协作？**
>
> 作者：扎克的自留地

---

## 中文

### 0. 这套系统的起源（为什么会有它）

在我设计这套字段约束系统之前，我跟我的 AI 助手协作时，反复走过一条**失败循环**：

> 我指出错误 → AI 承认 → AI 承诺下次改 → 下次 AI **犯同样的错** → 我再指出 → AI 再承认 → AI 再承诺 → 下次再犯

这条循环我走过的具体例子：

| 我说过的话 | AI 的反应 | 下次发生了什么 |
|---|---|---|
| "下次**先说方案再动手**，不要先做再说" | AI："好的，下次一定" | AI 仍然先做再说 |
| "**改完必须 force run**，看到产物才算完成" | AI："我理解了，下次一定先验证" | AI 仍然凭感觉说"应该可以" |
| "凭据 value **绝对不能**出现在 commit / 文件 / 任何地方" | AI："我明白了，value 不能泄露" | commit message 里**真的出现了一行 token** |
| "敏感操作前**先 read** credentials-handling.md" | AI："好的，调用前必读" | AI 又凭记忆操作了一次 |

**每一次"承认"都是真的。每一次"承诺"都是真的。但 AI 自己守不住。**

直到我接受一件事：

> **不是 AI "愿不愿意"守规矩的问题。是"它**能不能**"的问题。**
>
> 一句"凭据 value 不能出现"——AI 可能会违反。
> 一个触发器："调用 curl 带 Authorization header 前**必须先 read** credentials-handling.md"——AI 不可能违反。

**这套方法论不是优雅设计出来的——是被失败挤出来的。**

下面 17 个组件、4 层结构、6 种约束机制，是这条失败循环沉淀下来的硬产物。

---

### 1. 核心问题

我用的 AI 助手每次 session 都是"全新启动"。这意味着：

- ❌ 我的 AI 不能依赖对话上下文做决策
- ❌ 我的 AI 不能依赖"自省"维持行为一致性
- ❌ 我的 AI 不能依赖 prompt 工程做长期约束
- ❌ 我的 AI 不能依赖"承诺下次改"——**这是我花了几十次失败才承认的**

那么，**我如何让同一个 AI 在面对我时，表现出稳定、可靠、可验证的行为？**

答案是我自己设计一个**字段级的约束系统**——用结构化文件 + 触发器 + 状态机，把 AI 的行为约束从"软建议"变成"硬边界"。**靠的不是 AI 的承诺，是结构本身的不可能性。**

### 2. 我的系统由 17 个组件构成

| 组件 | 职责 | 一句话定位 |
|---|---|---|
| SOUL.md | AI 价值观与行为底线 | "我对自己的认知" |
| AGENTS.md | 抽象规则（理解我的意图的尺子） | "读懂我的话 / 推导意图" |
| MEMORY.md | 核心事实库 + 关键决策 | "装上就不动" |
| IDENTITY.md | 默认对话身份 + 产物署名映射 | "AI 在不同时刻是谁" |
| TOOLS.md | 客观事实：工具路径、凭据、部署命令 | "工具操作的 cheat sheet" |
| HEARTBEAT.md | 心跳检查项与通知触发条件 | "什么时候 AI 该主动找我" |
| events.md | 近1个月动态事件链（默认不加载） | "最近 1 个月发生了什么" |
| tasks.md | 我交代过的待办（按需加载） | "我交代但还没做完的" |
| memory/YYYY-MM-DD.md | 每日日志（按日期） | "当天发生了什么" |
| diary/ai-YYYY-MM-DD.md | AI 工作日记（按日期） | "AI 每天的反思" |
| observations/ | 对我的观察（永不主动加载） | "AI 对我持续观察" |
| chat-log/ | 长对话存档 | "这个 session 完整内容" |
| wiki/ | 沉淀的结构化知识 | "已经成体系的东西" |
| .learnings/ | 错误库 / 经验库 / 凭据通解 | "AI 犯过的错和学到的" |
| facts/ | 事件归档（按月 + 按事件双索引） | "过去做过的事" |
| skills/ | 可复用的工作流技能 | "做这类事该这么做" |
| cron jobs | 自动化任务 | "到点了自动跑的事" |

> 注：cron jobs 不是文件，而是一类调度机制。

### 3. 组件分层架构（四层）

整个系统不是平铺的，而是一个**四层结构**。每一层有不同的加载策略、不同的生命周期：

```
┌────────────────────────────────────────────────────┐
│ 第 0 层：身份与底线（Identity & Boundaries）        │
│   SOUL.md + AGENTS.md                               │
│   加载策略：每次 session 必读                       │
└────────────────────────────────────────────────────┘
              ↓ 推导 / 引用
┌────────────────────────────────────────────────────┐
│ 第 1 层：长期事实（Long-term Facts）                │
│   MEMORY.md + IDENTITY.md + TOOLS.md               │
│   加载策略：每次 session 必读                       │
└────────────────────────────────────────────────────┘
              ↓ 索引 / 关联
┌────────────────────────────────────────────────────┐
│ 第 2 层：上下文（Contextual State）                 │
│   HEARTBEAT.md + events.md + tasks.md              │
│   + memory/今天昨天 + wiki/相关                     │
│   加载策略：按需加载                                │
└────────────────────────────────────────────────────┘
              ↓ 触发 / 调用
┌────────────────────────────────────────────────────┐
│ 第 3 层：沉淀与积累（Accumulated Knowledge）        │
│   diary/ + observations/ + chat-log/              │
│   + .learnings/ + facts/ + skills/                 │
│   + memory/历史 + cron jobs                         │
│   加载策略：永不主动调度 / 按需检索                  │
└────────────────────────────────────────────────────┘
```

### 4. 每层的设计意图

#### 第 0 层：身份与底线

**为什么必读？**
- SOUL.md 定义 AI **价值观与行为底线**——违反就是越线
- AGENTS.md 定义 AI **理解我意图的尺子**——不读就理解错

**不读这一层的代价**：AI 不知道自己是谁、不知道什么能做、什么不能做。

#### 第 1 层：长期事实

**为什么必读？**
- MEMORY.md 装载**关键决策 + 工具偏好**——不读就会重复犯错
- IDENTITY.md 装载**对话身份 + 产物署名**——不读就会搞错 AI 在不同时刻是谁
- TOOLS.md 装载**工具路径 + 凭据 + 部署命令**——不读就会操作失误

**关键原则**：MEMORY.md **仅在主 session 加载**（非主 session 不读）——这是安全底线，避免我的个人事实泄漏给第三方。

#### 第 2 层：上下文

**为什么按需？**
- HEARTBEAT.md：心跳检查项，不需要每次重读，但需要时是 cheat sheet
- events.md：近 1 个月动态事件链——不读**当前事件上下文**，AI 会做错判断
- tasks.md：我交代过的待办——不读**当前未完成事项**，AI 会漏做事

**关键原则**：events.md **默认不加载**——只有任务与某事件上下文相关时才查索引。

#### 第 3 层：沉淀与积累

**为什么永不主动加载？**
- diary/observations/chat-log：是**时间序列日志**，不主动调度避免 context 爆炸
- .learnings/：是**经验库**，按需查询而不是全文塞进 context
- facts/：是**归档**，完成的事情不应该再消耗注意力
- skills/：是**可复用流程**，触发时再加载

**关键原则**：observations **永不主动调度**——只在 AI 提到相关主题时才调入。

### 5. 跨层约束机制（6 种）

整个系统不只是"文件清单"，它由 **6 种约束机制**贯穿所有层：

1. **硬规则**（AGENTS.md §1）—— 违反必停
2. **触发器**（AGENTS.md §1.2.1）—— 场景化拦截
3. **状态枚举**（events.md / tasks.md）—— to-do / doing / done / stop / drop
4. **时间窗口**（事件调减 / 30 天观察期 / session 启动 7 步清单）
5. **加载机制**（必读 / 按需 / 永不加载）
6. **索引表**（AGENTS.md §5.5 "要查的东西 → 去哪"）

→ 详细机制见 [docs/03-constraints.md](03-constraints.md)

### 6. 单一事实源 + 索引表

这套系统的**核心反模式**是"同一信息写两处"——例如把工具偏好同时写在 AGENTS.md 和 MEMORY.md。

**正确做法**：
- **抽象规则** → AGENTS.md
- **具体场景规范** → skills/
- **工具偏好** → MEMORY.md
- **部署命令** → TOOLS.md
- **协作意图** → 写在当前对话，不进入永久文件

然后用一张**索引表**（"要查的东西 → 去哪"）解决"我不知道该看哪个文件"的问题。

### 7. 一句话总结

**这套系统的本质不是"AI 记忆"，是**一个 17 个组件 + 4 层结构 + 6 种约束机制组成的协作约束网络**——目的是让"AI 在面对我时，不可能做出低质量、不合规、不可信的行为"。

这个"不可能"，不是我希望它发生——是**结构本身**让它不可能。我走过几十次"承诺 → 失败"循环才明白这件事。

---

## English

### 0. The Origin of This System (Why it Exists)

Before I designed this field-level harness system, my collaboration with my AI assistant kept falling into a **failure loop**:

> I point out a mistake → AI admits it → AI promises to fix it next time → next time AI **makes the same mistake** → I point it out again → AI admits again → AI promises again → AI makes the same mistake again

Specific instances of this loop:

| What I said | AI's response | What happened next time |
|---|---|---|
| "Next time **state the plan before acting**, don't do-then-talk" | AI: "Got it, definitely next time" | AI still did-then-talked |
| "**Must force run after changes**, completion = seeing the artifact" | AI: "Understood, I'll verify first next time" | AI still said "it should work" by feel |
| "Credentials value **must absolutely not** appear in commit / file / anywhere" | AI: "I understand, value must not leak" | commit message **literally contained a token** |
| "Before sensitive operations **must read** credentials-handling.md" | AI: "OK, must-read before invocation" | AI operated by memory again |

**Every "admission" was genuine. Every "promise" was genuine. But AI cannot enforce on itself.**

Until I accepted one thing:

> **This is not about whether AI "wants to" follow rules. It's about whether AI "can".**
>
> A statement like "credentials value must not appear" — AI may violate.
> A trigger: "Before calling curl with Authorization header, **must first read** credentials-handling.md" — AI cannot violate.

**This methodology was not designed from elegance — it was distilled from failure.**

The 17 components, 4-layer structure, and 6 constraint mechanisms below are the hard sediment of this failure loop.

---

### 1. Core Problem

My AI assistant starts every session "from scratch". This means:

- ❌ My AI cannot rely on conversation context for decisions
- ❌ My AI cannot rely on "self-reflection" for behavioral consistency
- ❌ My AI cannot rely on prompt engineering for long-term constraints
- ❌ My AI cannot rely on "promising to do better next time" — **this took me dozens of failures to admit**

So **how can the same AI, facing me, behave consistently, reliably, and verifiably?**

The answer is a field-level harness system I designed myself — using structured files + triggers + state machines to convert AI behavior constraints from "soft suggestions" to "hard boundaries". **It relies not on AI's promises, but on the impossibility embedded in structure itself.**

### 2. My System Consists of 17 Components

| Component | Responsibility | One-line Position |
|---|---|---|
| SOUL.md | AI values and behavioral boundaries | "Self-knowledge" |
| AGENTS.md | Abstract rules (ruler for understanding my intent) | "Read my words / infer intent" |
| MEMORY.md | Core fact base + key decisions | "Loaded and unchanged" |
| IDENTITY.md | Default dialogue identity + signature mapping | "Who the AI is at different moments" |
| TOOLS.md | Objective facts: tool paths, credentials, deploy commands | "Cheat sheet for tool operations" |
| HEARTBEAT.md | Heartbeat checks + notification triggers | "When AI should proactively reach out" |
| events.md | Last-1-month event chain (default not loaded) | "What happened recently" |
| tasks.md | Outstanding tasks I assigned (load on demand) | "What I asked but unfinished" |
| memory/YYYY-MM-DD.md | Daily log (by date) | "What happened today" |
| diary/ai-YYYY-MM-DD.md | AI work diary (by date) | "AI's daily reflection" |
| observations/ | Observations of me (never auto-loaded) | "AI's continuous observation of me" |
| chat-log/ | Long conversation archive | "Full session content" |
| wiki/ | Distilled structured knowledge | "Things that became systematic" |
| .learnings/ | Error library / experience library / credential solutions | "AI's mistakes and lessons" |
| facts/ | Event archive (by month + by event, dual index) | "What was done before" |
| skills/ | Reusable workflow skills | "How to do this kind of task" |
| cron jobs | Automation tasks | "What runs automatically on schedule" |

> Note: cron jobs are not files, but a class of scheduling mechanism.

### 3. Four-Layer Component Architecture

The system is not flat — it's a **four-layer structure**. Each layer has different loading strategies and lifecycle:

```
┌────────────────────────────────────────────────────┐
│ Layer 0: Identity & Boundaries                     │
│   SOUL.md + AGENTS.md                               │
│   Loading: must-read every session                  │
└────────────────────────────────────────────────────┘
              ↓ derive / reference
┌────────────────────────────────────────────────────┐
│ Layer 1: Long-term Facts                           │
│   MEMORY.md + IDENTITY.md + TOOLS.md               │
│   Loading: must-read every session                  │
└────────────────────────────────────────────────────┘
              ↓ index / relate
┌────────────────────────────────────────────────────┐
│ Layer 2: Contextual State                          │
│   HEARTBEAT.md + events.md + tasks.md              │
│   + memory/today&yesterday + wiki/relevant          │
│   Loading: on demand                                │
└────────────────────────────────────────────────────┘
              ↓ trigger / invoke
┌────────────────────────────────────────────────────┐
│ Layer 3: Accumulated Knowledge                     │
│   diary/ + observations/ + chat-log/              │
│   + .learnings/ + facts/ + skills/                 │
│   + memory/history + cron jobs                      │
│   Loading: never auto-scheduled / query on demand   │
└────────────────────────────────────────────────────┘
```

### 4. Design Intent of Each Layer

#### Layer 0: Identity & Boundaries

**Why must-read?**
- SOUL.md defines AI **values and behavioral boundaries** — violation = crossing the line
- AGENTS.md defines AI **ruler for understanding my intent** — without it, AI misunderstands

**Cost of not reading this layer**: AI doesn't know who it is, what it can/cannot do.

#### Layer 1: Long-term Facts

**Why must-read?**
- MEMORY.md holds **key decisions + tool preferences** — without it, AI repeats mistakes
- IDENTITY.md holds **dialogue identity + signature mapping** — without it, AI confuses identity at different moments
- TOOLS.md holds **tool paths + credentials + deploy commands** — without it, AI makes operational errors

**Key principle**: MEMORY.md **only loads in main session** (non-main sessions do not read) — this is a security baseline to prevent my personal facts leaking to third parties.

#### Layer 2: Contextual State

**Why on demand?**
- HEARTBEAT.md: heartbeat checks, not needed every read, but cheat sheet when needed
- events.md: last-1-month event chain — without reading **current event context**, AI makes wrong judgments
- tasks.md: outstanding tasks I assigned — without reading **current unfinished items**, AI misses work

**Key principle**: events.md **not loaded by default** — only check index when task relates to a specific event.

#### Layer 3: Accumulated Knowledge

**Why never auto-load?**
- diary/observations/chat-log: **time-series logs**, not auto-scheduled to avoid context explosion
- .learnings/: **experience library**, query on demand rather than full-text into context
- facts/: **archive**, completed work should not consume attention
- skills/: **reusable workflows**, loaded when triggered

**Key principle**: observations **never auto-scheduled** — only loaded when AI needs to reference relevant topics.

### 5. Cross-Layer Constraint Mechanisms (6)

The system is not just a "file list" — it has **6 constraint mechanisms** spanning all layers:

1. **Hard rules** — violation stops immediately
2. **Triggers** — context-aware interception
3. **Status enums** — to-do / doing / done / stop / drop
4. **Time windows** — event reduction / 30-day observation / 7-step startup checklist
5. **Loading mechanisms** — must-read / on-demand / never-load
6. **Index tables** — "what to look up → where to look"

→ See [docs/03-constraints.md](03-constraints.md) for details.

### 6. Single Source of Truth + Index Tables

The **core anti-pattern** of this system is "writing the same info in two places" — e.g., tool preferences written in both AGENTS.md and MEMORY.md.

**Correct approach**:
- **Abstract rules** → AGENTS.md
- **Scenario-specific specs** → skills/
- **Tool preferences** → MEMORY.md
- **Deploy commands** → TOOLS.md
- **Collaborative intent** → current conversation, not in permanent files

Then use an **index table** ("what to look up → where to look") to solve "I don't know which file to read".

### 7. One-line Summary

**This system is not "AI memory" — it is **a collaboration constraint network of 17 components + 4 layers + 6 constraint mechanisms** — designed to make it **impossible** for AI to behave with low quality, non-compliance, or untrustworthiness when facing me.

This "impossibility" is not something I hope will happen — it is **embedded in structure itself**. I walked through dozens of "promise → failure" cycles before I understood this.

---

## 下一章 / Next

→ [docs/02-philosophy.md](02-philosophy.md) — 设计哲学与底层信念 / Design philosophy and underlying beliefs