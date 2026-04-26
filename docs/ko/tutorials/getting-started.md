---
title: "시작하기"
description: BMad 설치 및 첫 번째 프로젝트 만들기
---

특화된 에이전트가 기획, 아키텍처, 구현 과정을 안내하는 AI 기반 워크플로우로 소프트웨어를 더 빠르게 만드세요.

## 배울 내용

- 새 프로젝트에 BMad Method 설치 및 초기화
- **BMad-Help** 사용 — 다음에 무엇을 해야 할지 알려주는 지능형 안내자
- 프로젝트 규모에 맞는 기획 트랙 선택
- 요구사항에서 작동하는 코드까지 단계별 진행
- 에이전트와 워크플로우를 효과적으로 활용

:::note[사전 요구사항]
- **Node.js 20+** — 설치 도구 실행에 필요
- **Git** — 버전 관리를 위해 권장
- **AI 기반 IDE** — Claude Code, Cursor, 또는 유사한 도구
- **프로젝트 아이디어** — 학습용으로는 간단한 것도 충분합니다
:::

:::tip[가장 쉬운 경로]
**설치** → `npx bmad-method install`
**질문** → `bmad-help what should I do first?`
**빌드** → BMad-Help가 워크플로우 단계별로 안내해 드립니다
:::

## BMad-Help: 지능형 안내자 소개

**BMad-Help는 BMad를 시작하는 가장 빠른 방법입니다.** 워크플로우나 단계를 외울 필요가 없습니다. 그냥 물어보면 BMad-Help가 다음을 해드립니다:

- **프로젝트 파악** — 이미 완료된 작업 확인
- **옵션 제시** — 설치된 모듈에 따른 선택지 안내
- **다음 단계 추천** — 첫 번째 필수 작업 포함
- **질문 답변** — "SaaS 아이디어가 있는데 어디서 시작하나요?" 같은 질문에 답변

### BMad-Help 사용 방법

AI IDE에서 스킬을 호출하여 실행하세요:

```
bmad-help
```

또는 상황에 맞는 안내를 위해 질문과 함께 사용하세요:

```
bmad-help I have an idea for a SaaS product, I already know all the features I want. where do I get started?
```

BMad-Help가 다음을 알려드립니다:
- 현재 상황에 맞는 권장 사항
- 첫 번째 필수 작업
- 이후 전체 과정

### 워크플로우도 자동으로 실행

BMad-Help는 질문에 답변하는 것만이 아닙니다. **모든 워크플로우가 끝날 때 자동으로 실행**되어 다음에 무엇을 해야 할지 정확히 알려줍니다. 추측할 필요도, 문서를 뒤질 필요도 없습니다. 다음 필수 워크플로우에 대한 명확한 안내를 바로 받을 수 있습니다.

:::tip[여기서 시작하세요]
BMad를 설치한 후 즉시 `bmad-help` 스킬을 호출하세요. 설치된 모듈을 감지하고 프로젝트에 맞는 시작점으로 안내해 드립니다.
:::

## BMad 이해하기

BMad는 특화된 AI 에이전트와 함께하는 안내형 워크플로우로 소프트웨어 개발을 돕습니다. 이 과정은 4개의 단계로 진행됩니다:

| 단계 | 이름 | 내용 |
| --- | --- | --- |
| 1 | 분석 | 브레인스토밍, 리서치, 제품 브리프 또는 PRFAQ *(선택 사항)* |
| 2 | 기획 | 요구사항 정의 (PRD 또는 스펙) |
| 3 | 해결책 설계 | 아키텍처 설계 *(BMad Method/Enterprise만 해당)* |
| 4 | 구현 | 에픽별, 스토리별로 빌드 |

**[워크플로우 맵 열기](../reference/workflow-map.md)**에서 단계, 워크플로우, 컨텍스트 관리를 탐색할 수 있습니다.

프로젝트의 복잡도에 따라 BMad는 3가지 기획 트랙을 제공합니다:

| 트랙 | 적합한 경우 | 생성되는 문서 |
| --- | --- | --- |
| **Quick Flow** | 버그 수정, 간단한 기능, 명확한 범위 (1-15 스토리) | 기술 스펙만 |
| **BMad Method** | 제품, 플랫폼, 복잡한 기능 (10-50+ 스토리) | PRD + 아키텍처 + UX |
| **Enterprise** | 컴플라이언스, 멀티 테넌트 시스템 (30+ 스토리) | PRD + 아키텍처 + 보안 + DevOps |

:::note
스토리 수는 기준이지 기준이 아닙니다. 스토리 수가 아니라 기획 필요성에 따라 트랙을 선택하세요.
:::

## 설치

프로젝트 디렉토리에서 터미널을 열고 실행하세요:

```bash
npx bmad-method install
```

기본 릴리스 채널 대신 최신 프리릴리스 빌드를 원한다면 `npx bmad-method@next install`을 사용하세요.

모듈 선택 프롬프트가 나타나면 **BMad Method**를 선택하세요.

설치 도구가 두 개의 폴더를 생성합니다:
- `_bmad/` — 에이전트, 워크플로우, 작업, 설정
- `_bmad-output/` — 지금은 비어 있지만, 여기에 산출물이 저장됩니다

:::tip[다음 단계]
프로젝트 폴더에서 AI IDE를 열고 실행하세요:

```
bmad-help
```

BMad-Help가 완료된 작업을 감지하고 다음에 무엇을 해야 할지 정확히 추천해 드립니다. "어떤 옵션이 있나요?" 또는 "SaaS 아이디어가 있는데 어디서 시작해야 하나요?" 같은 질문도 할 수 있습니다.
:::

:::note[에이전트 로드 및 워크플로우 실행 방법]
각 워크플로우에는 IDE에서 이름으로 호출하는 **스킬**이 있습니다 (예: `bmad-create-prd`). AI 도구가 `bmad-*` 이름을 인식하고 실행합니다. 에이전트를 별도로 로드할 필요가 없습니다. 일반 대화를 위해 에이전트 스킬을 직접 호출할 수도 있습니다 (예: PM 에이전트는 `bmad-agent-pm`).
:::

:::caution[새 채팅 시작]
각 워크플로우마다 항상 새 채팅을 시작하세요. 컨텍스트 제한으로 인한 문제를 방지합니다.
:::

## 1단계: 계획 수립

1-3단계를 진행합니다. **워크플로우마다 새 채팅을 사용하세요.**

:::tip[프로젝트 컨텍스트 (선택 사항)]
시작 전에 기술적 선호도와 구현 규칙을 문서화하기 위해 `project-context.md`를 만드는 것을 고려해 보세요. 이 파일은 모든 AI 에이전트가 프로젝트 전체에서 규칙을 따르도록 보장합니다.

`_bmad-output/project-context.md`에 직접 만들거나, 아키텍처 작성 후 `bmad-generate-project-context`로 생성할 수 있습니다. [자세히 알아보기](../explanation/project-context.md).
:::

### Phase 1: 분석 (선택 사항)

이 단계의 모든 워크플로우는 선택 사항입니다. [**어떤 것을 사용해야 할지 모르겠다면?**](../explanation/analysis-phase.md)
- **brainstorming** (`bmad-brainstorming`) — 안내형 아이데이션
- **research** (`bmad-market-research` / `bmad-domain-research` / `bmad-technical-research`) — 시장, 도메인, 기술 리서치
- **product-brief** (`bmad-product-brief`) — 개념이 명확할 때 권장되는 기초 문서
- **prfaq** (`bmad-prfaq`) — 제품 개념을 검증하고 다듬는 Working Backwards 방식

### Phase 2: 기획 (필수)

**BMad Method 및 Enterprise 트랙:**
1. 새 채팅에서 **PM 에이전트** (`bmad-agent-pm`) 호출
2. `bmad-create-prd` 워크플로우 실행 (`bmad-create-prd`)
3. 결과: `PRD.md`

**Quick Flow 트랙:**
- `bmad-quick-dev` 실행 — 기획과 구현을 단일 워크플로우로 처리하며, 구현 단계로 바로 넘어갑니다

:::note[UX 디자인 (선택 사항)]
프로젝트에 사용자 인터페이스가 있다면, PRD 작성 후 **UX 디자이너 에이전트** (`bmad-agent-ux-designer`)를 호출하고 UX 디자인 워크플로우 (`bmad-create-ux-design`)를 실행하세요.
:::

### Phase 3: 해결책 설계 (BMad Method/Enterprise)

**아키텍처 생성**
1. 새 채팅에서 **아키텍트 에이전트** (`bmad-agent-architect`) 호출
2. `bmad-create-architecture` 실행 (`bmad-create-architecture`)
3. 결과: 기술적 의사결정이 담긴 아키텍처 문서

**에픽 및 스토리 생성**

:::tip[V6 개선 사항]
이제 에픽과 스토리는 아키텍처 *이후에* 생성됩니다. 아키텍처 결정(데이터베이스, API 패턴, 기술 스택)이 작업 분해 방식에 직접 영향을 미치므로 더 높은 품질의 스토리가 생성됩니다.
:::

1. 새 채팅에서 **PM 에이전트** (`bmad-agent-pm`) 호출
2. `bmad-create-epics-and-stories` 실행 (`bmad-create-epics-and-stories`)
3. 워크플로우가 PRD와 아키텍처를 모두 활용하여 기술적으로 정교한 스토리를 생성합니다

**구현 준비 확인** *(강력 권장)*
1. 새 채팅에서 **아키텍트 에이전트** (`bmad-agent-architect`) 호출
2. `bmad-check-implementation-readiness` 실행 (`bmad-check-implementation-readiness`)
3. 모든 기획 문서 간의 일관성을 검증합니다

## 2단계: 프로젝트 빌드

기획이 완료되면 구현으로 넘어갑니다. **각 워크플로우는 새 채팅에서 실행해야 합니다.**

### 스프린트 플래닝 초기화

**개발자 에이전트** (`bmad-agent-dev`)를 호출하고 `bmad-sprint-planning`을 실행합니다 (`bmad-sprint-planning`). 이렇게 하면 모든 에픽과 스토리를 추적하는 `sprint-status.yaml`이 생성됩니다.

### 빌드 사이클

각 스토리마다 새 채팅으로 다음 사이클을 반복합니다:

| 단계 | 에이전트 | 워크플로우 | 명령어 | 목적 |
| --- | --- | --- | --- | --- |
| 1 | DEV | `bmad-create-story` | `bmad-create-story` | 에픽에서 스토리 파일 생성 |
| 2 | DEV | `bmad-dev-story` | `bmad-dev-story` | 스토리 구현 |
| 3 | DEV | `bmad-code-review` | `bmad-code-review` | 품질 검증 *(권장)* |

에픽의 모든 스토리를 완료한 후, **개발자 에이전트** (`bmad-agent-dev`)를 호출하고 `bmad-retrospective`를 실행합니다 (`bmad-retrospective`).

## 완료한 것들

BMad로 빌드하는 기본 토대를 익혔습니다:

- BMad 설치 및 IDE 설정
- 선택한 기획 트랙으로 프로젝트 초기화
- 기획 문서 생성 (PRD, 아키텍처, 에픽 & 스토리)
- 구현을 위한 빌드 사이클 이해

이제 프로젝트에 다음이 생겼습니다:

```text
your-project/
├── _bmad/                                   # BMad 설정
├── _bmad-output/
│   ├── planning-artifacts/
│   │   ├── PRD.md                           # 요구사항 문서
│   │   ├── architecture.md                  # 기술적 의사결정
│   │   └── epics/                           # 에픽 및 스토리 파일
│   ├── implementation-artifacts/
│   │   └── sprint-status.yaml               # 스프린트 추적
│   └── project-context.md                   # 구현 규칙 (선택 사항)
└── ...
```

## 빠른 참고

| 워크플로우 | 명령어 | 에이전트 | 목적 |
| --- | --- | --- | --- |
| **`bmad-help`** ⭐ | `bmad-help` | 모든 에이전트 | **지능형 안내자 — 무엇이든 물어보세요!** |
| `bmad-create-prd` | `bmad-create-prd` | PM | 제품 요구사항 문서 작성 |
| `bmad-create-architecture` | `bmad-create-architecture` | Architect | 아키텍처 문서 작성 |
| `bmad-generate-project-context` | `bmad-generate-project-context` | Analyst | 프로젝트 컨텍스트 파일 생성 |
| `bmad-create-epics-and-stories` | `bmad-create-epics-and-stories` | PM | PRD를 에픽으로 분해 |
| `bmad-check-implementation-readiness` | `bmad-check-implementation-readiness` | Architect | 기획 일관성 검증 |
| `bmad-sprint-planning` | `bmad-sprint-planning` | DEV | 스프린트 추적 초기화 |
| `bmad-create-story` | `bmad-create-story` | DEV | 스토리 파일 생성 |
| `bmad-dev-story` | `bmad-dev-story` | DEV | 스토리 구현 |
| `bmad-code-review` | `bmad-code-review` | DEV | 구현 코드 리뷰 |

## 자주 묻는 질문

**아키텍처가 항상 필요한가요?**
BMad Method와 Enterprise 트랙에만 필요합니다. Quick Flow는 스펙에서 구현으로 바로 넘어갑니다.

**나중에 계획을 변경할 수 있나요?**
네. `bmad-correct-course` 워크플로우가 구현 중간의 범위 변경을 처리합니다.

**먼저 브레인스토밍을 하고 싶다면?**
분석가 에이전트 (`bmad-agent-analyst`)를 호출하고 PRD 작성 전에 `bmad-brainstorming`을 실행하세요 (`bmad-brainstorming`).

**반드시 순서를 따라야 하나요?**
꼭 그렇지는 않습니다. 흐름을 익히고 나면 위의 빠른 참고표를 사용해 워크플로우를 직접 실행할 수 있습니다.

## 도움 받기

:::tip[첫 번째 선택: BMad-Help]
**언제든지 `bmad-help`를 호출하세요** — 막혔을 때 가장 빠른 해결책입니다. 무엇이든 물어보세요:
- "설치 후 무엇을 해야 하나요?"
- "워크플로우 X에서 막혔어요"
- "Y에 어떤 옵션이 있나요?"
- "지금까지 진행된 것을 보여주세요"

BMad-Help가 프로젝트를 파악하고, 완료된 작업을 감지하고, 다음에 정확히 무엇을 해야 할지 알려드립니다.
:::

- **워크플로우 진행 중** — 에이전트가 질문과 설명으로 안내합니다
- **커뮤니티** — [Discord](https://discord.gg/gk8jAdXWmj) (#bmad-method-help, #report-bugs-and-issues)

## 핵심 요약

:::tip[기억하세요]
- **`bmad-help`로 시작** — 프로젝트와 옵션을 아는 지능형 안내자
- **항상 새 채팅 사용** — 각 워크플로우마다 새 채팅 시작
- **트랙이 중요** — Quick Flow는 `bmad-quick-dev` 사용; Method/Enterprise는 PRD와 아키텍처 필요
- **BMad-Help 자동 실행** — 모든 워크플로우가 다음 단계 안내와 함께 종료됩니다
:::

시작할 준비가 됐나요? BMad를 설치하고, `bmad-help`를 호출하고, 지능형 안내자의 안내에 따르세요.
