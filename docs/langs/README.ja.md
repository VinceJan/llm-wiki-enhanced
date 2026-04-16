# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[English](../../README.md) | [中文](README.zh-CN.md) | [Français](README.fr.md) | [Português](README.pt.md) | [한국어](README.ko.md)**

---

> **オリジナルソースを保存する唯一のLLM Wiki実装。**

他の実装とは異なり、このフレームワークは**不変の `raw/` ディレクトリ**にインポートしたすべての内容を完全に保存します—完全なソース追跡と将来の再コンパイルが可能です。

## LLM Wikiとは？

LLM Wikiは[Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)が導入した知識管理概念です：従来のRAGでLLMが各クエリで知識を再発見する代わりに、LLMが永続的な相互リンクMarkdown wikiを段階的に構築・維持します。

**核となるアイデア**：「LLMがwikiを書いて維持する；人間は読んで質問する。」

## 主な機能

| 機能 | 本プロジェクト | 他の実装 |
|------|---------------|----------|
| **Rawアーカイブ** | ✅ 不変 `raw/` ディレクトリ | ❌ 保存されない |
| **L1/L2キャッシュ** | ✅ 自動ロードルール + オンデマンドwiki | ⚠️ 異なる |
| **サブコマンド** | ✅ 5つの専用コマンド | ⚠️ 単一コマンド |

## インストール

### 方法1：Setupスクリプト（推奨）

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

### 方法2：手動インストール

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

## コマンド

| コマンド | 機能 |
|----------|------|
| `/wiki-ingest <source>` | ソース処理 → raw → wiki |
| `/wiki-query <question>` | wiki検索 → 引用付き回答 |
| `/wiki-lint [--fix]` | 健全性チェック |
| `/wiki-status` | メトリクスと活動 |
| `/wiki-import <dir>` | 既存Markdownノートをインポート |

## クレジット

- オリジナル概念：[Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2アーキテクチャ：[MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)

## ライセンス

[MIT](LICENSE)