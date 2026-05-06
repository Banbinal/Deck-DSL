---
name: dsl-deck
description: Generate single-page visual decks in the "Deck DSL" — a markdown-extended format that renders to consulting-grade HTML/CSS/SVG (neutral palette, configurable accent color, embedded schemas). The DSL ships with a standalone live editor (`index.html`) where the user pastes DSL on the left and sees the rendered document on the right. Block types include KPI tiles, timelines, 2×2 matrices, flow/decision diagrams, sequence diagrams, funnels, pyramids, org charts, cards, comparison tables, and callouts. Use this skill whenever the user asks for any "polished one-pager with diagrams" — even if they don't name the DSL by name. Strong triggers: strategy review, executive summary, status update, monthly business review, MBR, QBR, board update, decision memo, RFC summary, project brief, postmortem write-up, kickoff doc, "consulting-style document", "McKinsey-style deliverable", "deck-style page", "visual one-pager", "exec-ready document". Also trigger when the user mentions `.dsl.md` files, the file `index.html` in the deck-dsl repo, or any time text + diagrams need to live in the same document. Default to using this skill rather than mermaid or plain markdown when the user wants a multi-section professional output, because mermaid produces single isolated diagrams and plain markdown lacks the visual schemas this DSL specializes in.
---

# Deck DSL

A skill for producing single-page visual decks written in the **Deck DSL** — markdown extended with fenced visual blocks. Output renders inside the shipped `index.html` (a standalone editor: source pane left, rendered document right) and prints cleanly to PDF.

## When to use this skill

Use it when the user asks for:
- A strategy review, exec summary, MBR, QBR, board update
- A decision memo, RFC summary, project brief, postmortem, kickoff doc
- A "polished one-pager", "consulting-style deliverable", or "visual document with diagrams"
- Any deliverable where prose and diagrams need to coexist on a single scrolling page

Don't use it for:
- A single isolated diagram (use mermaid or just SVG)
- Long technical reference docs / API documentation
- Slide decks with multiple pages (this DSL produces one continuous page)
- Plain text outputs with no visual structure

## Output workflow

1. **Clarify only what's missing.** Title, audience, the 3–5 key messages, any specific numbers to highlight. Don't ask the user which block types to use — pick them yourself based on the data shape.
2. **Write the deck** as one Deck DSL document. Save it as `<topic>.dsl.md` if a path makes sense, or print it inline so the user can paste it into `index.html`.
3. **Tell the user how to render it**: open `index.html` in a browser, paste the DSL into the left pane (or click `Sample` then replace), and the document renders on the right. Use the toolbar's `Print / PDF` button to export.

## Document anatomy

A deck is one file with two parts:

1. **Frontmatter** (YAML between `---` markers) — title, subtitle, eyebrow, author, date, theme.
2. **Body** — markdown headings, paragraphs, lists, callouts, horizontal rules, and **fenced visual blocks**.

Minimum viable deck:

```markdown
---
title: Q4 2026 Strategy Review
subtitle: Path to scale
author: Strategy Team
date: 2026-05-06
theme:
  accent: "#7A1F2B"
---

# Executive summary

We hit our **revenue target by 12%** and ...

## Headline metrics

` ` `kpi
Revenue | $4.2M | +12%
NPS | 62 | +8
` ` `
```

(Fences shown spaced above so they render in this skill file. Use real triple-backticks in actual output.)

## Frontmatter fields

All fields are optional but `title` is strongly recommended.

| Field | Purpose | Example |
|-------|---------|---------|
| `title` | Document title (large serif, top of page) | `Q4 Strategy Review` |
| `subtitle` | Italic tagline below title | `Path to scale` |
| `eyebrow` | Small uppercase label above title (audience, classification) | `Strategy Team · Confidential` |
| `author` | Author or team name | `Strategy Office` |
| `date` | Date in any human-readable form | `2026-05-06` |
| `theme.accent` | Hex color used everywhere as accent | `"#7A1F2B"` |

**Theme accent — picking a color.** Default is burgundy `#7A1F2B`. Match the user's brand if mentioned. Common neutral-friendly options:

- `#7A1F2B` burgundy (default)
- `#1F4E79` corporate blue
- `#2E5D38` forest green
- `#A65A1B` rust / terracotta
- `#3E2A56` plum
- `#1F2328` near-black (monochrome)

## Markdown elements

Standard markdown is supported with these specifics:

- `#`, `##`, `###`, `####` — headings (h1–h4). `##` gets a small accent dash before it.
- Paragraphs — separated by blank lines.
- `**bold**`, `*italic*`, `` `code` `` — inline marks.
- `[label](url)` — inline links.
- `- item` or `* item` — unordered list.
- `---` (alone on a line) — horizontal rule.
- `> [!type] text` — callout (see below).

### Callouts

Callouts are quote-style lines starting with `>`. Five flavors:

- `> [!note]` — neutral (default if no marker, accent color)
- `> [!tip]` — green, positive insight
- `> [!warning]` — amber, caution
- `> [!danger]` — red, critical risk
- `> [!info]` — blue, FYI

If the first line is short and headline-shaped, it renders bold as the callout title; subsequent lines are the body.

```markdown
> [!tip]
> Biggest unlock
> Product-led growth. We expect 30% of new logos via self-serve by year end.
```

Use callouts sparingly — one per page max keeps them effective.

## Visual block reference

All visual blocks use fenced code with a language tag. Lines inside use `|` as the field separator. **Use exactly these names** — unknown fences fall through to a code block.

### `kpi` — headline metric tiles

```
` ` `kpi
Label | Value | Delta
` ` `
```

- 2–6 tiles works best (auto-grids at 160px min column).
- Delta is optional. Prefix `+` for positive (green), `-` or `−` for negative (red), no prefix for flat.
- Format numbers human-friendly: `$4.2M`, `120K`, `8,400`. The renderer does not reformat.

**Example:**
```
` ` `kpi
Revenue | $4.2M | +12%
New logos | 84 | +5%
NPS | 62 | +8
CAC | $890 | +18%
` ` `
```

### `timeline` — horizontal milestones

```
` ` `timeline
Date | Label | Optional note
` ` `
```

- 3–6 milestones is the sweet spot.
- Dates render in small caps above the line; labels in serif below.
- Items alternate above/below the line.

**Example:**
```
` ` `timeline
2026 Q1 | Discovery
2026 Q2 | Pilot launch
2026 Q3 | GA release | EU + US
2026 Q4 | Scale
` ` `
```

### `matrix` — 2×2 prioritization grid

```
` ` `matrix
x: <X axis name> | <low label> | <high label>
y: <Y axis name> | <low label> | <high label>
Item label | <low|high on X> | <low|high on Y>
...
` ` `
```

- The top-right quadrant (high X / high Y) is highlighted in accent — use this axis convention so the priority cell is the one users naturally read first.
- Classic axes: Effort × Impact, Cost × Value, Difficulty × Importance.
- 4–10 items split across quadrants.

**Example:**
```
` ` `matrix
x: Effort | low | high
y: Impact | low | high
Self-serve onboarding | low | high
Enterprise tier | high | high
Logo refresh | low | low
SOC2 audit | high | low
` ` `
```

### `flow` — decision tree / process diagram

```
` ` `flow
[NodeA] -> [NodeB]
[NodeA] -label-> {Decision}
{Decision} -yes-> (End)
` ` `
```

Node shapes:
- `[Label]` — rectangle (default, processes/states)
- `{Label}` — diamond (decisions)
- `(Label)` — pill / rounded (terminal states, accent-filled)

Edges:
- `->` — plain arrow
- `-label->` — labeled arrow (the label appears on the connector)

**Layout:** auto-layered top-to-bottom from source nodes. Best for 4–10 nodes; larger graphs get cramped.

**Example:**
```
` ` `flow
[Lead] -> {Qualified?}
{Qualified?} -yes-> [Demo]
{Qualified?} -no-> [Nurture]
[Demo] -> [Trial]
[Trial] -> (Close)
` ` `
```

### `sequence` — actor interaction diagram

```
` ` `sequence
ActorA -> ActorB: Message
ActorB --> ActorA: Response (dashed)
note over ActorA, ActorB: Annotation
` ` `
```

- `->` solid, `-->` dashed (use dashed for responses/async).
- Actor order = order of first appearance.
- `note over A, B: text` adds a side note across actors.
- 2–5 actors, up to ~10 messages reads cleanly.

**Example:**
```
` ` `sequence
Prospect -> Sales: Books demo
Sales -> Prospect: Sends trial link
Prospect -> Product: Activates
Product --> Sales: Notifies
Sales -> Prospect: Closes deal
` ` `
```

### `funnel` — conversion stages

```
` ` `funnel
Stage | Value
` ` `
```

- Width is proportional to value.
- Values are extracted as numbers but rendered as written (`120K`, `$4.2M` etc).
- 3–5 stages.

**Example:**
```
` ` `funnel
Visitors | 120000
Sign-ups | 18000
Activated | 6500
Paying | 920
` ` `
```

### `pyramid` — hierarchical layers

```
` ` `pyramid
Layer label
Another layer
Optional: with subtitle
` ` `
```

- Top layer narrowest, bottom widest. Top is the most abstract / strategic.
- Each line is one layer. If a line contains `:`, the part before is rendered as a small uppercase label and the part after as the layer text.
- 3–5 layers. Classic chain: Vision → Strategy → Tactics → Execution.

**Example:**
```
` ` `pyramid
Vision: Make data accessible
Strategy: Self-serve + AI
Tactics: Onboarding redesign, copilot
Execution: 3 squads, 12-week sprints
` ` `
```

### `org` — reporting tree

```
` ` `org
Root
- Child A
  - Grandchild
- Child B
` ` `
```

- Indent with **two spaces** per level. Tabs and 3-space indents will break the layout.
- The first line(s) at indent 0 are the roots — usually one root.
- Optional `-` or `*` bullets are accepted but not required.

**Example:**
```
` ` `org
CEO
- CTO
  - Eng Lead
  - Platform Lead
- COO
  - Sales
  - CS
- CFO
` ` `
```

### `cards` — feature / value-prop tiles

```
` ` `cards
Title | Body | Optional icon
` ` `
```

- 2–4 cards per row works best.
- Icon is one character (emoji or symbol like ⚡ ◆ ✦ ▲).
- Body is one short sentence.

**Example:**
```
` ` `cards
Speed | Cut time-to-value from 21 to 4 days | ⚡
Cost | 30% lower implementation cost | ◆
Trust | SOC2 + region-locked storage | ✦
` ` `
```

### `table` — comparison table

```
` ` `table
| Header 1 | Header 2 | Header 3 |
| Cell | Cell | Cell |
| Cell | Cell | Cell |
` ` `
```

- Use full markdown table syntax with leading/trailing pipes.
- A separator row (`|---|---|---|`) is **tolerated but optional** — the renderer treats the first row as the header.
- Best for plan/option comparisons, not raw data dumps.

**Example:**
```
` ` `table
| Tier | Seats | Storage | Price |
| Starter | 5 | 10 GB | $29 |
| Team | 25 | 100 GB | $99 |
| Enterprise | unlimited | 1 TB | Contact |
` ` `
```

## Authoring heuristics

The point of this DSL is **visual communication**, not exhaustive prose. Apply these:

- **One headline per section.** Each `##` heading should be the takeaway, not a category. *"We're losing mid-market customers"* beats *"Customer segmentation"*.
- **Pick one schema per section.** Stacking three diagrams under one heading dilutes the message.
- **Prefer schemas over bullet lists.** If the user gives you 4–6 metrics, render them as `kpi`, not as `- Revenue: $4.2M`.
- **Order matters.** A typical exec deck flows: summary → metrics → why/how → priorities (matrix) → process/flow → next steps. Mirror that unless the user asks otherwise.
- **Keep paragraphs short.** 2–4 sentences. Anything longer signals you should be using a schema.
- **Numbers are formatted.** `$4.2M` not `4200000`. The renderer doesn't reformat.

## Common mistakes to avoid

- **Wrong fence name.** It's `kpi` not `metrics`, `flow` not `flowchart`, `matrix` not `2x2`, `cards` not `tiles`. Unknown fences render as plain code blocks — easy to spot.
- **Pipe count mismatch.** `matrix` data lines need exactly **3** fields (`label | x | y`). `kpi` accepts 2 or 3 (`label | value [| delta]`). `cards` accepts 2 or 3 (`title | body [| icon]`).
- **Indentation in `org`.** Two spaces per level. Tabs and three-space indents break the tree.
- **Mixing `>` and code fences.** Callouts can't contain fenced blocks. Keep them prose-only.
- **Frontmatter quoting.** Quote the accent hex: `accent: "#7A1F2B"` (without quotes some YAML parsers treat `#` as a comment). The shipped parser is lenient but be safe.
- **Forgetting `theme:` is nested.** It's:
  ```
  theme:
    accent: "#7A1F2B"
  ```
  Not `theme.accent: "#7A1F2B"`.

## Worked example

A complete deck for a strategy review is in `examples/strategy-review.dsl.md`. Read it to see the full set of blocks composed in a realistic document.

## Renderer compatibility

The renderer lives in `index.html` (sibling to this skill folder). When in doubt about a block, open `index.html` in a browser and verify your output renders. The parser is forgiving but specific: if a block doesn't appear, check the fence name and field count first, then YAML quoting, then indentation in `org`.
