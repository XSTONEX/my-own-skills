---
name: ielts-reading-synonym-lark
description: Use when the user asks to summarize IELTS Reading synonym substitutions, IELTS reading vocabulary replacement tables, or append a new reading passage's synonym table into the user's Lark/Feishu project document. The workflow creates an incremental Part/reading section, preserves a fixed four-column table format, centers all cells, highlights high-frequency IELTS words in light red, and summarizes repeated vocabulary patterns across previous readings.
---

# IELTS Reading Synonym Lark

## Target

Use this skill to turn one IELTS Reading passage into an incremental Lark/Feishu document entry.

Default project document:
`https://my.feishu.cn/wiki/WEcdwUYciimC2qkaWygcwI6QnTb`

Always pair this skill with `lark-doc` and `lark-doc-format-writer` for Feishu/Lark reads and writes. All `lark-cli docs` commands must use `--api-version v2`.

## Required Output

For each new passage, append or insert a section in the project document:

1. Use the Part label as an H1, for example `Part 1`.
2. Use the reading passage title as an H2, for example `Australia's cane toad problem`.
3. Add a blockquote containing the access path, formatted as `访问路径：...`.
4. If repeated vocabulary patterns exist, add a `重复出现` module before the main synonym table.
5. Add the main synonym table with exactly four columns:
   - `文章原词`
   - `含义`
   - `题目替换词`
   - `含义`

## Table Rules

- Tables must be centered by default:
  - every `<th>` and `<td>` must include `vertical-align="middle"`
  - every paragraph inside table cells must be `<p align="center">...</p>`
- Table headers use `background-color="rgb(242,243,245)"` or `light-gray`.
- Highlight very high-frequency IELTS Reading vocabulary with inline light red background:
  - preferred XML: `<span background-color="light-red">word</span>`
  - fetched documents may normalize this to an RGB value; that is acceptable.
- Highlight only the key English word or phrase, not the whole row.
- Use light red sparingly for high-frequency exam vocabulary and reusable synonym anchors.
- Include both single words and phrases when they are needed for answering questions.
- If an item is a single English word, include its part of speech in the meaning column, for example `名词, ...`, `形容词, ...`, `动词过去式或过去分词, ...`, `副词, ...`.
- If an item is an English phrase, write the phrase type when useful, for example `名词短语, ...`, `动词短语, ...`, `介词短语, ...`.
- Preserve question direction: column 1 is from the passage; column 3 is the replacement wording from the question.
- Include NOT GIVEN / no information relationships when they matter for the reading answer.

## Repeated Vocabulary Module

Before writing the new passage's table, read existing project content and compare vocabulary from previous entries.

Add a `重复出现` H3 only when at least one meaningful repeated pattern is found. Use one table with these columns:

- `重复原词`
- `含义`
- `常见替换`
- `替换词含义`
- `出现形式`

Summarize patterns in either direction:

- one source often maps to multiple replacements, for example `A -> B / C / D`
- multiple sources often map to one replacement, for example `A / B / C -> D`
- phrases that recur as a family, for example `introduction / introduced / bring to`

Write meanings for both sides. Keep this module compact and useful for review, not exhaustive.

## Extraction Workflow

1. Identify the reading title, Part, source/access path, passage wording, question wording, and the user's marked highlights or mistakes if provided.
2. Extract synonym substitutions that are necessary for solving the questions:
   - direct word replacements
   - phrase-to-phrase paraphrases
   - grammar transformations
   - broader category vs specific example
   - opposite/inference relationships
   - time/order/degree traps
   - no-information relationships
3. Normalize each row into the fixed four-column table.
4. Add part of speech for single-word entries.
5. Mark high-frequency vocabulary with light red inline spans.
6. Read the project document and build the repeated vocabulary module from previous entries.
7. Insert the new section incrementally:
   - if the target Part already exists, insert the reading under that Part without overwriting older readings
   - if the Part does not exist, append a new H1 Part section
8. Fetch the updated section with `--detail full` and verify headings, table columns, vertical center, paragraph center, and high-frequency highlights.

## Lark XML Pattern

Read `references/docxxml-pattern.md` when constructing the final XML table or repeated-vocabulary module.

Use precise block operations where possible:

- `block_insert_after` to add a new reading section
- `block_replace` only for the target block being corrected
- avoid `overwrite` unless the user explicitly asks to rebuild the whole document

## Quality Bar

- The final document should be useful as an IELTS review notebook, not just a word list.
- Prefer high-signal rows tied to question solving over generic vocabulary.
- Keep Chinese meanings concise but explicit.
- Do not silently drop the user's customized headers or formatting.
- After writing, report the document link and what was added.
