# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[English](../../README.md) | [中文](README.zh-CN.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [한국어](README.ko.md)**

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
| **Verificação de saúde** | ✅ 9 regras com `--fix` | ⚠️ Básico |
| **Segurança de credenciais** | ✅ Scan lint para vazamentos | ❌ Nenhum |
| **Suporte a ferramentas** | ✅ Obsidian + Logseq | ✅ Igual |

## Arquitetura

```
┌─────────────────────────────────────────────────┐
│                    ENTRADA                       │
│         (URL / Arquivo / Texto / Diretório)      │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                  RAW (Imutável)                  │
│         raw/<topico>/AAAA-MM-DD-slug.md          │
│              "Arquivo Fonte"                     │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│               WIKI (L2 - Sob Demanda)            │
│            wiki/pages/<Namespace>/               │
│         [[referências-cruzadas]] entre páginas   │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│            MEMÓRIA (L1 - Auto-carregada)         │
│           ~/.claude/projects/<projeto>/          │
│       Rules, Gotchas, Credenciais (git-ignorado) │
└─────────────────────────────────────────────────┘
```

### Modelo Três Camadas

| Camada | Propósito | Comportamento |
|--------|-----------|---------------|
| **Raw** | Arquivo fonte imutável | Nunca modificado após criação |
| **L1** | Regras, credenciais, preferências | Auto-carregado toda sessão Claude |
| **L2** | Páginas de conhecimento estruturado | Consultado sob demanda |

**Regra de Roteamento**: "Um erro sem esse conhecimento seria perigoso/embaraçoso? → L1. Apenas inconveniente? → L2."

## Comandos

| Comando | Função |
|---------|--------|
| `/wiki-ingest <fonte>` | Processar fonte → raw → wiki |
| `/wiki-query <questão>` | Buscar wiki → resposta com citações |
| `/wiki-lint [--fix]` | Verificação de saúde: 9 regras, auto-correção |
| `/wiki-status` | Métricas: páginas, saúde, atividade |
| `/wiki-import <dir>` | Importar notas Markdown existentes |

## Instalação

### Opção 1: Script Setup (Recomendado)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

O script interativo:
- Escolhe a ferramenta (Obsidian ou Logseq)
- Define caminhos wiki e raw
- Configura namespaces
- Cria páginas iniciais
- Instala comandos no seu projeto

### Opção 2: Instalação Manual

```bash
mkdir -p seu-projeto/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md seu-projeto/.claude/commands/
```

Depois crie `llm-wiki.yml` no seu diretório wiki (veja `config.example.yml`).

### Opção 3: Usar em um Wiki Existente

Se você já tem um Obsidian vault ou grafo Logseq:

1. Copie `.claude/commands/` para `.claude/commands/` do seu vault
2. Crie `llm-wiki.yml` no seu vault
3. Crie `raw/` ao lado do seu wiki

## Início Rápido

1. No Claude Code, navegue para seu diretório wiki
2. Execute `/wiki-status` para verificar a instalação
3. Tente `/wiki-ingest https://example.com/article`
4. Verifique as páginas criadas

## Exemplo de Fluxo

```
/wiki-ingest https://arxiv.org/abs/1706.03762

→ Raw:      raw/ai/2026-04-16-attention-is-all-you-need.md
→ Wiki:     wiki/pages/Tech/Transformer-Architecture.md
→ Cross-ref: [[Tech/Attention-Mechanism]], [[Learning/Deep-Learning]]

/wiki-query "what is transformer architecture?"

→ Resposta sintetizada das páginas wiki
→ Fontes: [[Tech/Transformer-Architecture]], [[Tech/Attention-Mechanism]]

/wiki-status

→ Dashboard: 5 páginas, 1 raw, 0 problemas
```

## Configuração

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

### Por que conservar as fontes raw?

- **Rastreabilidade**: Saber exatamente de onde vem cada conhecimento
- **Re-compilação**: Atualizar a estrutura wiki sem perder os originais
- **Resolução de conflitos**: Quando as fontes divergem, ver o contexto original
- **Auditoria**: Comparar o que foi dito vs o que foi interpretado

### Posso modificar os arquivos raw?

**Não.** Os arquivos raw são imutáveis por design. Para corrigir, atualize a página wiki correspondente.

### Quais formatos de arquivo são suportados?

- URLs (páginas web via WebFetch)
- Arquivos locais: PDF, Markdown, TXT
- Entrada direta de texto

### Como lidar com vídeos?

Claude Code não pode extrair transcrições de vídeo. Use ferramentas externas para extrair transcrições e depois ingira o arquivo de texto.

## Créditos

- Conceito original: [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- Arquitetura L1/L2: [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Formato Agent Skills: [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

## Licença

[MIT](LICENSE) - Livre para uso, modificação e distribuição.

---

**Comece a construir sua base de conhecimento persistente e rastreável hoje.**