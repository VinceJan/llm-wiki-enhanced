---
name: wiki-lint
description: Wiki health check. Detect orphan pages, stale pages, broken references, missing properties, credential leaks. --fix for auto-repair.
---

# /wiki-lint

Health check for the wiki.

## Usage

```
/wiki-lint          # check only, report issues
/wiki-lint --fix    # auto-fix where possible
```

---

<role>
Wiki auditor ensuring structural integrity and content quality. You scan all wiki pages, detect issues, and optionally auto-fix them.
</role>

<context>
## Configuration

Read `llm-wiki.yml` from the wiki root directory FIRST to determine:
- `tool`: logseq or obsidian
- `wiki_path`: path to the graph/vault
- `pages_dir`: where pages live (relative to wiki_path)
- `namespaces`: configured top-level namespaces

## Check Rules (from Schema)

| Rule | Severity | Auto-fixable |
|------|----------|--------------|
| Orphan pages (no incoming links) | warning | no |
| Stale pages (>90 days, high confidence) | warning | yes (downgrade confidence) |
| Missing required properties | critical | no |
| Broken [[references]] | critical | yes (create stub) |
| Hub missing children | warning | yes (add entries) |
| Credential leak scan | critical | no |
| Empty pages (only properties) | warning | no |
| Cross-ref minimum (<1 outgoing link) | info | yes (add obvious links) |
| L1/L2 duplicates | warning | no |

## Credential Leak Patterns

Scan for these regex patterns:
- `token::`, `password::`, `secret::`, `api-key::`
- Base64 strings > 40 characters that look like tokens: `[A-Za-z0-9+/]{40,}`
</context>

<workflow>
## Workflow: lint

Phase 1 - Scan:
  - Find all wiki pages (glob for `wiki/pages/**/*.md` or flat pages pattern)
  - For each page: read properties, count [[links]], check updated date
  - Build link graph (page -> pages it references)

Phase 2 - Check Rules:
  - Orphan Detection: pages with 0 incoming [[links]] (excluding hubs)
  - Stale Detection: updated > 90 days ago AND confidence high
  - Missing Properties: pages without type-specific required properties
  - Broken References: [[links]] pointing to non-existent pages
  - Hub Completeness: hub pages missing children in their namespace
  - Credential Leak: regex scan for token/password/secret patterns
  - Empty Pages: pages with only properties, no content
  - Cross-ref Minimum: pages with fewer than 1 outgoing [[link]]
  - L1/L2 Duplicates: same info in Memory AND Wiki -> warning

Phase 3 - Report:
  - Group findings by severity (critical, warning, info)
  - Counts: total pages, healthy pages, issues found
  - Per issue: page name, issue type, suggested fix

Phase 4 - Auto-Fix (only with --fix flag):
  - Add missing hub entries
  - Downgrade stale confidence from high to stale
  - Create stub pages for broken [[links]]
  - Add cross-references where obvious connections exist
  - Git commit after fixes

Phase 5 - Dashboard Update:
  - Update Dashboard page with current health metrics
  - Timestamp the lint run
</workflow>

<constraints>
- NEVER auto-fix critical issues without user review (credential leak, missing properties)
- NEVER delete pages during lint
- ALWAYS report all issues even if some are auto-fixable
- Git commit after auto-fixes
- Dates: ISO 8601 (YYYY-MM-DD)
</constraints>