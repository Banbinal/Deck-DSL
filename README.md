# Deck DSL

A standalone editor for writing professionally-styled visual one-pagers. Source on the left, rendered HTML/CSS/SVG document on the right. Single HTML file, no build step, no backend.

## What it does

Write a markdown-extended document with embedded visual blocks — KPI tiles, timelines, 2×2 matrices, decision flows, sequence diagrams, funnels, pyramids, org charts, cards, comparison tables — and see it render in real time as a consulting-grade page. Print to PDF, export standalone HTML, share via a self-contained link, or have an LLM author the DSL for you with the **Generate with AI** button (BYOK Gemini).

## Toolbar features

- **Sample / Clear** — load the example deck or wipe the editor.
- **Share** — encode the deck into a JWT-formatted URL fragment and copy to clipboard. Optional expiration (1/7/30/90 days). No backend needed; see *Sharing decks* below.
- **Skill ↓** — download the Claude Code skill as a `.zip` (`dsl-deck.zip`). Extract into `~/.claude/skills/` to give Claude Code the DSL knowledge.
- **✨ Generate** — describe a deck in plain language; an LLM (your-own-key Google Gemini) produces complete Deck DSL using the skill spec as system prompt. Key + model preference are saved in localStorage; requests go directly browser → Google.

## Try it

[Open the editor](https://YOUR-USERNAME.github.io/dslexample/) and click **Sample** to load an example deck.

## Deploy to GitHub Pages

This whole repo is static — GitHub Pages serves it directly with no build configuration.

1. Push this repo to GitHub.
2. **Settings → Pages → Build and deployment**: set source to `Deploy from a branch`, branch `main` (root `/`).
3. Wait for the first deploy (~1 minute). Your editor is live at `https://YOUR-USERNAME.github.io/<repo-name>/`.

That's the entire setup. `index.html` is fully self-contained; nothing else is needed at runtime.

## Sharing decks

Click **Share** in the editor toolbar. A dialog generates a link containing the entire deck — gzip-compressed, base64url-encoded, wrapped in a JWT envelope, embedded in the URL fragment. Anyone with the link can view the deck without an account.

```
https://you.github.io/dslexample/#deck=eyJhbGciOiJub25lIiwidHlwIjoiREVDSyIsInYiOjF9.eyJ2IjoxLCJpYXQiOjE3MTQ5MDAwMDAsInRpdGxlIjoiUTQgU3RyYXRlZ3kiLCJ6IjoiZ3ppcCIsImRzbCI6Ii4uLiJ9.
```

Optional expiration: set the dialog's dropdown to 1, 7, 30, or 90 days. Expired links show a clear message; never-expires links live as long as the URL is in someone's history.

URLs typically come out at 1–5 KB for everyday decks. Anything past 30 KB risks getting truncated by chat apps.

### Token format

The token follows the JWT compact serialization (`header.payload.signature`) so future versions can adopt real signing without breaking old links.

```json
// header
{ "alg": "none", "typ": "DECK", "v": 1 }

// payload
{
  "v": 1,
  "iat": 1714900000,
  "exp": 1717492000,        // optional
  "title": "Q4 Strategy Review",
  "z": "gzip",              // or "none"
  "dsl": "<base64url(gzip(source))>"
}
```

**Important caveat — the signature is not cryptographic.** `alg: "none"` means no MAC or asymmetric signing is applied. Treat the link as a *capability URL*: whoever holds it can read the deck, and nothing prevents a recipient from modifying the payload and re-sharing a forged version. If you need real authorship guarantees later, swap `alg` to `ES256` and store an asymmetric keypair in IndexedDB via WebCrypto — the verifier code path is already structured for it.

### Privacy

The deck data lives in the URL **fragment** (`#...`), which browsers do not send to the server. GitHub's Pages servers therefore never see the deck content — they only serve the static page, and decoding happens entirely in the recipient's browser.

That said: anyone you forward the link to can read it, and link-shorteners or screenshot tools may capture it. Don't put genuinely sensitive content in a shared link.

## Files

| Path | Purpose |
|------|---------|
| `index.html` | The editor + renderer — single self-contained file |
| `skills/dsl-deck/SKILL.md` | A Claude Code skill teaching Claude to author Deck DSL |
| `skills/dsl-deck/examples/strategy-review.dsl.md` | Reference example deck |
| `README.md` | This file |

## The Claude Code skill

The `skills/dsl-deck/` folder is a self-contained skill that teaches Claude Code to write valid Deck DSL. To install:

```bash
# Project-scoped (only this repo):
mkdir -p .claude/skills && cp -r skills/dsl-deck .claude/skills/

# Or user-scoped (every project):
cp -r skills/dsl-deck ~/.claude/skills/
```

Then ask Claude things like *"draft a Q4 strategy review for ACME using the deck DSL"* — the skill triggers automatically on phrases like "strategy review", "exec summary", "MBR/QBR", "decision memo", "consulting-style document", etc.

## DSL quick reference

```markdown
---
title: My Document
subtitle: Tagline goes here
eyebrow: Confidential · Strategy
author: You
date: 2026-05-06
theme:
  accent: "#7A1F2B"
---

# Section heading

Prose with **bold** and *italic*. Use callouts for highlights:

> [!tip]
> Key insight
> One-line elaboration.

## Metrics

```kpi
Revenue | $4.2M | +12%
NPS | 62 | +8
```

## Priorities

```matrix
x: Effort | low | high
y: Impact | low | high
Quick win | low | high
Big bet | high | high
```
```

The full reference (every block type, fields, common mistakes) lives in `skills/dsl-deck/SKILL.md`.

## Browser support

- **Modern Chromium, Firefox 113+, Safari 16.4+** — full support including gzip compression of share links.
- **Older browsers** — share links fall back to uncompressed base64 (longer URLs, but functional).

## License

Do whatever you want with this. Attribution appreciated but not required.
