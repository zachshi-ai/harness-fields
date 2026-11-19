# harness-fields

> © 2026 zachshi-ai · 保留所有权利 · 仅限学术研究与非商业性使用,禁止任何商业用途;商业授权需作者书面许可。

> 字段约束 / Field-level Harness for AI Collaboration
>
> 一套**被失败挤出来的**字段约束系统方法论。基于 159 天迭代实践。
>
> A field-level harness system methodology **distilled from repeated failure**, by 扎克的自留地. 159 days of iterative design.

---

## 中文

### 这是什么

**harness-fields** 不是一套优雅设计出来的方法论。**它是被失败挤出来的。**

我最初跟我的 AI 助手协作时，相信的是一些很朴素的东西：

- "AI 应该能记住我说过的话"
- "AI 应该自己反省、主动改错"
- "我只要把规则讲清楚，AI 下次就会遵守"

然后我反复失败：

- AI 每次 session 都"全新启动"——我说过的事它忘了
- AI 答应"下次会改"，下次**同样的错再犯一次**
- AI 嘴上承诺"凭据不能出现在文件里"，commit message 里**真的就出现了 token**
- AI 说自己"已经学会了"，下次**再犯第三次**

我承认过很多次错误。**每一次承认都是真的，每一次承诺都是真的——但 AI 自己守不住。**

直到我意识到：

> **不是 AI "愿不愿意"守规矩的问题。是"它**能不能**"的问题。**
>
> 一句"凭据 value 不能出现"——AI 可能会违反。
> 一个触发器："调用 curl 带 Authorization header 前必须先 read credentials-handling.md"——AI 不可能违反。

**这套方法论就是从"我承认错误 → AI 再次犯同样的错 → 我承认 → AI 又犯"这个循环里，被一步一步逼出来的。**

它不是优雅的设计产出，它是**几百次失败**沉淀下来的硬约束。

### 核心洞察

> AI 的稳定行为**不能靠 AI 自省，必须靠结构**。
>
> 一条原则 = AI 可能违反；
> 一个触发器 = AI 不可能违反。
>
> **我花了几十次"承认错误 → 再次犯同样的错"才接受这件事。**

### 它由哪些约束组成

- ✅ 结构化文件分层（17 个组件 / 4 层）
- ✅ 触发器式规则（不是"原则"，是"什么场景下必做什么"）
- ✅ 状态枚举 + 时间窗口（to-do / doing / done / stop / drop）
- ✅ 索引表（避免双源漂移）
- ✅ 知识分级写入（错误 → 经验 → 强制规则的升级路径）
- ✅ session 启动 7 步检查清单（强制流程）

### 读者

- 想设计 AI 协作系统的工程师 / 产品经理
- 研究 AI 行为约束的研究者
- 用 AI 做长期协作、已经受够 AI "承诺了又犯"的个人 / 团队
- 对"AI harness"概念感兴趣的从业者

### 内容

- **[docs/01-architecture.md](docs/01-architecture.md)** — 字段记忆系统的逻辑架构
- **[docs/02-philosophy.md](docs/02-philosophy.md)** — 设计哲学与底层信念
- **[docs/03-constraints.md](docs/03-constraints.md)** — 6 种约束机制详解
- **[docs/04-evolution.md](docs/04-evolution.md)** — 159 天演进路径
- **[docs/05-evaluation.md](docs/05-evaluation.md)** — 量化佐证与效果评价

### 复用

本仓库提供 3 份脱敏模板（[examples/](examples/)），可直接基于此搭建自己的字段约束系统：

- `AGENTS.md.template` — 抽象规则层
- `MEMORY.md.template` — 长期事实层
- `SOUL.md.template` — 价值观与底线层

### 许可

本仓库采用[非商业许可](LICENSE)，仅限学术研究与非商业用途。非商业使用、复制、分享与修改须保留许可声明并署名作者。任何商业用途均须事先获得作者书面许可。

---

## English

### What is this

**harness-fields** is not an elegantly designed methodology. **It was distilled from repeated failure.**

When I first started collaborating with my AI assistant, I believed in some simple things:

- "AI should remember what I've said"
- "AI should self-reflect and fix its own mistakes"
- "If I just state the rules clearly, AI will follow them next time"

Then I failed repeatedly:

- AI starts every session "from scratch" — it forgets what I've said
- AI promises "I'll do better next time" — then **makes the same mistake again**
- AI verbally promises "credentials must never appear in files" — then **literally puts a token in the commit message**
- AI says "I've learned" — then **makes the same mistake a third time**

I admitted my mistakes many times. **Each admission was genuine, each promise was genuine — but AI cannot enforce on itself.**

Until I realized:

> **This is not about whether AI "wants to" follow rules. It's about whether AI "can".**
>
> A statement like "credentials value must not appear" — AI may violate.
> A trigger: "Before calling curl with Authorization header, must first read credentials-handling.md" — AI cannot violate.

**This methodology was forced out, step by step, from the loop of "I admit → AI makes the same mistake again → I admit → AI makes it again".**

It's not an elegant design output — it's **hard constraints sedimented from hundreds of failures**.

### Core Insight

> Stable AI behavior **cannot rely on AI self-reflection. It must rely on structure.**
>
> A principle = AI may violate it;
> A trigger = AI cannot violate it.
>
> **It took me dozens of "admit mistake → make the same mistake again" cycles to accept this.**

### What constraints it comprises

- ✅ Structured file layering (17 components / 4 layers)
- ✅ Trigger-based rules (not "principles" — "in what scenario must do what")
- ✅ Status enums + time windows (to-do / doing / done / stop / drop)
- ✅ Index tables (avoid dual-source drift)
- ✅ Knowledge-graded writing (error → experience → hard rule upgrade path)
- ✅ 7-step session startup checklist (forced flow)

### Audience

- Engineers / PMs who want to design AI collaboration systems
- Researchers studying AI behavioral constraints
- Individuals / teams doing long-term AI collaboration who are tired of AI "promising then repeating"
- Practitioners interested in the "AI harness" concept

### Contents

- **[docs/01-architecture.md](docs/01-architecture.md)** — Logical architecture of the field memory system
- **[docs/02-philosophy.md](docs/02-philosophy.md)** — Design philosophy and underlying beliefs
- **[docs/03-constraints.md](docs/03-constraints.md)** — 6 constraint mechanisms in detail
- **[docs/04-evolution.md](docs/04-evolution.md)** — 159-day evolution path
- **[docs/05-evaluation.md](docs/05-evaluation.md)** — Quantitative evidence and evaluation

### Reuse

This repo provides 3 sanitized templates ([examples/](examples/)) you can clone directly to build your own field harness system:

- `AGENTS.md.template` — Abstract rules layer
- `MEMORY.md.template` — Long-term facts layer
- `SOUL.md.template` — Values and boundaries layer

### License

This repository is subject to the [Non-Commercial License](LICENSE) for academic research and non-commercial use only. Non-commercial use, copying, sharing, and modification require retaining the license notice and crediting the author. Any commercial use requires prior written permission from the author.

---

## Author

**扎克的自留地** — A practitioner's working notes on AI collaboration systems.

**Not designed from elegance — distilled from failure.**

Based on 159 days of iterative design starting from 2026-01-29.

---

> 基于 159 天迭代设计实践，始于 2026-01-29。
>
> **不是从优雅设计出来的——是从失败里挤出来的。**