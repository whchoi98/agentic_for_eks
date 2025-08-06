---
description: 'Update : 2025.08.06'
---

# Amazon Q Developer CLI의 Agent 활용

## 1. Custom Agent&#x20;

### 1.1 개요

Amazon Q CLI의 Custom Agent는 특정 사용 사례와 워크플로우에 맞춰 구성할 수 있는 맞춤형 AI 어시스턴트입니다.

기존의 범용 어시스턴트와 달리, 도구, 권한, 컨텍스트를 미리 설정하여 반복 작업을 자동화하고 집중도 높은 개발 환경을 제공합니다.

### 1.2. Custom Agent의 핵심 개념

#### ✅ 기본 에이전트 vs Custom Agent

<table data-header-hidden><thead><tr><th width="144.996826171875"></th><th width="268.40625"></th><th></th></tr></thead><tbody><tr><td>항목</td><td>기본 에이전트</td><td>Custom Agent</td></tr><tr><td>도구 접근</td><td>모든 도구 사용 가능</td><td>특정 도구만 허용 가능</td></tr><tr><td>권한 확인</td><td>매 작업마다 사용자 확인 필요</td><td>신뢰된 도구는 자동 실행</td></tr><tr><td>컨텍스트</td><td>수동으로 추가 필요</td><td>자동 포함 가능</td></tr><tr><td>사용자 경험</td><td>보안 중심이지만 흐름 단절</td><td>빠르고 집중된 워크플로우</td></tr></tbody></table>

#### ✅ 주요 이점

* 워크플로우 최적화: IaC 작성, 배포 자동화, 코드 리뷰 등 작업에 특화
* 작업 중단 감소: 자주 사용하는 도구는 사전 승인으로 자동 실행
* &#x20;지능형 컨텍스트: 프로젝트 문서, 구성 파일 자동 포함
* 팀 협업 최적화: 구성 공유로 일관된 개발 환경 유지
* 보안 제어: 필요한 도구만 접근 허용

### 1.3 Custom Agent 생성 및 명령어

#### ✅  Custom Agent 생성 예시

```
# 1. Amazon Q CLI 채팅 세션 시작
q chat

# 2. 기존 에이전트 목록 확인
/agent list

# 3. 새 에이전트 생성
/agent create my-agent

# 4. 새 채팅 세션에서 Custom Agent 사용
q chat --agent my-agent
```

#### ✅ 주요 명령어

| 명령어                   | 설명                   |
| --------------------- | -------------------- |
| /agent list           | 사용 가능한 모든 에이전트 목록 확인 |
| /agent create \[name] | 새 에이전트 생성 및 편집기 열기   |
| /agent schema         | JSON 구성 템플릿 확인       |

***

### 1.4  Custom Agent 구성 파일 구조

#### ✅  파일 위치

* 전역 에이전트: \~/.aws/amazonq/cli-agents/{agent-name}.json
* 프로젝트 에이전트: .amazonq/cli-agents/{agent-name}.json

#### ✅  구성 요소

* **기본 정보**

```
{
  "name": "my-agent",
  "description": "Custom Agent의 목적 설명"
}
```

* **도구 및 권한**

```
{
  "tools": ["fs_read", "use_aws", "execute_bash"],
  "allowedTools": ["fs_read", "use_aws"],
  "toolAliases": {
    "@git/git_status": "status"
  },
  "toolsSettings": {
    "use_aws": {
      "allowedServices": ["s3", "lambda"]
    }
  }
}
```

* **MCP 서버 설정**

```
{
  "mcpServers": {
    "git": {
      "command": "git-mcp-server",
      "timeout": 30000
    }
  }
}
```

* **리소스 및 컨텍스트 훅**

```
{
  "resources": [
    "file://README.md",
    "file://infrastructure/**/*.yaml"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "aws sts get-caller-identity",
        "timeout_ms": 10000
      }
    ]
  }
}
```

### 1.5 활용 예시

#### ✅ AWS 전문가용 Custom Agent

* 목적: AWS 인프라, 배포, 보안 분석 자동화

```
{
  "description": "AWS 전문가용",
  "tools": ["fs_read", "use_aws", "fs_write"],
  "allowedTools": ["fs_read", "use_aws"],
  "toolsSettings": {
    "use_aws": {
      "allowedServices": ["ec2", "lambda", "s3", "cloudformation"]
    }
  },
  "resources": ["file://README.md", "file://infrastructure/**/*.yaml"]
}
```

***

#### ✅ Git 기반 개발용 Custom Agent

* 목적: Git 명령 자동 실행, 브랜치 상태 확인

```
{
  "mcpServers": {
    "git": {
      "command": "git-mcp-server"
    }
  },
  "tools": ["fs_read", "@git"],
  "allowedTools": ["@git/git_status", "@git/git_log"],
  "toolAliases": {
    "@git/git_status": "status"
  },
  "hooks": {
    "agentSpawn": [
      { "command": "git status --porcelain" }
    ]
  }
}
```

***

#### ✅ 코드 리뷰 전용 Custom Agent

* 목적: PR 변경점 분석, 린팅, 코드 표준 확인

```
{
  "tools": ["fs_read", "execute_bash"],
  "allowedTools": ["fs_read", "execute_bash"],
  "toolsSettings": {
    "execute_bash": {
      "allowedCommands": ["git diff", "eslint", "pylint"]
    }
  },
  "resources": ["file://docs/security-guidelines.md"]
}
```

***

### 1.6  MCP 통합

* 도구 소스 통합: 내장 도구 + MCP 서버 조합
* 도구 제한: 도구 범위와 경로 제한 가능
* 훅 활용: 현재 상태 정보를 에이전트 스폰 시점에 자동 삽입
* 충돌 방지: 도구 별칭(alias)으로 이름 중복 방지

### 1.7 모범 사례

#### ✅ Custom Agent 구성 팁

1. 간단한 도구부터 시작해 점진적 확장
2. 직관적인 에이전트 이름 사용 (예. aws-builder, review-bot)
3. .amazonq/cli-agents에 프로젝트 문서 포함
4. 자주 쓰는 명령어 자동 실행 (agentSpawn)
5. 도구 접근은 최소 권한 원칙 적용
6. 컨텍스트는 파일 + 훅으로 동적 구성

#### ✅ 팀 협업 팁

* 공유 에이전트는 Git에 포함하여 버전 관리
* 목적 및 변경 이력은 README로 문서화
* 프로젝트 내 .amazonq/cli-agents/ 폴더 공유

### 1.8  문제 해결

| 문제                 | 해결 방법                                |
| ------------------ | ------------------------------------ |
| Custom Agent 전환 불가 | 새로운 채팅 세션을 시작해야 함                    |
| 구성 파일 인식 안됨        | 올바른 디렉터리에 위치했는지 확인                   |
| JSON 오류            | VSCode 또는 jq로 JSON 유효성 검사            |
| 도구 오류              | allowedTools, toolsSettings 권한 확인 필요 |

***

### 1.9 우선순위 규칙

Custom Agent 로딩 우선순위:

1. 📂 로컬 Custom Agent (.amazonq/cli-agents)
2. 🏠 전역 Custom Agent (\~/.aws/amazonq/cli-agents)
3. ⚙️ 기본 에이전트



