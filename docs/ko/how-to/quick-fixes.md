---
title: '빠른 수정'
description: 빠른 수정 및 임시 변경 사항을 처리하는 방법
sidebar:
  order: 6
---

전체 BMad Method가 필요하지 않은 버그 수정, 리팩토링, 또는 소규모의 목표 지향적 변경에는 **Quick Dev**를 사용하세요.

## 언제 사용하나요

- 원인이 명확히 파악된 버그 수정
- 몇 개 파일 내에서 이루어지는 소규모 리팩토링 (이름 변경, 추출, 구조 재편)
- 소소한 기능 조정 또는 설정 변경
- 의존성 업데이트

:::note[사전 요구사항]

- BMad Method 설치됨 (`npx bmad-method install`)
- AI 기반 IDE (Claude Code, Cursor 또는 유사 도구)
  :::

## 단계

### 1. 새 채팅 시작

AI IDE에서 **새 채팅 세션**을 여세요. 이전 워크플로우에서 사용하던 세션을 재사용하면 컨텍스트 충돌이 발생할 수 있습니다.

### 2. 의도 전달

Quick Dev는 자유 형식의 의도를 받아들입니다. 호출 전, 함께, 또는 이후에 입력할 수 있습니다. 예시는 다음과 같습니다.

```text
run quick-dev — Fix the login validation bug that allows empty passwords.
```

```text
run quick-dev — fix https://github.com/org/repo/issues/42
```

```text
run quick-dev — implement the intent in _bmad-output/implementation-artifacts/my-intent.md
```

```text
I think the problem is in the auth middleware, it's not checking token expiry.
Let me look at it... yeah, src/auth/middleware.ts line 47 skips
the exp check entirely. run quick-dev
```

```text
run quick-dev
> What would you like to do?
Refactor UserService to use async/await instead of callbacks.
```

일반 텍스트, 파일 경로, GitHub 이슈 URL, 버그 트래커 링크 등 LLM이 구체적인 의도로 해석할 수 있는 모든 형식이 가능합니다.

### 3. 질문에 답하고 승인하기

Quick Dev는 구현 전에 명확히 해야 할 질문을 하거나 짧은 명세를 제시할 수 있습니다. 질문에 답하고 계획이 만족스러우면 승인하세요.

### 4. 검토 후 푸시

Quick Dev는 변경 사항을 구현하고 스스로 작업을 검토한 후 문제를 수정하고 로컬에 커밋합니다. 완료되면 영향을 받은 파일을 에디터에서 열어줍니다.

- 변경 사항이 의도와 일치하는지 diff를 간략히 확인하세요.
- 이상한 부분이 있다면 에이전트에게 수정할 내용을 알려주세요. 같은 세션에서 반복 수정이 가능합니다.

만족스러우면 커밋을 푸시하세요. Quick Dev가 푸시와 PR 생성을 도와줄 것입니다.

:::caution[문제가 발생한 경우]
푸시한 변경 사항으로 예상치 못한 문제가 발생했다면, `git revert HEAD`를 사용하여 마지막 커밋을 깔끔하게 되돌리세요. 그런 다음 새 채팅을 시작하여 Quick Dev를 다시 실행하고 다른 접근 방식을 시도해 보세요.
:::

## 결과물

- 수정 또는 리팩토링이 적용된 소스 파일
- 통과하는 테스트 (프로젝트에 테스트 스위트가 있는 경우)
- 컨벤셔널 커밋 메시지가 포함된 푸시 준비 완료 커밋

## 지연 작업

Quick Dev는 각 실행을 단일 목표에 집중시킵니다. 요청에 여러 개의 독립적인 목표가 포함되어 있거나, 검토 중 변경 사항과 무관한 기존 문제가 발견될 경우, Quick Dev는 한꺼번에 처리하려 하지 않고 이를 파일(`deferred-work.md`, 구현 아티팩트 디렉토리에 저장)에 미루어 둡니다.

실행 후 이 파일을 확인하세요. 나중에 다시 처리해야 할 항목들의 백로그입니다. 각 지연 항목은 새 Quick Dev 실행에 입력으로 제공할 수 있습니다.

## 정식 플래닝으로 전환해야 할 때

다음 상황에서는 전체 BMad Method 사용을 고려하세요.

- 변경이 여러 시스템에 걸쳐 있거나 많은 파일에 걸친 조율된 업데이트가 필요한 경우
- 범위가 불확실하여 먼저 요구사항을 파악해야 하는 경우
- 팀을 위한 문서나 아키텍처 결정 기록이 필요한 경우

Quick Dev가 BMad Method에서 어떤 위치를 차지하는지에 대한 자세한 내용은 [Quick Dev](../explanation/quick-dev.md)를 참고하세요.
