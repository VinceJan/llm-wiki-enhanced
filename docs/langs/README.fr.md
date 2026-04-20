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
| **Sous-commandes** | ✅ 6 commandes dédiées | ⚠️ Commande unique |
| **Contrôle de santé** | ✅ 9 règles avec `--fix` | ⚠️ Basique |
| **Sécurité credentials** | ✅ Scan lint pour leaks | ❌ Aucun |
| **Support d'outils** | ✅ Obsidian + Logseq | ✅ Identique |

## Architecture

```
┌─────────────────────────────────────────────────┐
│                    ENTRÉE                        │
│         (URL / Fichier / Texte / Répertoire)     │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                  RAW (Immuable)                  │
│         raw/<sujet>/AAAA-MM-JJ-slug.md           │
│              "Archive Source"                    │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                WIKI (L2 - À la demande)          │
│            wiki/pages/<EspaceNom>/               │
│         [[références-croisées]] entre pages      │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│             MÉMOIRE (L1 - Auto-chargé)           │
│           ~/.claude/projects/<projet>/           │
│       Rules, Gotchas, Credentials (git-ignoré)   │
└─────────────────────────────────────────────────┘
```

### Modèle Trois Couches

| Couche | Objectif | Comportement |
|--------|----------|--------------|
| **Raw** | Archive source immuable | Jamais modifié après création |
| **L1** | Rules, credentials, préférences | Auto-chargé chaque session Claude |
| **L2** | Pages de connaissances structurées | Consulté à la demande |

**Règle de Routage** : "Une erreur sans cette connaissance serait-elle dangereuse/gênante ? → L1. Seulement incommode ? → L2."

## Commandes

| Commande | Fonction |
|----------|----------|
| `/wiki-ingest <source>` | Traiter source → raw → wiki |
| `/wiki-query <question>` | Rechercher wiki → réponse avec citations |
| `/wiki-lint [--fix]` | Contrôle de santé : 9 règles, auto-correction |
| `/wiki-status` | Métriques : pages, santé, activité |
| `/wiki-import <dir>` | Importer notes Markdown existantes |

## Installation

### Option 1 : Script Setup (Recommandé)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

Le script interactif :
- Choisit l'outil (Obsidian ou Logseq)
- Définit les chemins wiki et raw
- Configure les espaces de noms
- Crée les pages initiales
- Installe les commandes dans votre projet

### Option 2 : Installation Manuelle

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

Puis créez `llm-wiki.yml` dans votre répertoire wiki (voir `config.example.yml`).

### Option 3 : Utiliser dans un Wiki Existant

Si vous avez déjà un Obsidian vault ou un graphe Logseq :

1. Copiez `.claude/commands/` dans `.claude/commands/` de votre vault
2. Créez `llm-wiki.yml` dans votre vault
3. Créez `raw/` à côté de votre wiki

## Démarrage Rapide

1. Dans Claude Code, naviguez vers votre répertoire wiki
2. Exécutez `/wiki-status` pour vérifier l'installation
3. Essayez `/wiki-ingest https://example.com/article`
4. Vérifiez les pages créées

## Exemple de Flux

```
/wiki-ingest https://arxiv.org/abs/1706.03762

→ Raw:      raw/ai/2026-04-16-attention-is-all-you-need.md
→ Wiki:     wiki/pages/Tech/Transformer-Architecture.md
→ Cross-ref: [[Tech/Attention-Mechanism]], [[Learning/Deep-Learning]]

/wiki-query "what is transformer architecture?"

→ Réponse synthétisée depuis les pages wiki
→ Sources: [[Tech/Transformer-Architecture]], [[Tech/Attention-Mechanism]]

/wiki-status

→ Dashboard : 5 pages, 1 raw, 0 problèmes
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

### Pourquoi conserver les sources raw ?

- **Traçabilité** : Savoir exactement d'où vient chaque connaissance
- **Re-compilation** : Mettre à jour la structure wiki sans perdre les originaux
- **Résolution de conflits** : Quand les sources divergent, voir le contexte original
- **Audit** : Comparer ce qui a été dit vs ce qui a été interprété

### Puis-je modifier les fichiers raw ?

**Non.** Les fichiers raw sont immuables par conception. Pour corriger, mettez à jour la page wiki correspondante.

### Quels formats de fichiers sont supportés ?

- URLs (pages web via WebFetch)
- Fichiers locaux : PDF, Markdown, TXT
- Saisie directe de texte

### Comment gérer les vidéos ?

Claude Code ne peut pas extraire les transcriptions vidéo. Utilisez des outils externes pour extraire les transcriptions, puis ingérez le fichier texte.

## Crédits

- Concept original : [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- Architecture L1/L2 : [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Format Agent Skills : [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

## Licence

[MIT](LICENSE) - Libre d'utilisation, modification et distribution.

---

**Commencez à construire votre base de connaissances persistante et traçable dès aujourd'hui.**