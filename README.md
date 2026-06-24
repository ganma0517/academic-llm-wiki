# Academic LLM Wiki

A Karpathy-style LLM knowledge base for academic research — where Claude acts as a **compiler**, not a retriever. No RAG, no vector DB, no complex setup. Just structured Markdown files that Claude reads and maintains.

> "Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."  
> — Andrej Karpathy ([original gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f))

---

## Why This Exists

Traditional note-taking systems fail because the human has to do all the maintenance: tagging, cross-referencing, deduplication, and summarizing. This wiki flips that: **you decide what to read, Claude handles the rest**.

Every paper you add gets a structured summary with theory, hypotheses, methods, variables, and page-cited arguments — then Claude cross-links related concepts automatically. The knowledge compounds.

---

## Directory Structure

```
your-research-topic/
├── raw/                    # Immutable sources — never edit files here
│   ├── papers/             # Academic PDFs
│   ├── articles/           # Web articles, blog posts
│   └── misc/               # Notes, transcripts, other
├── wiki/                   # Claude-maintained structured pages
│   ├── index.md            # Master catalogue — always up to date
│   ├── log.md              # Append-only record of all operations
│   ├── sources/            # One summary page per paper/article
│   ├── concepts/           # Abstract ideas, frameworks, methods
│   ├── entities/           # People, organizations, datasets, tools
│   ├── comparisons/        # Side-by-side analysis of 2+ items
│   └── overview/           # Synthesis pages spanning multiple sources
├── Attachments/            # Images and resources (Obsidian standard)
├── outputs/                # Reports, essays, exports — things you generate
└── CLAUDE.md               # Wiki schema — Claude reads this first every session
```

**Rule of thumb**: `raw/` is what you add. `wiki/` is what Claude builds and maintains.

---

## Quick Start

### 1. Clone and rename

```bash
git clone https://github.com/YOUR_USERNAME/academic-llm-wiki.git my-research
cd my-research
```

### 2. Add your PDFs

Drop papers into `raw/papers/`. Filenames don't matter much; use something readable:

```
raw/papers/Bowler_2004_recall-representation.pdf
raw/papers/Mansbridge_2003_rethinking-representation.pdf
```

### 3. Tell Claude to ingest

Open the folder in Claude Code (or Cursor with Claude) and say:

```
Ingest the papers in raw/papers/
```

Claude will read `CLAUDE.md` first, then process each paper into a structured wiki page.

### 4. Query your knowledge base

```
What do these papers say about institutional design and recall frequency?
```

Claude reads `wiki/index.md`, loads the relevant pages, and synthesizes a cited answer. If the answer reveals a valuable new synthesis, it saves it automatically to `wiki/overview/`.

### 5. Lint periodically

```
Lint the wiki
```

Claude scans for contradictions, orphan pages, missing cross-links, and research gaps.

---

## What a Source Summary Looks Like

Every ingested paper gets a page in `wiki/sources/` with this structure:

```markdown
---
title: Recall and Representation
type: source-summary
sources: "Bowler (2004). Recall and representation. Representation, 40(3), 200–212."
related:
  - "[[recall-mechanisms]]"
  - "[[electoral-representation]]"
created: 2026-06-24
updated: 2026-06-24
confidence: high
---

## Research Question
## Main Theory (主要理論)
## Research Hypothesis (研究假設)
## Research Design (研究方法)
  - Data Sources
  - Variables (DV / IV / Controls)
  - Method
## Main Arguments (主要論點)
  - **Argument name** (p. XX): description
## Key Findings (研究發現)
## Geographic Focus
## Limitations / Notes
```

Page citations (`p. 12`, `pp. 12–15`) are extracted directly from the PDF so you can trace every claim.

---

## The Three Operations

| Command | What Claude does |
|---------|-----------------|
| `Ingest [file or folder]` | Reads source → creates/updates wiki pages → updates index + log |
| `Query: [your question]` | Reads index → loads relevant pages → synthesizes cited answer |
| `Lint the wiki` | Scans for contradictions, orphans, gaps → writes report → appends to log |

---

## Obsidian Integration

This repo is Obsidian-compatible out of the box. Open the root folder as a vault:

1. Obsidian → Open folder as vault → select this directory
2. `[[wikilinks]]` will resolve automatically across all wiki pages
3. Graph view shows how concepts connect
4. `Attachments/` follows Obsidian's default attachment folder convention

---

## Scaling

- **Up to ~200 papers**: works perfectly as-is
- **200–500 papers**: split `wiki/index.md` by sub-topic sections
- **500+ papers**: consider splitting into multiple wikis by research area

---

## Workflow Tips

- Add papers in batches — ingesting 5 at once is as easy as 1
- Use `wiki/overview/` pages for literature reviews that span many sources
- Use `wiki/comparisons/` when you want a structured side-by-side (e.g., two competing theoretical frameworks)
- `outputs/` is for things you export from the wiki (draft sections, bibliographies) — not wiki pages themselves
- `git diff` after every ingest so you can see exactly what changed; `git checkout` to revert a bad ingest

---

## Credits

Based on [Andrej Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).  
Template by [beck](https://github.com/ganma0517).
