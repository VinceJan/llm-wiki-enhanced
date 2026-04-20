# Usage Guide

This guide helps you get started with llm-wiki-enhanced.

## Quick Reference

| Command | Purpose | Example |
|---------|---------|---------|
| `/wiki-ingest URL` | Crawl webpage | `/wiki-ingest https://blog.example.com/article` |
| `/wiki-ingest FILE` | Process local file | `/wiki-ingest D:/Docs/paper.pdf` |
| `/wiki-ingest TEXT` | Process plain text | `/wiki-ingest Today I learned...` |
| `/wiki-query QUESTION` | Search wiki | `/wiki-query What is RAG?` |
| `/wiki-lint` | Check issues | `/wiki-lint` |
| `/wiki-lint --fix` | Auto-fix issues | `/wiki-lint --fix` |
| `/wiki-status` | View metrics | `/wiki-status` |
| `/wiki-import DIR` | Batch import | `/wiki-import D:/old-notes/` |

## Input Types

### 1. Web URLs

```
/wiki-ingest https://example.com/article-title
```

Claude uses `WebFetch` to crawl the page content and saves it to `raw/web/YYYY-MM-DD-article-title.md`.

**Limitations**: Some sites may require login or have anti-crawl measures. For these, manually copy the content and use text input.

### 2. Local Files

```
/wiki-ingest D:/Documents/my-paper.pdf
/wiki-ingest E:/Projects/readme.md
```

Claude reads the file with `Read` tool and extracts the text content to `raw/<topic>/`. The original file remains unchanged.

**Supported formats**: PDF, Markdown (.md), Text (.txt)

**Unsupported**: DOCX, XLSX require pre-conversion to PDF or text.

### 3. Plain Text

```
/wiki-ingest Today I learned about a new concept: Transformers use self-attention...
```

Claude parses the text directly and saves it to `raw/misc/` or an appropriate topic directory.

## Directory Structure

```
your-wiki/
├── llm-wiki.yml           # Configuration
├── raw/                   # Immutable sources
│   ├── ai/
│   │   └── 2026-04-16-transformer-paper.md
│   └── web/
│       └── 2026-04-16-claude-code-blog.md
└── wiki/
    └── pages/
        ├── Wiki/
        │   ├── Schema.md
        │   ├── Hub.md
        │   └── Dashboard.md
        ├── Tech/
        │   ├── Hub.md
        │   └── Transformer-Architecture.md
        └── Projects/
            └── Hub.md
```

## Page Types

Each wiki page has YAML frontmatter:

### Knowledge (most common)

```yaml
---
type: knowledge
domain: deep-learning
confidence: high
created: 2026-04-16
updated: 2026-04-16
source: https://arxiv.org/abs/1706.03762
---

# Transformer Architecture

Content here...

## Related
[[Tech/Attention-Mechanism]]
```

### Entity

```yaml
---
type: entity
entity-type: tool
status: active
source: https://github.com/anthropics/claude-code
---
```

### Project

```yaml
---
type: project
status: active
started: 2026-04-01
---
```

### Feedback (lessons learned)

```yaml
---
type: feedback
severity: high
verified: true
applies-to: coding
---
```

## Workflow Example

```
# Day 1: Start your wiki
/wiki-status
→ Dashboard shows: 3 pages, 0 raw files, 0 issues

# Day 2: Ingest first source
/wiki-ingest https://arxiv.org/abs/1706.03762
→ Raw: raw/ai/2026-04-17-attention-is-all-you-need.md
→ Wiki: wiki/pages/Tech/Transformer-Architecture.md

# Day 3: Query your wiki
/wiki-query What is the transformer architecture?
→ Answer from [[Tech/Transformer-Architecture]]

# Day 7: Health check
/wiki-lint --fix
→ Fixes broken references, updates stale confidence

# Weekly: Review metrics
/wiki-status
→ 12 pages, 8 raw files, 42 cross-refs, 1 orphan
```

## Best Practices

### Regular Maintenance

Run `/wiki-lint --fix` weekly to keep wiki healthy.

### Namespace Organization

- **Tech**: Technical knowledge, tools, frameworks
- **Business**: Business intelligence, clients, strategy, market analysis
- **Content**: Content strategy, writing, media, publishing
- **Projects**: Active/completed projects
- **People**: Contacts, collaborators, mentors
- **Learning**: Courses, books, study notes
- **Reference**: Cheat sheets, gotchas, FAQs
- **Careers**: Career planning, job search, professional development

### Confidence Levels

- `high`: Verified, trusted source
- `medium`: Reasonable but needs verification
- `low`: Speculative or unverified

## FAQ

### How do I handle videos?

Claude cannot extract video transcripts. Use external tools (YouTube subtitles, Bilibili subtitle extractor) and ingest the transcript file.

### Can I delete raw files?

Raw files are **immutable by design**. If you must delete, do it manually, but this breaks source traceability.

### How do I update a wiki page?

Just run `/wiki-ingest` with a new source covering the same topic. The existing page will be **appended**, not overwritten.

### What if sources disagree?

The framework annotates contradictions within wiki pages. Both viewpoints are preserved with source attribution.