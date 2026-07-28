# Internalization Projects

**Internalization** 是你与一个对象（书、课程、论文、代码库、任何值得专注面对的材料）之间一段有意向的关系的容器。

与传统的 project（对外交付软件、设计稿、研究报告）不同，internalization project 的对象不被你加工改变——**改变的恰恰是你自己**。你让对象影响你、塑造你，而你留下的笔记、框架、总结，都是这个内化过程的自然沉淀，而非对外交付物。

内外对照：

| | Externalization（传统项目） | Internalization（本项目） |
|---|---|---|
| 加工方向 | 人 → 对象 | 对象 → 人 |
| 对象状态 | 被改变（代码被重构、设计稿被输出） | **不被改变**（书还是那本书） |
| 产出 | 对外交付（软件、设计稿、研究报告） | **对内沉淀**（笔记、理解、感受） |
| 结束标志 | 对象达到预定状态 | 你觉得「够了」 |

---

## 核心公式

```
project = object × intent
```

- **Object（对象）**：你面对的那份材料。它有自己的结构、类型和设计意图——书有书的写法，课有课的体系，代码库有代码库的架构。你不改变它。
- **Intent（意图）**：你以什么态度进入这段关系。这一次是体验？还是掌握？还是批判？同一对象可以有多段关系，每一段是一个独立的 project。

---

## Intent 类型

| Intent | 含义 | 推荐的产出格式 | 行为 |
|--------|------|---------------|------|
| **master** | 掌握知识体系 | atoms + review + framework | 概念关系图式回顾 |
| **experience** | 体验式阅读 | notes + optional reading-notes | 感触/困惑/共鸣式回顾 |
| **explore** | 探索，不确定 | 随缘 | 不推荐任何产出 |
| **critique** | 批判分析 | atoms + review + summary | 分析+评析式总结 |
| **reference** | 查询/参考 | 已回答的问题清单 | 不产出，只回答 |
| **custom** | 自定义 | 你来定义 | 我引导你配置工作流 |
| **open** | 无明确意图 | 不预设 | 第一句话决定方向 |

---

## 目录结构

```
intn_projects/
├── README.md                              ← 本文件，系统总览
├── AGENTS.md                              ← Pi 全局上下文，所有子项目继承
├── material-vault/                        ← 源文件统一仓库（可选，避免重复存储）
│   ├── educated.pdf
│   ├── transformers-paper.pdf
│   └── ...
│
├── educated-experience/                   ← {object}-{intent}
│   ├── .pi/
│   │   └── project.md                     ← 项目元信息
│   ├── material/ → (路径引用或软链接)
│   ├── notes/
│   │   └── 读到一半的感悟.md
│   └── reading-notes.md
│
├── educated-critique/                     ← 同一对象，不同意图，独立项目
│   ├── .pi/project.md
│   ├── material/ → (同源文件)
│   ├── atoms/
│   │   ├── 自传与虚构的边界.md
│   │   └── 教育的代价.md
│   ├── review.md
│   └── summary.md
│
├── desktop-pc-master/
│   ├── .pi/project.md
│   ├── material/
│   ├── atoms/
│   ├── review.md
│   └── framework.md
│
└── transformers-explore/
    ├── .pi/project.md
    ├── material/
    └── notes/
```

### project.md 格式

```yaml
object: 你当像鸟飞往你的山       # 对象名称
intent: experience               # 意图
author: 塔拉·韦斯特弗            # 作者/创作者（可选）
material_path: ../material-vault/educated.pdf  # 源文件路径（可选）
date_start: 2026-07-22           # 开始日期
```

---

## 产出格式一览

| 格式 | 用途 | 与 intent 的亲和性 |
|------|------|-------------------|
| `*.outline.md` | 材料大纲 | 通用，所有类型 |
| `notes/*.md` | 随手感想，不提炼 | experience, explore, critique |
| `atoms/*.md` | 可复用的知识单元 | master, critique |
| `review.md` | 学习回顾 | master, critique |
| `framework.md` | 概念关系图 | master |
| `summary.md` | 背景+梗概+评析+联系+升华 | experience, critique |
| `reading-notes.md` | 阅读手记 | experience |

所有产出都是**可选**的，不是流水线。你觉得需要才产出。

---

## 技能工具

以下 Pi 技能（Skill）辅助这个工作流。它们存放在 `~/.pi/agent/skills/`（全局）或项目内的 `.pi/skills/`。

| 技能 | 功能 | 调用方式 |
|------|------|---------|
| `extract-outline` | 从材料提取章节目录大纲 | `/skill:extract-outline material/xxx.pdf` |
| `link-atoms` | 扫描 atoms/ 补交叉引用 | `/skill:link-atoms` |
| `learning-review` | 分析当前会话，生成回顾 | `/skill:learning-review` |
| `learning-framework` | 综合笔记+回顾+大纲，输出最终总结 | `/skill:learning-framework` |

除技能外，你随时可以让我直接做任何事——写一段总结、画一张概念图、解释一个概念——无需通过技能。

---

## 首次进入新项目时的引导

当你在一个没有 `.pi/project.md` 的新目录里启动 Pi 时，我会检测到并询问：

1. **Object 是什么？**（书名 / 课程名 / 代码库名）
2. **你的 Intent 是？**（master / experience / explore / critique / reference / custom / open）
3. **材料文件在哪？**（可选，留空也可以开始）
4. 我写入 `.pi/project.md`，然后根据 intent 告诉你我推荐怎么用我

引导不花哨，几句对话完成。

---

## 工作流不是流水线

这个系统的设计原则是：

- **技能是工具箱，不是流水线。** 你可以选任何一个用，也可以一个都不用。
- **产出是自然沉淀，不是作业。** 有感触就写 notes，有清晰概念就写 atoms，想回顾就跑 review。什么都没有也完全可以——读本身就是目的。
- **intent 决定推荐，但不强制。** `master` 不一定非要有 `framework.md`，`experience` 也不是必须写 `reading-notes.md`。

一切回到那句话：

> 一场旅行，不是为了带走什么，留下什么。只是为了去穿过，去见证。
> 如果偶有灵感，随手一记，也是为了给旅程本身增色。
