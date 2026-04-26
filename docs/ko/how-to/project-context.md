---
title: '프로젝트 컨텍스트 관리'
description: AI 에이전트를 안내하기 위한 project-context.md 파일 생성 및 관리 방법
sidebar:
  order: 9
---

`project-context.md` 파일을 사용하면 모든 워크플로우에서 AI 에이전트가 프로젝트의 기술적 선호 사항과 구현 규칙을 일관되게 따르도록 할 수 있습니다. 이 파일이 항상 참조되도록 하려면, `AGENTS.md` 같은 도구 컨텍스트 또는 상시 규칙 파일에 `Important project context and conventions are located in [path to project context]/project-context.md` 줄을 추가하세요.

:::note[사전 요구사항]

- BMad Method 설치됨
- 프로젝트의 기술 스택 및 코딩 규칙에 대한 이해
  :::

## 언제 사용하나요

- 아키텍처 설계 전부터 특정 기술적 선호 사항이 있는 경우
- 아키텍처 설계를 마치고 구현 단계를 위해 결정 사항을 기록하려는 경우
- 이미 확립된 패턴을 가진 기존 프로젝트에서 작업하는 경우
- 에이전트가 스토리 전반에 걸쳐 일관성 없는 결정을 내리고 있다고 느끼는 경우

## 1단계: 접근 방식 선택

**직접 작성** — 문서화할 규칙을 정확히 알고 있을 때 적합합니다.

**아키텍처 이후 생성** — 해결책 설계 중에 내린 결정을 기록하려 할 때 적합합니다.

**기존 프로젝트용 생성** — 기존 코드베이스의 패턴을 발견하려 할 때 적합합니다.

## 2단계: 파일 생성

### 옵션 A: 직접 작성

`_bmad-output/project-context.md` 경로에 파일을 생성합니다.

```bash
mkdir -p _bmad-output
touch _bmad-output/project-context.md
```

기술 스택과 구현 규칙을 추가합니다.

```markdown
---
project_name: 'MyProject'
user_name: 'YourName'
date: '2026-02-15'
sections_completed: ['technology_stack', 'critical_rules']
---

# Project Context for AI Agents

## Technology Stack & Versions

- Node.js 20.x, TypeScript 5.3, React 18.2
- State: Zustand
- Testing: Vitest, Playwright
- Styling: Tailwind CSS

## Critical Implementation Rules

**TypeScript:**

- Strict mode enabled, no `any` types
- Use `interface` for public APIs, `type` for unions

**Code Organization:**

- Components in `/src/components/` with co-located tests
- API calls use `apiClient` singleton — never fetch directly

**Testing:**

- Unit tests focus on business logic
- Integration tests use MSW for API mocking
```

### 옵션 B: 아키텍처 이후 생성

새 채팅에서 다음 워크플로우를 실행합니다.

```bash
bmad-generate-project-context
```

이 워크플로우는 아키텍처 문서와 프로젝트 파일을 스캔하여 내려진 결정 사항을 담은 컨텍스트 파일을 생성합니다.

### 옵션 C: 기존 프로젝트용 생성

기존 프로젝트의 경우 다음을 실행합니다.

```bash
bmad-generate-project-context
```

이 워크플로우는 코드베이스를 분석하여 규칙을 파악한 뒤, 검토하고 다듬을 수 있는 컨텍스트 파일을 생성합니다.

## 3단계: 내용 검토

생성된 파일을 검토하여 다음 사항이 포함되었는지 확인합니다.

- 정확한 기술 버전
- 일반적인 모범 사례가 아닌 실제 프로젝트 규칙
- 흔한 실수를 방지하는 규칙
- 프레임워크별 패턴

누락된 내용을 추가하거나 부정확한 내용을 제거하려면 직접 파일을 수정하세요.

## 결과물

`project-context.md` 파일은 다음과 같은 역할을 합니다.

- 모든 에이전트가 동일한 규칙을 따르도록 보장
- 스토리 전반에 걸쳐 일관성 없는 결정 방지
- 구현 단계를 위한 아키텍처 결정 사항 기록
- 프로젝트 패턴 및 규칙의 참고자료 역할

## 팁

:::tip[모범 사례]

- **비자명한 것에 집중하세요** — 에이전트가 놓칠 수 있는 패턴을 문서화하세요 (예: "모든 public 클래스에 JSDoc 추가"). "의미 있는 변수명 사용" 같은 보편적인 관행은 제외하세요.
- **간결하게 유지하세요** — 이 파일은 모든 구현 워크플로우에서 로드됩니다. 파일이 길면 컨텍스트가 낭비됩니다. 특정 범위나 특정 스토리에만 적용되는 내용은 제외하세요.
- **필요에 따라 업데이트하세요** — 패턴이 변경되면 직접 편집하거나, 주요 아키텍처 변경 후에는 다시 생성하세요.
- Quick Flow 프로젝트와 전체 BMad Method 프로젝트 모두에 적용 가능합니다.
  :::

## 다음 단계

- [**프로젝트 컨텍스트 개념 설명**](../explanation/project-context.md) — 동작 원리에 대해 더 알아보기
- [**워크플로우 맵**](../reference/workflow-map.md) — 프로젝트 컨텍스트를 로드하는 워크플로우 확인
