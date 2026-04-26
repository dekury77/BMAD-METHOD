---
title: 한국어 번역 용어집 및 스타일 가이드
description: BMad Method 한국어 번역의 일관성을 위한 용어 표기와 번역 규칙
sidebar:
  hidden: true
---

# 한국어 번역 용어집 (Translation Glossary)

이 문서는 `docs/ko/` 한국어 번역의 **용어 일관성**을 유지하기 위한 단일 기준입니다.
번역 작업 시 모든 파일이 이 규칙을 따라야 합니다.

## 1. 어조 / 문체

- **존댓말 사용**: `-합니다`, `-입니다` 체. (예: "에이전트는 워크플로우를 실행합니다.")
- **명령형 헤딩**: 영어 명령형(`Install BMad`)은 한국어에서도 명령형(`BMad 설치하기`)으로 번역.
- **번역체 회피**: 직역으로 "~를 가질 것이다", "~될 수 있다" 같은 어색한 표현 대신 자연스러운 한국어로 풀어쓴다.
- **존중 표현**: 독자를 "여러분"보다 생략하는 편을 선호. ("`bmad-help` 스킬을 사용하면 안내를 받을 수 있습니다.")

## 2. 그대로 두는 용어 (영문 유지)

다음은 **번역하지 않고 영문 그대로** 사용합니다:

| 용어 | 비고 |
| --- | --- |
| BMad, BMad Method | 제품명 |
| BMM | 모듈명 (Agile suite) |
| BMad Builder | 제품명 |
| TEA, Test Architect | 모듈명 |
| Diataxis | 문서 분류 체계 이름 |
| Claude, Claude Code, Cursor, Codex CLI, GitHub Copilot, Anthropic, OpenAI | 제품/회사명 |
| PRD, MVP, FAQ, CLI, IDE, CI/CD, API, SDK, MCP, LLM | 약어 |
| Discord, GitHub, YouTube, npm | 플랫폼/도구명 |
| Markdown, YAML, JSON, HTML, CSS | 포맷 |
| skill ID 값 (`bmad-dev`, `bmad-pm` 등) | 식별자 |
| 트리거 코드 (`CP`, `DS`, `QD` 등) | 메뉴 코드 |
| 사람 이름 (Mary, John, Winston, Amelia, Sally, Paige, Brian Madison 등) | 에이전트/저자 이름 |

## 3. 핵심 용어 표준 번역

| 영어 | 한국어 | 비고 |
| --- | --- | --- |
| agent | 에이전트 | |
| agentic | 에이전틱 | (에이전트 기반의) |
| workflow | 워크플로우 | |
| skill | 스킬 | |
| module | 모듈 | |
| epic | 에픽 | |
| story | 스토리 | |
| sprint | 스프린트 | |
| trigger | 트리거 | |
| prompt | 프롬프트 | |
| context | 컨텍스트 | |
| ecosystem | 생태계 | |
| framework | 프레임워크 | |
| pipeline | 파이프라인 | |
| installer | 설치 도구 | |
| installation | 설치 | |
| menu | 메뉴 | |
| command | 명령어 | |
| flag / option | 옵션 | |
| repository / repo | 저장소 / 저장소 | (구어 'repo'도 '저장소') |
| branch | 브랜치 | |
| pull request, PR | 풀 리퀘스트 (PR) | 첫 등장 시 한국어, 이후 PR |
| issue | 이슈 | |
| commit | 커밋 | |
| AI coding assistant | AI 코딩 어시스턴트 | |
| coding agent | 코딩 에이전트 | |
| user / developer | 사용자 / 개발자 | |
| documentation, docs | 문서 | |
| reference | 참고자료 | (Diataxis 카테고리) |
| how-to guide | 방법 안내 | (Diataxis 카테고리) |
| tutorial | 튜토리얼 | (Diataxis 카테고리) |
| explanation | 개념 설명 | (Diataxis 카테고리) |

## 4. 작업/방법론 관련 용어

| 영어 | 한국어 | 비고 |
| --- | --- | --- |
| brainstorming | 브레인스토밍 | |
| ideation | 아이데이션 | |
| elicitation | 요구사항 끌어내기 | |
| advanced elicitation | 고급 요구사항 끌어내기 | |
| adversarial review | 적대적 리뷰 | |
| party mode | 파티 모드 | (BMad 고유 기능) |
| checkpoint | 체크포인트 | |
| checkpoint preview | 체크포인트 프리뷰 | |
| agent conflict | 에이전트 충돌 | |
| sharding (large documents) | 문서 샤딩 | (분할) |
| dev story | 개발 스토리 | (Dev Story 워크플로우는 영문 그대로) |
| quick dev | 퀵 데브 | |
| code review | 코드 리뷰 | |
| sprint planning | 스프린트 플래닝 | |
| dev loop automation | 개발 루프 자동화 | |
| skills architecture | 스킬 아키텍처 | |
| solutioning | 해결책 설계 | |
| analysis phase | 분석 단계 | |
| project context | 프로젝트 컨텍스트 | |
| established project | 기존 프로젝트 | |
| greenfield project | 신규 프로젝트 | |

## 5. 에이전트/역할 표기

에이전트 이름은 그대로, 역할은 한국어로 표기합니다.

| 영어 | 한국어 |
| --- | --- |
| Analyst (Mary) | 분석가 (Mary) |
| Product Manager (John) | 프로덕트 매니저 (John) |
| Architect (Winston) | 아키텍트 (Winston) |
| Developer (Amelia) | 개발자 (Amelia) |
| UX Designer (Sally) | UX 디자이너 (Sally) |
| Technical Writer (Paige) | 테크니컬 라이터 (Paige) |

## 6. 마크다운 보존 규칙

번역 시 **반드시 그대로 보존**해야 하는 요소:

1. **frontmatter 키**: `title:`, `description:`, `sidebar:` 등의 키는 그대로. 값(특히 `title`, `description`)만 한국어로.
2. **코드 블록 내부**: ```...``` 안의 코드/명령어/출력 예시는 번역하지 않음. 단, 코드 블록 위/아래의 설명문은 번역.
3. **인라인 코드** `\`...\``: 백틱 안의 식별자/명령어/경로는 그대로.
4. **링크**:
   - URL은 절대 번역하지 않음.
   - 상대 경로 링크(`./tutorials/getting-started.md`)는 그대로 유지 — Starlight가 locale별로 자동 매핑.
   - 링크 텍스트는 번역.
5. **이미지**: `![alt](path)`의 `alt` 텍스트는 번역, 경로는 유지.
6. **admonition**: `:::note[제목]`, `:::tip`, `:::caution`, `:::warning`, `:::danger`의 키워드(`note`/`tip` 등)는 보존, 제목과 본문은 번역.
7. **테이블**: 헤더와 셀 내용 번역. 단, 셀 안의 코드/식별자는 그대로.
8. **HTML 태그**: `<br/>`, `<details>` 등은 보존.
9. **MDX 컴포넌트**(`.mdx` 파일): `<CardGrid>`, `<Card>` 등 컴포넌트 태그·속성은 보존, 자식 텍스트만 번역.

## 7. 외래어 / 표기 원칙

- **외래어 표기법**: 국립국어원 외래어 표기법을 따른다.
  - `vi-VN` 식 로케일 코드는 그대로.
  - `Astro`, `Starlight`, `Vite` 등 도구명은 그대로.
- **숫자**: 본문 내 작은 숫자(1~10)는 한글(하나, 둘) 대신 아라비아 숫자로 통일.
- **따옴표**: 영문 인용은 한글 따옴표 `"..."` 또는 작은따옴표 `'...'`로. 코드/식별자는 백틱.
- **문장 부호**: 영어 원문의 콜론·세미콜론·대시(`—`)는 한국어 문장 흐름에 맞게 자연스럽게 조정 (반드시 1:1 대응 강제하지 않음).

## 8. 번역하면 어색해지는 표현 (의역 권장)

- "Out of the box" → "별도 설정 없이", "기본 제공으로"
- "Under the hood" → "내부적으로", "동작 원리는"
- "Get your hands dirty" → "직접 해보다"
- "Battle-tested" → "검증된"
- "First-class" → "기본 지원되는"
- "Opinionated" → "정형화된" 또는 "선호 방식이 정해진"

## 9. 메타 정보

- 모든 한국어 페이지의 frontmatter `description`은 한국어로.
- 외부 링크(Discord, GitHub, YouTube 등)는 동일 URL 유지.
- "Last updated" 등 사이트 UI 텍스트는 Starlight i18n에서 자동 처리(별도 번역 불필요).
