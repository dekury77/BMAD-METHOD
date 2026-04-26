---
title: 'BMad 설치하기'
description: 로컬 개발, 팀, CI를 위한 BMad 설치, 업데이트, 버전 고정 방법
sidebar:
  order: 1
---

`npx bmad-method install`로 프로젝트에 BMad를 설정합니다. 하나의 명령어로 최초 설치, 업그레이드, 채널 전환, 스크립트 CI 실행을 모두 처리합니다. 이 페이지에서 모든 내용을 다룹니다.

## 언제 사용하나요

- BMad로 새 프로젝트 시작 시
- 기존 설치에서 모듈 추가 또는 제거 시
- 모듈을 main-HEAD로 전환하거나 특정 릴리스에 고정 시
- CI 파이프라인, Dockerfile, 엔터프라이즈 배포를 위한 설치 스크립트 작성 시

:::note[사전 조건]

- **Node.js** 20+ (설치 도구 실행에 필요)
- **Git** (외부 모듈 클론에 필요)
- **AI 도구** (Claude Code 또는 Cursor 등) — 없다면 `--tools none`으로 설치 가능

:::

## 첫 번째 설치 (빠른 방법)

```bash
npx bmad-method install
```

대화형 흐름에서 5가지를 묻습니다:

1. 설치 디렉터리 (기본값: 현재 작업 디렉터리)
2. 설치할 모듈 (core, bmm, bmb, cis, gds, tea 체크박스)
3. **"준비가 됐나요 (전부 안정 버전)?"** — Yes를 선택하면 모든 외부 모듈의 최신 릴리스 태그로 설치
4. 통합할 AI 도구/IDE (claude-code, cursor 등)
5. 모듈별 설정 (이름, 언어, 출력 폴더)

기본값을 수락하면 선택한 도구에 맞게 설정된 모든 모듈의 최신 안정 릴리스로 설치됩니다.

:::tip[최신 프리릴리스를 원하시나요?]

```bash
npx bmad-method@next install
```

프리릴리스 설치 도구를 실행하며, 최신 core와 bmm 스냅샷이 포함됩니다. 변경이 잦지만 개발과 릴리스 사이의 지연이 줄어듭니다.
:::

## 특정 버전 선택하기

디스크에 설치되는 내용을 결정하는 두 가지 독립적인 축이 있습니다.

### 축 1: 외부 모듈 채널

모든 외부 모듈 — bmb, cis, gds, tea, 커뮤니티 모듈 포함 — 은 세 가지 채널 중 하나로 설치됩니다:

| 채널 | 설치되는 내용 | 대상 사용자 |
| ------------------ | ---------------------------------------------------------------------------- | --------------------------------------- |
| `stable` (기본값) | 가장 높은 릴리스 semver 태그. `v2.0.0-alpha.1` 같은 프리릴리스는 제외. | 대부분의 사용자 |
| `next` | 설치 시점의 main 브랜치 HEAD | 기여자, 얼리 어답터 |
| `pinned` | 직접 지정한 특정 태그 | 엔터프라이즈 설치, CI 재현성 |

채널은 모듈별로 설정합니다. bmb는 `next`로, cis는 `stable`로 유지하는 등 아래 플래그로 자유롭게 조합할 수 있습니다.

### 축 2: 설치 도구 바이너리 버전

`bmad-method` npm 패키지 자체에는 두 가지 dist-tag가 있습니다:

| 명령어 | 설치되는 내용 |
| ------------------------------------- | ----------------------------------------------------------------- |
| `npx bmad-method install` (`@latest`) | 최신 안정 설치 도구 릴리스 |
| `npx bmad-method@next install` | 최신 프리릴리스 설치 도구, main에 푸시할 때마다 자동 게시됨 |

**설치 도구 바이너리가 core와 bmm 버전을 결정합니다.** 이 두 모듈은 별도 저장소에서 클론되는 대신 설치 도구 패키지에 번들로 포함됩니다.

### core와 bmm에 별도 채널이 없는 이유

실행한 설치 도구 바이너리에 고정되어 있습니다:

- `npx bmad-method install` → 최신 안정 core와 bmm
- `npx bmad-method@next install` → 프리릴리스 core와 bmm
- `node /path/to/local-checkout/tools/installer/bmad-cli.js install` → 로컬 체크아웃의 내용

`--pin bmm=v6.3.0`과 `--next=bmm`은 번들 모듈에 효과가 없으며, 설치 도구가 시도 시 경고합니다. 향후 릴리스에서 bmm이 설치 도구 패키지에서 분리될 예정이며, 그때는 bmb처럼 채널 선택기를 갖게 됩니다.

## 기존 설치 업데이트하기

이미 `_bmad/`가 있는 디렉터리에서 `npx bmad-method install`을 실행하면 메뉴가 나타납니다:

| 선택지 | 동작 |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **빠른 업데이트** | 기존 설정으로 설치를 다시 실행합니다. 파일을 새로 고치고, 패치 및 소규모 안정 업그레이드를 적용하며, 주요 업그레이드는 거부합니다. 빠르고 비대화형. |
| **설치 수정** | 전체 대화형 흐름. 모듈 추가 또는 제거, 설정 재구성, 선택적으로 기존 모듈의 채널 검토 및 전환. |

### 업그레이드 프롬프트

수정 모드가 `stable`로 설치된 모듈의 최신 안정 태그를 감지하면 차이를 분류하고 다음과 같이 묻습니다:

| 업그레이드 유형 | 예시 | 기본값 |
| ------------ | --------------- | ------- |
| 패치 | v1.7.0 → v1.7.1 | Y |
| 마이너 | v1.7.0 → v1.8.0 | Y |
| 메이저 | v1.7.0 → v2.0.0 | **N** |

메이저 업그레이드는 예상치 못했을 때 "불안정성"으로 나타나는 브레이킹 체인지가 자주 있으므로 기본값이 N입니다. 프롬프트에는 GitHub 릴리스 노트 URL이 포함되어 수락 전에 변경 내용을 확인할 수 있습니다.

`--yes` 모드에서는 패치와 마이너 업그레이드가 자동으로 적용됩니다. 메이저는 고정 상태를 유지합니다 — 비대화형으로 수락하려면 `--pin <code>=<new-tag>`를 전달하세요.

### 모듈 채널 전환하기

**대화형:** 수정 선택 → "채널 할당을 검토하시겠습니까?" **Yes** → 각 외부 모듈에서 유지, stable로 전환, next로 전환, 태그 고정 중 선택.

**플래그 사용:** 다음 섹션의 레시피가 일반적인 경우를 다룹니다.

## 헤드리스 CI 설치

### 플래그 참고자료

| 플래그 | 용도 |
| ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| `--yes`, `-y` | 모든 프롬프트 건너뜀; 플래그 값 + 기본값 수락 |
| `--directory <path>` | 이 디렉터리에 설치 (기본값: 현재 작업 디렉터리) |
| `--modules <a,b,c>` | 정확한 모듈 집합. core는 자동 추가됨. 델타가 아님 — 유지할 모든 항목을 나열. |
| `--tools <a,b>` 또는 `--tools none` | IDE/도구 선택. `none`은 도구 설정을 완전히 건너뜀. |
| `--action <type>` | `install`, `update`, 또는 `quick-update`. 기존 설치 상태에 따라 기본값이 결정됨. |
| `--custom-source <urls>` | Git URL 또는 로컬 경로에서 커스텀 모듈 설치 |
| `--channel <stable\|next>` | 모든 외부 모듈에 적용 (`--all-stable` / `--all-next`의 별칭) |
| `--all-stable` | `--channel=stable`의 별칭 |
| `--all-next` | `--channel=next`의 별칭 |
| `--next=<code>` | 하나의 모듈을 next로 설정. 반복 가능. |
| `--pin <code>=<tag>` | 하나의 모듈을 특정 태그에 고정. 반복 가능. |
| `--user-name`, `--communication-language`, `--document-output-language`, `--output-folder` | 사용자별 설정 기본값 오버라이드 |

플래그 충돌 시 우선순위: `--pin` > `--next=` > `--channel` / `--all-*` > 레지스트리 기본값(`stable`).

:::note[해석 예시]
`--all-next --pin cis=v0.2.0`은 bmb, gds, tea를 next로 설정하면서 cis는 v0.2.0에 고정합니다.
:::

### 레시피

**기본 설치 — 모든 항목 최신 안정 버전:**

```bash
npx bmad-method install --yes --modules bmm,bmb,cis --tools claude-code
```

**엔터프라이즈 고정 — 바이트 단위 재현 가능:**

```bash
npx bmad-method install --yes \
  --modules bmm,bmb,cis \
  --pin bmb=v1.7.0 --pin cis=v0.2.0 \
  --tools claude-code
```

**최신 엣지 — 외부 모듈을 main HEAD로:**

```bash
npx bmad-method install --yes --modules bmm,bmb --all-next --tools claude-code
```

**기존 설치에 모듈 추가** (나머지는 유지):

```bash
npx bmad-method install --yes --action update \
  --modules bmm,bmb,gds \
  --tools none
```

**채널 혼합 — bmb는 next, gds는 stable:**

```bash
npx bmad-method install --yes --action update \
  --modules bmm,bmb,cis,gds \
  --next=bmb \
  --tools none
```

:::caution[공유 IP에서의 API 속도 제한]
익명 GitHub API 호출은 IP당 시간당 60회로 제한됩니다. 단일 설치는 안정 태그를 해석하기 위해 외부 모듈당 API를 한 번 호출합니다. NAT 뒤 사무실, CI 러너 풀, VPN은 집합적으로 이 한도를 소진할 수 있습니다.

환경에 `GITHUB_TOKEN=<개인 액세스 토큰>`을 설정하면 계정당 시간당 5000회로 한도가 올라갑니다. 공개 저장소 읽기 권한이 있는 PAT이면 충분하며, 별도 스코프가 필요 없습니다.
:::

## 설치된 내용 확인

설치 후 `_bmad/_config/manifest.yaml`에 디스크에 있는 내용이 정확히 기록됩니다:

```yaml
modules:
  - name: bmb
    version: v1.7.0 # 태그, 또는 next의 경우 "main"
    channel: stable # stable | next | pinned
    sha: 86033fc9aeae2ca6d52c7cdb675c1f4bf17fc1c1
    source: external
    repoUrl: https://github.com/bmad-code-org/bmad-builder
```

`sha` 필드는 git 기반 모듈(external, community, URL 기반 커스텀)에 기록됩니다. 번들 모듈(core, bmm)과 로컬 경로 커스텀 모듈은 설치 도구 바이너리나 파일시스템에서 코드를 가져오므로 `sha`가 없습니다.

크로스 머신 재현성을 위해 동일한 `--modules` 명령어 재실행에 의존하지 마세요. stable 채널 설치는 **설치 시점의** 가장 높은 릴리스 태그로 해석되므로, 이후 재실행하면 그사이 릴리스된 버전으로 설치됩니다. `manifest.yaml`에 기록된 태그를 대상 머신에서 명시적인 `--pin` 플래그로 변환하세요:

```bash
npx bmad-method install --yes --modules bmb,cis \
  --pin bmb=v1.7.0 --pin cis=v0.4.2 --tools none
```

## 문제 해결

### "Could not resolve stable tag" 또는 "API rate limit exceeded"

GitHub의 시간당 60회 익명 한도에 도달했습니다. `GITHUB_TOKEN`을 설정하고 다시 시도하세요. 이미 토큰이 설정되어 있다면 만료되었거나 자체 예산에서 속도 제한이 걸린 것일 수 있습니다 — 다른 토큰을 사용하거나 시간당 리셋을 기다리세요.

### "Tag 'vX.Y.Z' not found"

`--pin`에 전달한 태그가 모듈 저장소에 존재하지 않습니다. GitHub의 해당 저장소 릴리스 페이지에서 유효한 태그를 확인하세요.

### 고정된 설치가 계속 업그레이드됨

고정된 설치는 업그레이드되지 않습니다. 빠른 업데이트는 stable 채널에서만 패치와 마이너를 적용하며, `pinned`나 `next`는 건드리지 않습니다. 고정 설치가 변경되었다면 `_bmad/_config/manifest.yaml`을 열어 확인하세요 — 플래그로 명시적으로 오버라이드하지 않는 한 `channel: pinned`와 고정된 `version` 및 `sha`가 실행 간 유지되어야 합니다.

### `--pin bmm=X`가 아무 효과가 없음

bmm은 번들 모듈입니다 — `--pin`과 `--next=`가 적용되지 않습니다. 프리릴리스 core/bmm을 원한다면 `npx bmad-method@next install`을 사용하거나, 릴리스되지 않은 변경 사항을 원한다면 bmad-bmm 저장소를 체크아웃하고 로컬에서 설치 도구를 실행하세요.
