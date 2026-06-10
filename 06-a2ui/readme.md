# A2UI：当前端框架成为多余的中间层

> Created By [RV](mailto:rodney.vin@gmail.com), and licensed with Creative Commons "[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/)"

### 一个思想实验

假设，有一个 AI Agent，它可以：

1. 理解用户的意图
2. 根据意图，动态生成 UI 的结构、布局、数据绑定
3. 以 JSON 流的形式推送到客户端
4. 客户端只需把 JSON 映射到本地组件，渲染出来

那么问题来了：**React 在中间扮演的是什么角色？**

一个渲染器。

仅此而已。

不要路由——路由由 Agent 根据当前对话状态决定。
不要 Store——状态在 Agent 侧，客户端没有"本地状态"需要管理。
不要数据获取/缓存层——数据随 UI 描述一起推下来，用完即弃。
不要生命周期效应——组件生灭由 Agent 的消息流驱动，不需要 `useEffect` 去"同步"什么。

当这些东西全部被抽走之后，React 还剩下什么？

一个带 Diff 算法的 `render()` 函数。

这就是 A2UI（Agent to User Interface，Google 发起并开源）所揭示的未来。它不是要替代 React，而是要**重新定义 React 的位置**——从一个"应用框架"被降级为"渲染引擎"。

### 前端框架的困境

前端框架的复杂度源自一个隐含假设：**UI 是静态确定的，应用逻辑必须在前端运行。**

在这个假设下，我们发明了：

- 组件化（封装 UI 逻辑）
- 状态管理（解决组件间通信）
- 路由（管理视图切换）
- 数据获取/缓存（对接后端 API）
- 构建工具（打包、优化、分块）

每一层都有其存在的理由，每一层也都有其历史背景。

但 A2UI 的假设完全不同：**UI 是 Agent 动态生成的，应用逻辑运行在 Agent 侧。**

当这个假设成立时，上述所有层的必要性都开始动摇。

这不是渐进式的改进，而是范式层面的颠覆。就像是马车的每一个部件都优化到极致后，汽车出现了——你不再需要马鞭、马鞍、草料袋了，因为动力源整个变了。

### 状态管理是最先被淘汰的

Redux 的存在理由：多个组件需要共享状态，且状态变化逻辑复杂，需要可预测的管理。

在 A2UI 的世界里：

- 状态在 Agent 侧（后端），以状态机的形式存在
- 组件不需要共享状态——它们是"一次性投影"，当前状态驱动当前 UI，状态变了，Agent 发新的 UI 描述过来
- 组件之间不需要通信——它们唯一的"通信对象"是 Agent

```
传统前端：
  Store → Component A
       ↘→ Component B → 同步 → Component C
       ↗→ Component D

A2UI 前端：
  Agent → JSON → [Renderer → Component A
                                 Component B
                                 Component C
                                 Component D]
```

组件之间不需要知道彼此的存在。它们都是 Agent 的"输出终端"。

所以 Redux、Zustand、Jotai、Pinia……这些解决"多组件共享状态"问题的工具，在 A2UI 的语境下，丧失了存在的基础。

这不代表它们不好，而是**它们解决的问题消失了**。

### 路由是第二个被淘汰的

前端路由的存在理由：单页应用需要在多个视图间切换，而不触发页面刷新。

在 A2UI 的世界里：

- "视图"是 Agent 生成的 Surface
- 一个 Surface 的存在与消失，由 Agent 的 `createSurface` / `deleteSurface` 消息控制
- Surface 之间的切换，对应 Agent 内部的**状态转移**，而非 URL 变化

```
传统 SPA：
  URL /users → UsersPage
  URL /users/:id → UserDetailPage
  URL /settings → SettingsPage
  URL 变化 → 路由匹配 → 组件切换

A2UI：
  Agent 状态 S1 → createSurface("main", [UserList])
  Agent 状态 S2 → updateComponents("main", [UserDetail])
  Agent 状态 S3 → createSurface("dialog", [SettingsForm])
  Agent 状态 S4 → deleteSurface("dialog")
```

URL 作为"应用状态的外部编码"这个需求，在 Agent 内部已经做了——Agent 的状态机就是"路由表"。前端不需要 React Router，不需要 Vue Router，不需要任何路由器。

### 数据获取/缓存是第三个被淘汰的

React Query、SWR、Apollo Client 的存在理由：前端需要从后端获取数据，并管理数据的加载、缓存、失效、重试状态。

在 A2UI 的世界里：

- 数据不是"前端主动获取"的，而是"Agent 随 UI 一起推送"的
- 不存在"缓存失效"——因为前端根本没有缓存
- 不存在"重试"——Agent 知道数据状态，它决定何时推送
- 不存在"加载状态"——Agent 可以在 UI 描述中包含 `loading` 组件，或者干脆先推骨架屏再推真实数据

```
传统前端：
  组件挂载 → fetch(/api/users) → loading → data → re-render

A2UI 前端：
  Agent 发送 { surface, components: [Skeleton] }
  Agent 发送 { surface, components: [UserList], dataModel: { users: [...] } }
  Renderer 依次渲染
```

哪有什么数据获取？数据是**和 UI 一起来的**。

### 无框架化，不是不要框架

说"前端无框架化"，听起来很激进。实际上，我说的不是"不要框架"，而是：

1. **不再需要"应用框架"**——React/Vue/Angular 作为"应用框架"的那一部分（路由、状态管理、数据层）变得多余
2. **只需要"渲染框架"**——一个高效的、能将 A2UI JSON 映射到本地组件的渲染层

这就像终端模拟器不需要 Redux——它只需要高效地渲染字符。A2UI 的客户端，本质上就是**一个图形化的终端模拟器**，只是它渲染的不是字符，而是 Component Catalog 中的组件。

### 那前端工程师做什么？

很多人听到"无框架化"就恐慌——那前端工程师是不是失业了？

正好相反。

前端工程师的关注点从"如何管理状态/路由/数据"，转移到了更本质的问题上：

1. **设计 Component Catalog**——你提供的组件库质量，决定了 A2UI 渲染的上限。好的 Catalog 让 Agent 能表达丰富的 UI，差的 Catalog 让 Agent 只能输出"文字 + 按钮"
2. **定义组件的约束与行为**——Agent 生成的 UI 必须受控。Catalog 不仅仅是组件列表，更是 Agent 可以"安全使用"的组件白名单。组件需要清晰的 Props 契约、安全边界、Fallback 策略
3. **确保渲染性能**——即使 UI 结构变简单了，Agent 高频推送时的渲染性能仍然是挑战。增量更新、动画平滑、内存管理，需要专门优化
4. **构建 Agent 可理解的组件文档**——Agent 不是人类，它通过 Catalog 的 Schema 来理解组件。如何描述组件的用途、用法、约束，让 Agent 能准确调用，这是一门新的 engineering 手艺

### 后端状态机：新的架构核心

当客户端被去框架化（确切地说，是被去应用逻辑化）之后，架构的核心就转移到了后端——更具体地说，是**后端的状态机**。

这个状态机：

- 是系统的 Single Source of Truth
- 驱动 UI 的生成与更新
- 处理用户交互的反馈
- 管理多步业务流程
- 是 AI Agent 的"思维骨架"

而这个状态机的输出，通过 A2UI 协议投递到客户端。

```
┌──────────────────────────────────────────┐
│            AI Agent / 后端                 │
│                                           │
│  状态机 (State Machine / Statechart)       │
│  ┌─────┐    ┌─────┐    ┌─────┐           │
│  │ S1  │ →  │ S2  │ →  │ S3  │  ...      │
│  └─────┘    └─────┘    └─────┘           │
│       ↕ 状态转移 ↕                         │
│  ┌─────────────────────────────┐          │
│  │ A2UI Message Generator      │          │
│  │ f(S) → { createSurface,     │          │
│  │          updateComponents,   │          │
│  │          updateDataModel }   │          │
│  └─────────────────────────────┘          │
└───────────────┬──────────────────────────┘
                │ A2UI JSON Stream
                ▼
┌──────────────────────────────────────────┐
│     客户端：A2UI Renderer                  │
│                                           │
│  React / Lit / Angular / Flutter          │
│  (仅作为渲染引擎存在)                       │
│                                           │
│  无 Store / 无 Router / 无 Data Layer     │
└──────────────────────────────────────────┘
```

这个模式跟 kreex-spec 一直在说的"AI 时代的架构工程"完全一致：

- **自顶向下，全局约束** → 状态机是顶层的全局约束
- **AI 引导的人类意图理清** → 状态机是意图的形式化表达
- **面向 AI：结构化语言模型** → A2UI JSON 是 AI 输出的结构化 UI 描述
- **面向人类：图形化视觉模型** → Catalog 组件是人类可见的 UI 呈现

从 SDD 到架构工程，到 A2UI，逻辑链条是连贯的：**我们一直在做的事，就是把 AI Agent 的输出从"不可控的文字"变成"可控的结构化 UI"。**

### 所以，我们可以聊什么？

这个方向涉及的主题很多，面也很广，初步拟了几个方向，可以作为系列文章的骨架：

---

## 系列文章主题规划

### 第一组：认知篇——A2UI 是什么，为什么重要

| # | 主题 | 核心问题 |
|---|------|---------|
| 1 | **A2UI 协议解读：Agent 如何"说"UI** | 什么是 Surface、Component、Data Model、Catalog？4 种核心消息如何工作？ |
| 2 | **A2UI 的哲学：UI 所有权从客户端转移到 Agent** | 为什么说 A2UI 不是一个 UI 库，而是一种架构范式？ |
| 3 | **为什么 Google 要做 A2UI** | 从 GenUI SDK、Flutter、A2A 协议看 Google 的战略意图 |

### 第二组：批判篇——现有模式的局限性

| # | 主题 | 核心问题 |
|---|------|---------|
| 4 | **前端状态管理的黄昏** | Redux/Zustand/Jotai 在 A2UI 语境下为何变得多余 |
| 5 | **前端路由的终结** | 当 Agent 控制 Surface 生命周期，React Router 还有什么用 |
| 6 | **数据获取层的消失** | React Query/SWR 在 A2UI 世界中的位置 |
| 7 | **构建工具的简化** | A2UI 客户端不需要 Code Splitting 和动态加载？ |

### 第三组：建构篇——新架构的形态

| # | 主题 | 核心问题 |
|---|------|---------|
| 8 | **后端状态机：AI 时代的新架构核心** | State Machine / Statechart 如何取代前端的 Store + Router |
| 9 | **Component Catalog：前端工程师的新资产** | 如何设计一个 Agent 可理解、人类可使用的 Catalog |
| 10 | **A2UI + 状态机的双向同步模式** | 状态机如何映射到 UI，UI 交互如何反馈到状态机 |
| 11 | **从 SDD 到 A2UI：Spec 驱动开发的自然延伸** | A2UI 作为 Spec 的可视化输出端，闭环是什么 |

### 第四组：实践篇——如何落地

| # | 主题 | 核心问题 |
|---|------|---------|
| 12 | **现有项目如何迁移到 A2UI** | 增量改造策略：先替换局部 UI，再逐步扩大 |
| 13 | **设计一个最小的 A2UI Renderer** | 从消息处理到组件渲染，一个 Renderer 至少需要什么 |
| 14 | **在多 Agent 架构中使用 A2UI** | 编排 Agent 与子 Agent 各自生成 Surface，如何协调 |
| 15 | **安全性：为什么 A2UI 比代码生成更安全** | Catalog 白名单、声明式 vs 可执行、跨信任边界的 UI 传递 |

### 第五组：展望篇——更远的未来

| # | 主题 | 核心问题 |
|---|------|---------|
| 16 | **前端工程师的新角色** | 从 "UI Developer" 到 "Catalog Designer + Renderer Engineer" |
| 17 | **A2UI 与 IRE（Integrated Requirement Environment）** | A2UI 如何成为"人类可视化输入意图、AI 生成 UI"的桥梁 |
| 18 | **无框架化不是倒退** | 为什么说"变薄"是进步——复杂度的转移是架构进化的本质 |

---

## 这个目录的定位

`06-a2ui` 这个目录，将是 kreex-spec 整体叙事中的一个独立但又紧密关联的分支。

它与其它目录的关系：

- `01-problems` — 定义了问题（人与 AI 的沟通鸿沟、前端架构的困境）
- `02-ai-ide-sdd` — 定义了"开发级别"的应对方案（SDD）
- `02-arch-engineering` — 定义了"架构工程级别"的解决方案（全局约束、逐层细化）
- `04-ai-structural-lang` — 定义了 AI 的输入语言（C4、PlantUML、Mermaid）
- `05-human-visual-model` — 定义了人类的视觉模型（树形结构、C4 可视化、类图、序列图）
- **`06-a2ui` — 定义 AI 的输出语言：Agent 如何以 UI 的形式与人类交互**

A2UI 是"面向 AI 的结构化语言"的反向——它是 AI **输出给人类**的结构化语言。

如果能做到"结构化语言输入 → AI 处理 → A2UI 输出"这个闭环，SDD 到架构工程的升级链路才算真正完整。

对此，你怎么看？从哪里开始聊起比较好？
