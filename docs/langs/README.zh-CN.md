# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[English](../../README.md) | [中文](README.zh-CN.md) | [Français](README.fr.md) | [Português](README.pt.md) | [日本語](README.ja.md) | [한국어](README.ko.md)**

---

> **唯一保留原始来源的 LLM Wiki 实现。**

与其他实现不同，本框架在**不可变的 `raw/` 目录**中保存所有导入内容的完整归档——实现完整的来源追溯和未来重新编译能力。

## 什么是 LLM Wiki？

LLM Wiki 是 [Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 提出的知识管理概念：不同于传统 RAG 每次 query 都重新发现知识，LLM 逐步构建并维护一个持久的、相互链接的 Markdown wiki。

**核心思想**："LLM 编写和维护 wiki；人类阅读和提问。"

## 核心特性

| 特性 | 本项目 | 其他实现 |
|------|--------|----------|
| **Raw 源归档** | ✅ 不可变 `raw/` 目录 | ❌ 不保留 |
| **L1/L2 缓存架构** | ✅ 会话加载规则 + 按需 wiki | ⚠️ 各异 |
| **子命令** | ✅ 5 个专用命令 | ⚠️ 单命令 |
| **健康检查** | ✅ 9 条规则 + `--fix` | ⚠️ 基础 |
| **凭证安全** | ✅ Lint 扫描泄漏 | ❌ 无 |
| **工具支持** | ✅ Obsidian + Logseq | ✅ 相同 |

## 架构

```
输入 (URL / 文件 / 文本 / 目录)
    ↓
RAW (不可变) — raw/<topic>/YYYY-MM-DD-slug.md
    "源归档"
    ↓
WIKI (L2 - 按需) — wiki/pages/<Namespace>/
    页面间 [[交叉引用]]
    ↓
MEMORY (L1 - 自动加载) — ~/.claude/projects/<project>/
    规则、注意事项、凭证 (git-ignored)
```

### 三层模型

| 层级 | 作用 | 行为 |
|------|------|------|
| **Raw** | 不可变源归档 | 创建后永不修改 |
| **L1** | 规则、凭证、偏好 | 每次 Claude 会话自动加载 |
| **L2** | 结构化知识页面 | 按需查询 |

**路由规则**："没有这个知识会导致危险/尴尬的错误？→ L1。只是不方便？→ L2。"

## 命令

| 命令 | 功能 |
|------|------|
| `/wiki-ingest <source>` | 处理源 → 保存到 raw → 编译到 wiki |
| `/wiki-query <question>` | 搜索 wiki → 综合回答（带引用） |
| `/wiki-lint [--fix]` | 健康检查：9 条规则，可自动修复 |
| `/wiki-status` | 指标：页面统计、健康状态、最近活动 |
| `/wiki-import <dir>` | 批量导入已有 Markdown 笔记 |

## 安装

### 方式 1：Setup 脚本（推荐）

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

交互式脚本会：
- 选择工具（Obsidian 或 Logseq）
- 设置 wiki 和 raw 路径
- 配置命名空间
- 创建初始页面
- 安装命令到你的项目

### 方式 2：手动安装

复制 `.claude/commands/` 到你的项目：

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

然后在 wiki 目录创建 `llm-wiki.yml`（参考 `config.example.yml`）。

### 方式 3：在现有 Wiki 中使用

如果你已有 Obsidian vault 或 Logseq graph：

1. 复制 `.claude/commands/` 到你的 vault 的 `.claude/commands/`
2. 在 vault 中创建 `llm-wiki.yml`
3. 在 wiki 旁创建 `raw/` 目录

## 快速开始

1. 在 Claude Code 中导航到你的 wiki 目录
2. 运行 `/wiki-status` 验证安装
3. 尝试 `/wiki-ingest https://example.com/article`
4. 查看创建的 wiki 页面

## 示例工作流

```
/wiki-ingest https://arxiv.org/abs/1706.03762

→ Raw:      raw/ai/2026-04-16-attention-is-all-you-need.md
→ Wiki:     wiki/pages/Tech/Transformer-Architecture.md
→ Cross-ref: [[Tech/Attention-Mechanism]], [[Learning/Deep-Learning]]

/wiki-query "什么是 transformer 架构？"

→ 从 wiki 页面综合回答
→ Sources: [[Tech/Transformer-Architecture]], [[Tech/Attention-Mechanism]]

/wiki-status

→ Dashboard 显示：5 页面，1 raw 文件，0 问题
```

## 配置

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
  - Projects
  - Learning
  - Reference
```

## FAQ

### 为什么保留 raw 源？

- **追溯性**：精确知道每条知识来自哪里
- **重编译**：更新 wiki 结构时不丢失原始内容
- **冲突解决**：来源矛盾时，查看原始上下文
- **审计**：对比实际内容 vs. 解读内容

### 可以修改 raw 文件吗？

**不行。** Raw 文件设计为不可变。需要修正时，更新对应的 wiki 页面。

### 支持什么文件类型？

- URL（通过 WebFetch 抓取网页）
- 本地文件：PDF、Markdown、TXT
- 直接文本输入

### 如何处理视频？

Claude Code 无法直接提取视频字幕。使用外部工具提取字幕，然后导入文本文件。

## 致谢

- 原始概念：[Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2 架构：[MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Agent Skills 格式：[Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

## 许可证

[MIT](LICENSE) - 自由使用、修改和分发。

---

**今天就开始构建你的持久、可追溯的知识库。**