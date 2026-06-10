# SDD锦上添花之一：使用独立工程管理SPEC

> Created By [RV](mailto:rodney.vin@gmail.com), and licensed with Creative Commons "[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/)"

在SDD的体系中，存在两个标志性、核心信念式的Slogan：

- Spec is a first-class citizen：Spec是一等公民
- The spec is the single source of truth：SPEC是唯一的事实来源

但是，回头看看实际的工程组织层面，事情么，有些幽默。

您的spec在哪里？

在.claude、.cursor、.github、.kiro、.trae目录里。

这些目录原本是工具meta目录，而您的Spec混在其中。

### OpenSpec Issue [#449](https://github.com/Fission-AI/OpenSpec/issues/449) 在讨论什么？

#449在讨论一个简单的问题：

*“Use dot folder instead of root folder for OpenSpec #449”*

使用"**.openspec**"，而不是“**openspec**”目录存放OpenSpec相关文件。

而OpenSpec团队最终的选择是使用不带点的“**openspec**”目录。

理由么，很简单：

*Spec 是一等公民，不是隐藏的配置。它应该像 `src/` 一样显眼，而不是像 `.git/` 一样藏起来。*

`.xxx`的思维定式，为何如此强大？

两个字回答：**惯例**

### 历史惯例：以 `.` 开头的含义

在Dev的世界里，以点"**.**"开头去命名一个文件，或者一个目录，其含义是“**隐藏**”。

当我们希望一个东西存在，但又不希望它的存在扰乱了我们的视野，扰乱当前视图上下文时，名字前边加一个点，简单又easy。

并且，这是一个历史惯例

这个惯例，来自1971年Unix V1，`ls`命令的一个设计决定(bug)。

Unix/Linux系的文件系统中，存在两个条目

- `.`指代当前目录
- `..`指代上级目录

`ls`命令实现时为了默认不显示这两个条目，在代码中粗暴的加入了一个判断：忽略以`.`开头的文件

而用户很快发现，如果把一个文件命名为`.xxx`，它就不会出现在ls的输出中。

于是，bug成了feature，feature成了惯例，惯例而成了轶事美谈。

而这个本质是个bug的feature所导致的余波，在AI时代还在继续荡漾。

### 两种设计哲学

OpenSpec项目中， “**.openspec**"还是“**openspec**”的取舍，本身是小事。

命名背后的两种设计哲学，才是根本的对立点。

- **隐藏配置**：spec是工具的内部状态，开发者不应该直接操作
- **显示规范**：spec是一等公民和单一事实来源，开发者应该主动看到和编辑它

谁对？谁错？

这是一潭浑水，跳入这个争论，就别想干干净净出来。

强如OpenSpec，也成了折中党，和稀泥，搞了一个[#697](https://github.com/Fission-AI/OpenSpec/issues/697)

*"Support custom openspec/ directory path"*

**加不加点真的重要么？**

其实，**.openspec** vs **openspec** 的争论，只是表象。

面对一个工程时，随便放在哪里，都不会导致严重的问题。

真正的杀手级场景，单工程中是看不到。

面对杀手级场景时，加不加点根本不重要，spec**无处可放**，才最重要。

### 局限在Dev内，问题无解

不论“.openspec"还是“openspec”，**当面对多工程时，不论放在哪里都是错**，问题是无解的。

**单任务，多工程**

企业级开发，我们很难用一种技术栈、一个工程搞定一切。

即使是简单的一个Web CRUD，也存在前端、后端。

此时，一个Feature、一个Task所对应的spec，您将它放到哪个工程才合适？

**Current Truth**，系统的全局Req+Design，您将它放到哪里才合适？

**MonoRepo**

于是，我们引入了MonoRepo的概念，一个Repo多个Packages，多个工程。

将specs放置到MonoRepo的根目录，放到Workspace的根目录。

看起来，问题已解决。

**MonoRepo的递归**

最近在看一些开源项目，打开之后，一眼望过去，看到工程结构后，人是懵逼的。不知所云的话，建议您去看A2UI……

一个Git Repo，多个不同的MonoRepo工程。eg.

- AI代码一个MonoRepo

- Server一个MonoRepo

- Client一个MonoRepo

- examples中一堆嵌套小工程

- Python、TypeScript/JavaScript、React、Agular……

自闭中……

一个Feature横跨多个MonoRepo，遍及一串工程……

此时，Current Truth又该放在哪里？

将视野局限在“Dev”，这个我们最舒适的领域，其实问题是没有解决方案的。

而视野放大，看到更大的地图，难题才有答案。

### Spec一等公民，值得一个独立工程

既然，Spec是SDD的一等公民，就不要搞得见不得人似的，藏到隐藏目录中。

既然，放到任何一个工程下，都不合适，那么就不要放到“任何”工程下。

工程化去管理Spec本身，在SDD的话语体系中，应该是一个显而易见的结论。

AI时代，AI会导致整个软件工程链条的完全重组。

将视野放大到整个软件工程全链条粒度去看，Req+Design+验收规则，是整个工程链条的核心和根本。

让Spec承载的Req+Design，成为真正的一等公民。

**一个独立的工程，Spec值得拥有。**

如何使用独立的工程去管理Req + Design，进而导出面向单次任务的Spec，这就是另外一个话题了。

后续再聊。



