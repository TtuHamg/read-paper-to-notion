---
name: read-paper-to-notion
description: Analyze academic papers from PDFs, arXiv/DOI URLs, local files, or citations, extract only the TL;DR, motivation, method innovations, experimental benchmarks, compared baselines, and experimental conclusions, then append that concise Chinese reading note directly into the user's Notion Paper page without creating a child page or database item. Always answer the user in Simplified Chinese and write the Notion note in Simplified Chinese, while preserving paper titles, model names, dataset names, metrics, and official terminology in their original language when clearer. Use when the user asks to read, understand, summarize, dissect, compare, archive, deeply read, explain, or answer questions about a research paper; in detailed reading or Q&A mode, explain abstract or difficult concepts in beginner-friendly language.
---

# Read Paper to Notion

## Overview

Read one research paper deeply enough to explain its core contribution and evidence, then append a compact structured note directly into the user's Notion Paper page.

Always answer the user in Simplified Chinese and write the Notion note in Simplified Chinese. Preserve paper titles, model names, dataset names, metrics, benchmark names, and official terms in their original language when translating them would reduce precision. Start the Notion note with the paper title as a second-level heading (`## <paper title>`), then keep the body limited to the six requested bold section labels: **TL;DR**, **论文 motivation**, **方法创新点**, **实验 benchmark**, **比较的 baseline**, **实验结论**.

When the user asks for detailed reading, deeper explanation, or answers to specific questions, make the user-facing answer beginner-friendly by explaining abstract concepts, methods, metrics, and assumptions before using them. Keep the Notion note concise unless the user explicitly asks to store the detailed explanation too.

## Workflow

1. Locate and load the paper.
   - Prefer the user-provided PDF, local file, arXiv URL, DOI, publisher page, or repository link.
   - If the user gives only a title or citation, find the official PDF or primary source before analyzing.
   - If the paper text is incomplete, say exactly which sections/tables are missing and avoid filling gaps with guesses.

2. Analyze the paper using `references/paper-analysis-template.md`.
   - Read abstract, introduction, method, experiments, main tables, ablations, limitations, and conclusion.
   - Tie claims to specific sections, figures, or tables when possible.
   - Explain baseline comparisons with actual metrics and benchmark names whenever the paper reports them.
   - Do not include source metadata, author lists, local paths, upload status, or separate limitation/reading-note sections in the final Notion content unless the user explicitly asks.
   - Include the paper title in the final Notion content as a second-level heading before the six sections.

3. Save the note to Notion using `references/notion-paper-target.md`.
   - Prefer the Notion connector/tools over browser automation.
   - Append the note directly to the configured `Paper` page.
   - Do not create a child page, standalone page, or database item unless the user explicitly changes the destination.
   - Do not attach or link the PDF in the Notion note unless the user explicitly asks.

4. Report back to the user.
   - Provide the reading summary in Simplified Chinese and say whether Notion archiving succeeded.
   - If Notion upload is blocked by missing authorization/tooling, provide the completed Chinese note and the exact blocker.
   - For detailed reading or Q&A requests, include short concept explanations in the user-facing answer so a beginner can follow the paper's logic.

## Analysis Standards

- Center the analysis on motivation, method innovation, benchmarks, baselines, and experimental conclusions.
- Prefer concrete evidence over generic praise: dataset names, metrics, table numbers, ablation findings, and error cases.
- State limitations and assumptions even if the paper underplays them.
- Mark uncertain statements with "可能" or "推测" and explain why they are not directly supported by the paper.
- Avoid long verbatim excerpts from copyrighted papers; summarize in your own words and quote only short phrases when necessary.
- Keep the final note content free of metadata sections such as authors, venue, source links, code links, local file paths, and upload status.
- Format the Notion note with `## <paper title>` followed by bold section labels, not heading-style section labels.

## Beginner-Friendly Detailed Mode

Use this mode when the user asks for "详细阅读", "深度解读", "解释一下", "我不懂", "回答问题", or asks a specific technical question about the paper.

- Identify 3-8 concepts that are necessary to understand the answer, such as model families, losses, metrics, datasets, training stages, ablations, scaling laws, or domain assumptions.
- Explain each difficult concept in plain Chinese before relying on it. Prefer "它想解决什么问题 / 为什么需要它 / 在本文里怎么用" over textbook definitions.
- Use a simple analogy only when it makes the concept clearer; do not replace the paper's actual mechanism with the analogy.
- Connect every explanation back to the paper's claim or experiment so the answer does not become a generic tutorial.
- Separate paper-stated facts from your own interpretation with phrases like "论文中直接说明..." and "我的理解是...".
- Keep detailed explanations in the user-facing answer. Only append them to Notion if the user explicitly asks for detailed notes.

## Resources

- Read `references/paper-analysis-template.md` before drafting the paper note.
- Read `references/notion-paper-target.md` before writing to Notion.
