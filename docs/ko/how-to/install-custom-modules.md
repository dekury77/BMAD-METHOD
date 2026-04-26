---
title: '커스텀 및 커뮤니티 모듈 설치하기'
description: 커뮤니티 레지스트리, Git 저장소, 또는 로컬 경로에서 서드파티 모듈을 설치합니다
sidebar:
  order: 3
---

BMad 설치 도구를 사용하여 커뮤니티 레지스트리, 서드파티 Git 저장소, 또는 로컬 파일 경로에서 모듈을 추가합니다.

## 언제 사용하나요

- BMad 레지스트리에서 커뮤니티 기여 모듈 설치 시
- 서드파티 Git 저장소(GitHub, GitLab, Bitbucket, 자체 호스팅)에서 모듈 설치 시
- BMad Builder로 개발 중인 모듈을 로컬에서 테스트 시
- 비공개 또는 자체 호스팅 Git 서버에서 모듈 설치 시

:::note[사전 조건]
[Node.js](https://nodejs.org) v20+ 및 `npx`(npm에 포함)가 필요합니다. 커스텀 및 커뮤니티 모듈은 새로 설치 시 선택하거나 기존 설치에 추가할 수 있습니다.
:::

## 커뮤니티 모듈

커뮤니티 모듈은 [BMad 플러그인 마켓플레이스](https://github.com/bmad-code-org/bmad-plugins-marketplace)에 큐레이션되어 있습니다. 카테고리별로 정리되어 있으며 안전을 위해 승인된 커밋에 고정됩니다.

### 1. 설치 도구 실행

```bash
npx bmad-method install
```

### 2. 커뮤니티 카탈로그 탐색

공식 모듈을 선택한 후 설치 도구가 묻습니다:

```
Would you like to browse community modules?
```

**Yes**를 선택하면 카탈로그 브라우저로 진입합니다. 다음을 할 수 있습니다:

- 카테고리별 탐색
- 추천 모듈 보기
- 모든 사용 가능한 모듈 보기
- 키워드로 검색

### 3. 모듈 선택

카테고리에서 모듈을 선택합니다. 설치 도구에서 설명, 버전, 신뢰 등급을 확인할 수 있습니다. 이미 설치된 모듈은 업데이트 대상으로 미리 선택됩니다.

### 4. 설치 계속하기

커뮤니티 모듈을 선택한 후 설치 도구는 커스텀 소스로 넘어가고, 이어서 도구/IDE 설정 및 나머지 설치 흐름을 진행합니다.

## 커스텀 소스 (Git URL 및 로컬 경로)

커스텀 모듈은 모든 Git 저장소 또는 로컬 디렉터리에서 가져올 수 있습니다. 설치 도구가 소스를 해석하고, 모듈 구조를 분석하고, 다른 모듈과 함께 설치합니다.

### 대화형 설치

설치 중 커뮤니티 모듈 단계 이후에 설치 도구가 묻습니다:

```
Would you like to install from a custom source (Git URL or local path)?
```

**Yes**를 선택하고 소스를 입력합니다:

| 입력 유형 | 예시 |
| --------------------- | ------------------------------------------------- |
| HTTPS URL (모든 호스트) | `https://github.com/org/repo` |
| 서브디렉터리가 있는 HTTPS URL | `https://github.com/org/repo/tree/main/my-module` |
| SSH URL | `git@github.com:org/repo.git` |
| 로컬 경로 | `/Users/me/projects/my-module` |
| 틸드 포함 로컬 경로 | `~/projects/my-module` |

설치 도구가 저장소를 클론하거나(URL의 경우) 디스크에서 직접 읽은 후(로컬 경로의 경우) 발견된 모듈을 선택지로 제시합니다.

### 비대화형 설치

명령줄에서 `--custom-source` 플래그로 커스텀 모듈을 설치합니다:

```bash
npx bmad-method install \
  --directory . \
  --custom-source /path/to/my-module \
  --tools claude-code \
  --yes
```

`--modules` 없이 `--custom-source`만 지정하면 core와 커스텀 모듈만 설치됩니다. 공식 모듈도 포함하려면 `--modules`를 추가하세요:

```bash
npx bmad-method install \
  --directory . \
  --modules bmm \
  --custom-source https://gitlab.com/myorg/my-module \
  --tools claude-code \
  --yes
```

여러 소스는 쉼표로 구분합니다:

```bash
--custom-source /path/one,https://github.com/org/repo,/path/two
```

## 모듈 발견 방식

설치 도구는 두 가지 모드로 소스에서 설치 가능한 모듈을 찾습니다:

| 모드 | 트리거 | 동작 |
| --------- | ------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| 발견(Discovery) | 소스에 `.claude-plugin/marketplace.json`이 있는 경우 | 매니페스트에서 모든 플러그인을 나열; 설치할 항목 선택 |
| 직접(Direct) | marketplace.json이 없는 경우 | 디렉터리에서 스킬(`SKILL.md`가 있는 서브디렉터리)을 스캔하여 단일 모듈로 해석 |

발견 모드는 게시된 모듈에 일반적입니다. 직접 모드는 로컬 개발 중 스킬 디렉터리를 가리킬 때 편리합니다.

:::note[`.claude-plugin/`에 대해]
`.claude-plugin/marketplace.json` 경로는 여러 AI 도구 설치 도구에서 플러그인 발견성을 위해 채택한 표준 관례입니다. Claude가 필요하지 않고, Claude API를 사용하지 않으며, 사용하는 AI 도구에 영향을 주지 않습니다. 이 파일이 있는 모든 모듈은 해당 관례를 따르는 모든 설치 도구에서 발견할 수 있습니다.
:::

## 로컬 개발 워크플로우

[BMad Builder](https://github.com/bmad-code-org/bmad-builder)로 모듈을 개발 중이라면 작업 디렉터리에서 직접 설치할 수 있습니다:

```bash
npx bmad-method install \
  --directory ~/my-project \
  --custom-source ~/my-module-repo/skills \
  --tools claude-code \
  --yes
```

로컬 소스는 캐시에 복사되지 않고 경로로 참조됩니다. 모듈 소스를 업데이트하고 재설치하면 설치 도구가 최신 변경 사항을 반영합니다.

:::caution[소스 제거]
설치 후 로컬 소스 디렉터리를 삭제해도 `_bmad/`에 설치된 모듈 파일은 유지됩니다. 소스 경로가 복원될 때까지 업데이트 시 해당 모듈은 건너뜁니다.
:::

## 설치 결과

설치 후 커스텀 모듈은 공식 모듈과 함께 `_bmad/`에 나타납니다:

```
your-project/
├── _bmad/
│   ├── core/              # 내장 core 모듈
│   ├── bmm/               # 공식 모듈 (선택된 경우)
│   ├── my-module/         # 커스텀 모듈
│   │   ├── my-skill/
│   │   │   └── SKILL.md
│   │   └── module-help.csv
│   └── _config/
│       └── manifest.yaml  # 모든 모듈, 버전, 소스 추적
└── ...
```

매니페스트에는 각 커스텀 모듈의 소스가 기록됩니다(Git 소스는 `repoUrl`, 로컬 소스는 `localPath`). 빠른 업데이트 시 소스를 다시 찾는 데 사용됩니다.

## 커스텀 모듈 업데이트

커스텀 모듈은 일반 업데이트 흐름에 참여합니다:

- **빠른 업데이트** (`--action quick-update`): 모든 모듈을 원본 소스에서 새로 고칩니다. Git 기반 모듈은 다시 가져오고, 로컬 모듈은 소스 경로에서 다시 읽습니다.
- **전체 업데이트**: 모듈 선택을 다시 실행하여 커스텀 모듈을 추가하거나 제거할 수 있습니다.

## 직접 모듈 만들기

[BMad Builder](https://github.com/bmad-code-org/bmad-builder)를 사용하여 다른 사람이 설치할 수 있는 모듈을 만드세요:

1. `bmad-module-builder`를 실행하여 모듈 구조를 스캐폴딩합니다
2. 다양한 BMad Builder 도구로 스킬, 에이전트, 워크플로우를 추가합니다
3. Git 저장소에 게시하거나 폴더 모음을 공유합니다
4. 다른 사람이 `--custom-source <your-repo-url>`로 설치합니다

발견 모드를 지원하려면 저장소 루트에 `.claude-plugin/marketplace.json`을 포함하세요(이는 Claude 전용이 아닌 크로스 도구 관례입니다). marketplace.json 형식은 [BMad Builder 문서](https://github.com/bmad-code-org/bmad-builder)를 참고하세요.

:::tip[먼저 로컬에서 테스트하기]
개발 중에는 로컬 경로로 모듈을 설치하여 Git 저장소에 게시하기 전에 빠르게 반복 작업을 할 수 있습니다.
:::
