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
- 通过 Notion 官方 API 和本地 Integration token 直接追加到目标 Notion `Paper` 页面本身，不创建子页面或数据库条目。
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
https://app.notion.com/p/Paper-Reading-38f75a34c926807bb1c5c880e5c6a001?source=copy_link
```

如果要改成自己的 Notion 页面，编辑：

```text
references/notion-paper-target.md
```

把页面 URL 和 page ID 改成你的目标页面。

这个 skill 推荐使用 Notion 官方 Integration，而不是依赖 Codex 内置 Notion connector。配置步骤：

1. 在 Notion 中进入 `Settings -> Connections -> Develop or manage integrations`。
2. 创建 internal integration。建议分成 `Codex Notion Read` 和 `Codex Notion Write` 两个 integration。
3. 给只读 integration 开启 `Read content`；给写入 integration 开启 `Read content`、`Insert content`、`Update content`。
4. 在目标 Notion 页面右上角 `Share -> Invite / Connections` 中添加对应 integration。
5. 在本机设置环境变量：

```bash
export NOTION_TOKEN_READ="your_read_integration_token"
export NOTION_TOKEN_WRITE="your_write_integration_token"
```

如果使用 Windows PowerShell：

```powershell
setx NOTION_TOKEN_READ "your_read_integration_token"
setx NOTION_TOKEN_WRITE "your_write_integration_token"
```

设置完成后重启 Codex，使环境变量对 Codex 进程可见。不要把 token 粘贴到聊天窗口，也不要在终端中直接打印 token。

注意：Notion 的公开编辑权限不等于 API integration 权限。即使页面所有人可编辑，如果没有把目标页面显式分享给对应 Integration，Notion API 仍可能返回 `object_not_found` 或 `404`。

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
4. 使用 `NOTION_TOKEN_WRITE` 调用 Notion API，将笔记直接追加到目标 `Paper` 页面。

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

通常是目标 Notion 页面没有显式授权给当前 Integration。请在目标 Notion 页面中通过 `Share -> Invite / Connections` 添加对应 Integration。

### 只生成了内容，没有写入 Notion

通常是 `NOTION_TOKEN_WRITE` 不存在、Codex 启动时没有读取到环境变量，或目标页面没有授权给写入 Integration。这个 skill 会避免退而创建子页面。

### 想写入其他 Notion 页面

编辑 `references/notion-paper-target.md` 中的 URL 和 page ID。

## 适用场景

- 快速判断一篇论文值不值得细读。
- 为论文阅读记录生成统一格式。
- 把 embodied AI、robot learning、VLA/WAM 等方向论文归档到 Notion。
