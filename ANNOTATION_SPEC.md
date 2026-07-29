# Annotation Specification — 通用文件注释规范

基于 W3C Web Annotation 标准，针对本地文件场景扩展。

---

## 核心概念

一条注释（Annotation）是在一个文件的位置 b 上附加一段信息 x，并可选地记录这条信息来自另一个文件的位置 a。

```
文件 A（笔记）
  at 位置 a ──── 注释 ────→ 文件 B（源文件）
                         at 位置 b
```

注释本身存储在**独立的 JSON 文件**中，不嵌入任何源文件。

---

## 数据结构

### 完整字段

```json
{
  "id": "ref-001",
  "motivation": "citing",
  "created": "2026-07-22T18:30:00+08:00",
  "creator": "puremapping",

  "target": {
    "source": "educated.pdf",
    "selector": [
      { "type": "TextQuoteSelector", "exact": "...", "prefix": "...", "suffix": "..." }
    ]
  },

  "body": {
    "type": "TextualBody",
    "value": "梯度是损失函数对权重的偏导数……"
  },

  "ref_by": {
    "source": "atoms/gradient-vanishing.md",
    "selector": [
      { "type": "TextPositionSelector", "start": 284, "end": 292 }
    ]
  },

  "tags": ["反向传播", "链式法则"]
}
```

### 字段说明

| 字段 | 必需 | 来源 | 说明 |
|------|------|------|------|
| `id` | ✅ | 扩展 | 全局唯一标识，建议 `{prefix}-{seq}`，如 `ref-001` |
| `motivation` | ✅ | W3C | 注释意图，见下文 |
| `created` | ✅ | W3C | 创建时间，ISO 8601 |
| `creator` | 推荐 | W3C | 创建者标识 |
| `target` | ✅ | W3C | 指向被引文件 B 及其精确位置 b |
| `target.source` | ✅ | W3C | 被引文件的路径 |
| `target.selector` | 推荐 | W3C | 位置的精确锚定，可以是多个选择器的冗余 |
| `body` | 因动机而异 | W3C | 注释正文内容 |
| `ref_by` | 因动机而异 | 扩展 | 指向引用端文件 A 及其精确位置 a |
| `ref_by.source` | 因动机而异 | 扩展 | 引用端文件的路径 |
| `ref_by.selector` | 推荐 | 扩展 | 引用端的位置锚定 |
| `tags` | 可选 | W3C | 标签列表，用于检索和分类 |

---

## 选择器（Selector）

### TextQuoteSelector（推荐——最通用）

基于原文文本本身定位，不依赖文件格式、页码、坐标。

```json
{
  "type": "TextQuoteSelector",
  "exact": "the gradient of the loss function",
  "prefix": "is computed by applying",
  "suffix": "backwards through the network"
}
```

- `exact`：被引原文（必需）
- `prefix`：原文前面的上下文（推荐，用于消歧）
- `suffix`：原文后面的上下文（推荐，用于消歧）

**只要原文文字不变，文件重新生成、页码变化、格式转换，这个锚点仍然有效。**

### TextPositionSelector（辅助）

基于字符偏移，优先级低于 TextQuoteSelector，仅作为辅助。

```json
{
  "type": "TextPositionSelector",
  "start": 15352,
  "end": 15843
}
```

### HeadingSelector（Markdown 专用）

```json
{
  "type": "HeadingSelector",
  "heading": "## 链式法则"
}
```

### FragmentSelector（PDF 页码/矩形坐标，辅助）

```json
{
  "type": "FragmentSelector",
  "value": "page=42&rect=120,340,450,360"
}
```

### 多层冗余原则

建议同时提供多种选择器，一种失效时退到另一种。TextQuoteSelector 永远是最后的兜底。

```json
"selector": [
  { "type": "FragmentSelector", "value": "page=42&rect=120,340,450,360" },
  { "type": "TextPositionSelector", "start": 15352, "end": 15843 },
  { "type": "TextQuoteSelector", "exact": "the gradient of...", "prefix": "...", "suffix": "..." }
]
```

---

## 动机（Motivation）

| 动机 | target | body | ref_by | tags | 含义 |
|------|--------|------|--------|------|------|
| `citing` | ✅ B/b | ✅ 注释 | ✅ A/a | 可选 | **引用**：你在 A 中写正文时，引用 B 中的内容作为支撑 |
| `commenting` | ✅ B/b | ✅ 批注 | 缺省 | 可选 | **批注**：你在读 B 时，在 B 的原文上附加想法 |
| `highlighting` | ✅ B/b | 缺省 | 缺省 | 可选 | **高亮**：纯标记，不附加文字 |
| `replying` | ✅ 注释 id | ✅ 回复 | 可选 | 可选 | **回复**：对另一条注释的回应 |
| `linking` | ✅ B/b | 缺省 | 可选 | 可选 | **链接**：纯粹建立关联，不写注释 |
| `tagging` | ✅ B/b | 缺省 | 可选 | ✅ 必需 | **打标签**：贴关键词或层级分类路径 |

### citing——最常用的动机

这是整个工作流的核心。记录「我在笔记 A 的位置 a 引用了源文件 B 的位置 b，我写了如下注释」。

```json
{
  "id": "ref-001",
  "motivation": "citing",
  "target": { "source": "educated.pdf", "selector": [...] },
  "ref_by": { "source": "atoms/gradient-vanishing.md", "selector": [...] },
  "body": { "type": "TextualBody", "value": "梯度是损失函数对权重的偏导数……" }
}
```

### highlighting——最轻量的动机

选中一段文字后标记，不写任何注释。

```json
{
  "id": "hl-001",
  "motivation": "highlighting",
  "target": { "source": "educated.pdf", "selector": [...] }
}
```

### replying——形成注释链

`target` 不指向源文件，而是指向另一条注释的 id。这样多条注释可以形成讨论链。

```json
{
  "id": "reply-001",
  "motivation": "replying",
  "target": { "source": "ref-001" },
  "body": { "type": "TextualBody", "value": "但我觉得这个解释忽略了学习率的动态调整。" }
}
```

### tagging——贴关键词或层级分类路径

`tags` 字段支持两种写法：

**扁平关键词**——快速检索用：

```json
{
  "id": "tag-001",
  "motivation": "tagging",
  "target": { "source": "educated.pdf", "selector": [...] },
  "tags": ["反向传播", "链式法则"]
}
```

**层级路径**——你自己的知识分类体系，路径中的 `/` 表示层级：

```json
{
  "id": "tag-002",
  "motivation": "tagging",
  "target": { "source": "educated.pdf", "selector": [...] },
  "tags": [
    "深度学习/训练算法/反向传播",
    "数学/微积分/链式法则"
  ]
}
```

解析器的处理规则：
1. 包含 `/` 的 tag 视为层级路径
2. 不包含 `/` 的 tag 视为扁平关键词
3. 查询时匹配前缀即可

不需要单独的 `classifying` 动机——层级路径就是分类。

---

## 文件存储

### 目录结构

```
educated/
├── educated.pdf                              ← 源文件，原样不变
├── educated.annotations/                     ← 侧车目录：所有关于这篇文件的注释
│   ├── ref-001.json
│   ├── ref-002.json
│   ├── hl-001.json
│   └── reply-001.json
└── .annotations/                             ← 附件目录（截图快照等）
    └── educated/
        ├── ref-001.png
        └── ref-002.png
```

### 索引文件

当注释数量增多时，建议生成一个索引文件以加速反向查询：

```json
// educated.annotations/index.json
{
  "source": "educated.pdf",
  "annotations": ["ref-001", "ref-002", "hl-001", "reply-001"]
}
```

### 笔记中的引用写法

笔记文件（Markdown）中通过注释 id 来引用，不依赖任何编辑器插件：

```markdown
根据 [[ref-001]]，反向传播的核心是链式法则。
```

纯文本可读，任何 Markdown 渲染器都能正常显示。跳转和预览行为由渲染器实现。

---

## 通用查询操作

| 查询 | 方法 |
|------|------|
| 正向：A 在 a 处引用了 B 的哪个位置？ | 查 `ref_by.source = "A"` 且 `ref_by.selector` 包含 a |
| 反向：B 的 b 处被哪些笔记引用过？ | 查 `target.source = "B"` 且 `target.selector` 包含 b |
| 全文：B 有哪些注释？ | 查 `target.source = "B"` |
| 用户：puremapping 的所有 cite？ | 查 `creator = "puremapping"` 且 `motivation = "citing"` |
| 标签：所有「反向传播」相关的注释？ | 查 `tags` 包含 "反向传播" |

---

## 与 W3C 标准的关系

| 差异 | 说明 |
|------|------|
| 扩展 `ref_by` 字段 | W3C 的 target 只记录了「指向哪里」，缺少「来自哪里」。`ref_by` 是对称补充。 |
| 扩展 `citing` 动机 | W3C 没有 `citing`。最接近的是 `linking`，但 `linking` 不含注释正文。 |
| `target.source` 使用文件路径 | W3C 标准使用 HTTP URI。本地场景下用相对/绝对路径替代。 |
| 其他字段 | 与 W3C 完全兼容。W3C 的 body 可以是字符串也可以是对象，这里统一使用对象形式。 |
