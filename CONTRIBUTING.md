# Contributing to llm-wiki-enhanced

Thank you for your interest in contributing! This project aims to provide a robust, source-preserving LLM Wiki framework for Claude Code users.

## Ways to Contribute

### 1. Report Issues

Found a bug or have a suggestion? Please open an issue with:
- Clear description of the problem or suggestion
- Steps to reproduce (if bug)
- Expected vs actual behavior

### 2. Submit Pull Requests

We welcome improvements! Before submitting:
- Fork the repository
- Create a feature branch (`git checkout -b feature/your-feature`)
- Make your changes
- Test thoroughly
- Submit a PR with clear description

### 3. Translate Documentation

Help make this project accessible to more users:
- Translate README to your language
- Follow existing translation format: `README.<lang>.md`
- Include language code (e.g., `README.fr.md` for French)

### 4. Improve Commands

The `.claude/commands/*.md` files are the core of this framework:
- Enhance workflow descriptions
- Add missing edge cases
- Improve constraint definitions
- Keep everything in **English** (framework language)

## Guidelines

### Code Style

- Markdown files: use consistent formatting
- Commands: follow existing structure (frontmatter + sections)
- Templates: include all required YAML properties

### Commit Messages

Use clear, descriptive commit messages:
```
feat: add new namespace support
fix: correct raw path resolution in setup.sh
docs: improve installation instructions
translate: add French README
```

### Testing

Before submitting changes:
1. Run `setup.sh` in a test directory
2. Try all commands (`/wiki-ingest`, `/wiki-query`, `/wiki-lint`, `/wiki-status`)
3. Verify file structure is correct

## Project Structure

```
llm-wiki-enhanced/
├── .claude/commands/     # Core framework commands (English)
├── templates/pages/      # Wiki template pages (English)
├── setup.sh              # Installation script
├── config.example.yml    # Configuration example
├── README.md             # English documentation
├── docs/
│   ├── USAGE.md          # Detailed usage guide
│   └── langs/            # Translated README files
│       ├── README.zh-CN.md
│       ├── README.fr.md
│       ├── README.pt.md
│       ├── README.ja.md
│       └── README.ko.md
└── SECURITY.md           # Security model and disclosure
```

## Questions?

Feel free to open an issue for any questions or discussions.

---

**Thank you for helping improve llm-wiki-enhanced!**