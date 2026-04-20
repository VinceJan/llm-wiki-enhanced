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
- `raw_path`: path to the raw source directory
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

### Logseq Mode
- Every line starts with `- ` (outliner format)
- Properties: `property:: value` syntax (NO YAML frontmatter)
- File naming: Triple-underscore (`Wiki___Tech___Strapi.md`)

### Obsidian Mode
- Standard flat markdown
- Properties: YAML frontmatter (`---\ntype: knowledge\n---`)
- File naming: Folder hierarchy (`Wiki/Tech/Strapi.md`)

### Both
- Cross-references: `[[Wiki/Namespace/Page]]` syntax
- Dates: ISO 8601 (YYYY-MM-DD)
</context>

<workflow>
## Workflow: import

Phase 0 - Source Preservation (raw/):
  - For each source .md file, copy to `raw/imported/YYYY-MM-DD-original-name.md`
  - These raw copies are IMMUTABLE — preserve original text for re-compilation
  - If raw/ directory doesn't exist, create it

Phase 1 - Inventory:
  - Scan source directory for `.md` files (glob `**/*.md`)
  - Skip binary files, empty files (< 50 words), and non-markdown
  - Classify each file by content type using rules above
  - Identify namespace mapping
  - If no .md files found: tell the user the directory has no markdown files and stop
  - Report: total files found, classification breakdown, skipped files

Phase 2 - User Confirmation:
  - Present classification summary to user
  - Show which files will be created vs skipped (if target already exists)
  - For files where target already exists: ask user to skip, append, or overwrite
  - Wait for user confirmation before proceeding

Phase 3 - Conversion:
  - Convert to wiki format (Logseq outliner or Obsidian flat, per config)
  - Add required properties:
    - `type`: entity/project/knowledge/feedback/hub
    - `created`: original file date or today
    - `updated`: today
    - `source`: relative path to raw/imported/ copy
    - `confidence`: high (imported from user's own notes)
  - Convert internal `[[links]]` to `[[Wiki/Namespace/Page]]` cross-references where possible
  - Preserve original content — do not rewrite or summarize

Phase 4 - Create Pages:
  - Create namespace hub pages first (if missing)
  - Create content pages under appropriate namespace directory
  - Link back to raw source: `../../raw/imported/YYYY-MM-DD-original-name.md`
  - Update all hub pages with children links

Phase 5 - Verification:
  - Lightweight lint on imported pages (broken refs, missing properties)
  - Report: pages imported, skipped, issues found
  - Git commit with import summary

Error Handling:
  - If a file fails to read (permissions, encoding): skip it, report the error, continue
  - If classification is ambiguous: flag for user review, don't guess
  - One file failure should not abort the entire import
</workflow>

<constraints>
- NEVER modify files in the source directory
- NEVER overwrite existing wiki pages without user confirmation
- ALWAYS save source to raw/ before converting
- ALWAYS classify before converting
- ALWAYS add required properties
- User must confirm classification before proceeding
- Git commit after import
- Dates: ISO 8601 (YYYY-MM-DD)
</constraints>
