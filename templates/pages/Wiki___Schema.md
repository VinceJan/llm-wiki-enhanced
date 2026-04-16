---
type: hub
namespace: Wiki
created: 2026-04-16
updated: 2026-04-16
---

# Wiki Schema

This page defines the conventions for all wiki pages in this knowledge base.

## Page Types

### Entity
- Represents: people, tools, services, technologies
- Required properties: `type: entity`, `entity-type`, `status`, `source`
- Optional: `confidence`, `tags`

### Project
- Represents: active or completed projects with timelines
- Required properties: `type: project`, `status`, `started`
- Optional: `completed`, `confidence`, `tags`

### Knowledge
- Represents: synthesized knowledge (most common type)
- Required properties: `type: knowledge`, `domain`, `confidence`
- Optional: `tags`

### Feedback
- Represents: lessons learned, gotchas, operational rules
- Required properties: `type: feedback`, `severity`, `verified`, `applies-to`
- Optional: `tags`

### Hub
- Represents: namespace index pages (structural, not content)
- Required properties: `type: hub`, `namespace`

## Naming Convention

- File names use Title-Case with hyphens: `Machine-Learning-Basics.md`
- Files live under `pages/<Namespace>/` (e.g., `pages/Tech/Docker-Basics.md`)
- Cross-references use `[[Wiki/Namespace/Page]]` syntax

## L1/L2 Boundary

- **L1 (Memory)**: Rules, gotchas, identity, credentials — things the LLM must know EVERY session
- **L2 (Wiki)**: Projects, workflows, research, deep knowledge — queried on demand
- Routing rule: "Would a mistake without this knowledge be dangerous/embarrassing? → L1. Merely inconvenient? → L2."
- Credentials MUST stay in L1 (`.claude/memory/` is git-ignored; wiki is git-tracked)
- Same info in both? → Lint warning (duplication risk)

## Source Tracking (Raw)

- All ingested sources are saved to `raw/<topic>/YYYY-MM-DD-descriptive-slug.md`
- Raw files are immutable — never modify after creation
- Wiki pages must reference their raw sources via the `source` property