# LLM Wiki Enhanced

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/VinceJan/llm-wiki-enhanced?style=social)](https://github.com/VinceJan/llm-wiki-enhanced)

**[English](../../README.md) | [中文](README.zh-CN.md) | [Français](README.fr.md) | [Português](README.pt.md) | [日本語](README.ja.md)**

---

> **원본 소스를 보존하는 유일한 LLM Wiki 구현.**

다른 구현과 달리, 이 프레임워크는 **불변 `raw/` 디렉토리**에 모든 가져온 내용을 완전히 보관합니다—완전한 소스 추적과 향후 재컴파일이 가능합니다.

## LLM Wiki란?

LLM Wiki는 [Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)가 도입한 지식 관리 개념입니다: 기존 RAG에서 LLM이 각 쿼리에서 지식을 재발견하는 것과 달리, LLM이 지속적인 상호 연결 Markdown wiki를 점진적으로 구축하고 유지합니다.

**핵심 아이디어**: "LLM이 wiki를 작성하고 유지한다; 인간은 읽고 질문한다."

## 주요 기능

| 기능 | 이 프로젝트 | 다른 구현 |
|------|-------------|-----------|
| **Raw 보관** | ✅ 불변 `raw/` 디렉토리 | ❌ 보존 안됨 |
| **L1/L2 캐시** | ✅ 자동 로드 규칙 + 온디맨드 wiki | ⚠️ 다양함 |
| **서브커맨드** | ✅ 5개 전용 커맨드 | ⚠️ 단일 커맨드 |

## 설치

### 방법 1: Setup 스크립트 (추천)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

### 방법 2: 수동 설치

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

## 커맨드

| 커맨드 | 기능 |
|--------|------|
| `/wiki-ingest <소스>` | 소스 처리 → raw → wiki |
| `/wiki-query <질문>` | wiki 검색 → 인용 답변 |
| `/wiki-lint [--fix]` | 건전성 체크 |
| `/wiki-status` | 메트릭과 활동 |
| `/wiki-import <디렉토리>` | 기존 Markdown 노트 가져오기 |

## 크레딧

- 원본 개념: [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2 아키텍처: [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)

## 라이선스

[MIT](LICENSE)