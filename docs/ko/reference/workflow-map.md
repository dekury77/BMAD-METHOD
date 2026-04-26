---
title: "워크플로우 맵"
description: BMad Method 워크플로우 단계 및 산출물 시각적 참고자료
sidebar:
  order: 1
---

BMad Method (BMM)는 컨텍스트 엔지니어링과 기획 모범 사례를 따르는 BMad 생태계의 모듈입니다. AI 에이전트는 명확하고 구조화된 컨텍스트에서 가장 잘 작동합니다. BMM 시스템은 4개의 뚜렷한 단계에 걸쳐 컨텍스트를 점진적으로 구축합니다. 각 단계와 단계 내 여러 워크플로우는 다음 단계에 정보를 제공하는 문서를 생성하므로, 에이전트는 항상 무엇을 왜 만들어야 하는지 알고 있습니다.

이 방식의 근거와 개념은 업계에서 정신적 프레임워크로 큰 성공을 거둔 애자일 방법론에서 비롯됩니다.

진행 중에 무엇을 해야 할지 모를 때는 `bmad-help` 스킬이 올바른 방향을 유지하거나 다음 단계를 알려드립니다. 참고자료로 이 문서를 활용할 수도 있지만, BMad Method를 이미 설치했다면 `bmad-help`가 완전히 대화형으로 훨씬 빠르게 안내해 드립니다. 또한 BMad Method를 확장했거나 다른 보완 모듈을 사용하는 경우 `bmad-help`는 사용 가능한 모든 것을 파악하여 가장 적합한 실시간 조언을 제공합니다.

마지막으로 중요한 점: 아래의 모든 워크플로우는 스킬을 통해 원하는 도구로 직접 실행하거나, 에이전트를 먼저 로드한 후 에이전트 메뉴의 항목을 사용하여 실행할 수 있습니다.

<iframe src="/workflow-map-diagram.html" title="BMad Method Workflow Map Diagram" width="100%" height="100%" style="border-radius: 8px; border: 1px solid #334155; min-height: 900px;"></iframe>

<p style="font-size: 0.8rem; text-align: right; margin-top: -0.5rem; margin-bottom: 1rem;">
  <a href="/workflow-map-diagram.html" target="_blank" rel="noopener noreferrer">새 탭에서 다이어그램 열기 ↗</a>
</p>

## Phase 1: 분석 (선택 사항)

기획을 시작하기 전에 문제 공간을 탐색하고 아이디어를 검증합니다. [**각 도구의 역할과 사용 시점 알아보기**](../explanation/analysis-phase.md).

| 워크플로우 | 목적 | 생성물 |
| --- | --- | --- |
| `bmad-brainstorming` | 브레인스토밍 코치의 안내로 프로젝트 아이디어 도출 | `brainstorming-report.md` |
| `bmad-domain-research`, `bmad-market-research`, `bmad-technical-research` | 시장, 기술, 도메인 가정 검증 | 리서치 결과물 |
| `bmad-product-brief` | 전략적 비전 정리 — 개념이 명확할 때 적합 | `product-brief.md` |
| `bmad-prfaq` | Working Backwards — 제품 개념 검증 및 다듬기 | `prfaq-{project}.md` |

## Phase 2: 기획

무엇을, 누구를 위해 만들지 정의합니다.

| 워크플로우 | 목적 | 생성물 |
| --- | --- | --- |
| `bmad-create-prd` | 요구사항 정의 (기능/비기능 요구사항) | `PRD.md` |
| `bmad-create-ux-design` | 사용자 경험 설계 (UX가 중요한 경우) | `ux-spec.md` |

## Phase 3: 해결책 설계

어떻게 만들지 결정하고 작업을 스토리로 분해합니다.

| 워크플로우 | 목적 | 생성물 |
| --- | --- | --- |
| `bmad-create-architecture` | 기술적 의사결정 명시화 | ADR이 포함된 `architecture.md` |
| `bmad-create-epics-and-stories` | 요구사항을 구현 가능한 작업으로 분해 | 스토리가 포함된 에픽 파일 |
| `bmad-check-implementation-readiness` | 구현 전 검증 게이트 | PASS/CONCERNS/FAIL 판정 |

## Phase 4: 구현

스토리 하나씩 만들어갑니다. 곧 Phase 4 전체 자동화가 출시됩니다!

| 워크플로우 | 목적 | 생성물 |
| --- | --- | --- |
| `bmad-sprint-planning` | 추적 초기화 (개발 사이클 순서 설정을 위해 프로젝트당 1회 실행) | `sprint-status.yaml` |
| `bmad-create-story` | 다음 스토리를 구현 준비 상태로 준비 | `story-[slug].md` |
| `bmad-dev-story` | 스토리 구현 | 작동하는 코드 + 테스트 |
| `bmad-code-review` | 구현 품질 검증 | 승인 또는 변경 요청 |
| `bmad-correct-course` | 스프린트 중간의 중요한 변경 사항 처리 | 업데이트된 계획 또는 방향 전환 |
| `bmad-sprint-status` | 스프린트 진행 상황 및 스토리 상태 추적 | 스프린트 상태 업데이트 |
| `bmad-retrospective` | 에픽 완료 후 회고 | 학습 사항 정리 |

## Quick Flow (병렬 트랙)

소규모의 잘 이해된 작업에서는 1-3단계를 건너뜁니다.

| 워크플로우 | 목적 | 생성물 |
| --- | --- | --- |
| `bmad-quick-dev` | 통합 퀵 플로우 — 의도 명확화, 기획, 구현, 리뷰, 발표 | `spec-*.md` + 코드 |

## 컨텍스트 관리

각 문서는 다음 단계의 컨텍스트가 됩니다. PRD는 아키텍트에게 어떤 제약이 중요한지 알려줍니다. 아키텍처는 개발자 에이전트에게 따라야 할 패턴을 알려줍니다. 스토리 파일은 구현을 위한 집중된 완전한 컨텍스트를 제공합니다. 이 구조 없이는 에이전트가 일관성 없는 결정을 내립니다.

### 프로젝트 컨텍스트

:::tip[권장 사항]
`project-context.md`를 만들어 AI 에이전트가 프로젝트의 규칙과 선호도를 따르도록 하세요. 이 파일은 프로젝트의 헌법처럼 작동하여 모든 워크플로우에서 구현 결정을 안내합니다. 이 선택적 파일은 아키텍처 작성 완료 시 생성하거나, 기존 프로젝트에서도 현재 규칙과의 일관성 유지를 위해 생성할 수 있습니다.
:::

**생성 방법:**

- **직접 작성** — 기술 스택과 구현 규칙을 담아 `_bmad-output/project-context.md` 생성
- **자동 생성** — `bmad-generate-project-context`를 실행하여 아키텍처 또는 코드베이스에서 자동 생성

[**project-context.md에 대해 자세히 알아보기**](../explanation/project-context.md)
