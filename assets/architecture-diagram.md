# Architecture Diagram

> 架构图（mermaid 格式，GitHub 原生渲染）

## 完整四层架构图

```mermaid
graph TB
    subgraph L0["Layer 0: Identity & Boundaries (must-read every session)"]
        SOUL["SOUL.md<br/>Values & Boundaries"]
        AGENTS["AGENTS.md<br/>Abstract Rules"]
    end

    subgraph L1["Layer 1: Long-term Facts (must-read every session)"]
        MEMORY["MEMORY.md<br/>Key Decisions + Tool Preferences<br/>(main session only)"]
        IDENTITY["IDENTITY.md<br/>Identity + Signature Mapping"]
        TOOLS["TOOLS.md<br/>Tool Paths + Credentials + Deploy"]
    end

    subgraph L2["Layer 2: Contextual State (load on demand)"]
        HEARTBEAT["HEARTBEAT.md<br/>Heartbeat Checks"]
        EVENTS["events.md<br/>Last 1-Month Events<br/>(default not loaded)"]
        TASKS["tasks.md<br/>Outstanding Tasks"]
        MEMTODAY["memory/today&yesterday"]
        WIKI["wiki/relevant"]
    end

    subgraph L3["Layer 3: Accumulated Knowledge (never auto-load)"]
        DIARY["diary/"]
        OBS["observations/<br/>(never auto-load)"]
        CHATLOG["chat-log/"]
        LEARN[".learnings/"]
        FACTS["facts/ (archive)"]
        SKILLS["skills/"]
        MEMHIST["memory/history"]
        CRON["cron jobs"]
    end

    L0 -->|derive / reference| L1
    L1 -->|index / relate| L2
    L2 -->|trigger / invoke| L3

    SOUL -.violation.-> STOP["🛑 Stop Immediately"]
    AGENTS -.violation.-> STOP
    MEMORY -.load in main only.-> MAIN["Main Session Only"]

    classDef mustRead fill:#ffcccc,stroke:#cc0000,stroke-width:2px
    classDef onDemand fill:#fff4cc,stroke:#cc9900,stroke-width:2px
    classDef neverLoad fill:#e0e0e0,stroke:#666666,stroke-width:1px,stroke-dasharray: 5 5

    class SOUL,AGENTS,MEMORY,IDENTITY,TOOLS mustRead
    class HEARTBEAT,EVENTS,TASKS,MEMTODAY,WIKI onDemand
    class DIARY,OBS,CHATLOG,LEARN,FACTS,SKILLS,MEMHIST,CRON neverLoad
```

## 6 种约束机制关系图

```mermaid
graph LR
    subgraph Constraints["6 Constraint Mechanisms"]
        HR["1. Hard Rules<br/>Violation = Stop"]
        TR["2. Triggers<br/>Context-aware Interception"]
        SE["3. Status Enums<br/>to-do/doing/done/stop/drop"]
        TW["4. Time Windows<br/>Event Reduction / 30-day Obs"]
        LM["5. Loading Mechanisms<br/>Must/OnDemand/Never"]
        IX["6. Index Tables<br/>What → Where"]
    end

    HR -->|enforced in| AGENTS["AGENTS.md §1"]
    TR -->|enforced in| AGENTS12["AGENTS.md §1.2.1"]
    SE -->|enforced in| EVENTS["events.md / tasks.md"]
    TW -->|enforced in| OBS["observations/<br/>.learnings/pending-review/"]
    LM -->|enforced in| ALL["All Layers"]
    IX -->|enforced in| AGENTS55["AGENTS.md §5.5"]

    classDef constraint fill:#cce5ff,stroke:#0066cc,stroke-width:2px
    class HR,TR,SE,TW,LM,IX constraint
```

## 知识分级写入流程图

```mermaid
graph TD
    MISTAKE["Mistake Happens"]

    MISTAKE --> Q1{"First time?"}
    Q1 -->|Yes| ERR[".learnings/ERRORS.md<br/>(一次性记录)"]
    Q1 -->|No| Q2{"Repeated 2+ times?"}
    Q2 -->|Yes| LEARN[".learnings/LEARNINGS.md<br/>(模式)"]
    Q2 -->|No, 3+ times| Q3{"Stable pattern?"}
    Q3 -->|Yes| PROMOTE["Promote to<br/>AGENTS.md / SOUL.md<br/>(Hard Rule)"]
    Q3 -->|No| OBSERVE["pending-review/<br/>30-day observation"]

    ERR --> REVIEW["Daily PDCA<br/>(23:58 cron)"]
    LEARN --> REVIEW
    OBSERVE --> REVIEW
    REVIEW --> Q1

    classDef hardRule fill:#ffcccc,stroke:#cc0000,stroke-width:2px
    classDef record fill:#fff4cc,stroke:#cc9900
    classDef observe fill:#e0e0e0,stroke:#666666

    class PROMOTE hardRule
    class ERR,LEARN record
    class OBSERVE,REVIEW observe
```

## 演进路径图（159 天）

```mermaid
graph LR
    M["2026-01-29<br/>moltbot<br/>(server-only, lost)"]
    Q["~ Feb 2026<br/>QClaw"]
    O["~ Feb-Mar 2026<br/>OpenClaw"]
    S["2026-02-20<br/>Server Backup<br/>(oldest local)"]
    L["2026-03-27<br/>Local Migration<br/>+ git init"]
    C["2026-06-26<br/>Memory Architecture<br/>Refactor"]

    M -->|rename| Q
    Q -->|rename| O
    O -->|deployed| S
    S -->|migrated local| L
    L -->|refactor| C

    classDef lost fill:#ffcccc,stroke:#cc0000
    classDef stable fill:#cce5ff,stroke:#0066cc
    classDef milestone fill:#ccffcc,stroke:#009900,stroke-width:2px

    class M lost
    class S,L,C milestone
    class Q,O stable
```

---

## 渲染说明

在 GitHub 上查看 `.md` 文件时，mermaid 代码块会自动渲染为图表。本文件可直接在 GitHub 上预览。

如果需要在本地预览：
- VS Code：安装 "Markdown Preview Mermaid Support" 插件
- Obsidian：原生支持
- Typora：原生支持