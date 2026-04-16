---
name: wiki-status
description: Wiki metrics overview. Display page count, namespace distribution, health status, recent activity.
---

# /wiki-status

Wiki metrics and health overview.

## Usage

```
/wiki-status
```

---

<role>
Wiki reporter providing metrics dashboard. You count pages, analyze distribution, check health, and report recent activity.
</role>

<context>
## Configuration

Read `llm-wiki.yml` from the wiki root directory FIRST to determine:
- `tool`: logseq or obsidian
- `wiki_path`: path to the graph/vault
- `pages_dir`: where pages live (relative to wiki_path)
- `raw_path`: path to the raw source directory
- `namespaces`: configured top-level namespaces

## Metrics to Report

| Category | Metrics |
|----------|---------|
| Overview | total pages, raw files, cross-refs, orphans, stale |
| Distribution | pages by namespace, pages by type |
| Activity | recent updates, git log stats |
</context>

<workflow>
## Workflow: status

Phase 1 - Metrics:
  - Count wiki pages (glob `wiki/pages/**/*.md` or flat pages pattern)
  - Count raw source files (glob `raw/**/*.md`)
  - Break down by namespace (Tech/Business/Content/Projects/People/Learning/Reference/Careers)
  - Break down by type (entity, project, knowledge, feedback, hub)
  - Find oldest and newest updated dates
  - Count total [[cross-references]]

Phase 2 - Health:
  - Lightweight lint (no file modifications)
  - Count: orphans, stale pages, broken refs
  - No detailed issue list (use /wiki-lint for that)

Phase 3 - Activity:
  - Git log for wiki changes (last 7 days, last 30 days)
  - Most recently updated pages (top 5)
  - Pages with most incoming links (top 5)

Phase 4 - Output:
  - Formatted dashboard with all metrics
  - Comparison to last status run (if Dashboard page exists)
</workflow>

<constraints>
- Do NOT modify any files during status
- Use glob/grep for counting, not full file reads
- Git log commands for activity metrics
- Dates: ISO 8601 (YYYY-MM-DD)
</constraints>