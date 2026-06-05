---
name: ielts-reading-analysis-kami
description: Use when the user gives an IELTS Reading passage and questions, asks for IELTS reading analysis, 雅思阅读解析, 阅读答案解析, 按 task1/task2/task3 分析, or wants the final analysis saved as Markdown and HTML. The skill first applies the user's Task 1 global scan, then Task 2 question-type reasoning, then outputs each question only in the Task 3 format and saves polished .md and .html analysis documents.
metadata:
  targets: [codex]
---

# IELTS Reading Analysis Markdown + HTML

## Purpose

Turn a pasted IELTS Reading passage plus questions into compact Chinese answer-analysis Markdown and HTML documents.

The workflow is mandatory:

1. Run Task 1: classify question types, set priority, and mark order/random-order warnings.
2. Run Task 2: apply the dedicated reasoning flow for each question type.
3. Produce the final analysis strictly in the Task 3 per-question format.
4. Save the final analysis as polished Markdown and HTML documents.

Do not stop after giving a plan. If the user supplies the passage and questions, analyze and save both output documents in the same turn whenever feasible.

## Required References

Read `references/task-spec.md` before analyzing any real passage. It is the user's authoritative spec for Task 1, Task 2, and Task 3.

The final deliverables are Markdown and HTML only. Do not create any other export format unless the user explicitly asks for it in that turn.

## Input Contract

Accept pasted text, screenshots converted to text, or local files. The useful input normally includes:

- the passage text, with paragraph letters if present
- all question groups and question numbers
- answer options, headings, names, tables, summaries, or word limits
- any user-marked mistakes or preferred focus, if provided

If a passage or question group is missing and cannot be inferred, ask one concise question. Otherwise proceed from context.

## Task 1 Internal Pass

Before reading deeply, produce an internal routing map:

- classify every question group into Level 1, Level 2, or Level 3
- decide the solving order: Level 1 first, Level 2 next, Level 3 last
- mark sequence behavior for each group:
  - completion questions: generally ordered; table is never random; summary/flow may be locally random
  - T/F/NG or Y/N/NG: ordered, possible paragraph skip
  - matching: unordered; names have ordered first appearances
  - multiple choice and sentence completion: ordered evidence; options can be traps
- identify paragraph-matching "one scan, two gains" opportunities while solving easier groups

This pass is for reasoning quality. Do not put a long Task 1 report in the final Markdown document unless it directly helps a question explanation.

## Task 2 Reasoning Rules

Apply the matching module from `references/task-spec.md` by question type.

Core invariant: IELTS Reading is evidence-based. Confirm answers only through the "1+1 principle" or "2plus principle": one original sentence, or adjacent sentences when pronouns/links require it, must contain enough corresponding points. Reject subjective guessing, broad translation, or answer choices supported only by vibe.

Use these shorthand rules while solving:

- Fill-in questions: locate with concrete nouns; use the word beside the blank as the question eye; predict part of speech, singular/plural, polarity, and entity type; apply "countable nouns do not run naked."
- T/F/NG or Y/N/NG: choose exactly one question eye by priority: extreme word, comparison word, then tense/predicate. Judge only the relationship between that eye and the evidence.
- Matching: extract one or two "special words" with polarity, concreteness, or image value; match through synonym, hypernym/hyponym, and attitude mapping; require at least two corresponding points.
- List of Headings: avoid relying on the first sentence alone; look for repeated concepts, including synonyms and hyponyms.
- Multiple choice: treat detail questions as short-answer first; exclude options with "rat-dropping" concepts not mentioned in the evidence; extreme/comparison claims need explicit evidence.

## Task 3 Final Output Format

The final analysis must be grouped by question type and then by question number. Use exactly the relevant Task 3 shape; do not add generic introductions.

For Level 1 fill-in questions:

1. `题目`
2. `定位词 & 题眼`
3. `预判`
4. `原文依据`
5. `推理解析`
6. `要点延伸`
7. synonym table with columns `原文`, `原文含义`, `题目替换`, `题目替换含义`

For Level 1 judgment questions:

1. `题目`
2. `定位词 & 题眼`
3. `原文依据`
4. `推理解析`, ending with `答案：True/False/Not Given` or `答案：Yes/No/Not Given`
5. `要点延伸`
6. synonym table with columns `原文`, `原文含义`, `题目替换`, `题目替换含义`

For Level 2 matching questions:

1. `题目`
2. `特别的字`
3. `原文依据`
4. `推理解析`, ending with `答案：...`
5. `要点延伸`
6. synonym table with columns `原文`, `原文含义`, `题目替换`, `题目替换含义`

For Level 3 single-choice/detail questions:

1. `题目`, including options when needed
2. `定位词 & 题眼`
3. `原文依据`
4. `推理解析`, including eliminated trap options and ending with `答案：...`
5. `要点延伸`
6. synonym table with columns `原文`, `原文含义`, `题目替换`, `题目替换含义`

For every evidence quote:

- quote only the shortest sentence or adjacent two sentences needed
- highlight no more than the key evidence words with Markdown bold in draft text
- never quote large passage chunks
- if evidence is inferred across adjacent sentences, state the link briefly

## Markdown + HTML Delivery

After producing the Task 3 analysis text, save the deliverable in both Markdown and HTML.

Output contract:

- language: Chinese unless the user explicitly asks otherwise
- output format: `.md` + `.html`
- target length: compact review note; no page limit
- save location: the active workspace's user-facing output directory when one exists; otherwise the current working directory
- final content: Task 3 per-question analysis only; Task 1/Task 2 notes stay internal unless a concise "做题顺序" strip fits naturally

Markdown rules:

- compress repeated wording; keep "要点延伸" short and exam-practical
- use Markdown headings grouped by question type
- keep evidence quotes as short blockquotes
- keep synonym tables in standard Markdown table format
- if there are many questions, group same-type questions into dense sections and keep each table compact
- do not invent evidence, answers, paragraph labels, or vocabulary meanings

HTML rules:

- the HTML must contain the same analysis content as the Markdown file
- use a self-contained static HTML file with embedded CSS
- preserve heading hierarchy, blockquotes, ordered lists, and synonym tables
- use a quiet Kami-like reading style: parchment background, ink-blue accent, warm text colors, serif-led hierarchy
- keep the HTML printable and readable, but do not create another export format

Verification:

- check the Markdown and HTML files exist
- check it has no unresolved placeholders such as `{{...}}`, todo markers, or `[DATA NEEDED]`
- check every solved question in the Markdown ends with an explicit `答案：...`
- check the HTML contains the same question headings and no unresolved placeholders
- report the absolute paths to both files

## Failure Handling

- If OCR/text is incomplete, analyze the questions whose evidence is present and list missing question numbers.
- If no answer can be supported by the text, mark it as unresolved rather than guessing.
- If either output file cannot be written, still provide the Task 3 analysis in chat and report the filesystem blocker clearly.
