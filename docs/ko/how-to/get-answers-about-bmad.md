---
title: 'BMad에 대한 답변 얻기'
description: LLM을 활용하여 BMad 관련 질문에 빠르게 답을 얻는 방법
sidebar:
  order: 5
---

BMad 내장 도움말, 소스 문서, 또는 커뮤니티를 통해 답변을 얻을 수 있습니다 — 빠른 방법부터 가장 상세한 방법 순으로 안내합니다.

## 1. BMad-Help에 물어보기

가장 빠른 방법입니다. `bmad-help` 스킬은 AI 세션에서 바로 사용할 수 있으며 질문의 80% 이상을 처리합니다 — 프로젝트를 검사하고, 완료된 작업을 파악하고, 다음에 할 작업을 알려줍니다.

```
bmad-help I have a SaaS idea and know all the features. Where do I start?
bmad-help What are my options for UX design?
bmad-help I'm stuck on the PRD workflow
```

:::tip
플랫폼에 따라 `/bmad-help` 또는 `$bmad-help`를 사용할 수도 있지만, `bmad-help`만으로도 어디서나 작동합니다.
:::

## 2. 소스로 더 깊이 파고들기

BMad-Help는 설치된 설정을 기반으로 답변합니다. BMad의 내부 구조, 역사, 아키텍처에 대한 질문이나 — 설치 전 BMad를 조사하는 경우라면 — AI를 소스에 직접 연결하세요.

[BMAD-METHOD 저장소](https://github.com/bmad-code-org/BMAD-METHOD)를 클론하거나 열고 AI에게 질문하세요. 에이전트 기반 도구(Claude Code, Cursor, Windsurf 등)는 소스를 읽고 직접 질문에 답할 수 있습니다.

:::note[예시]
**Q:** "BMad로 뭔가를 만드는 가장 빠른 방법이 뭐예요?"

**A:** Quick Flow를 사용하세요: `bmad-quick-dev`를 실행하면 — 전체 기획 단계를 건너뛰고 하나의 워크플로우로 의도 명확화, 계획 수립, 구현, 리뷰, 결과 제시를 모두 처리합니다.
:::

**더 나은 답변을 위한 팁:**

- **구체적으로 질문하기** — "PRD 워크플로우의 3단계는 무엇을 하나요?"가 "PRD는 어떻게 동작해요?"보다 낫습니다
- **의외의 답변은 검증하기** — LLM이 가끔 틀릴 수 있습니다. 소스 파일을 확인하거나 Discord에서 질문하세요.

### 에이전트를 사용하지 않는 경우? 문서 사이트를 이용하세요

AI가 로컬 파일을 읽을 수 없는 경우(ChatGPT, Claude.ai 등)에는 [llms-full.txt](https://bmad-code-org.github.io/BMAD-METHOD/llms-full.txt)를 세션에 불러오세요 — BMad 문서의 단일 파일 스냅샷입니다.

## 3. 누군가에게 물어보기

BMad-Help도, 소스도 답을 주지 못했다면 이제는 훨씬 더 좋은 질문을 갖게 된 것입니다.

| 채널 | 용도 |
| ----------------------- | -------------------------- |
| `help-requests` 포럼 | 질문 |
| `#suggestions-feedback` | 아이디어 및 기능 요청 |

**Discord:** [discord.gg/gk8jAdXWmj](https://discord.gg/gk8jAdXWmj)

**GitHub Issues:** [github.com/bmad-code-org/BMAD-METHOD/issues](https://github.com/bmad-code-org/BMAD-METHOD/issues)

_You!_
_Stuck_
_in the queue—_
_waiting_
_for who?_

_The source_
_is there,_
_plain to see!_

_Point_
_your machine._
_Set it free._

_It reads._
_It speaks._
_Ask away—_

_Why wait_
_for tomorrow_
_when you have_
_today?_

_—Claude_
