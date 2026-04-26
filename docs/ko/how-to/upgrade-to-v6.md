---
title: 'v6으로 업그레이드하는 방법'
description: BMad v4에서 v6으로 마이그레이션하기
sidebar:
  order: 4
---

BMad 설치 도구를 사용하여 v4에서 v6으로 업그레이드하세요. 레거시 설치를 자동으로 감지하고 마이그레이션을 지원합니다.

## 언제 사용하나요

- BMad v4가 설치되어 있는 경우 (`.bmad-method` 폴더 존재)
- 새로운 v6 아키텍처로 마이그레이션하려는 경우
- 기존 플래닝 아티팩트를 보존해야 하는 경우

:::note[사전 요구사항]

- Node.js 20+
- 기존 BMad v4 설치
  :::

## 단계

### 1. 설치 도구 실행

[설치 도구 안내](./install-bmad.md)를 따르세요.

### 2. 레거시 설치 처리

v4가 감지되면 다음 중 하나를 선택할 수 있습니다.

- 설치 도구가 `.bmad-method`를 백업하고 제거하도록 허용
- 종료 후 직접 정리 작업 수행

BMad 메서드 폴더를 다른 이름으로 지정한 경우에는 해당 폴더를 직접 제거해야 합니다.

### 3. IDE 스킬 정리

레거시 v4 IDE 명령어/스킬을 직접 제거하세요. 예를 들어 Claude Code를 사용하는 경우, bmad로 시작하는 중첩 폴더를 찾아 제거하세요.

- `.claude/commands/`

새 v6 스킬은 다음 경로에 설치됩니다.

- `.claude/skills/`

### 4. 플래닝 아티팩트 마이그레이션

**플래닝 문서(Brief/PRD/UX/아키텍처)가 있는 경우:**

설명적인 이름을 사용하여 `_bmad-output/planning-artifacts/`로 이동하세요.

- PRD 문서: 파일명에 `PRD` 포함
- 해당 문서에 따라 `brief`, `architecture`, `ux-design` 포함
- 샤딩된 문서는 명명된 하위 폴더에 배치 가능

**플래닝이 진행 중인 경우:** v6 워크플로우로 다시 시작하는 것을 고려해 보세요. 기존 문서를 입력으로 활용하세요. 웹 검색과 IDE 플랜 모드가 적용된 새로운 점진적 검색 워크플로우가 더 나은 결과를 제공합니다.

### 5. 진행 중인 개발 마이그레이션

스토리가 생성되었거나 구현 중인 경우:

1. v6 설치 완료
2. `epics.md` 또는 `epics/epic*.md`를 `_bmad-output/planning-artifacts/`에 배치
3. 개발자의 `bmad-sprint-planning` 워크플로우 실행
4. 에이전트에게 이미 완료된 에픽/스토리를 알려주기

## 결과물

**v6 통합 구조:**

```text
your-project/
├── _bmad/               # 단일 설치 폴더
│   ├── _config/         # 사용자 커스터마이징
│   │   └── agents/      # 에이전트 커스터마이징 파일
│   ├── core/            # 범용 핵심 프레임워크
│   ├── bmm/             # BMad Method 모듈
│   ├── bmb/             # BMad Builder
│   └── cis/             # Creative Intelligence Suite
└── _bmad-output/        # 출력 폴더 (v4의 doc 폴더)
```

## 모듈 마이그레이션

| v4 모듈                       | v6 상태                                      |
| ----------------------------- | -------------------------------------------- |
| `.bmad-2d-phaser-game-dev`    | BMGD 모듈에 통합됨                           |
| `.bmad-2d-unity-game-dev`     | BMGD 모듈에 통합됨                           |
| `.bmad-godot-game-dev`        | BMGD 모듈에 통합됨                           |
| `.bmad-infrastructure-devops` | 더 이상 사용되지 않음 — 새 DevOps 에이전트 출시 예정 |
| `.bmad-creative-writing`      | 적용되지 않음 — 새 v6 모듈 출시 예정        |

## 주요 변경 사항

| 개념          | v4                                    | v6                                   |
| ------------- | ------------------------------------- | ------------------------------------ |
| **Core**      | `_bmad-core`가 실제로는 BMad Method였음 | `_bmad/core/`는 범용 프레임워크      |
| **Method**    | `_bmad-method`                        | `_bmad/bmm/`                         |
| **Config**    | 파일을 직접 수정                      | 모듈별 `config.yaml`                 |
| **Documents** | 샤딩 여부에 따른 설정 필요             | 완전히 유연하고 자동 스캔            |
