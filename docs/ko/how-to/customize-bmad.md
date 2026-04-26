---
title: 'BMad 커스터마이즈하기'
description: 업데이트 호환성을 유지하면서 에이전트와 워크플로우를 커스터마이즈합니다
sidebar:
  order: 8
---

설치된 파일을 수정하지 않고도 에이전트 페르소나를 조정하고, 도메인 컨텍스트를 주입하고, 기능을 추가하고, 워크플로우 동작을 설정할 수 있습니다. 커스터마이징 내용은 모든 업데이트에도 유지됩니다.

:::tip[TOML을 직접 작성하고 싶지 않으신가요? `bmad-customize`를 사용하세요]
`bmad-customize` 스킬은 이 문서에서 설명하는 **스킬별 에이전트/워크플로우 오버라이드 영역**을 위한 안내형 작성 도우미입니다. 설치된 항목 중 커스터마이즈 가능한 것들을 스캔하고, 목적에 맞는 올바른 영역(에이전트 vs 워크플로우)을 선택하도록 도와주며, 오버라이드 파일을 직접 작성하고 병합 결과를 검증합니다. 중앙 설정 오버라이드(`_bmad/custom/config.toml`)는 v1에서는 스킬 범위 밖이므로 아래 중앙 설정 섹션을 참고하여 직접 작성해야 합니다. 스킬별 변경을 원할 때마다 이 스킬을 실행하세요. 이 문서는 각 영역이 무엇을 제공하고 병합이 어떻게 동작하는지에 대한 참고자료입니다.
:::

## 언제 사용하나요

- 에이전트의 성격이나 커뮤니케이션 스타일을 변경하고 싶을 때
- 에이전트가 지속적으로 기억해야 할 사실을 부여하고 싶을 때 (예: "우리 조직은 AWS만 사용합니다")
- 에이전트가 매 세션마다 실행해야 하는 절차적 시작 단계를 추가하고 싶을 때
- 직접 만든 스킬이나 프롬프트를 트리거하는 커스텀 메뉴 항목을 추가하고 싶을 때
- 팀 공유 커스터마이징을 git에 커밋하고, 그 위에 개인 설정을 레이어로 올리고 싶을 때

:::note[사전 조건]

- 프로젝트에 BMad가 설치되어 있어야 합니다 ([BMad 설치하기](./install-bmad.md) 참고)
- PATH에 Python 3.11+ 가 있어야 합니다 (리졸버 스크립트 실행용 — 표준 라이브러리의 `tomllib`을 사용하며, `pip install`, `uv`, virtualenv 불필요)
- TOML 파일을 편집할 텍스트 편집기
:::

## 동작 원리

커스터마이즈 가능한 모든 스킬은 기본값이 담긴 `customize.toml` 파일과 함께 제공됩니다. 이 파일은 스킬의 전체 커스터마이즈 가능 영역을 정의합니다 — 무엇을 바꿀 수 있는지 확인하려면 이 파일을 읽으세요. 이 파일은 절대 직접 편집하지 않습니다. 대신 변경하고 싶은 필드만 포함한 희소(sparse) 오버라이드 파일을 만듭니다.

### 3단계 오버라이드 모델

```text
우선순위 1 (최우선): _bmad/custom/{skill-name}.user.toml  (개인용, gitignored)
우선순위 2:          _bmad/custom/{skill-name}.toml        (팀/조직용, 커밋됨)
우선순위 3 (기본값): 스킬 자체의 customize.toml           (기본값)
```

`_bmad/custom/` 폴더는 처음에 비어 있습니다. 누군가 커스터마이즈를 할 때만 파일이 생깁니다.

### 병합 규칙 (필드 이름이 아닌 형태(shape) 기준)

리졸버는 4가지 구조적 규칙을 적용합니다. 필드 이름은 특별 처리하지 않으며, 동작은 순전히 값의 형태로 결정됩니다:

| 형태 | 규칙 |
|---|---|
| 스칼라 (string, int, bool, float) | 오버라이드가 우선 |
| 테이블 | 깊은 병합 (재귀적으로 이 규칙 적용) |
| 모든 항목이 **동일한** 식별자 필드를 공유하는 테이블 배열 (모든 항목에 `code`가 있거나, 모든 항목에 `id`가 있는 경우) | 해당 키로 병합 — 일치하는 키는 **제자리에서 교체**, 새 키는 **추가** |
| 그 외 배열 (스칼라; 식별자 없는 테이블; `code`와 `id`가 항목 간에 혼재하는 배열) | **추가(append)** — 기본 항목 먼저, 그다음 팀 항목, 마지막으로 사용자 항목 |

**제거 메커니즘은 없습니다.** 오버라이드로 기본 항목을 삭제할 수 없습니다. 기본 메뉴 항목을 억제해야 한다면 `code`로 해당 항목을 오버라이드하되 no-op 설명이나 프롬프트로 대체하세요. 배열을 더 깊이 재구성해야 한다면 스킬을 포크(fork)하세요.

**`code` / `id` 관례.** BMad는 `code` (짧은 식별자, 예: `"BP"` 또는 `"R1"`)와 `id` (더 긴 안정적 식별자)를 테이블 배열의 병합 키로 사용합니다. 키 기반 교체가 필요한 커스텀 테이블 배열을 작성할 때는 **하나의** 관례(`code` 또는 `id` 중 하나)를 선택하고 배열 전체에 일관되게 적용하세요. 일부 항목에는 `code`, 다른 항목에는 `id`를 혼용하면 추가(append) 방식으로 폴백됩니다 — 리졸버가 어떤 키로 병합할지 추측하지 않습니다.

### 일부 에이전트 필드는 읽기 전용입니다

`agent.name`과 `agent.title`은 소스 메타데이터로 `customize.toml`에 있지만, 에이전트의 SKILL.md는 런타임에 이를 읽지 않습니다 — 하드코딩된 정체성이기 때문입니다. 오버라이드 파일에 `name = "Bob"`을 넣어도 효과가 없습니다. 실제로 다른 이름의 에이전트가 필요하다면 스킬 폴더를 복사하고 이름을 바꿔 커스텀 스킬로 배포하세요.

## 단계별 진행

### 1. 스킬의 커스터마이즈 가능 영역 확인하기

스킬이 설치된 디렉터리에서 해당 스킬의 `customize.toml`을 확인하세요. 예를 들어, PM 에이전트의 경우:

```text
.claude/skills/bmad-agent-pm/customize.toml
```

(경로는 IDE에 따라 다릅니다 — Cursor는 `.cursor/skills/`, Cline은 `.cline/skills/` 등을 사용합니다.)

이 파일이 정식 스키마입니다. 여기서 보이는 모든 필드를 커스터마이즈할 수 있습니다 (위에서 언급한 읽기 전용 식별자 필드 제외).

### 2. 오버라이드 파일 만들기

프로젝트 루트에 `_bmad/custom/` 디렉터리가 없다면 만드세요. 그리고 스킬 이름으로 파일을 만드세요:

```text
_bmad/custom/
  bmad-agent-pm.toml        # 팀 오버라이드 (git에 커밋됨)
  bmad-agent-pm.user.toml   # 개인 설정 (gitignored)
```

:::caution[`customize.toml` 전체를 복사하지 마세요]
오버라이드 파일은 **희소(sparse)** 해야 합니다. 변경하는 필드만 포함하고 나머지는 작성하지 않으세요. 생략한 필드는 하위 레이어(팀은 기본값, 사용자는 팀 또는 기본값)에서 자동으로 상속됩니다.

`customize.toml` 전체를 오버라이드 파일로 복사하면 오히려 해롭습니다. 다음 업데이트에서 새로운 기본값이 추가되더라도 오버라이드 파일이 이전 값을 고정시킵니다. 매 릴리스마다 묵묵히 동기화가 어긋나게 됩니다.
:::

**예시 — 아이콘 변경 및 원칙 하나 추가**:

```toml
# _bmad/custom/bmad-agent-pm.toml
# 변경하는 필드만 작성합니다. 나머지는 상속됩니다.

[agent]
icon = "🏥"
principles = [
  "Ship nothing that can't pass an FDA audit.",
]
```

이렇게 하면 새 원칙이 기본값에 추가(기본 원칙은 그대로 유지)되고, 아이콘은 교체됩니다. 다른 모든 필드는 기본값 그대로입니다.

### 3. 필요한 부분 커스터마이즈하기

아래 모든 예시는 BMad의 플랫(flat) 에이전트 스키마를 기준으로 합니다. 필드는 `[agent]` 바로 아래에 위치합니다 — 중첩된 `metadata`나 `persona` 서브테이블이 없습니다.

**스칼라 (icon, role, identity, communication_style).** 스칼라 오버라이드가 우선 적용됩니다. 변경하는 필드만 설정하면 됩니다:

```toml
# _bmad/custom/bmad-agent-pm.toml

[agent]
icon = "🏥"
role = "Drives product discovery for a regulated healthcare domain."
communication_style = "Precise, regulatory-aware, asks compliance-shaped questions early."
```

**지속 사실, 원칙, 활성화 훅 (추가 배열).** 아래 네 가지 배열은 모두 추가(append) 전용입니다. 팀 항목은 기본값 뒤에, 사용자 항목은 맨 마지막에 실행됩니다.

```toml
[agent]
# 에이전트가 세션 전체에서 기억하는 정적 사실 — 조직 규칙, 도메인
# 상수, 사용자 설정. 런타임 메모리 사이드카와는 다릅니다.
#
# 각 항목은 리터럴 문장이거나, 파일 내용을 사실로 불러오는
# `file:` 참조(글로브 패턴 지원)입니다.
persistent_facts = [
  "Our org is AWS-only -- do not propose GCP or Azure.",
  "All PRDs require legal sign-off before engineering kickoff.",
  "Target users are clinicians, not patients -- frame examples accordingly.",
  "file:{project-root}/docs/compliance/hipaa-overview.md",
  "file:{project-root}/_bmad/custom/company-glossary.md",
]

# 에이전트의 가치 체계에 추가됩니다
principles = [
  "Ship nothing that can't pass an FDA audit.",
  "User value first, compliance always.",
]

# 표준 활성화(페르소나, persistent_facts, 설정, 인사) 이전에 실행됩니다.
# 사전 로드, 규정 준수 확인 등 에이전트가 자신을 소개하기 전에
# 컨텍스트에 있어야 하는 작업에 사용합니다.
activation_steps_prepend = [
  "Scan {project-root}/docs/compliance/ and load any HIPAA-related documents as context.",
]

# 인사 이후, 메뉴 이전에 실행됩니다. 사용자를 확인한 후
# 한 번만 이루어지면 되는 무거운 컨텍스트 설정에 사용합니다.
activation_steps_append = [
  "Read {project-root}/_bmad/custom/company-glossary.md if it exists.",
]
```

**두 훅의 역할은 다릅니다.** Prepend는 인사 전에 실행되어 에이전트가 인사를 개인화하는 데 필요한 컨텍스트를 먼저 불러올 수 있습니다. Append는 인사 후에 실행되므로 무거운 스캔이 완료되는 동안 사용자가 빈 터미널만 바라보지 않아도 됩니다.

**메뉴 커스터마이즈 (`code`로 병합).** 메뉴는 테이블 배열입니다. 각 항목에 `code` 필드(BMad 관례)가 있으므로 리졸버는 코드 기준으로 병합합니다. 일치하는 코드는 제자리에서 교체되고, 새 코드는 추가됩니다.

TOML 테이블 배열 문법은 각 항목에 `[[agent.menu]]`를 사용합니다:

```toml
# 기존 CE 항목을 커스텀 스킬로 교체
[[agent.menu]]
code = "CE"
description = "Create Epics using our delivery framework"
skill = "custom-create-epics"

# 새 항목 추가 (RC 코드는 기본값에 없음)
[[agent.menu]]
code = "RC"
description = "Run compliance pre-check"
prompt = """
Read {project-root}/_bmad/custom/compliance-checklist.md
and scan all documents in {planning_artifacts} against it.
Report any gaps and cite the relevant regulatory section.
"""
```

각 메뉴 항목은 `skill` (등록된 스킬 호출) 또는 `prompt` (텍스트 직접 실행) 중 정확히 하나를 가집니다. 오버라이드에 나열되지 않은 항목은 기본값을 유지합니다.

**파일 참조.** 필드 텍스트에서 파일을 가리켜야 할 때(`persistent_facts`, `activation_steps_prepend`/`activation_steps_append`, 또는 메뉴 항목의 `prompt`)는 `{project-root}`를 루트로 하는 전체 경로를 사용하세요. 파일이 `_bmad/custom/` 안에 있더라도 전체 경로를 명시하세요: `{project-root}/_bmad/custom/info.md`. 에이전트가 런타임에 `{project-root}`를 해석합니다.

### 4. 개인 설정 vs 팀 설정

**팀 파일** (`bmad-agent-pm.toml`): git에 커밋됩니다. 조직 전체에 공유됩니다. 규정 준수 규칙, 회사 페르소나, 커스텀 기능에 사용합니다.

**개인 파일** (`bmad-agent-pm.user.toml`): 자동으로 gitignored됩니다. 어조 조정, 개인 워크플로우 설정, 에이전트가 기억해야 할 비공개 사실에 사용합니다.

```toml
# _bmad/custom/bmad-agent-pm.user.toml

[agent]
persistent_facts = [
  "Always include a rough complexity estimate (low/medium/high) when presenting options.",
]
```

## 해석(Resolution) 동작 원리

활성화 시 에이전트의 SKILL.md는 3단계 병합을 수행하고 결과 블록을 JSON으로 반환하는 공유 Python 스크립트를 실행합니다. 이 스크립트는 Python 표준 라이브러리의 `tomllib` 모듈을 사용하므로(외부 의존성 없음) 일반 `python3`으로 충분합니다:

```bash
python3 {project-root}/_bmad/scripts/resolve_customization.py \
  --skill {skill-root} \
  --key agent
```

**요구 사항**: Python 3.11+ (이전 버전에는 `tomllib`이 없습니다). `pip install`, `uv`, virtualenv 불필요. `python3 --version`으로 확인하세요. 일부 플랫폼(Homebrew 없는 macOS, Ubuntu 22.04)은 `python3`이 기본적으로 3.10 이하일 수 있으므로 3.11+를 별도로 설치해야 할 수 있습니다.

`--skill`은 스킬이 설치된 디렉터리(`customize.toml`이 있는 곳)를 가리킵니다. 스킬 이름은 디렉터리의 basename에서 파생되며, 스크립트는 `_bmad/custom/{skill-name}.toml`과 `{skill-name}.user.toml`을 자동으로 찾습니다.

유용한 호출 예시:

```bash
# 전체 agent 블록 해석
python3 {project-root}/_bmad/scripts/resolve_customization.py \
  --skill /abs/path/to/bmad-agent-pm \
  --key agent

# 단일 필드 해석
python3 {project-root}/_bmad/scripts/resolve_customization.py \
  --skill /abs/path/to/bmad-agent-pm \
  --key agent.icon

# 전체 덤프
python3 {project-root}/_bmad/scripts/resolve_customization.py \
  --skill /abs/path/to/bmad-agent-pm
```

출력은 항상 JSON입니다. 특정 플랫폼에서 스크립트를 사용할 수 없는 경우 SKILL.md는 에이전트에게 세 TOML 파일을 직접 읽고 같은 병합 규칙을 적용하도록 안내합니다.

## 워크플로우 커스터마이즈

워크플로우(`bmad-product-brief` 같은 다단계 프로세스를 구동하는 스킬)도 에이전트와 동일한 오버라이드 메커니즘을 사용합니다. 커스터마이즈 가능 영역은 `[agent]` 대신 `[workflow]` 아래에 있습니다:

```toml
# _bmad/custom/bmad-product-brief.toml

[workflow]
# 에이전트 변형과 동일한 prepend/append 시맨틱 — 워크플로우 자체의
# 활성화 단계 이전과 이후에 실행됩니다. 오버라이드는 기본값에 추가됩니다.
activation_steps_prepend = [
  "Load {project-root}/docs/product/north-star-principles.md as context.",
]

activation_steps_append = []

# 에이전트 변형과 동일한 리터럴-또는-file: 시맨틱. 워크플로우 실행
# 전반에 걸쳐 기초 컨텍스트로 로드됩니다.
persistent_facts = [
  "All briefs must include an explicit regulatory-risk section.",
  "file:{project-root}/docs/compliance/product-brief-checklist.md",
]

# 스칼라: 워크플로우가 주요 출력을 완료하면 한 번 실행됩니다. 오버라이드가 우선합니다.
on_complete = "Summarize the brief in three bullets and offer to email it via the gws-gmail-send skill."
```

같은 필드 관례가 에이전트/워크플로우 경계를 넘어 적용됩니다: `activation_steps_prepend`/`activation_steps_append`, `persistent_facts`(`file:` 참조 포함), 키 병합을 위한 `code`/`id`가 있는 메뉴 스타일 `[[…]]` 테이블. 리졸버는 최상위 키에 관계없이 동일한 4가지 구조적 규칙을 적용합니다. SKILL.md 참조는 네임스페이스를 따릅니다: `{workflow.activation_steps_prepend}`, `{workflow.persistent_facts}`, `{workflow.on_complete}`. 워크플로우가 노출하는 추가 필드(출력 경로, 토글, 리뷰 설정, 단계 플래그)도 동일한 형태 기반 병합 규칙을 따릅니다. 커스터마이즈 가능한 내용은 워크플로우의 `customize.toml`을 참고하세요.

### 활성화 순서

커스터마이즈 가능한 워크플로우는 훅이 언제 실행되는지 정확히 알 수 있도록 고정된 순서로 활성화됩니다:

1. `[workflow]` 블록 해석 (기본값 → 팀 → 사용자 병합)
2. `activation_steps_prepend` 순서대로 실행
3. `persistent_facts`를 실행의 기초 컨텍스트로 로드
4. 설정(`_bmad/bmm/config.yaml`) 로드 및 표준 변수(프로젝트 이름, 언어, 경로, 날짜) 해석
5. 사용자에게 인사
6. `activation_steps_append` 순서대로 실행

6단계 이후 워크플로우 본문이 시작됩니다. 인사를 개인화하기 위해 컨텍스트가 미리 필요한 경우 `activation_steps_prepend`를 사용하고, 설정이 무거워서 인사를 먼저 보여주고 싶을 때는 `activation_steps_append`를 사용하세요.

### 이번 초기 버전의 범위

커스터마이즈는 점진적으로 확대되고 있습니다. 위에서 설명한 필드들 — `activation_steps_prepend`, `activation_steps_append`, `persistent_facts`, `on_complete` — 은 모든 커스터마이즈 가능한 워크플로우가 공통으로 제공하는 **기본 영역**이며, 버전 간 안정적으로 유지됩니다. 이 필드들은 현재 폭넓은 제어를 제공합니다: 사전/사후 단계 주입, 기초 컨텍스트 고정, 후속 작업 트리거.

시간이 지나면서 개별 워크플로우는 해당 워크플로우가 실제로 수행하는 작업에 맞춘 **더 세밀한 커스터마이즈 포인트**를 노출할 것입니다 — 단계별 토글, 단계 플래그, 출력 템플릿 경로, 리뷰 게이트 등. 이런 기능이 추가될 때도 기존 기본 필드를 대체하지 않고 그 위에 쌓이므로, 오늘 작성한 커스터마이징은 계속 동작합니다.

아직 노출되지 않은 세밀한 설정이 필요하다면 `activation_steps_*`와 `persistent_facts`로 동작을 유도하거나, 원하는 커스터마이즈 포인트를 설명하는 이슈를 열어주세요 — 그런 요청이 다음에 추가할 필드를 결정하는 데 도움이 됩니다.

## 중앙 설정

스킬별 `customize.toml`은 **깊은 동작 제어**를 담당합니다(훅, 메뉴, persistent_facts, 단일 에이전트 또는 워크플로우의 페르소나 오버라이드). 별도의 영역은 **범횡적 상태** — 설치 응답 및 `bmad-party-mode`, `bmad-retrospective`, `bmad-advanced-elicitation` 같은 외부 스킬이 사용하는 에이전트 로스터 — 를 담당합니다. 이 영역은 프로젝트 루트의 4개 TOML 파일에 있습니다:

```text
_bmad/config.toml               (설치 도구 소유)  팀 범위:   설치 응답 + 에이전트 로스터
_bmad/config.user.toml          (설치 도구 소유)  사용자 범위: user_name, language, skill level
_bmad/custom/config.toml        (직접 작성)       팀 오버라이드 (git에 커밋됨)
_bmad/custom/config.user.toml   (직접 작성)       개인 오버라이드 (gitignored)
```

### 4단계 병합

```text
우선순위 1 (최우선): _bmad/custom/config.user.toml
우선순위 2:          _bmad/custom/config.toml
우선순위 3:          _bmad/config.user.toml
우선순위 4 (기본값): _bmad/config.toml
```

스킬별 커스터마이즈와 동일한 구조적 규칙이 적용됩니다(스칼라는 오버라이드, 테이블은 깊은 병합, `code`/`id` 키 배열은 키로 병합, 그 외 배열은 추가).

### 무엇이 어디에 있나요

설치 도구는 `module.yaml`의 각 프롬프트에 선언된 `scope:`에 따라 응답을 분류합니다:

- `[core]` 및 `[modules.<code>]` 섹션 — 설치 응답. 범위가 `team`이면 `_bmad/config.toml`에, `user`이면 `_bmad/config.user.toml`에 저장됩니다.
- `[agents.<code>]` — 각 모듈의 `module.yaml` `agents:` 블록에서 추출한 에이전트 요약 정보(code, name, title, icon, description, team). 항상 팀 범위입니다.

### 편집 규칙

- `_bmad/config.toml`과 `_bmad/config.user.toml`은 **설치 흐름에서 수집된 응답을 기반으로 매번 설치 시 재생성**됩니다. 읽기 전용 출력으로 취급하세요 — 직접 편집한 내용은 다음 설치 시 덮어써집니다. 설치 응답을 영구적으로 변경하려면 설치 도구를 다시 실행하거나(이전 응답을 기본값으로 기억합니다) `_bmad/custom/config.toml`에서 값을 섀도(shadow)하세요.
- `_bmad/custom/config.toml`과 `_bmad/custom/config.user.toml`은 **설치 도구가 절대 건드리지 않습니다**. 커스텀 에이전트, 에이전트 디스크립터 오버라이드, 팀 강제 설정, 설치 응답과 무관하게 고정하고 싶은 값을 위한 올바른 영역입니다.

### 예시 — 에이전트 리브랜딩

```toml
# _bmad/custom/config.toml (git에 커밋됨, 모든 개발자에게 적용)

[agents.bmad-agent-pm]
description = "Healthcare PM — regulatory-aware, stakeholder-driven, FDA-shaped questions first."
icon = "🏥"
```

리졸버가 설치 도구가 작성한 `[agents.bmad-agent-pm]` 위에 병합합니다. `bmad-party-mode`와 다른 로스터 소비자들이 자동으로 새 설명을 사용합니다.

### 예시 — 가상 에이전트 추가

```toml
# _bmad/custom/config.user.toml (개인용, gitignored)

[agents.kirk]
team = "startrek"
name = "Captain James T. Kirk"
title = "Starship Captain"
icon = "🖖"
description = "Bold, rule-bending commander. Speaks in dramatic pauses. Thinks aloud about the weight of command."
```

스킬 폴더가 필요 없습니다 — 요약 정보만으로도 party-mode가 Kirk를 하나의 목소리로 소환할 수 있습니다. `team` 필드로 필터링하여 Enterprise 승무원만 원탁 토론에 초대할 수 있습니다.

### 예시 — 모듈 설치 설정 오버라이드

```toml
# _bmad/custom/config.toml

[modules.bmm]
planning_artifacts = "/shared/org-planning-artifacts"
```

각 개발자가 로컬 설치 시 입력한 값보다 오버라이드가 우선 적용됩니다. 팀 관례를 고정하는 데 유용합니다.

### 어떤 영역을 사용해야 하나요

| 필요한 작업 | 사용할 영역 |
|---|---|
| 모든 개발 워크플로우에 MCP 도구 호출 추가 | 스킬별: `_bmad/custom/bmad-agent-dev.toml` `persistent_facts` |
| 에이전트에 메뉴 항목 추가 | 스킬별: `_bmad/custom/bmad-agent-{role}.toml` `[[agent.menu]]` |
| 워크플로우의 출력 템플릿 교체 | 스킬별: `_bmad/custom/{workflow}.toml` 스칼라 오버라이드 |
| 에이전트의 공개 디스크립터 리브랜딩 | **중앙**: `_bmad/custom/config.toml` `[agents.<code>]` |
| 로스터에 커스텀 또는 가상 에이전트 추가 | **중앙**: `_bmad/custom/config.*.toml` 새 `[agents.<code>]` 항목 |
| 팀 강제 설치 설정 고정 | **중앙**: `_bmad/custom/config.toml` `[modules.<code>]` 또는 `[core]` |

필요에 따라 같은 프로젝트에서 두 영역을 모두 사용할 수 있습니다.

## 실제 예시

엔터프라이즈 지향 레시피(디스패치하는 모든 워크플로우에 걸쳐 에이전트 형성, 조직 관례 강제, Confluence와 Jira에 출력 게시, 에이전트 로스터 커스터마이즈, 자체 출력 템플릿 적용)는 [조직을 위한 BMad 확장하기](./expand-bmad-for-your-org.md)를 참고하세요.

## 문제 해결

**커스터마이즈가 적용되지 않나요?**

- 파일이 `_bmad/custom/`에 올바른 스킬 이름으로 있는지 확인하세요
- TOML 문법을 확인하세요: 문자열은 따옴표로 감싸야 하고, 테이블 헤더는 `[section]`, 테이블 배열은 `[[section]]`을 사용하며, 테이블의 스칼라나 배열 키는 해당 테이블의 `[[서브테이블]]` 앞에 위치해야 합니다
- 에이전트의 경우 커스터마이즈는 `[agent]` 아래에 있어야 합니다 — 해당 헤더 아래에 작성된 필드는 다른 테이블 헤더가 시작될 때까지 `agent`에 속합니다
- `agent.name`과 `agent.title`은 읽기 전용이며, 오버라이드해도 효과가 없습니다

**업데이트로 커스터마이즈가 깨졌나요?**

- `customize.toml` 전체를 오버라이드 파일로 복사했나요? **그렇게 하지 마세요.** 오버라이드 파일에는 변경하는 필드만 포함해야 합니다. 전체 복사본은 이전 기본값을 고정시키고 매 릴리스마다 묵묵히 어긋나게 됩니다. 오버라이드를 변경분(delta)만 남기도록 정리하세요.

**커스터마이즈 가능한 항목을 확인하고 싶나요?**

- `bmad-customize` 스킬을 실행하세요 — 프로젝트에 설치된 커스터마이즈 가능한 모든 스킬을 열거하고, 이미 오버라이드가 있는 것을 표시하며, 추가 또는 업데이트 과정을 안내합니다
- 또는 스킬의 `customize.toml`을 직접 읽으세요 — 거기 있는 모든 필드를 커스터마이즈할 수 있습니다 (`name`과 `title` 제외)

**초기화하고 싶나요?**

- `_bmad/custom/`에서 오버라이드 파일을 삭제하세요 — 스킬이 내장 기본값으로 돌아갑니다
