---
title: "프로젝트 컨텍스트"
description: project-context.md가 AI 에이전트에게 프로젝트 규칙과 선호 방식을 안내하는 방법
sidebar:
  order: 7
---

`project-context.md` 파일은 AI 에이전트를 위한 프로젝트 구현 가이드입니다. 다른 개발 시스템의 "헌법"과 유사하게, 모든 워크플로우에서 일관된 코드 생성을 보장하는 규칙, 패턴, 선호 방식을 담고 있습니다.

## 역할

AI 에이전트는 구현 과정에서 끊임없이 결정을 내립니다. 어떤 패턴을 따를지, 코드를 어떻게 구성할지, 어떤 컨벤션을 사용할지가 그 예입니다. 명확한 지침이 없으면 에이전트는:
- 기존 코드베이스와 맞지 않는 일반적인 모범 사례를 따를 수 있습니다
- 스토리마다 일관성 없는 결정을 내릴 수 있습니다
- 프로젝트 고유의 요구사항이나 제약 조건을 놓칠 수 있습니다

`project-context.md` 파일은 에이전트가 알아야 할 내용을 간결하고 LLM에 최적화된 형식으로 문서화하여 이 문제를 해결합니다.

## 작동 방식

모든 구현 워크플로우는 `project-context.md`가 존재하면 자동으로 불러옵니다. 아키텍트 워크플로우도 이 파일을 불러와 아키텍처 설계 시 기술적 선호 방식을 반영합니다.

**이 워크플로우들이 불러옵니다:**
- `bmad-create-architecture` — 해결책 설계 시 기술적 선호 방식 반영
- `bmad-create-story` — 프로젝트 패턴을 바탕으로 스토리 생성
- `bmad-dev-story` — 구현 결정 안내
- `bmad-code-review` — 프로젝트 표준 기준으로 검증
- `bmad-quick-dev` — 스펙 구현 시 패턴 적용
- `bmad-sprint-planning`, `bmad-retrospective`, `bmad-correct-course` — 프로젝트 전체 컨텍스트 제공

## 생성 시점

`project-context.md` 파일은 프로젝트 어느 단계에서든 유용합니다:

| 시나리오 | 생성 시점 | 목적 |
|----------|-----------|------|
| **신규 프로젝트, 아키텍처 이전** | `bmad-create-architecture` 실행 전 수동으로 | 아키텍트가 기술적 선호 방식을 반영하도록 문서화 |
| **신규 프로젝트, 아키텍처 이후** | `bmad-generate-project-context` 또는 수동으로 | 구현 에이전트를 위해 아키텍처 결정 사항 캡처 |
| **기존 프로젝트** | `bmad-generate-project-context`로 | 에이전트가 기존 컨벤션을 따르도록 패턴 탐색 |
| **Quick Flow 프로젝트** | `bmad-quick-dev` 실행 전 또는 도중 | 빠른 구현 시에도 패턴을 유지하도록 보장 |

:::tip[권장 방법]
신규 프로젝트에서 기술적 선호 방식이 확고하다면 아키텍처 전에 수동으로 생성하세요. 그렇지 않으면 아키텍처 완료 후 생성하여 결정 사항을 캡처하세요.
:::

## 파일 구성

파일은 두 가지 주요 섹션으로 구성됩니다:

### 기술 스택 및 버전

프로젝트에서 사용하는 프레임워크, 언어, 도구와 구체적인 버전을 문서화합니다:

```markdown
## Technology Stack & Versions

- Node.js 20.x, TypeScript 5.3, React 18.2
- State: Zustand (not Redux)
- Testing: Vitest, Playwright, MSW
- Styling: Tailwind CSS with custom design tokens
```

### 핵심 구현 규칙

에이전트가 놓칠 수 있는 패턴과 컨벤션을 문서화합니다:

```markdown
## Critical Implementation Rules

**TypeScript Configuration:**
- Strict mode enabled — no `any` types without explicit approval
- Use `interface` for public APIs, `type` for unions/intersections

**Code Organization:**
- Components in `/src/components/` with co-located `.test.tsx`
- Utilities in `/src/lib/` for reusable pure functions
- API calls use the `apiClient` singleton — never fetch directly

**Testing Patterns:**
- Unit tests focus on business logic, not implementation details
- Integration tests use MSW to mock API responses
- E2E tests cover critical user journeys only

**Framework-Specific:**
- All async operations use the `handleError` wrapper for consistent error handling
- Feature flags accessed via `featureFlag()` from `@/lib/flags`
- New routes follow the file-based routing pattern in `/src/app/`
```

코드 조각만으로는 추론하기 어려운 **비자명한(unobvious)** 내용에 집중하세요. 누구에게나 적용되는 범용 모범 사례는 문서화할 필요가 없습니다.

## 파일 생성 방법

세 가지 방법 중 선택할 수 있습니다:

### 수동 생성

`_bmad-output/project-context.md` 경로에 파일을 만들고 규칙을 추가합니다:

```bash
# In your project root
mkdir -p _bmad-output
touch _bmad-output/project-context.md
```

기술 스택과 구현 규칙을 직접 작성하면 아키텍트 및 구현 워크플로우가 자동으로 불러옵니다.

### 아키텍처 이후 생성

아키텍처 작업이 완료된 후 `bmad-generate-project-context` 워크플로우를 실행합니다:

```bash
bmad-generate-project-context
```

아키텍처 문서와 프로젝트 파일을 분석하여 결정 사항을 담은 컨텍스트 파일을 생성합니다.

### 기존 프로젝트에서 생성

기존 프로젝트의 경우 `bmad-generate-project-context`를 실행하여 기존 패턴을 탐색합니다:

```bash
bmad-generate-project-context
```

코드베이스를 분석하여 컨벤션을 파악한 후, 검토하고 다듬을 수 있는 컨텍스트 파일을 생성합니다.

## 중요한 이유

`project-context.md`가 없으면 에이전트가 프로젝트와 맞지 않는 가정을 하게 됩니다:

| 컨텍스트 없음 | 컨텍스트 있음 |
|--------------|--------------|
| 일반적인 패턴 사용 | 기존 컨벤션 준수 |
| 스토리마다 스타일 불일치 | 일관된 구현 |
| 프로젝트 고유 제약 조건 누락 | 모든 기술 요구사항 반영 |
| 에이전트마다 독립적으로 결정 | 모든 에이전트가 동일한 규칙에 정렬 |

특히 다음 경우에 중요합니다:
- **Quick Flow** — PRD와 아키텍처를 건너뛰므로 컨텍스트 파일이 그 역할을 대신합니다
- **팀 프로젝트** — 모든 에이전트가 동일한 표준을 따르도록 보장합니다
- **기존 프로젝트** — 기존에 확립된 패턴이 깨지지 않도록 방지합니다

## 편집 및 업데이트

`project-context.md` 파일은 살아있는 문서입니다. 다음 상황에서 업데이트하세요:

- 아키텍처 결정이 변경될 때
- 새로운 컨벤션이 확립될 때
- 구현 과정에서 패턴이 발전할 때
- 에이전트 동작에서 누락된 부분을 발견할 때

언제든 수동으로 편집하거나, 중요한 변경 이후 `bmad-generate-project-context`를 다시 실행하여 업데이트할 수 있습니다.

:::note[파일 위치]
기본 경로는 `_bmad-output/project-context.md`입니다. 워크플로우는 이 경로를 먼저 탐색하며, 프로젝트 내 `**/project-context.md` 경로도 함께 확인합니다.
:::
