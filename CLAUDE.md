# Wiki Schema

If `PURPOSE.md` exists in this directory, read it first — it provides the research goal and overrides judgment calls about what deserves its own page.

Read this file at the start of every session before touching any wiki pages.

---

## Origin

This system implements Andrej Karpathy's LLM Wiki pattern (https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

Core idea: instead of RAG (retrieve-and-generate), the LLM acts as a **compiler** — reading raw sources and writing/maintaining a structured Markdown wiki. The wiki is a **persistent, compounding artifact**: cross-references already exist, knowledge accumulates, and the LLM handles the maintenance burden that causes human-maintained wikis to be abandoned.

> "The wiki is a persistent, compounding artifact. The cross-references are already there." — Karpathy

---

## Directory Structure

```
<topic>/
├── raw/              # Immutable sources — never edited after adding
│   ├── articles/     # Web articles, blog posts
│   ├── papers/       # Academic papers, PDFs
│   └── misc/         # Other notes, transcripts
├── wiki/             # Claude-maintained structured pages
│   ├── index.md      # Content-oriented catalogue by category
│   ├── log.md        # Append-only record: ingests + queries + maintenance
│   ├── concepts/     # Abstract ideas, frameworks, methods
│   ├── entities/     # People, tools, organizations, datasets
│   ├── sources/      # One summary page per raw document
│   ├── comparisons/  # Side-by-side analysis pages
│   └── overview/     # Synthesis pages spanning multiple sources
├── Attachments/      # Images and resources (Obsidian standard)
├── outputs/          # Reports, exports, generated artifacts
└── CLAUDE.md         # This file
```

---

## Page Types

| Type | Directory | Purpose |
|------|-----------|---------|
| `source-summary` | `wiki/sources/` | One page per raw document ingested |
| `concept` | `wiki/concepts/` | Abstract idea, framework, or method |
| `entity` | `wiki/entities/` | Person, tool, organization, or dataset |
| `comparison` | `wiki/comparisons/` | Side-by-side analysis of 2+ items |
| `overview` | `wiki/overview/` | Synthesis spanning multiple sources/concepts |

### Page Creation Decision Rules

Before creating a new wiki page, apply these rules:

- **3+ sources reference it** → create a `concept` or `entity` page
- **Appears in only 1 source** → don't create a page; mention it inline in the `source-summary`
- **Insight spans 5+ pages** → create an `overview` page
- **concept vs. entity**: concept = abstract framework or method (e.g., "selectorate theory", "KHB method"); entity = concrete thing that exists (e.g., a person, organization, dataset, country)
- **When in doubt**: check PURPOSE.md — if it's not central to the research question, don't create a page

---

## Frontmatter Rules

Every page in `wiki/` must include this YAML frontmatter:

```yaml
---
title: Page Title
type: concept | entity | source-summary | comparison | overview
sources:
  - raw/papers/filename.pdf
related:
  - "[[related-page-name]]"
created: YYYY-MM-DD
updated: YYYY-MM-DD
confidence: high | medium | low
---
```

- `sources`: trace every claim back to a file in `raw/`
- `related`: wikilinks using `[[double brackets]]`
- `confidence`: how well-established the claim is
- Filenames: kebab-case, matching the concept name

---

## Source-Summary Template (Academic Papers)

When ingesting an academic paper, use this section structure:

```markdown
## Research Question
One sentence: what question does this paper answer?

## Main Theory (主要理論)
The theoretical framework or model the paper builds on or proposes.

## Research Hypothesis (研究假設)
The specific hypotheses tested, if stated explicitly.

## Research Design (研究方法)

**Data Sources (資料來源)**: survey, election results, archival records, etc.

**Variables (變數設定)**:
- Dependent variable (DV): ...
- Independent variable(s) (IV): ...
- Control variables: ...

**Method**: regression / case study / comparative / experiment / etc.

## Main Arguments (主要論點)
Each argument on its own line with page reference:

- **Argument name** (p. XX): description
- **Argument name** (pp. XX–YY): description

## Key Findings (研究發現)
Bullet list of empirical results, each traceable to the paper.

## Geographic Focus
Country/region and time period.

## Limitations / Notes
Any caveats, contested claims, or things to flag for future reading.
```

**Page number format**: `(p. 12)` for single page, `(pp. 12–15)` for range.  
Sections can be omitted if not applicable (e.g., theoretical pieces may lack DV/IV).

---

## The Three Operations

### Ingest
When user adds files to `raw/` and says "ingest":
1. Read this CLAUDE.md to confirm schema
2. For each new file:
   a. Read the source thoroughly
   b. Create a summary page in `wiki/sources/` (type: `source-summary`)
   c. Identify 5–15 concepts and entities
   d. Create or update wiki pages for each — update existing pages with new info, create new ones with cross-links
   e. Update `wiki/index.md`
   f. Append to `wiki/log.md`: date, source filename, pages created/updated, 2–3 key takeaways
3. Report: "Ingested X sources. Created N pages, updated M pages."

### Query
When user asks a question about their knowledge base:
1. Read `wiki/index.md` to find relevant pages
2. Load those pages and synthesize an answer
3. Always cite with `[[wikilinks]]` — never answer from memory if the wiki has the answer
4. If the answer reveals a valuable synthesis not yet in the wiki: **proactively file it** as a new `wiki/overview/` page (tell the user what you saved)
5. Append to `wiki/log.md`: date, question asked, pages consulted, any new page created

**When more than 10 pages match:**
- Priority order: `overview` > `concept` / `entity` > `source-summary`
- Read all overview and concept/entity pages that match
- For source-summaries: read at most 5 (highest confidence, most recently updated first)
- Tell the user: "N additional source-summary pages were not read — narrow the question to go deeper"
- Never try to load the entire wiki into context

### Lint
Scan for and report:
- **Contradictions**: two pages making conflicting claims
- **Orphan pages**: no incoming `[[wikilinks]]` from other pages
- **Missing pages**: concepts referenced with `[[wikilinks]]` but no file exists
- **Stale claims**: pages whose sources may have been superseded
- **Research gaps**: topics mentioned across pages but never given their own page

Output a categorized report with severity (HIGH/MEDIUM/LOW) and fix priority.
Append summary to `wiki/log.md` after running.

---

## Working Principles

**Claude writes the wiki, the human curates the inputs.** Deciding what to read and what to ask is the human's job. Summarizing, cross-referencing, and maintaining consistency is Claude's job.

**Every claim traces to `raw/`.** The `raw/` directory is immutable — never edit files there. Add new sources to correct errors; don't rewrite history.

**Git is your undo button.** `git diff` shows what changed; `git checkout` reverts a bad ingest.

**log.md is append-only.** Never edit past entries — only append new ones.

**Lean pages beat bloated pages.** Each page answers: "What is this? Why does it matter? How does it relate to X?" — not an encyclopedia entry. Split pages that exceed ~300 lines.
