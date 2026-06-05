---
name: lark-doc-format-writer
description: Use when creating or rewriting Feishu/Lark documents where the user wants polished formatting, richer Docx blocks, layout optimization, readable structure, or format-aware document writing
metadata:
  targets: [codex]
---

# Lark Doc Format Writer

## Overview

Use this skill together with `lark-doc` when writing Feishu/Lark docs. The goal is not just to transfer text into a document, but to choose document blocks that make the content easier to scan, compare, verify, and maintain.

## Required Pairing

- Load and follow `lark-doc` for the actual Feishu API workflow, authentication, and XML syntax rules.
- Read `references/format-patterns.md` when deciding which document blocks to use or when turning a plain draft into a polished Feishu document.
- Prefer XML for creation/editing unless the user explicitly asks for Markdown import.

## Workflow

1. Identify the document purpose: spec, meeting notes, proposal, review checklist, reference guide, tutorial, or status report.
2. Preserve the user's substance; improve structure, hierarchy, and block choice.
3. Start the document with a short callout that front-loads the core purpose or conclusion.
4. Convert dense text into richer blocks:
   - Tables for fields, enums, matrices, ownership, and comparisons.
   - Lists for rules, requirements, and ordered workflows.
   - Checkboxes for action items and verification lists.
   - Callouts for conclusions, warnings, assumptions, and decisions.
   - Grid columns for two-side comparisons or parallel tracks.
   - Code blocks for JSON, commands, SQL, configs, Mermaid source, or API examples.
   - Whiteboards/Mermaid/PlantUML/SVG for important flows, architectures, timelines, or state machines.
5. After writing, fetch or inspect the document enough to verify that headings, key blocks, and at least a few representative examples rendered.

## Formatting Rules

- Add breathing space around every h1/h2/h3 section: insert one blank paragraph block after the heading, and one blank paragraph block at the end of that section before the next same-level or higher-level heading.
- Add semantic blank paragraph blocks inside dense text where the meaning shifts, such as between overview and details, rules and examples, input and output, or separate decision groups. Do not remove intentional blank paragraphs merely because they are empty.
- When a section or list needs numbered structure, use Lark's native numbering instead of typing numbers into visible text. In DocxXML, use `seq="auto"` and `seq-level="auto"` on numbered headings or `seq="auto"` on ordered-list items as applicable.
- Every code block must be followed by one blank paragraph block, usually `<p></p>`, so later manual editing in Lark keeps spacing stable.
- Tables must be centered by default: every th/td should use `vertical-align="middle"`, and each cell's paragraph content should use `align="center"`.
- Do not use U+3002 Chinese full stop anywhere in generated or edited document text. Prefer commas, semicolons, line breaks, or shorter sentence fragments.
- Each major h1/h2 section should contain at least one non-plain-text block when the content supports it.
- Avoid more than three consecutive plain paragraphs; introduce a table, list, callout, code block, grid, or diagram.
- Use the same visual treatment for the same semantic purpose across the document.
- Use color sparingly and semantically: blue for information, yellow for caution, red for risk, green for recommended/success.
- Do not force a fancy block when a simple paragraph is clearer.

## Output Standard

When the user asks to create a Feishu document, return the document link and mention what formatting strategy was applied. If some format could not be demonstrated because it needs real IDs, files, or permissions, state that briefly.
