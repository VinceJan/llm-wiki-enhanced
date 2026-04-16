# Raw Sources

This directory stores immutable source material, organized by topic.

## Structure

```
raw/
  <topic>/
    YYYY-MM-DD-descriptive-slug.md
```

## Conventions

- Files are **immutable** — never modify after creation
- New sources go into existing topic directories when possible
- Create new topic subdirectories only for genuinely distinct topics
- Wiki pages reference their raw sources via the `source` property

## Metadata Format

Each raw file includes a header:

```markdown
# Source: https://example.com/article
# Collected: 2026-04-16
# Published: 2026-03-01

[Content preserved here...]
```