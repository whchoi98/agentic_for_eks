---
description: 'Update : 2025.08.06'
---

# Amazon EKS 환경에서의 Q CLI

***

## 1.Amazon Q Developer CLI Basics

Amazon Q Developer CLI는 터미널에서 실행되는 AI 기반 도우미로, 개발자의 질문에 응답하고, 코드를 생성하며, AWS 환경과 상호작용할 수 있는 대화형 채팅 인터페이스를 제공합니다.

***

### 2. 시작하기: Chat 세션 열기

```
q chat
```

실행하면 다음과 같은 대화형 프롬프트가 열립니다:

```
To learn more about MCP safety, see https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-mcp-security.html

Welcome to 

 █████╗ ███╗   ███╗ █████╗ ███████╗ ██████╗ ███╗   ██╗     ██████╗ 
██╔══██╗████╗ ████║██╔══██╗╚══███╔╝██╔═══██╗████╗  ██║    ██╔═══██╗
███████║██╔████╔██║███████║  ███╔╝ ██║   ██║██╔██╗ ██║    ██║   ██║
██╔══██║██║╚██╔╝██║██╔══██║ ███╔╝  ██║   ██║██║╚██╗██║    ██║▄▄ ██║
██║  ██║██║ ╚═╝ ██║██║  ██║███████╗╚██████╔╝██║ ╚████║    ╚██████╔╝
╚═╝  ╚═╝╚═╝     ╚═╝╚═╝  ╚═╝╚══════╝ ╚═════╝ ╚═╝  ╚═══╝     ╚══▀▀═╝ 
                                                        


╭───────────────────────── Did you know? ─────────────────────────╮
│                                                                 │
│         You can use /editor to edit your prompt with a          │
│                       vim-like experience                       │
│                                                                 │
╰─────────────────────────────────────────────────────────────────╯


/help all commands  •  ctrl + j new lines  •  ctrl + k fuzzy search
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

이 환경에서 Amazon Q는 다음과 같은 작업을 도와줄 수 있습니다:

* AWS 서비스에 대한 질문
* 인프라 구성, EKS, IAM, S3 등 Best Practice 안내
* 코드 스니펫 생성
* &#x20;쉘 명령어 실행
* 문제 해결 및 디버깅 조언
* 로컬 파일 읽기/쓰기 (허용 시)

***

### 3. EKS 환경에서의 예시 프롬프트

* 예시1. EKS 보안 권장 사항 묻기 프롬프트:

```
“EKS 클러스터를 보호하기 위한 몇 가지 모범 사례는 무엇인가요?”
~/output 디렉토리에 결과를 markdown으로 저장해 주세요.

```

* 실제 결과 예시

```
EKS 클러스터 보안 모범 사례를 정리하여 /home/ec2-user/output/eks-security-best-practices.md 파일에 저장했습니다.

주요 보안 영역별로 다음과 같은 내용을 포함했습니다:

• 클러스터 접근 제어: API 서버 보안, IAM 및 RBAC 통합
• 네트워크 보안: 네트워크 정책, 보안 그룹 구성
• 파드 보안: Pod Security Standards, 컨테이너 이미지 보안
• 시크릿 관리: AWS Secrets Manager 통합, IRSA 활용
• 로깅 및 모니터링: CloudWatch, GuardDuty, Falco 활용
• 노드 보안: 워커 노드 강화, 런타임 보안
• 데이터 보호: 저장 및 전송 중 암호화
• 컴플라이언스: 정책 관리, CIS 벤치마크 준수
• 백업 및 재해 복구: Velero, 멀티 AZ 구성
• 지속적인 보안 관리: 자동화, 교육, 인시던트 대응

각 섹션에는 구체적인 구현 방법과 설정 예시도 포함되어 있어 실제 적용 시 참고하실 수 있습니다.
```

[Markdown 예시](https://whchoi98.notion.site/eks-security-best-practices-24704ef7e60e8081a2a9d32be8ebf829?source=copy_link)



* 예시2. nginx 배포 YAML 생성 프롬프트:

```
~/environment/myeks 디렉토리에 nginx-deployment.yaml이라는 이름으로 기본 Kubernetes nginx 배포를 위한 YAML 파일을 생성해주세요.

```

* 실제 결과

```
Kubernetes nginx 배포를 위한 기본 YAML 파일을 생성하겠습니다.


🛠️  Using tool: fs_write
 ⋮ 
 ● Path: /home/ec2-user/nginx-deployment.yaml

+     1: apiVersion: apps/v1
+     2: kind: Deployment
+     3: metadata:
+     4:   name: nginx-deployment
+     5:   labels:
+     6:     app: nginx
+     7: spec:
+     8:   replicas: 3
+     9:   selector:
+    10:     matchLabels:
+    11:       app: nginx
+    12:   template:
+    13:     metadata:
+    14:       labels:
+    15:         app: nginx
+    16:     spec:
+    17:       containers:
+    18:       - name: nginx
+    19:         image: nginx:1.14.2
+    20:         ports:
+    21:         - containerPort: 80
+    22:         resources:
+    23:           limits:
+    24:             cpu: "0.5"
+    25:             memory: "512Mi"
+    26:           requests:
+    27:             cpu: "0.2"
+    28:             memory: "256Mi"
+    29: ---
+    30: apiVersion: v1
+    31: kind: Service
+    32: metadata:
+    33:   name: nginx-service
+    34: spec:
+    35:   selector:
+    36:     app: nginx
+    37:   ports:
+    38:   - port: 80
+    39:     targetPort: 80
+    40:   type: ClusterIP


Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> t
Creating: /home/ec2-user/nginx-deployment.yaml

 ⋮ 
 ● Completed in 0.0s


nginx-deployment.yaml 파일을 성공적으로 생성했습니다. 이 파일에는 다음 내용이 포함되어 있습니다:

1. Deployment 리소스:
   • 3개의 복제본(replicas)
   • nginx:1.14.2 이미지 사용
   • 포트 80 노출
   • 리소스 제한 설정:
     • CPU 제한: 0.5 코어, 요청: 0.2 코어
     • 메모리 제한: 512Mi, 요청: 256Mi

2. Service 리소스:
   • ClusterIP 타입 서비스
   • 포트 80을 통해 Deployment에 연결

이 YAML 파일을 사용하여 Kubernetes 클러스터에 nginx를 배포할 수 있습니다:

bash
kubectl apply -f nginx-deployment.yaml


배포 후 상태를 확인하려면:

bash
kubectl get deployments
kubectl get pods
kubectl get services

```

***

### 4. 채팅 세션 내 명령어 모음

| 명령어      | 설명                                         |
| -------- | ------------------------------------------ |
| !        | 현재 세션 내에서 쉘 명령 실행 (!ls, !kubectl get pods) |
| ctrl + j | 멀티라인 입력                                    |
| /editor  | 프롬프트를 편집기로 작성                              |
| /help    | 사용 가능한 명령어 목록 표시                           |
| /issue   | 버그 또는 기능 요청 전송                             |
| /quit    | 채팅 세션 종료                                   |
| /clear   | 현재 세션의 히스토리 초기화                            |
| /reset   | 전체 대화 맥락 초기화 (컨텍스트 제거)                     |
| /tools   | Q가 사용할 수 있는 도구 목록 및 권한 관리                  |
| /agent   | 다양한 Q agent 관리                             |
| /context | 대화에 사용되는 컨텍스트 파일 관리                        |
| /compact | 대화 히스토리를 요약하여 컨텍스트 최적화                     |

* 예시: 도움말 확인

```
/help
```

***

### 5. 요약

| 항목    | 설명                         |
| ----- | -------------------------- |
| 실행 명령 | q chat                     |
| 주요 기능 | 질문 응답, 코드 생성, AWS 연동, 쉘 실행 |
| 로컬 권한 | 파일 읽기/쓰기 가능 (허용 시)         |
| 확장성   | 프로파일, 컨텍스트, MCP 기반 확장 지원   |

***

Q CLI는 단순한 터미널 도우미가 아닌, 개발자 환경과 대화형으로 연결된 AI 개발 파트너입니다.

\
