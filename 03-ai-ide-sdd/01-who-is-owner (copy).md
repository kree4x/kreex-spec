# Developer的错觉：SPEC是谁的?

> Created By [RV](mailto:rodney.vin@gmail.com), and licensed with Creative Commons "[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/)"

整个软件行业，以“Development”，以“开发阶段”作为一切的核心太久了。

基本是“有史以来”，皆是如此。

理由，林林总总，身在其中，我们每个人都清楚地知道，这一切是怎么发生，以及这一切为什么会发生的。

这，带来了巨大的行业惯性，以及严重的思维定式。

在Spec驱动开发，AI Coding的时代，顺理成章的、我们下意识的认为Spec是属于开发者，属于Developer的。

那么，Spec是Developer的么？

它，是，也不是。

这取决于，我们如何理解Spec是什么，Developer当前在做什么，以及如何在一个更大的视图中，以何种视角来看AI带给软件行业的这场颠覆性的变革。

### Spec是什么？

在当前的各种AI IDE的语境中，我们讲spec的时候，是在指代一系列的文档，另一方面也是在讲一个三阶段(或四阶段)的工作流程。

以OpenSpec为例:

```text
openspec/
├── project.md          # Project conventions
├── AGENTS.md           # AI instructions
├── specs/              # Current truth - what IS built
│   └── [capability]/
│       ├── spec.md     # Requirements and scenarios
│       └── design.md   # Technical patterns (optional)
└── changes/            # Proposals - what SHOULD change
    ├── [change-name]/
    │   ├── proposal.md
    │   ├── tasks.md
    │   └── specs/      # Delta changes
    └── archive/        # Completed changes
```



形态上表现为proposal.md、spec.md、requirement.md、task.md、checklist.md等一些列md文件。



### Developer在做什么？

在当前的各种AI IDE的语境中，我们讲spec的时候，另一方面也是在讲一个三阶段(或四阶段)的工作流程。

三阶段流程：

**提案(Proposal) -> 实施(Implementation) -> 归档(Archiving)** 

或者扩展的四阶段：

**提案（Proposal）→ 规划（Planning）→ 实施（Implementation）→ 归档（Archiving）** 

```text
┌────────────────────┐
│ Draft Change       │
│ Proposal           │
└────────┬───────────┘
         │ share intent with your AI
         ▼
┌────────────────────┐
│ Review & Align     │
│ (edit specs/tasks) │◀──── feedback loop ────┐
└────────┬───────────┘                        │
         │ approved plan                      │
         ▼                                    │
┌────────────────────┐                        │
│ Implement Tasks    │────────────────────────┘
│ (AI writes code)   │
└────────┬───────────┘
         │ ship the change
         ▼
┌────────────────────┐
│ Archive & Update   │
│ Specs (source)     │
└────────────────────┘
```



### AI辅助？还是AI驱动？

SDD是一种AI辅助

从AI驱动的







