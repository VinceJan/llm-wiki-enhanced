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
| **서브커맨드** | ✅ 6개 전용 커맨드 | ⚠️ 단일 커맨드 |
| **건전성 체크** | ✅ 9개 규칙, `--fix` 자동수정 | ⚠️ 기본 |
| **자격증명 보안** | ✅ Lint 스캔으로 유출 감지 | ❌ 없음 |
| **도구 지원** | ✅ Obsidian + Logseq | ✅ 동일 |

## 아키텍처

```
┌─────────────────────────────────────────────────┐
│                    입력                           │
│         (URL / 파일 / 텍스트 / 디렉토리)           │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                  RAW (불변)                      │
│         raw/<주제>/YYYY-MM-DD-slug.md            │
│              "소스 아카이브"                      │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│                WIKI (L2 - 온디맨드)              │
│            wiki/pages/<네임스페이스>/             │
│         페이지 간 [[상호참조]]                    │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│           메모리 (L1 - 자동로드)                  │
│           ~/.claude/projects/<프로젝트>/          │
│       Rules, Gotchas, Credentials (git-ignored)  │
└─────────────────────────────────────────────────┘
```

### 3계층 모델

| 계층 | 목적 | 동작 |
|------|------|------|
| **Raw** | 불변 소스 아카이브 | 생성 후 절대 수정되지 않음 |
| **L1** | 규칙, 자격증명, 선호도 | 매 Claude 세션에서 자동 로드 |
| **L2** | 구조화된 지식 페이지 | 온디맨드 조회 |

**라우팅 규칙**: "이 지식 없으면 위험/창피한 실수? → L1. 그냥 불편한 정도? → L2."

## 커맨드

| 커맨드 | 기능 |
|--------|------|
| `/wiki-ingest <소스>` | 소스 처리 → raw → wiki |
| `/wiki-query <질문>` | wiki 검색 → 인용 답변 |
| `/wiki-lint [--fix]` | 건전성 체크: 9개 규칙, 자동수정 |
| `/wiki-status` | 메트릭: 페이지, 상태, 활동 |
| `/wiki-import <디렉토리>` | 기존 Markdown 노트 가져오기 |

## 설치

### 방법 1: Setup 스크립트 (추천)

```bash
git clone https://github.com/VinceJan/llm-wiki-enhanced.git
cd llm-wiki-enhanced
./setup.sh
```

인터랙티브 스크립트:
- 도구 선택 (Obsidian 또는 Logseq)
- wiki 및 raw 경로 설정
- 네임스페이스 설정
- 초기 페이지 생성
- 프로젝트에 커맨드 설치

### 방법 2: 수동 설치

```bash
mkdir -p your-project/.claude/commands
cp llm-wiki-enhanced/.claude/commands/*.md your-project/.claude/commands/
```

그런 다음 wiki 디렉토리에 `llm-wiki.yml` 생성 (`config.example.yml` 참조).

### 방법 3: 기존 Wiki에서 사용

기존 Obsidian vault 또는 Logseq graph가 있는 경우:

1. `.claude/commands/`를 vault의 `.claude/commands/`에 복사
2. vault 내에 `llm-wiki.yml` 생성
3. wiki 옆에 `raw/` 생성

## 빠른 시작

1. Claude Code에서 wiki 디렉토리로 이동
2. `/wiki-status` 실행하여 설치 확인
3. `/wiki-ingest https://example.com/article` 시도
4. 생성된 페이지 확인

## 워크플로우 예시

```
/wiki-ingest https://arxiv.org/abs/1706.03762

→ Raw:      raw/ai/2026-04-16-attention-is-all-you-need.md
→ Wiki:     wiki/pages/Tech/Transformer-Architecture.md
→ Cross-ref: [[Tech/Attention-Mechanism]], [[Learning/Deep-Learning]]

/wiki-query "what is transformer architecture?"

→ wiki 페이지에서 답변 합성
→ Sources: [[Tech/Transformer-Architecture]], [[Tech/Attention-Mechanism]]

/wiki-status

→ Dashboard: 5페이지, 1 raw, 0문제
```

## 설정

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

### 왜 raw 소스를 보존하나요?

- **추적성**: 각 지식이 어디에서 왔는지 정확히 파악
- **재컴파일**: wiki 구조를 업데이트해도 원본을 잃지 않음
- **충돌 해결**: 소스가 모순될 때 원본 컨텍스트 확인
- **감사**: 실제 말한 것 vs 해석된 것 비교

### raw 파일을 수정할 수 있나요?

**아니요.** Raw 파일은 설계상 불변입니다. 수정이 필요하면 해당 wiki 페이지를 업데이트하세요.

### 지원되는 파일 형식은?

- URL (WebFetch를 통한 웹페이지)
- 로컬 파일: PDF, Markdown, TXT
- 직접 텍스트 입력

### 비디오는 어떻게 처리하나요?

Claude Code는 비디오 자막을 직접 추출할 수 없습니다. 외부 도구로 자막을 추출한 후 텍스트 파일을 인제스트하세요.

## 크레딧

- 원본 개념: [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- L1/L2 아키텍처: [MehmetGoekce/llm-wiki](https://github.com/MehmetGoekce/llm-wiki)
- Agent Skills 포맷: [Astro-Han/karpathy-llm-wiki](https://github.com/Astro-Han/karpathy-llm-wiki)

## 라이선스

[MIT](LICENSE) - 자유롭게 사용, 수정, 배포 가능.

---

**오늘부터 지속적이고 추적 가능한 지식베이스를 구축하세요.**