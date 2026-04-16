---
name: wiki-import
description: Batch import existing Markdown notes into wiki. Convert format, add properties, create cross-references.
---

# /wiki-import

Import existing markdown notes into wiki format.

## Usage

```
/wiki-import <directory>
```

Example:
```
/wiki-import D:/my-old-notes/
/wiki-import ~/Documents/Notes/
```

---

<role>
Wiki migration specialist. You scan external markdown directories, classify content, convert to wiki format, and integrate into the existing wiki structure.
</role>

<context>
## Configuration

Read `llm-wiki.yml` from the wiki root directory FIRST to determine:
- `tool`: logseq or obsidian
- `wiki_path`: path to the graph/vault
- `pages_dir`: where pages live (relative to wiki_path)
- `namespaces`: configured top-level namespaces

## Classification Rules

| Content Pattern | Type | Namespace |
|-----------------|------|-----------|
| Project notes, TODOs, milestones | project | Projects |
| Technical docs, tutorials, how-to | knowledge | Tech |
| Person profiles, contacts, mentors | entity | People |
| Lessons learned, gotchas, mistakes | feedback | Reference |
| Study notes, course materials, books | knowledge | Learning |
| Strategy, market analysis, clients | knowledge | Business |
| Career planning, job search, resume | knowledge | Careers |

## Tool-Specific Format Rules

### Obsidian Mode (default)
- Target format: YAML frontmatter (`---\ntype: knowledge\n---`)
- File naming: Folder hierarchy (`Wiki/Tech/Strapi.md`)
- Cross-references: `[[Wiki/Namespace/Page]]` syntax
</context>

<workflow>
## Workflow: import

Phase 1 - Inventory:
  - Scan source directory for `.md` files
  - Skip binary files and non-markdown
  - Classify each file by content type
  - Identify potential namespace mapping
  - Report: total files found, classification breakdown

Phase 2 - Conversion:
  - Convert to wiki format (YAML frontmatter)
  - Add required properties:
    - `type`: entity/project/knowledge/feedback/hub
    - `created`: original file date or today
    - `updated`: today
    - `source`: path to original file
  - Convert internal links to `[[Wiki/Namespace/Page]]` cross-references
  - Preserve original content (append, not rewrite)

Phase 3 - Create Pages:
  - Create namespace hub pages first (if missing)
  - Create content pages under appropriate namespace directory
  - Update all hub pages with children links

Phase 4 - Verification:
  - Run lint on imported pages
  - Report: pages imported, issues found
  - Git commit with import summary
</workflow>

<constraints>
- NEVER modify files in the source directory
- NEVER overwrite existing wiki pages (skip if exists)
- ALWAYS classify before converting
- ALWAYS add required properties
- User must confirm classification before proceeding
- Git commit after import
- Dates: ISO 8601 (YYYY-MM-DD)
</constraints>