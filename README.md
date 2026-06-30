# Read Paper to Notion

一个用于快速精读论文、归档论文笔记、或进行论文问答的 Codex skill。它会读取本地 PDF、arXiv/DOI URL 或论文引用，用中文提炼论文核心内容；归档模式会把精简笔记直接追加到指定的 Notion `Paper` 页面，详细问答模式默认只在聊天中回答。

## 功能概览

这个 skill 聚焦于快速理解一篇论文的核心贡献。输出内容固定为：

- `## <论文标题>`
- `**TL;DR**`
- `**论文 motivation**`
- `**方法创新点**`
- `**实验 benchmark**`
- `**比较的 baseline**`
- `**实验结论**`

默认规则：

- 始终用简体中文回答和写入 Notion。
- 保留论文标题、模型名、benchmark、数据集名、指标名等英文术语，避免翻译损失精度。
- 不写入作者列表、会议年份、来源链接、本地路径、PDF 上传状态等元信息。
- 直接追加到目标 Notion `Paper` 页面本身，不创建子页面或数据库条目。
- 如果用户要求详细阅读、深度解读或回答具体问题，会只在聊天回答中解释抽象概念，帮助小白理解论文；默认不写入 Notion。

## 目录结构

```text
read-paper-to-notion/
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
└── references/
    ├── notion-paper-target.md
    └── paper-analysis-template.md
```

## 安装方式

### 方式一：克隆到 Codex skills 目录

```bash
mkdir -p ~/.codex/skills
git clone <your-repo-url> ~/.codex/skills/read-paper-to-notion
```

重启或刷新 Codex 后，即可通过 `$read-paper-to-notion` 使用。

### 方式二：手动复制

将整个 `read-paper-to-notion` 文件夹复制到：

```bash
~/.codex/skills/read-paper-to-notion
```

确保目录中至少包含：

- `SKILL.md`
- `references/paper-analysis-template.md`
- `references/notion-paper-target.md`
- `agents/openai.yaml`

## Notion 配置

默认写入目标在：

```text
https://app.notion.com/p/Paper-37e51a5692e0807a862ed0b6b55f23aa?source=copy_link
```

如果要改成自己的 Notion 页面，编辑：

```text
references/notion-paper-target.md
```

把页面 URL 和 page ID 改成你的目标页面。

注意：Notion 的公开编辑权限不等于 API integration 权限。需要在 Notion 页面右上角 `...` -> `Connections` 中添加当前 ChatGPT/OpenAI/Codex 对应的 Notion connection，否则工具可能返回 `object_not_found`。

## 使用方式

给 Codex 一个论文 PDF、arXiv 链接、DOI 或本地文件路径，并显式调用 skill：

```text
$read-paper-to-notion https://arxiv.org/pdf/2606.20521
```

或：

```text
$read-paper-to-notion /path/to/paper.pdf
```

Codex 会：

1. 读取论文内容。
2. 抽取 motivation、方法创新、benchmark、baseline 和实验结论。
3. 生成中文六段式笔记。
4. 使用 Notion connector 将笔记直接追加到目标 `Paper` 页面。

### 详细阅读 / 问答模式

当你希望更彻底理解论文，或想让 Codex 回答某个具体问题时，可以这样问：

```text
$read-paper-to-notion 详细解释这篇论文里的 World-Action Model 和 OOD generalization，我是小白。
```

或：

```text
$read-paper-to-notion 这篇论文为什么说 egocentric video 比 real-robot data 更适合 pretraining？请把关键概念解释清楚。
```

在这种模式下，Codex 会先解释必要概念，例如 pretraining/post-training、World-Action Model、validation action loss、OOD generalization、benchmark、baseline、ablation 或 scaling law，再回答问题。默认情况下，这类详细问答不会写入 Notion；只有你明确要求“写入 Notion / 归档 / 保存”，才会执行 Notion 写入。

## 输出示例

```markdown
## HumanScale: Egocentric Human Video Can Outperform Real-Robot Data for Embodied Pretraining

**TL;DR**

...

**论文 motivation**

...

**方法创新点**

...

**实验 benchmark**

...

**比较的 baseline**

...

**实验结论**

...
```

## 常见问题

### Notion 返回 `object_not_found`

通常是 Notion 页面没有授权给当前 integration。请在目标 Notion 页面中添加对应 connection。

### 只生成了内容，没有写入 Notion

当前会话可能没有暴露 `update_page` 工具。这个 skill 会避免退而创建子页面，并要求用户重新授权或重新加载 Notion connector。

### 想写入其他 Notion 页面

编辑 `references/notion-paper-target.md` 中的 URL 和 page ID。

## 适用场景

- 快速判断一篇论文值不值得细读。
- 为论文阅读记录生成统一格式。
- 把 embodied AI、robot learning、VLA/WAM 等方向论文归档到 Notion。
