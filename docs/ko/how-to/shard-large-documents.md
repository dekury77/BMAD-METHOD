---
title: '큰 문서 샤딩하기'
description: 더 나은 컨텍스트 관리를 위해 큰 마크다운 파일을 작은 파일들로 분할하는 방법
sidebar:
  order: 10
---

컨텍스트 관리를 위해 큰 마크다운 파일을 작고 체계적인 파일들로 분할해야 할 경우 `bmad-shard-doc` 도구를 사용하세요.

:::caution[더 이상 권장되지 않음]
이 방법은 더 이상 권장되지 않습니다. 워크플로우가 업데이트되고 대부분의 주요 LLM과 도구가 서브프로세스를 지원하게 되면 곧 불필요해질 것입니다.
:::

## 언제 사용하나요

선택한 도구/모델 조합이 필요한 문서를 입력으로 모두 로드하고 읽지 못하는 문제가 발생하는 경우에만 사용하세요.

## 문서 샤딩이란?

문서 샤딩은 큰 마크다운 파일을 2단계 헤딩(`## Heading`)을 기준으로 작고 체계적인 파일들로 분할합니다.

### 구조

```text
샤딩 전:
_bmad-output/planning-artifacts/
└── PRD.md (50k 토큰에 달하는 큰 파일)

샤딩 후:
_bmad-output/planning-artifacts/
└── prd/
    ├── index.md                    # 설명이 포함된 목차
    ├── overview.md                 # 섹션 1
    ├── user-requirements.md        # 섹션 2
    ├── technical-requirements.md   # 섹션 3
    └── ...                         # 추가 섹션
```

## 단계

### 1. Shard-Doc 도구 실행

```bash
/bmad-shard-doc
```

### 2. 대화형 프로세스 진행

```text
Agent: Which document would you like to shard?
User: docs/PRD.md

Agent: Default destination: docs/prd/
       Accept default? [y/n]
User: y

Agent: Sharding PRD.md...
       ✓ Created 12 section files
       ✓ Generated index.md
       ✓ Complete!
```

## 워크플로우 검색 방식

BMad 워크플로우는 **이중 검색 시스템**을 사용합니다.

1. **전체 문서 우선 시도** — `document-name.md` 파일 탐색
2. **샤딩된 버전 확인** — `document-name/index.md` 파일 탐색
3. **우선순위 규칙** — 두 가지가 모두 존재하면 전체 문서가 우선 적용됩니다. 샤딩된 버전을 사용하려면 전체 문서 파일을 삭제하세요.

## 워크플로우 지원 범위

모든 BMM 워크플로우는 다음 두 형식을 모두 지원합니다.

- 전체 문서
- 샤딩된 문서
- 자동 감지
- 사용자에게 투명하게 처리
