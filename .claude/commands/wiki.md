---
name: wiki
description: LLM Wiki knowledge management entry point. Sub-commands: wiki-ingest, wiki-query, wiki-lint, wiki-status, wiki-import
---

# /wiki - LLM Wiki Entry Point

Persistent knowledge management powered by Claude Code. Maintains a structured wiki with the **L1/L2 + Raw** three-layer architecture.

---

## Recommended: Use Specific Sub-Commands

| Command | Purpose |
|---------|---------|
| `/wiki-ingest <source>` | Process source → save to raw/ → compile to wiki |
| `/wiki-query <question>` | Search wiki → synthesize answer (with citations) |
| `/wiki-lint [--fix]` | Health check → detect issues → auto-fix |
| `/wiki-status` | Metrics overview → page stats → activity report |
| `/wiki-import <dir>` | Batch import existing Markdown notes |

---

## Architecture: L1/L2 + Raw

| Layer | Purpose | Location | Behavior |
|-------|---------|----------|----------|
| **Raw** | Immutable source material | `raw/<topic>/` | Never modified after creation |
| **L1** | Auto-loaded rules/credentials | Global memory directory | Loaded every session |
| **L2** | On-demand knowledge | `wiki/pages/` | Accessed via /wiki-query |

**Routing Rule**: "Would a mistake without this knowledge be dangerous/embarrassing? → L1. Merely inconvenient? → L2."

---

## Configuration

`llm-wiki.yml` defines:
- `tool`: obsidian or logseq
- `wiki_path`: path to wiki directory
- `raw_path`: path to raw directory
- `namespaces`: configured namespaces

---

## Tool-Specific Format (Obsidian Mode)

- Properties: YAML frontmatter (`---\ntype: knowledge\n---`)
- File naming: Folder hierarchy (`Wiki/Tech/Page.md`)
- Cross-references: `[[Wiki/Namespace/Page]]` syntax
- Dates: ISO 8601 (YYYY-MM-DD)

---

## Constraints Summary

- Credentials must stay in L1, never in wiki (git-tracked!)
- Wiki content: append only, never overwrite
- Raw files: immutable after creation
- Read `llm-wiki.yml` before every operation
- Git commit after structural changes

---

**Start with `/wiki-ingest https://...` to begin.**