---
title: '조직을 위한 BMad 확장하기'
description: 포크 없이 BMad를 재구성하는 5가지 커스터마이즈 패턴 — 에이전트 전체 규칙, 워크플로우 관례, 외부 게시, 템플릿 교체, 에이전트 로스터 변경
sidebar:
  order: 9
---

BMad의 커스터마이즈 영역을 사용하면 설치된 파일을 편집하거나 스킬을 포크하지 않고도 조직의 동작을 재구성할 수 있습니다. 이 가이드는 대부분의 엔터프라이즈 요구를 충족하는 5가지 레시피를 안내합니다.

:::note[사전 조건]

- 프로젝트에 BMad가 설치되어 있어야 합니다 ([BMad 설치하기](./install-bmad.md) 참고)
- 커스터마이즈 모델에 익숙해야 합니다 ([BMad 커스터마이즈하기](./customize-bmad.md) 참고)
- PATH에 Python 3.11+ 가 있어야 합니다 (리졸버 실행용 — 표준 라이브러리만 사용, `pip install` 불필요)
:::

:::tip[레시피 적용 방법]
아래의 **스킬별 레시피** (레시피 1–4)는 `bmad-customize` 스킬을 실행하고 의도를 설명하면 적용할 수 있습니다 — 스킬이 올바른 영역을 선택하고, 오버라이드 파일을 작성하고, 병합 결과를 검증합니다. 레시피 5(에이전트 로스터에 대한 중앙 설정 오버라이드)는 스킬 v1에서는 지원하지 않으며, 직접 작성해야 합니다. 여기 있는 레시피가 *무엇을* 오버라이드할지에 대한 진실의 원천이며, `bmad-customize`가 에이전트/워크플로우 영역에서 *어떻게* 할지를 처리합니다.
:::

## 3단계 멘탈 모델

레시피를 선택하기 전에 오버라이드가 어디에 위치하는지 파악하세요:

| 레이어 | 오버라이드 위치 | 범위 |
|---|---|---|
| **에이전트** (예: Amelia, Mary, John) | `_bmad/custom/bmad-agent-{role}.toml`의 `[agent]` 섹션 | 에이전트가 **디스패치하는 모든 워크플로우**에 페르소나와 함께 적용 |
| **워크플로우** (예: product-brief, create-prd) | `_bmad/custom/{workflow-name}.toml`의 `[workflow]` 섹션 | 해당 워크플로우 실행에만 적용 |
| **중앙 설정** | `_bmad/custom/config.toml`의 `[agents.*]`, `[core]`, `[modules.*]` | 에이전트 로스터(party-mode, retrospective, elicitation에 참여하는 인원), 전체 저장소에서 공유되는 설치 설정 |

경험칙: 개발자가 개발 작업을 할 때마다 규칙이 적용되어야 한다면 **dev 에이전트**를 커스터마이즈하세요. 누군가 제품 브리프를 작성할 때만 적용된다면 **product-brief 워크플로우**를 커스터마이즈하세요. *방에 있는 인원*을 변경하는 것이라면(에이전트 이름 바꾸기, 커스텀 목소리 추가, 공유 산출물 경로 강제) **중앙 설정**을 편집하세요.

## 레시피 1: 에이전트가 디스패치하는 모든 워크플로우에 걸쳐 에이전트 형성하기

**사용 사례:** 도구 사용과 외부 시스템 통합을 표준화하여 에이전트를 통해 디스패치되는 모든 워크플로우가 해당 동작을 상속하도록 합니다. 가장 파급력이 큰 패턴입니다.

**예시: Amelia(dev 에이전트)가 라이브러리 문서를 항상 Context7을 통해 조회하고, 에픽 목록에서 스토리를 찾지 못하면 Linear로 대체합니다.**

```toml
# _bmad/custom/bmad-agent-dev.toml

[agent]

# 모든 활성화에 적용됩니다. dev-story, quick-dev,
# create-story, code-review, qa-generate 등 Amelia가 디스패치하는 모든 스킬에 전달됩니다.
persistent_facts = [
  "For any library documentation lookup (React, TypeScript, Zod, Prisma, etc.), call the context7 MCP tool (`mcp__context7__resolve_library_id` then `mcp__context7__get_library_docs`) before relying on training-data knowledge. Up-to-date docs trump memorized APIs.",
  "When a story reference isn't found in {planning_artifacts}/epics-and-stories.md, search Linear via `mcp__linear__search_issues` using the story ID or title before asking the user to clarify. If Linear returns a match, treat it as the authoritative story source.",
]
```

**왜 효과적인가:** 두 문장이 조직 내 모든 dev 워크플로우를 재구성하며, 워크플로우별 중복이 없고 소스 변경도 없습니다. 저장소를 클론하는 모든 신규 개발자가 자동으로 관례를 상속받습니다.

**팀 파일 vs 개인 파일:**
- `bmad-agent-dev.toml`: git에 커밋됨; 전체 팀에 적용
- `bmad-agent-dev.user.toml`: gitignored; 개인 설정이 그 위에 레이어됨

## 레시피 2: 특정 워크플로우 내에서 조직 관례 강제하기

**사용 사례:** 규정 준수, 감사, 또는 하위 소비자 요구 사항을 충족하도록 워크플로우 출력의 *내용*을 형성합니다.

**예시: 모든 제품 브리프에 규정 준수 필드가 포함되어야 하고, 에이전트가 조직의 게시 관례를 알아야 합니다.**

```toml
# _bmad/custom/bmad-product-brief.toml

[workflow]

persistent_facts = [
  "Every brief must include an 'Owner' field, a 'Target Release' field, and a 'Security Review Status' field.",
  "Non-commercial briefs (internal tools, research projects) must still include a user-value section, but can omit market differentiation.",
  "file:{project-root}/docs/enterprise/brief-publishing-conventions.md",
]
```

**동작 방식:** 사실(facts)은 워크플로우 활성화의 3단계에서 로드됩니다. 에이전트가 브리프를 작성할 때 필수 필드와 엔터프라이즈 관례 문서를 알게 됩니다. 기본 제공값(`file:{project-root}/**/project-context.md`)은 추가(append)이므로 계속 로드됩니다.

## 레시피 3: 완료된 출력을 외부 시스템에 게시하기

**사용 사례:** 워크플로우가 출력을 생성하면 엔터프라이즈 시스템(Confluence, Notion, SharePoint)에 자동으로 게시하고, 후속 작업(Jira, Linear, Asana)을 열도록 합니다.

**예시: 브리프를 Confluence에 자동 게시하고, 선택적으로 Jira 에픽 생성을 제안합니다.**

```toml
# _bmad/custom/bmad-product-brief.toml

[workflow]

# 종료 훅. 스칼라 오버라이드가 빈 기본값을 완전히 교체합니다.
on_complete = """
Publish and offer follow-up:

1. Read the finalized brief file path from the prior step.
2. Call `mcp__atlassian__confluence_create_page` with:
   - space: "PRODUCT"
   - parent: "Product Briefs"
   - title: the brief's title
   - body: the brief's markdown contents
   Capture the returned page URL.
3. Tell the user: "Brief published to Confluence: <url>".
4. Ask: "Want me to open a Jira epic for this brief now?"
5. If yes, call `mcp__atlassian__jira_create_issue` with:
   - type: "Epic"
   - project: "PROD"
   - summary: the brief's title
   - description: a short summary plus a link back to the Confluence page.
   Report the epic key and URL.
6. If no, exit cleanly.

If either MCP tool fails, report the failure, print the brief path,
and ask the user to publish manually.
"""
```

**`activation_steps_append`가 아닌 `on_complete`를 사용하는 이유:** `on_complete`는 워크플로우의 주요 출력이 작성된 후 종료 단계에 정확히 한 번 실행됩니다. 바로 그 시점이 산출물을 게시할 올바른 순간입니다. `activation_steps_append`는 워크플로우가 작업을 수행하기 전에 매번 활성화 시 실행됩니다.

**트레이드오프:**
- **Confluence 게시는 비파괴적**이며 완료 시 항상 실행됩니다
- **Jira 에픽 생성은 전체 팀에 가시적**이며 스프린트 플래닝 신호를 시작하므로 사용자 확인을 게이트로 두세요
- **우아한 폴백:** MCP 도구가 실패하면 출력을 조용히 버리지 않고 사용자에게 넘깁니다

## 레시피 4: 자체 출력 템플릿으로 교체하기

**사용 사례:** 기본 출력 구조가 조직의 예상 형식과 맞지 않거나, 같은 저장소의 다른 조직이 각기 다른 템플릿을 필요로 할 때.

**예시: product-brief 워크플로우가 엔터프라이즈 소유 템플릿을 사용하도록 지정합니다.**

```toml
# _bmad/custom/bmad-product-brief.toml

[workflow]
brief_template = "{project-root}/docs/enterprise/brief-template.md"
```

**동작 방식:** 워크플로우의 `customize.toml`은 `brief_template = "resources/brief-template.md"` (스킬 루트에서 해석되는 단순 경로)로 제공됩니다. 오버라이드는 `{project-root}` 아래 파일을 가리키므로 에이전트가 4단계에서 기본 제공 템플릿 대신 자체 템플릿을 읽게 됩니다.

**템플릿 작성 팁:**
- 템플릿은 오버라이드 파일과 함께 버전이 관리될 수 있도록 `{project-root}/docs/` 또는 `{project-root}/_bmad/custom/templates/`에 두세요
- 제공된 템플릿과 동일한 구조 관례(섹션 헤딩, frontmatter)를 사용하세요. 에이전트는 있는 내용에 맞게 적응합니다
- 멀티 조직 저장소의 경우 `.user.toml`을 사용하면 커밋된 팀 파일을 건드리지 않고 각 팀이 자신의 템플릿을 지정할 수 있습니다

## 레시피 5: 에이전트 로스터 커스터마이즈하기

**사용 사례:** 소스를 편집하거나 포크하지 않고 `bmad-party-mode`, `bmad-retrospective`, `bmad-advanced-elicitation` 같은 로스터 기반 스킬의 *방에 있는 인원*을 변경합니다. 다음은 세 가지 일반적인 변형입니다.

### 5a. BMad 에이전트를 조직 전체에서 리브랜딩하기

모든 실제 에이전트에는 설치 도구가 `module.yaml`에서 합성하는 디스크립터가 있습니다. 이를 오버라이드하면 모든 로스터 소비자에서 목소리와 프레이밍이 바뀝니다:

```toml
# _bmad/custom/config.toml (커밋됨 — 모든 개발자에게 적용)

[agents.bmad-agent-analyst]
description = "Mary the Regulatory-Aware Business Analyst — channels Porter and Minto, but lives and breathes FDA audit trails. Speaks like a forensic investigator presenting a case file."
```

party-mode가 새 설명으로 Mary를 소환합니다. 분석가 활성화 자체는 정상적으로 실행됩니다 — Mary의 동작은 그녀의 스킬별 `customize.toml`에 있기 때문입니다. 이 오버라이드는 **외부 스킬이 그녀를 인식하고 소개하는 방식**을 변경하며, 내부 동작은 바꾸지 않습니다.

### 5b. 가상 또는 커스텀 에이전트 추가하기

로스터 기반 기능에는 전체 디스크립터만으로 충분하며, 스킬 폴더가 필요 없습니다. party-mode나 브레인스토밍 세션에서 다양한 개성을 위해 유용합니다:

```toml
# _bmad/custom/config.user.toml (개인용 — gitignored)

[agents.spock]
team = "startrek"
name = "Commander Spock"
title = "Science Officer"
icon = "🖖"
description = "Logic first, emotion suppressed. Begins observations with 'Fascinating.' Never rounds up. Counterpoint to any argument that relies on gut instinct."

[agents.mccoy]
team = "startrek"
name = "Dr. Leonard McCoy"
title = "Chief Medical Officer"
icon = "⚕️"
description = "Country doctor's warmth, short fuse. 'Dammit Jim, I'm a doctor not a ___.' Ethics-driven counterweight to Spock."
```

party-mode에 "Enterprise 승무원을 초대해줘"라고 하면, `team = "startrek"`으로 필터링하여 Spock과 McCoy를 해당 디스크립터로 소환합니다. 원한다면 실제 BMad 에이전트(Mary, Amelia)도 같은 자리에 함께 초대할 수 있습니다.

### 5c. 팀 설치 설정 고정하기

설치 도구는 각 개발자에게 `planning_artifacts` 경로 같은 값을 묻습니다. 조직에서 팀 전체가 동일한 답변을 사용해야 할 때는 중앙 설정에 고정하세요 — 개발자의 로컬 프롬프트 답변이 해석 시 오버라이드됩니다:

```toml
# _bmad/custom/config.toml

[modules.bmm]
planning_artifacts = "{project-root}/shared/planning"
implementation_artifacts = "{project-root}/shared/implementation"

[core]
document_output_language = "English"
```

`user_name`, `communication_language`, `user_skill_level` 같은 개인 설정은 각 개발자 자신의 `_bmad/config.user.toml`에 유지합니다. 팀 파일은 이런 설정을 건드리면 안 됩니다.

**왜 중앙 설정이 스킬별 customize.toml과 다른가:** 스킬별 파일은 에이전트가 활성화될 때 *하나의* 에이전트가 어떻게 동작할지를 형성합니다. 중앙 설정은 로스터 소비자가 *필드를 바라볼 때 보게 되는 것*을 형성합니다: 어떤 에이전트가 존재하고, 이름은 무엇이며, 어떤 팀에 속하고, 전체 저장소가 동의하는 공유 설치 설정이 무엇인지. 두 영역은 서로 다른 역할을 합니다.

## IDE 세션 파일에서 전역 규칙 강화하기

BMad 커스터마이징은 스킬이 활성화될 때 로드됩니다. 많은 IDE 도구들은 스킬이 실행되기 전 **모든 세션 시작 시** 전역 명령 파일을 로드합니다(`CLAUDE.md`, `AGENTS.md`, `.cursor/rules/`, `.github/copilot-instructions.md` 등). BMad 스킬 밖에서도 유지되어야 하는 규칙은 거기서도 다시 명시하세요.

**이중으로 명시할 시점:**
- 일반 채팅(활성 스킬 없음)에서도 규칙이 적용되어야 할 만큼 중요한 경우
- 학습 데이터 기본값이 모델을 이탈시킬 수 있어 이중 강제가 필요한 경우
- 세션 파일을 부풀리지 않고 반복할 수 있을 만큼 간결한 규칙인 경우

**예시: 레시피 1의 dev-agent 규칙을 강화하는 저장소 `CLAUDE.md`의 한 줄.**

```markdown
<!-- Any file-read of library docs goes through the context7 MCP tool
(`mcp__context7__resolve_library_id` then `mcp__context7__get_library_docs`)
before relying on training-data knowledge. -->
```

한 문장이 매 세션에 로드됩니다. `bmad-agent-dev.toml` 커스터마이징과 짝을 이루어 Amelia의 워크플로우 내부와 어시스턴트와의 임시 채팅 모두에서 규칙이 적용됩니다. 각 레이어가 자체 범위를 담당합니다:

| 레이어 | 범위 | 용도 |
|---|---|---|
| IDE 세션 파일 (`CLAUDE.md` / `AGENTS.md`) | 모든 세션, 스킬 활성화 전 | BMad 외부에서도 유지되어야 하는 짧고 보편적인 규칙 |
| BMad 에이전트 커스터마이즈 | 에이전트가 디스패치하는 모든 워크플로우 | 에이전트 페르소나별 동작 |
| BMad 워크플로우 커스터마이즈 | 하나의 워크플로우 실행 | 워크플로우별 출력 형태, 게시 훅, 템플릿 |
| BMad 중앙 설정 | 에이전트 로스터 + 공유 설치 설정 | 방에 있는 인원과 팀이 사용하는 공유 경로 |

IDE 파일은 **간결하게** 유지하세요. 잘 선택된 열두 줄이 방대한 목록보다 효과적입니다. 모델은 매 턴마다 이를 읽으며, 잡음은 신호를 밀어냅니다.

## 레시피 조합하기

5가지 레시피는 모두 조합할 수 있습니다. 현실적인 엔터프라이즈 `bmad-product-brief` 오버라이드는 하나의 파일에 `persistent_facts`(레시피 2), `on_complete`(레시피 3), `brief_template`(레시피 4)을 함께 설정할 수 있습니다. 에이전트 수준 규칙(레시피 1)은 에이전트 이름의 별도 파일에, 중앙 설정(레시피 5)은 공유 로스터와 팀 설정을 고정하며, 네 가지 모두 병렬로 적용됩니다.

```toml
# _bmad/custom/bmad-product-brief.toml (워크플로우 수준)

[workflow]
persistent_facts = ["..."]
brief_template = "{project-root}/docs/enterprise/brief-template.md"
on_complete = """ ... """
```

```toml
# _bmad/custom/bmad-agent-analyst.toml (에이전트 수준 — Mary가 product-brief를 디스패치)

[agent]
persistent_facts = ["Always include a 'Regulatory Review' section when the domain involves healthcare, finance, or children's data."]
```

결과: Mary가 페르소나 활성화 시 규정 리뷰 규칙을 로드합니다. 사용자가 product-brief 메뉴 항목을 선택하면 워크플로우가 자체 관례를 추가로 로드하고, 엔터프라이즈 템플릿에 작성하며, 완료 시 Confluence에 게시합니다. 모든 레이어가 기여하며, BMad 소스를 편집할 필요가 없습니다.

## 문제 해결

**오버라이드가 적용되지 않나요?** 파일이 `_bmad/custom/` 아래 정확한 스킬 디렉터리 이름으로 있는지 확인하세요 (예: `bmad-agent-dev.toml`, `bmad-dev.toml`이 아님). [BMad 커스터마이즈하기](./customize-bmad.md#troubleshooting)를 참고하세요.

**MCP 도구 이름을 모르시나요?** 현재 세션에서 MCP 서버가 노출하는 정확한 이름을 사용하세요. 확실하지 않으면 Claude Code에 사용 가능한 MCP 도구를 나열해달라고 하세요. MCP 서버가 연결되어 있지 않으면 `persistent_facts`나 `on_complete`에 하드코딩된 이름이 동작하지 않습니다.

**패턴이 내 설정에 맞지 않나요?** 위의 레시피는 예시입니다. 기본 메커니즘(3단계 병합, 구조적 규칙, 에이전트-워크플로우 범위)은 훨씬 더 다양한 패턴을 지원합니다. 필요에 맞게 조합하세요.
