# Format Patterns

Use these patterns when deciding how to format a Feishu/Lark document. Pair every meaningful format choice with a practical reason.

## Quick Mapping

| Format | Use when |
|-|-|
| Title/headings | Organizing sections, creating a readable outline, and enabling navigation. |
| Paragraph | Writing normal background, explanation, or narrative context. |
| Bold | Highlighting conclusions, rules, field names, warnings, or must-read text. |
| Italic | Adding light emphasis, tone, or secondary terms. |
| Underline | Marking short text that needs attention or confirmation. |
| Strikethrough | Marking deprecated content, old plans, or rejected ideas. |
| Inline code | Showing field names, status values, endpoint names, commands, or config keys. |
| Text color/background | Calling out status, risk, success, warnings, or changed terms. |
| Link | Pointing to references, external resources, or related pages. |
| URL preview card | Making an important external resource stand out as a rich entry point. |
| Quote | Preserving source text, external opinion, assumptions, or discussion context. |
| Unordered list | Listing parallel points, rules, responsibilities, or notes. |
| Ordered list | Showing steps, priority, sequence, lifecycle, or verification order. |
| Checkbox | Tracking action items, open questions, or review checks. |
| Table | Defining fields, enums, states, ownership, comparisons, and structured data. |
| Callout | Front-loading conclusions, decisions, risk notices, assumptions, or summaries. |
| Grid/columns | Comparing two paths, before/after states, pros/cons, or parallel workstreams. |
| Code block | Showing JSON, SQL, CLI commands, configuration, pseudocode, or logs. |
| Divider | Separating topics or resetting visual rhythm between major sections. |
| Image | Showing screenshots, designs, UI states, exported diagrams, or visual evidence. |
| Whiteboard | Explaining flows, architectures, state machines, timelines, or complex relationships. |
| Mermaid | Rendering simple flowcharts, sequence diagrams, and dependency graphs from text. |
| PlantUML | Rendering UML-style interactions, class diagrams, or engineering diagrams. |
| SVG whiteboard | Creating small custom diagrams with controlled visual style. |
| Blank whiteboard | Reserving space for complex diagrams to be filled later. |
| Bookmark | Saving important long-lived web references. |
| Button | Providing a clear action such as opening a link or duplicating/following a page. |
| Time/reminder | Showing deadlines, review times, or notification-worthy milestones. |
| @doc | Linking related Feishu documents inside the document body. |
| @user | Assigning ownership or attention when a real user ID is available. |
| Formula | Writing metrics, equations, scoring rules, or calculation definitions. |
| Attachment | Keeping PDFs, raw files, recordings, archives, or supporting materials with the doc. |
| Embedded sheet | Storing data that needs calculation, filtering, or ongoing maintenance. |
| Task block | Embedding real Feishu tasks when a task ID exists. |
| Chat card | Linking a relevant group chat when a chat ID exists. |

## Common Document Recipes

### Technical Spec

- Start with a callout for scope, decision, and unresolved risk.
- Use tables for API fields, data objects, state enums, and constraints.
- Use ordered lists for flows and lifecycle rules.
- Use Mermaid/PlantUML for sequence diagrams, state machines, and service interactions.
- Use code blocks for payloads, config, and examples.

### Meeting Notes

- Start with a callout containing conclusion and next decision.
- Use checkboxes for action items.
- Use @users for owners when user IDs are available.
- Use tables for decisions, owners, due dates, and open questions.
- Use quotes for important original wording or disputed points.

### Proposal Or Comparison

- Use grid columns for two-option comparisons.
- Use tables for multidimensional tradeoffs.
- Use callouts for recommendation and risk.
- Use Mermaid/SVG when the proposal changes a workflow or architecture.

### Reference Guide

- Use headings for each concept.
- For every format or feature, show a short demonstration followed by one sentence explaining when to use it.
- Use tables only for compact lookup sections; use real rendered blocks for teaching by example.

## XML Snippets

Place one blank paragraph after every h1/h2/h3 heading, and one more blank paragraph at the end of the section before the next same-level or higher-level heading:

```xml
<h2>Section title</h2>
<p></p>
<p>Section content...</p>
<p></p>
<h2>Next section title</h2>
```

Use additional blank paragraph blocks to separate semantic groups inside dense prose, especially before examples, after summary blocks, between rules and field definitions, and between input/output groups.

Never write U+3002 Chinese full stop in document text. Split long prose with commas, semicolons, lists, callouts, or blank paragraph blocks instead.

```xml
<callout emoji="📌" background-color="light-blue" border-color="blue">
  <p>Core purpose or conclusion goes here.</p>
</callout>
```

```xml
<table>
  <thead>
    <tr>
      <th background-color="light-gray" vertical-align="middle"><p align="center">Field</p></th>
      <th background-color="light-gray" vertical-align="middle"><p align="center">Use</p></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td vertical-align="middle"><p align="center"><code>status</code></p></td>
      <td vertical-align="middle"><p align="center">Shows current lifecycle state.</p></td>
    </tr>
  </tbody>
</table>
```

```xml
<grid>
  <column width-ratio="0.5"><p><b>Option A</b></p><p>Use for one side.</p></column>
  <column width-ratio="0.5"><p><b>Option B</b></p><p>Use for the other side.</p></column>
</grid>
```

```xml
<whiteboard type="mermaid">sequenceDiagram
  participant A
  participant B
  A->>B: Request
  B-->>A: Response
</whiteboard>
```
