# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[English](../../README.md) | [中文](README.zh-CN.md) | [Português](README.pt.md) | [日本語](README.ja.md) | [한국어](README.ko.md)**

---

> **La seule implémentation LLM Wiki qui conserve vos sources originales.**

Contrairement aux autres implémentations, ce framework conserve une archive complète de tout ce que vous avez ingéré dans un **répertoire `raw/` immuable** — permettant une traçabilité complète des sources et une re-compilation future.

## Qu'est-ce que LLM Wiki ?

LLM Wiki est un concept de gestion des connaissances introduit par [Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) : au lieu du RAG traditionnel où le LLM redécouvre les connaissances à chaque requête, le LLM construit et maintient progressivement un wiki persistant de fichiers Markdown interconnectés.

**L'idée centrale** : "Le LLM écrit et maintient le wiki ; l'humain lit et pose des questions."

## Caractéristiques Clés

| Fonctionnalité | Ce Projet | Autres Implémentations |
|----------------|-----------|------------------------|
| **Archive Raw** | ✅ Répertoire `raw/` immuable | ❌ Non conservé |
| **Architecture L1/L2** | ✅ Rules auto-chargées + wiki on-demand | ⚠️ Variable |
| **Sous-commandes** | ✅ 5 commandes dédiées | ⚠️ Commande unique |
| **Contrôle de santé** | ✅ 9 règles avec `--fix` | ⚠️ Basique |
| **Sécurité credentials** | ✅ Scan lint pour leaks | ❌ Aucun |

## Installation

### Option 1 : Script Setup (Recommandé)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

### Option 2 : Installation Manuelle

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

## Commandes

| Commande | Fonction |
|----------|----------|
| `/wiki-ingest <source>` | Traiter source → raw → wiki |
| `/wiki-query <question>` | Rechercher wiki → réponse avec citations |
| `/wiki-lint [--fix]` | Contrôle de santé avec auto-correction |
| `/wiki-status` | Métriques et activité |
| `/wiki-import <dir>` | Importer notes Markdown existantes |

## Crédits

- Concept original : [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- Architecture L1/L2 : [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)

## Licence

[MIT](LICENSE)