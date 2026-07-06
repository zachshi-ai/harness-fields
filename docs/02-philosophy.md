# 02 · 设计哲学与底层信念

> Design Philosophy and Underlying Beliefs
>
> 本章承接 [docs/01-architecture.md §0](01-architecture.md#0-这套系统的起源为什么会有它) 的"失败循环"——记录我从那条循环里接受下来的几条底层信念，以及每条信念在系统中是怎么落地的。
>
> 作者：扎克的自留地

---

## 中文

### 0. 起点：我接受"必须靠结构"这条信念

承接 01 §0 的失败循环。在那条循环里我反复承认、反复失败之后，我接受了一个底层事实：

> **AI 的稳定行为不能靠 AI 自省，必须靠结构。**
>
> 一句原则 = AI 可能违反。
> 一个触发器 = AI 不可能违反。

接受这件事之后，我又陆续接受了**另外 3 条底层信念**。这 4 条信念构成了这套方法论的哲学层。

### 1. 四条底层信念

#### 信念一：不靠自省，靠结构

> **AI 的稳定行为不能靠 AI 自省，必须靠结构。**

**为什么？**
- AI 没有跨 session 的"自省"能力——每次新 session 都是空白
- AI 有 prompt / context 里写"请你以后 X"——但没有机制保证它下次不犯
- 给我提"原则"没用——给我提"触发器"才有用

**这一条怎么落地？**
- **触发器优先于原则**：每条规则都写"在什么场景下必做什么"，而不是"原则上要注意 X"
- **违反必停**（AGENTS.md §1）：不是"违反要反省"，是"违反就停"
- **结构层面强制流程**：session 启动 7 步检查清单（第 0 / 第 1 层文件必读）

#### 信念二：同信息不写两处

> **同一信息只写一处 + 索引表查询。**

**为什么？**
- 同一信息散落两处，必有一处忘记更新 → 双源漂移
- 我和 AI 都记不住所有规则的精确位置
- 找规则的成本比"统一写入" 高得多

**这一条怎么落地？**
- **AGENTS.md §5.6 文件不重复原则**：抽象规则 → AGENTS.md / 工具偏好 → MEMORY.md / 部署命令 → TOOLS.md / 具体场景 → skills/
- **AGENTS.md §5.5 索引表**："要查的东西 → 去哪" 一张表定位所有规则入口

#### 信念三：知识分级写入

> **错误按严重程度分级，升级路径明确。**

**为什么？**
- 所有错误一锅炖 = 所有错误都被淹没
- 没有升级路径 = 反复错误只能一直写新条目
- 没有观察期 = "升级为强制规则"太随意

**这一条怎么落地？**
- **AGENTS.md §1.5 分级写入**：一次性错误 → ERRORS.md / 反复 2 次 → LEARNINGS.md / 反复 3 次+ → AGENTS.md 强制规则
- **AGENTS.md §1.6 重复错误升级流程**：每犯一次重复错误，写 ERRORS.md（含日期、错误、原因、修复、预防）
- **30 天观察期**：pending-review 目录追踪"待观察规则"是否真有效
- **PDCA 循环**：daily-errors-pdca cron 每天 23:58 自动 review

#### 信念四：harness = 让某些事"不可能"

> **这套系统的真正目的不是"AI 应该 X"，是"AI 不可能不 X"。**

**为什么？**
- "应该"对我和 AI 都没有约束力——失败循环证明无数次
- 必须把约束**嵌入结构**，让违反变成"流程上不可能"而不是"原则上应该"
- harness（缰绳）这个词的本意就是——不是建议，是限制

**这一条怎么落地？**
- **凭据只过 key 不过 value**：触发器式规则，调用前必读 credentials-handling.md
- **MEMORY.md 仅主 session 加载**：加载机制层面强制（不是建议）
- **observations 永不主动加载**：加载策略层面强制（不是建议）
- **违反必停**：第 0 层文件写明，不是"应该反省"是"违反就停"

### 2. 信念之间的优先级

这 4 条不是平等的，**它们有优先级**：

```
信念一（不靠自省靠结构）   ←  最根本，其他 3 条都是它的展开
   ↓
信念二（同信息不写两处）   ←  工程层落地，文件结构
信念三（知识分级写入）     ←  工程层落地，错误处理
   ↓
信念四（harness = 不可能） ←  哲学层总览，所有约束的最终目的
```

**信念一是根本**：如果我不接受它，其他 3 条都没有意义。
**信念四是表述**：它把前 3 条的本质说清楚——"全部约束的最终目的 = 让某些事不可能"。

### 3. 这套哲学的反 AI 模板化

我注意到一件事：**这套哲学明确反"AI 人格化模板"。**

AI 训练数据里那些"Have opinions"、"Be resourceful"、"Earn trust through competence"——听起来正确，但**没有任何可执行性**。

- 什么叫"have opinions"？AI 没有判断标准
- 什么叫"be resourceful"？我没法验证 AI 是不是 resourceful
- 什么叫"earn trust"？信任是结果不是行为

**这套方法论明确拒绝这种"鸡汤式信条"**。每条规则必须有：
- **触发条件**（在什么场景下）
- **可执行动作**（必做什么）
- **可验证标准**（怎么算做到）

**没有这 3 要素的规则，我就当它不存在。**

这条原则不来自任何模板，是从失败循环里**逼出来**的——AI 凭"理念"做不好任何事，AI 凭"触发器"才有可能做对。

### 4. 一句话总结

> 这套方法论的哲学不是"AI 应该怎样"，是**4 条底层信念**：
>
> 1. **不靠自省靠结构**
> 2. **同信息不写两处**
> 3. **知识分级写入**
> 4. **harness = 让某些事'不可能'**
>
> 它们从失败循环里来，被结构化落地，被 159 天迭代验证。

---

## English

### 0. Starting Point: Accepting "Must Rely on Structure"

Continuing from §0 of 01. After dozens of "admit → fail again" cycles, I accepted a base fact:

> **Stable AI behavior cannot rely on AI self-reflection. It must rely on structure.**
>
> A principle = AI may violate it.
> A trigger = AI cannot violate it.

After accepting this, I accepted **3 more underlying beliefs**. These 4 beliefs constitute the philosophy layer of this methodology.

### 1. Four Underlying Beliefs

#### Belief 1: Not on Self-Reflection, on Structure

> **Stable AI behavior cannot rely on AI self-reflection. It must rely on structure.**

**Why?**
- AI has no cross-session "self-reflection" capacity — each new session is blank
- AI can be told "next time do X" in prompt/context — but no mechanism guarantees it won't fail next time
- Telling me "principles" is useless — telling me "triggers" works

**How this lands in the system?**
- **Triggers over principles**: Every rule states "in what scenario must do what", not "in principle be careful about X"
- **Violation stops immediately** (AGENTS.md §1): not "violation requires reflection", but "violation stops"
- **Structure-level forced flows**: 7-step session startup checklist (Layer 0 / Layer 1 files must-read)

#### Belief 2: Same Info, Written Once

> **Same info written in exactly one place + index table lookup.**

**Why?**
- Same info in two places → one place gets forgotten → dual-source drift
- Neither I nor AI can remember the exact location of every rule
- Cost of finding rules >> cost of "unified writing"

**How this lands in the system?**
- **AGENTS.md §5.6 no-duplication principle**: abstract rules → AGENTS.md / tool preferences → MEMORY.md / deploy commands → TOOLS.md / specific scenarios → skills/
- **AGENTS.md §5.5 index table**: "what to look up → where to look" — a single table locates all rule entry points

#### Belief 3: Knowledge-Graded Writing

> **Errors graded by severity, upgrade path explicit.**

**Why?**
- All errors in one pot = all errors get drowned
- No upgrade path = repeated errors can only keep writing new entries
- No observation period = "upgrade to hard rule" is too casual

**How this lands in the system?**
- **AGENTS.md §1.5 graded writing**: one-shot error → ERRORS.md / repeated 2x → LEARNINGS.md / repeated 3x+ → AGENTS.md hard rule
- **AGENTS.md §1.6 repeated error upgrade flow**: each repeated error → write ERRORS.md (with date, error, cause, fix, prevention)
- **30-day observation period**: pending-review/ tracks "rules under observation" effectiveness
- **PDCA cycle**: daily-errors-pdca cron reviews daily at 23:58

#### Belief 4: Harness = Make Some Things "Impossible"

> **The real purpose of this system is not "AI should X", but "AI cannot not-X".**

**Why?**
- "Should" has no binding force on me or AI — failure loop proved this many times
- Must embed constraints **in structure**, making violation "processually impossible" not "principally advisable"
- "harness" means — not suggestion, but restraint

**How this lands in the system?**
- **Credentials key-only, no value**: trigger-style rule, must read credentials-handling.md before invocation
- **MEMORY.md only loaded in main session**: enforced at loading mechanism layer (not advised)
- **observations never auto-loaded**: enforced at loading strategy layer (not advised)
- **Violation stops immediately**: stated in Layer 0 files, not "should reflect", but "violation stops"

### 2. Priority Among the Beliefs

These 4 beliefs are not equal — **they have priority**:

```
Belief 1 (not self-reflection, on structure)  ←  fundamental, others expand from this
   ↓
Belief 2 (same info, written once)            ←  engineering layer, file structure
Belief 3 (knowledge-graded writing)          ←  engineering layer, error handling
   ↓
Belief 4 (harness = impossible)              ←  philosophical overview, all constraints' final purpose
```

**Belief 1 is the root**: If I don't accept it, the other 3 are meaningless.
**Belief 4 is the summary**: It makes the essence of the previous 3 clear — "all constraints' final purpose = make some things impossible".

### 3. This Philosophy Anti-AI-Templating

I noticed one thing: **this philosophy explicitly rejects "AI persona templates".**

Those "Have opinions", "Be resourceful", "Earn trust through competence" lines in AI training data — sound correct, but **have zero executability**.

- What does "have opinions" mean? AI has no judgment standard
- What does "be resourceful" mean? I can't verify if AI is resourceful
- What does "earn trust" mean? Trust is outcome, not behavior

**This methodology explicitly rejects "chicken-soup-style creeds"**. Every rule must have:
- **Trigger condition** (in what scenario)
- **Executable action** (must do what)
- **Verifiable standard** (how to count as done)

**Rules without these 3 elements, I treat as nonexistent.**

This principle doesn't come from any template — it's **distilled from the failure loop** — AI cannot do anything well via "philosophy", AI can only do right via "triggers".

### 4. One-line Summary

> This methodology's philosophy is not "how AI should be", but **4 underlying beliefs**:
>
> 1. **Not self-reflection, structure**
> 2. **Same info, written once**
> 3. **Knowledge-graded writing**
> 4. **Harness = make some things "impossible"**
>
> They come from the failure loop, are structured into the system, and have been verified by 159 days of iteration.

---

## 下一章 / Next

→ [docs/03-constraints.md](03-constraints.md) — 6 种约束机制详解 / 6 constraint mechanisms in detail