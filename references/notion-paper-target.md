# Notion Paper 目标

将完成后的中文论文阅读笔记直接追加到用户的 Notion Paper 页面：

https://app.notion.com/p/Paper-37e51a5692e0807a862ed0b6b55f23aa?source=copy_link

页面 ID：`37e51a56-92e0-807a-862e-d0b6b55f23aa`

## 写入策略

1. 使用可用的 Notion connector/tools。若当前没有暴露工具，先搜索 Notion 工具。
2. 目标是普通 Notion 页面 `Paper`，不是数据库目标。
3. 直接向该页面追加内容。优先使用 `update_page` 的 `insert_content` / append 能力。
4. 不要创建子页面、standalone page、database item 或 database row，除非用户明确改口要求。
5. 如果当前只暴露 `create_pages` 而没有 `update_page`，不要退而创建子页面；应告知用户当前工具不足以按要求直接写入目标页面，并提供待写入的中文内容。

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
