# Notion Paper 目标

将完成后的中文论文阅读笔记直接追加到用户的 Notion Paper 页面：

https://app.notion.com/p/Paper-Reading-38f75a34c926807bb1c5c880e5c6a001?source=copy_link

页面 ID：`38f75a34-c926-807b-b1c5-c880e5c6a001`

## 写入策略

1. 优先使用 Notion 官方 API 和本地环境变量，不依赖 Codex 内置 Notion connector 的工作区绑定。
2. 默认读取 Notion 内容时使用 `NOTION_TOKEN_READ`；只有用户明确要求写入、追加、保存或归档时，才使用 `NOTION_TOKEN_WRITE`。
3. 写入前只检查 token 是否存在，不要打印 token 内容，不要把 token 粘贴到聊天窗口或命令输出里。
4. 目标是普通 Notion 页面 `Paper`，不是数据库目标。
5. 直接向该页面追加 block，API endpoint 为 `PATCH https://api.notion.com/v1/blocks/<page_id>/children`。
6. 不要创建子页面、standalone page、database item 或 database row，除非用户明确改口要求。
7. 如果官方 API 返回 `object_not_found` 或 `404`，优先判断为目标页面没有显式分享给当前 Integration；让用户在 Notion 页面 `Share -> Invite / Connections` 中添加对应 Integration。
8. 只有当官方 API 路径不可用且用户接受时，才考虑 Notion connector/tools；不要因为 connector 只能创建页面就退而创建子页面。

## API 调用约定

写入时使用 `NOTION_TOKEN_WRITE`：

```bash
curl -sS -X PATCH "https://api.notion.com/v1/blocks/38f75a34-c926-807b-b1c5-c880e5c6a001/children" \
  -H "Authorization: Bearer $NOTION_TOKEN_WRITE" \
  -H "Content-Type: application/json" \
  -H "Notion-Version: 2022-06-28" \
  --data @payload.json
```

验证或读取页面内容时优先使用 `NOTION_TOKEN_READ`：

```bash
curl -sS "https://api.notion.com/v1/blocks/38f75a34-c926-807b-b1c5-c880e5c6a001/children?page_size=10" \
  -H "Authorization: Bearer $NOTION_TOKEN_READ" \
  -H "Notion-Version: 2022-06-28"
```

`payload.json` 应只包含追加 block，不包含 token。建议将 `## <论文标题>` 转为 `heading_2` block，将六个加粗 section 标签和正文转为 paragraph block；section 标签使用 rich text `annotations.bold: true`。

需要用户先在 Notion 中完成：

1. 创建 internal integration，例如 `Codex Notion Read` 和 `Codex Notion Write`。
2. 给读写 integration 开启 `Read content`、`Insert content`、`Update content`。
3. 在目标页面右上角 `Share -> Invite / Connections` 中显式添加对应 Integration。
4. 在本机环境变量中配置 `NOTION_TOKEN_READ` 和 `NOTION_TOKEN_WRITE`，然后重启 Codex。

## 页面正文

正文必须使用简体中文，并使用 `paper-analysis-template.md` 的标题加六段结构：

1. `## <论文标题>` 作为二级标题
2. `**TL;DR**`
3. `**论文 motivation**`
4. `**方法创新点**`
5. `**实验 benchmark**`
6. `**比较的 baseline**`
7. `**实验结论**`

不要写入来源链接、本地文件路径、作者列表、会议年份、代码链接、PDF 上传状态或其他元信息，除非用户额外要求。

## 追加格式

为避免多篇论文混在一起，必须在六段内容前添加论文标题，且标题使用二级标题：

`## <论文标题>`

除这行标题外，正文只保留六个指定部分。六个 section 名称必须使用加粗文本，不要使用 heading。

## 失败处理

如果 Notion 写入失败：

- 不要丢弃分析结果。
- 告诉用户失败发生在哪一步：授权、目标查找、工具缺失、页面更新或 schema 不匹配。
- 提供原本要写入 Notion 的中文正文，且正文仍只包含二级标题和六个加粗 section。
