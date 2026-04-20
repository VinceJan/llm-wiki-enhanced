# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[中文](docs/langs/README.zh-CN.md) | [Français](docs/langs/README.fr.md) | [Português](docs/langs/README.pt.md) | [日本語](docs/langs/README.ja.md) | [한국어](docs/langs/README.ko.md)**

---

> **The only LLM Wiki implementation that preserves your original sources.**

Unlike other implementations, this framework keeps a complete archive of everything you've ever ingested in an **immutable `raw/` directory** — enabling full source traceability and future re-compilation.

## What is LLM Wiki?

LLM Wiki is a knowledge management concept introduced by [Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): instead of traditional RAG where the LLM re-discovers knowledge on every query, the LLM incrementally builds and maintains a persistent, interlinked wiki of Markdown files.

**The core idea**: "The LLM writes and maintains the wiki; the human reads and asks questions."

## Key Features

| Feature | This Project | Other Implementations |
|---------|-------------|----------------------|
| **Raw Source Archive** | ✅ Immutable `raw/` directory | ❌ Not preserved |
| **L1/L2 Cache Architecture** | ✅ Session-loaded rules + on-demand wiki | ⚠️ Varies |
| **Sub-Commands** | ✅ 6 dedicated commands | ⚠️ Single command |
| **Health Check** | ✅ 9 rules with `--fix` | ⚠️ Basic |
| **Credential Safety** | ✅ Lint scans for leaks | ❌ None |
| **Tool Support** | ✅ Obsidian + Logseq | ✅ Same |

## Architecture

```
┌─────────────────────────────────────────────────┐
│                    INPUT                         │
│         (URL / File / Text / Directory)          │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                  RAW (Immutable)                 │
│         raw/<topic>/YYYY-MM-DD-slug.md           │
│              "Source Archive"                    │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                WIKI (L2 - On-Demand)             │
│            wiki/pages/<Namespace>/               │
│         [[cross-references]] between pages       │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│               MEMORY (L1 - Auto-Loaded)          │
│           ~/.claude/projects/<project>/          │
│       Rules, Gotchas, Credentials (git-ignored)  │
└─────────────────────────────────────────────────┘
```

### Three-Layer Model

| Layer | Purpose | Behavior |
|-------|---------|----------|
| **Raw** | Immutable source archive | Never modified after creation |
| **L1** | Rules, credentials, preferences | Auto-loaded every Claude session |
| **L2** | Structured knowledge pages | Queried on demand |

**Routing Rule**: "Would a mistake without this knowledge be dangerous/embarrassing? → L1. Merely inconvenient? → L2."

## Commands

| Command | Purpose |
|---------|---------|
| `/wiki-ingest <source>` | Process source → save to raw → compile to wiki |
| `/wiki-query <question>` | Search wiki → synthesize answer with citations |
| `/wiki-lint [--fix]` | Health check: 9 rules, auto-fix where possible |
| `/wiki-status` | Metrics: page counts, health, recent activity |
| `/wiki-import <dir>` | Batch import existing Markdown notes |

## Installation

### Option 1: Setup Script (Recommended)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

The interactive script will:
- Choose tool (Obsidian or Logseq)
- Set wiki and raw paths
- Configure namespaces
- Create initial pages
- Install commands to your project

> **Windows users**: Run `setup.sh` from Git Bash, WSL, or Cygwin. Alternatively, use Option 2 (Manual Installation).

### Option 2: Manual Installation

Copy `.claude/commands/` to your project:

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

Then create `llm-wiki.yml` in your wiki directory (see `config.example.yml`).

### Option 3: Use in Existing Wiki

If you already have an Obsidian vault or Logseq graph:

1. Copy `.claude/commands/` to your vault's `.claude/commands/`
2. Create `llm-wiki.yml` in your vault
3. Create `raw/` directory alongside your wiki

## Quick Start

1. In Claude Code, navigate to your wiki directory
2. Run `/wiki-status` to verify installation
3. Try `/wiki-ingest https://example.com/article`
4. Check the created pages in your wiki

## Example Workflow

```
/wiki-ingest https://arxiv.org/abs/1706.03762

→ Raw:      raw/ai/2026-04-16-attention-is-all-you-need.md
→ Wiki:     wiki/pages/Tech/Transformer-Architecture.md
→ Cross-ref: [[Tech/Attention-Mechanism]], [[Learning/Deep-Learning]]

/wiki-query "what is transformer architecture?"

→ Answer synthesized from wiki pages
→ Sources: [[Tech/Transformer-Architecture]], [[Tech/Attention-Mechanism]]

/wiki-status

→ Dashboard showing: 5 pages, 1 raw file, 0 issues
```

## Configuration

```yaml
# llm-wiki.yml
tool: obsidian
wiki_path: ./wiki
pages_dir: pages
raw_path: ./raw
memory_path: ~/.claude/projects/my-wiki/memory/

namespaces:
  - Tech
  - Business
  - Content
  - Projects
  - People
  - Learning
  - Reference
  - Careers
```

## FAQ

### Why preserve raw sources?

- **Traceability**: Know exactly where each knowledge came from
- **Re-compilation**: Update wiki structure without losing originals
- **Conflict resolution**: When sources disagree, see original context
- **Audit**: Review what was actually said vs. what was interpreted

### Can I modify raw files?

**No.** Raw files are immutable by design. If you need corrections, update the corresponding wiki page.

### What file types are supported?

- URLs (web pages via WebFetch)
- Local files: PDF, Markdown, TXT
- Direct text input

### How do I handle videos?

Claude Code cannot extract video transcripts directly. Use external tools to extract transcripts, then ingest the text file.

## Credits

- Original concept: [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2 architecture: [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Agent Skills format: [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

## License

[MIT](LICENSE) - Free to use, modify, and distribute.

---

**Start building your persistent, source-traceable knowledge base today.**