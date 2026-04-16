# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[English](README.md) | [中文](README.zh-CN.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [한국어](README.ko.md)**

---

> **A única implementação LLM Wiki que preserva suas fontes originais.**

Diferente de outras implementações, este framework mantém um arquivo completo de tudo que você importou em um **diretório `raw/` imutável** — permitindo rastreabilidade completa de fontes.

## O que é LLM Wiki?

LLM Wiki é um conceito de gerenciamento de conhecimento introduzido por [Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): em vez do RAG tradicional, o LLM constrói progressivamente um wiki persistente de arquivos Markdown interconectados.

**Ideia central**: "O LLM escreve e mantém o wiki; o humano lê e faz perguntas."

## Características Principais

| Funcionalidade | Este Projeto | Outras Implementações |
|----------------|--------------|----------------------|
| **Arquivo Raw** | ✅ Diretório `raw/` imutável | ❌ Não preservado |
| **Arquitetura L1/L2** | ✅ Regras auto-carregadas + wiki on-demand | ⚠️ Variável |
| **Sub-comandos** | ✅ 5 comandos dedicados | ⚠️ Comando único |

## Instalação

### Opção 1: Script Setup (Recomendado)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

### Opção 2: Instalação Manual

```bash
mkdir -p seu-projeto/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md seu-projeto/.claude/commands/
```

## Comandos

| Comando | Função |
|---------|--------|
| `/wiki-ingest <fonte>` | Processar fonte → raw → wiki |
| `/wiki-query <questão>` | Buscar wiki → resposta com citações |
| `/wiki-lint [--fix]` | Verificação de saúde |
| `/wiki-status` | Métricas e atividade |
| `/wiki-import <dir>` | Importar notas Markdown |

## Créditos

- Conceito original: [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- Arquitetura L1/L2: [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)

## Licença

[MIT](LICENSE)