# Examples — 脱敏模板 / Sanitized Templates

> 三份骨架模板。基于扎克的自留地的 harness-fields 方法论。
>
> Three skeleton templates. Based on 扎克的自留地's harness-fields methodology.

---

## 使用顺序 / Usage Order

如果你想从零搭一套字段约束系统，**推荐按这个顺序填充**：

### 第 1 步：`SOUL.md.template`（价值观与底线层）

**为什么先填这个？**

SOUL.md 是整套系统里最高层的约束——AI 启动时必读。

如果先填 AGENTS / MEMORY，后填 SOUL，AI 可能在还没"知道自己是谁"之前就学会了一堆规则——容易跑偏。

**填充要点**：
- 硬规则控制在 10 条以内（多了等于没规则）
- 每条规则必须有 4 要素：触发条件 / 必做动作 / 可验证标准 / 违反后果
- 不写"应该 X" / "原则上 X"——写"必做 X" 或删除

### 第 2 步：`AGENTS.md.template`（抽象规则层）

**为什么第二？**

AGENTS.md 是"读懂你的话 / 推导你的意图"的尺子。

SOUL.md 定义"什么能做 / 什么不能做"，AGENTS.md 定义"你的规则怎么写 / 流程怎么走"。

**填充要点**：
- §1 硬规则直接引用 SOUL.md 的硬规则
- §2 工作流规则填"重大任务判定 / 50% 自检 / 改完验证"等
- §5 文件分层结构与调度机制必须存在

### 第 3 步：`MEMORY.md.template`（长期事实层）

**为什么最后？**

MEMORY.md 是"装上就不动"的核心事实库——AI 启动时也读，但优先级低于 SOUL/AGENTS。

如果先填 MEMORY（关键决策 + 工具偏好），可能填了一堆"事实"但还没"价值底线"。

**填充要点**：
- 只装"不变的事实"——临时性内容放 `memory/YYYY-MM-DD.md`
- §2 关键决策要可追溯——日期 + 原因
- 保持精简——几百行已经偏多，几千行就是失控

---

## 三个文件之间的边界

| 文件 | 装什么 | 何时加载 |
|---|---|---|
| **SOUL.md** | 价值观与底线（硬规则） | 每次必读 |
| **AGENTS.md** | 抽象规则 + 工作流 + 文件分层 | 每次必读 |
| **MEMORY.md** | 关键决策 + 工具偏好 + 个人习惯 | 主 session 必读 |

**核心原则**："同信息不写两处"
- 抽象规则 → AGENTS.md（不写进 MEMORY.md）
- 工具偏好 → MEMORY.md（不写进 AGENTS.md）
- 部署命令 → TOOLS.md（不写进 AGENTS / MEMORY.md）
- 价值观与底线 → SOUL.md（不写进 AGENTS / MEMORY.md）

---

## 反 AI 模板化警告

这 3 个模板都明确拒绝"AI 人格化模板"——任何一条规则必须有以下要素之一：

- **触发条件**（在什么场景下）
- **可执行动作**（必做什么）
- **可验证标准**（怎么算做了）
- **违反后果**（违反了怎么处理）

**没有这 4 要素的规则，等于没有规则。**

详细原因见 [../docs/02-philosophy.md §3](../docs/02-philosophy.md)（反 AI 模板化章节）。

---

## 模板与原方法论的关系

| 模板 | 映射到方法论章节 |
|---|---|
| SOUL.md.template | docs/02-philosophy.md §1 信念一（不靠自省靠结构）+ 02 §1 硬规则 |
| AGENTS.md.template | docs/01-architecture.md §5 文件分层结构与调度机制 |
| MEMORY.md.template | docs/01-architecture.md §6 单一事实源 + 索引表 |

---

## 完整结构（填充完后的样子）

```
your-harness/
├── SOUL.md                          ← 你填的
├── AGENTS.md                        ← 你填的
├── MEMORY.md                        ← 你填的
├── TOOLS.md                         ← 可选（部署命令 / token）
├── IDENTITY.md                      ← 可选（身份 + 署名）
├── HEARTBEAT.md                     ← 可选（心跳检查）
├── USER.md                          ← 必填（用户信息）
├── events.md                        ← 推荐（事件链）
├── tasks.md                         ← 推荐（待办）
├── memory/
│   └── YYYY-MM-DD.md                ← 每天记
├── diary/
│   └── ai-YYYY-MM-DD.md             ← AI 反思
├── observations/                    ← 永不主动加载
├── chat-log/                        ← 长对话存档
├── wiki/                            ← 沉淀知识
├── .learnings/
│   ├── ERRORS.md                    ← 错误库
│   ├── LEARNINGS.md                 ← 经验库
│   └── credentials-handling.md      ← 凭据通解（如果适用）
├── facts/
│   └── history/                     ← 事件归档
└── skills/                          ← 可复用工作流
```

---

> 基于扎克的自留地的 harness-fields 方法论。适用[非商业许可](https://github.com/zachshi-ai/harness-fields/blob/main/LICENSE)；须保留许可声明和署名。未经作者事先书面许可，不得商用。
> Based on 扎克的自留地's harness-fields methodology. Subject to the [Non-Commercial License](https://github.com/zachshi-ai/harness-fields/blob/main/LICENSE); retain the license notice and attribution. Commercial use requires prior written permission from the author.