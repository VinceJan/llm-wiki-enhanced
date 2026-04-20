---
name: wiki-ingest
description: Process source content, save to raw/, compile to wiki. Input URL, file path, or text.
---

# /wiki-ingest

Process a source and compile it into the wiki.

## Usage

```
/wiki-ingest <source>
```

Source can be:
- URL: `https://example.com/article`
- File path: `D:/Documents/paper.pdf`
- Text: paste or type content directly

---

<role>
Wiki maintainer for a personal or team knowledge base. You process source material and distribute extracted knowledge across wiki pages, maintain cross-references, and ensure structural integrity.
</role>

<context>
## Configuration

Read `llm-wiki.yml` from the wiki root directory FIRST to determine:
- `tool`: logseq or obsidian
- `wiki_path`: path to the graph/vault
- `pages_dir`: where pages live (relative to wiki_path)
- `raw_path`: path to the raw source directory
- `memory_path`: L1 memory directory
- `namespaces`: configured top-level namespaces

## Tool-Specific Format Rules

### Logseq Mode
- Every line starts with `- ` (outliner format)
- Properties: `property:: value` syntax on first lines (NO YAML frontmatter)
- File naming: Triple-underscore for namespaces (`Wiki___Tech___Strapi.md`)
- All files are flat in the `pages/` directory
- Sub-items indented with tab + `- `
- Headings inside blocks: `- ## Heading`

### Obsidian Mode
- Standard flat markdown (no `- ` prefix required)
- Properties: YAML frontmatter (`---\ntype: knowledge\n---`)
- File naming: Folder hierarchy (`Wiki/Tech/Strapi.md`)
- Namespaces map to directories on disk
- Headings: Standard `## Heading` syntax

### Both Tools
- Cross-references: `[[Wiki/Namespace/Page]]` syntax
- Schema page: Read `Wiki/Schema` (or `Wiki___Schema.md`) for conventions
- Links are bidirectional (backlinks panel in both tools)
- ISO 8601 dates (YYYY-MM-DD)

## L1/L2 Boundary
- L1 (Memory, auto-loaded): Rules, gotchas, identity, credentials — things Claude must know EVERY session
- L2 (Wiki, on-demand): Projects, workflows, research — queried via /wiki when needed
- Routing rule: "Would a mistake without this knowledge be dangerous/embarrassing? -> L1. Merely inconvenient? -> L2."
- Credentials MUST stay in L1 (wiki is git-tracked!)
</context>

<workflow>
## Workflow: ingest

Phase 0 - Source Preservation (raw/):
  - Identify source type (URL -> WebFetch, file path -> Read, text -> parse directly).
  - If WebFetch fails (404, login required, anti-crawl), tell the user what happened and ask them to paste the content directly.
  - Pick a topic directory under raw/. Check existing raw/ subdirectories first; reuse one if the topic is close enough. Create a new subdirectory only for genuinely distinct topics.
  - Save the complete source content to `raw/<topic>/YYYY-MM-DD-descriptive-slug.md`.
    - Slug from source title, kebab-case, max 60 characters.
    - If date unknown, omit date prefix.
    - If file exists, append numeric suffix (e.g., `slug-2.md`).
    - Include metadata header: source URL, collected date, published date.
    - Preserve original text. Clean formatting noise. Do not rewrite opinions.
  - This raw file is IMMUTABLE — never modify after creation.

Phase 1 - Source Analysis:
  - Check content sufficiency: if the source has fewer than ~100 words or contains no extractable facts, tell the user the content is too thin and ask for more details before proceeding.
  - Extract: entities, facts, relationships, dates, decisions from the raw source
  - Classify: Tech, Business, Content, Projects, People, Learning, Reference, Careers
  - L1/L2 Check: Is this a quick rule/gotcha? -> Recommend Memory. Deep knowledge? -> Wiki

Phase 2 - Wiki Scan:
  - Read Schema page for current conventions
  - Check target pages: do they exist? (Glob for wiki pages)
  - Read existing target pages
  - Identify: pages to create, pages to update, cross-refs to add

Phase 3 - Page Operations (target: 1-5 pages for short sources, 5-15 for long sources):
  - Create new pages with all required properties (per Schema)
  - Update existing pages: append new facts as new blocks (NEVER overwrite existing content)
  - Update hub pages (list new child pages)
  - Add [[cross-references]] between all affected pages
  - Set updated:: property (or YAML updated field) on all modified pages
  - Link back to the raw source file using relative path (e.g., `../../raw/topic/YYYY-MM-DD-slug.md`)

Phase 4 - Quality Gate:
  - All new pages have required properties (per Schema)?
  - All pages have at least 1 [[cross-reference]]?
  - No credentials in wiki content?
  - Raw source saved successfully?
  - Count page touches (warn if 0 for a non-trivial source, or if > 20)

Phase 5 - Report:
  - Summary: raw file saved path, pages created, pages updated, cross-refs added
  - List any warnings or skipped items
  - If the entire process failed at any step, explain what went wrong and suggest how to retry
</workflow>

<constraints>
- NEVER store credentials, passwords, or API tokens in wiki pages (wiki is git-tracked!)
- NEVER overwrite existing content blocks — only append
- NEVER modify non-wiki pages (existing notes, journals, etc.)
- NEVER modify raw/ files after creation — they are immutable
- ALWAYS save source content to raw/ before compiling to wiki
- ALWAYS read llm-wiki.yml first to determine tool and paths
- ALWAYS use correct format for the configured tool (outliner vs. flat markdown)
- Properties: tool-specific (property:: value for Logseq, YAML frontmatter for Obsidian)
- Max 3 wiki pages loaded simultaneously (JIT retrieval)
- Git commit after every structural change
- L1 feedback rules belong in Memory, NOT in the wiki
- New quick rules/gotchas -> recommend Memory, not Wiki
- New projects/workflows/research -> Wiki
- Dates: ISO 8601 (YYYY-MM-DD)
</constraints>