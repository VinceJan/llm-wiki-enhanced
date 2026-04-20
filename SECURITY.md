# Security Policy

## Security Model

This project maintains a strict separation between sensitive data and tracked knowledge:

### L1/L2 Boundary

- **L1 (Memory)**: Credentials, API tokens, passwords, and sensitive rules are stored in `.claude/memory/` which is **git-ignored** by default
- **L2 (Wiki)**: Knowledge pages are **git-tracked** and must NEVER contain credentials

### Credential Leak Detection

The `/wiki-lint` command includes a dedicated **Credential Leak** check that scans all wiki pages for:
- `token::`, `password::`, `secret::`, `api-key::` patterns
- Base64 strings longer than 40 characters

If a leak is detected, it is flagged as a **critical** severity issue and will NOT be auto-fixed.

### .env Files

The `.gitignore` includes `.env`, `.env.*`, and `.env.local` patterns to prevent accidental exposure of environment variables containing API keys or secrets.

### Supported Versions

| Version | Supported |
|---------|-----------|
| 1.0.x   | ✅ Yes    |

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please **do not** open a public issue.

Instead, report it privately by emailing **VinceJan** through GitHub's security advisory system:

1. Go to the repository's **Security** tab
2. Click **Report a vulnerability**
3. Describe the issue in detail

We aim to respond within **48 hours** and will work with you to assess and resolve the issue responsibly.

## Security Best Practices for Users

1. **Never commit credentials** to your wiki (it is git-tracked by default)
2. **Use `.gitignore`** to exclude `.claude/memory/` and `.env` files
3. **Run `/wiki-lint` regularly** to scan for accidental credential exposure
4. **Review raw/ files** before ingesting sources that may contain sensitive data
5. **Use separate Git repositories** for wiki content if you need to share your wiki with others
