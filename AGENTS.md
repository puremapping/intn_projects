# Internalization Projects — Pi 上下文

这是一个 Internalization（内化）项目空间。下面每个子目录是一个 `object × intent` 项目。

## 核心概念

```
project = object × intent
```

- **Object**：你面对的材料（书、课程、论文、代码库）。它有自身的结构和设计意图。
- **Intent**：你以什么态度进入这段关系（master / experience / explore / critique / reference / custom / open）。

详见 `README.md`。

## 首次进入新子目录

如果用户在一个空目录启动 Pi 且该目录没有 `.pi/project.md`：

1. 检查同级目录 `material-vault/` 是否存在
2. 询问用户是否要设置项目——引导对话，不要自动创建
3. 按顺序问：object 名称 → intent → 材料路径（可选）
4. 写入 `.pi/project.md`
5. 根据 intent 推荐产出类型，但明确表示「不强制」

## Intent 与推荐产出

| intent | 推荐产出 | 技能 |
|--------|---------|------|
| master | atoms/ + reviews/（分阶段回顾）+ framework.md（可选 exam 查漏补缺） | 全套技能可用 |
| experience | notes/ + optional reading-notes.md | review, link-atoms 可选 |
| explore | notes/ 随缘 | 无预设 |
| critique | atoms/ + review.md + summary.md | 全套技能可用，但 framework 改为 summary |
| reference | 不产出 | 按需回答 |
| custom | 用户自定 | 引导用户配置 |
| open | 不预设 | 第一句话决定 |

## Skill 命令

- `/skill:extract-outline material/<file>` — 提取大纲
- `/skill:link-atoms` — 扫 atoms/ 补交叉引用
- `/skill:learning-review` — 生成回顾
- `/skill:learning-framework` — 生成最终总结
- `/skill:learning-exam` — 阶段考试与补习（查漏补缺闭环）

## 写作约定

- **署名规范**：所有产出的文件（README、summary、framework、notes、atoms）按以下格式署名：
  ```
  Concept, design & terminology by puremapping · Written by pi (AI coding agent)
  ```
  对于较小或非正式的文件（如 notes 随手记），可简写为 `by puremapping & pi`。
- `atoms/*.md` 的 frontmatter 用 `author: human / ai / mixed` 标记作者
- 用户手写的 [[引用]] 不覆盖
- AI 补充的引用用 `<!-- ai:suggested -->` 标注
- 写 atom 时，内容限于当前会话中实际涉及的，不要向外搬运知识

## 仓库更新规则（git / GitHub）

本仓库 `intn_projects` 只维护**系统设计**部分（AGENTS.md、README.md、ANNOTATION_SPEC.md 等框架/约定文档），**不纳入具体项目内容**（lmstudio/、stablediffusion/ 等 `object × intent` 项目目录，由 .gitignore 排除，靠 Syncthing 同步）。

- 允许更新：系统设计的约定、技能、规则调整
- 不提交：任何项目的产出（atoms/、notes/、reviews/、material/）
- 但可以在更新说明中**提到项目名**作为示例（如「lmstudio 与 stablediffusion 按 reviews/ 分阶段归档」）
- 提交粒度：系统设计改动分开 commit（如 AGENTS.md 约定与 .gitignore 配置分开）
