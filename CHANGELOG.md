# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-04-16

### Added

- **Raw Directory Support**: Unlike other LLM Wiki implementations, this framework preserves original sources in an immutable `raw/<topic>/` directory
- **Five Sub-Commands**: Split into dedicated commands for better UX:
  - `/wiki-ingest`: Process sources → save to raw → compile to wiki
  - `/wiki-query`: Search and synthesize answers with citations
  - `/wiki-lint`: Health check with 9 rules, `--fix` for auto-repair
  - `/wiki-status`: Metrics dashboard with activity tracking
  - `/wiki-import`: Batch import existing Markdown notes
- **L1/L2 Cache Architecture**: Inherited from MehmetGoekce/llm-wiki with enhancements
- **Tool Support**: Works with both Logseq and Obsidian
- **Credential Safety**: Lint rule scans for leaked credentials
- **Multi-Language README**: English, Chinese, French, Portuguese, Japanese, Korean

### Changed

- Commands rewritten in English (original MehmetGoekce used mixed language)
- Added `raw_path` configuration option
- Enhanced setup.sh with raw directory creation

### Based On

- Original concept by [Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2 architecture from [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Agent Skills format from [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

---

## Future Plans

- [ ] NPM installer package
- [ ] Video transcript extraction support
- [ ] CDP browser integration for login-gated pages
- [ ] Obsidian plugin for direct wiki integration
- [ ] Web UI for wiki visualization