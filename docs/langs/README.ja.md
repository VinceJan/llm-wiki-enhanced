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
| **サブコマンド** | ✅ 6つの専用コマンド | ⚠️ 単一コマンド |
| **健全性チェック** | ✅ 9ルール `--fix` 対応 | ⚠️ 基本的 |
| **資格情報セキュリティ** | ✅ Lintスキャンで漏洩検知 | ❌ なし |
| **ツールサポート** | ✅ Obsidian + Logseq | ✅ 同様 |

## アーキテクチャ

```
┌─────────────────────────────────────────────────┐
│                    入力                           │
│         (URL / ファイル / テキスト / ディレクトリ)  │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                  RAW (不変)                      │
│         raw/<トピック>/YYYY-MM-DD-slug.md        │
│              "ソースアーカイブ"                   │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│               WIKI (L2 - オンデマンド)            │
│            wiki/pages/<名前空間>/                 │
│         ページ間 [[相互参照]]                     │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│           メモリ (L1 - 自動ロード)                │
│           ~/.claude/projects/<プロジェクト>/      │
│       Rules, Gotchas, Credentials (git-ignored)  │
└─────────────────────────────────────────────────┘
```

### 三層モデル

| 層 | 目的 | 動作 |
|----|------|------|
| **Raw** | 不変ソースアーカイブ | 作成後は決して変更されない |
| **L1** | ルール、資格情報、設定 | 各Claudeセッションで自動ロード |
| **L2** | 構造化された知識ページ | オンデマンドで照会 |

**ルーティングルール**：「この知識がないと危険/恥ずかしい間違いにつながる？→ L1。単に不便？→ L2。」

## コマンド

| コマンド | 機能 |
|----------|------|
| `/wiki-ingest <source>` | ソース処理 → raw → wiki |
| `/wiki-query <question>` | wiki検索 → 引用付き回答 |
| `/wiki-lint [--fix]` | 健全性チェック：9ルール、自動修正 |
| `/wiki-status` | メトリクス：ページ数、健全性、アクティビティ |
| `/wiki-import <dir>` | 既存Markdownノートをインポート |

## インストール

### 方法1：Setupスクリプト（推奨）

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

インタラクティブスクリプト：
- ツールを選択（Obsidian または Logseq）
- wiki と raw のパスを設定
- 名前空間を設定
- 初期ページを作成
- コマンドをプロジェクトにインストール

### 方法2：手動インストール

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

その後 wiki ディレクトリに `llm-wiki.yml` を作成（`config.example.yml` を参照）。

### 方法3：既存Wikiで使用

既存の Obsidian vault または Logseq graph がある場合：

1. `.claude/commands/` を vault の `.claude/commands/` にコピー
2. vault 内に `llm-wiki.yml` を作成
3. wiki の隣に `raw/` を作成

## クイックスタート

1. Claude Code で wiki ディレクトリに移動
2. `/wiki-status` を実行してインストールを確認
3. `/wiki-ingest https://example.com/article` を試す
4. 作成されたページを確認

## ワークフロー例

```
/wiki-ingest https://arxiv.org/abs/1706.03762

→ Raw:      raw/ai/2026-04-16-attention-is-all-you-need.md
→ Wiki:     wiki/pages/Tech/Transformer-Architecture.md
→ Cross-ref: [[Tech/Attention-Mechanism]], [[Learning/Deep-Learning]]

/wiki-query "what is transformer architecture?"

→ wikiページから回答を合成
→ Sources: [[Tech/Transformer-Architecture]], [[Tech/Attention-Mechanism]]

/wiki-status

→ Dashboard: 5ページ、1 raw、0問題
```

## 設定

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

### なぜ raw ソースを保存するのか？

- **追跡性**：各知識がどこから来たかを正確に把握
- **再コンパイル**：wiki構造を更新しても原本を失わない
- **矛盾解決**：ソースが矛盾する場合、元のコンテキストを確認
- **監査**：実際に言われたこと vs 解釈されたことを比較

### raw ファイルは変更できますか？

**いいえ。** Raw ファイルは設計上不変です。修正が必要な場合は、対応する wiki ページを更新してください。

### サポートされているファイル形式は？

- URL（WebFetch 経由でウェブページ）
- ローカルファイル：PDF、Markdown、TXT
- 直接テキスト入力

### ビデオはどう処理しますか？

Claude Code はビデオの文字起こしを直接抽出できません。外部ツールで文字起こしを抽出してから、テキストファイルをインジェストしてください。

## クレジット

- オリジナル概念：[Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2アーキテクチャ：[MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Agent Skills フォーマット：[Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

## ライセンス

[MIT](LICENSE) - 自由に使用、改変、配布可能。

---

**今日から永続的で追跡可能な知識ベースを構築しましょう。**