---
description: 'Update : 2025.05.07'
---

# Amazon Q Developer CLI의 Context Management

## 1. Amazon Q Developer CLI의 Context Management

\
&#x20;Amazon Q Developer CLI에서 제공하는 컨텍스트와 프로파일 관리 기능에 대해 설명합니다. 컨텍스트를 활용하여 Amazon Q가 다양한 작업을 더 효과적으로 도와줄 수 있도록 설정하는 방법을 안내합니다.

***

## 2. 프로파일과 컨텍스트 이해하기

* 프로파일(Profile): Amazon Q가 사용자와 시스템에 맞춰 반응하도록 설정된 컨텍스트 모음입니다.
* 컨텍스트(Context): 개발 규칙, 프로젝트 세부사항, 코딩 표준 등을 포함한 정보로, Amazon Q의 응답을 더 정확하고 맞춤화되게 만듭니다.

### **2.1  기본 구조:**

* 글로벌 컨텍스트(Global context): 모든 프로파일에 공통적으로 적용되는 규칙
* 워크스페이스 컨텍스트(Workspace context): 특정 프로파일에만 적용되는 규칙

### 2.2 예시 프로파일: 

* terraform: IaC 가이드라인 포함
* python: 파이썬 코딩 표준
* aws\_cli: 리전 및 AWS 프로파일 관련 설정
* kubernetes: 쿠버네티스 모범 사례 포함

***

## 3. 프로파일 관리

### **3.1 프로파일 목록 보기**

```
/profile list
```

결과 예시

```
* default
```

### **3.2 새 프로파일 생성**

```
/profile create test_k8s_profile
```

**결과 예시**

```
> /profile create test_k8s_profile

Created profile: test_k8s_profile

[test_k8s_profile] > 
```

### **3.3 프로파일 전환**

```
/profile set test_k8s_profile
```

***

## 4. 컨텍스트 관리

컨텍스트 파일은 일반적으로 Markdown 형식이며, 다음과 같은 내용을 포함할 수 있습니다:

* 프로젝트 요구사항
* 개발 가이드라인
* 보안 정책
* 배포 표준 등

### **4.1  컨텍스트 파일 추가**

```
!cat > ./kubernetes_standards.md << 'EOF'
# Kubernetes 배포 표준

## 일반 가이드라인
- 모든 Kubernetes 리소스는 다음과 같은 라벨을 반드시 포함해야 합니다:
  - app: {애플리케이션 이름}
  - environment: {dev|staging|prod}
  - team: platform-engineering
  - cost-center: cc-12345

## 배포 구성
- 리소스 요청(requests) 및 제한(limits)을 반드시 명시해야 합니다
- CPU 요청: 최소 100m, 최대 1000m
- 메모리 요청: 최소 128Mi, 최대 1Gi
- 배포 전략은 롤링 업데이트를 사용하며, maxSurge는 25%, maxUnavailable도 25%로 설정
- 모든 컨테이너에 readiness 및 liveness probe를 설정해야 합니다
- 이미지 풀 정책은 "IfNotPresent"로 설정할 것

## 컨테이너 보안
- 컨테이너는 root가 아닌 사용자로 실행해야 함 (runAsNonRoot: true)
- 가능하면 읽기 전용 루트 파일 시스템을 사용할 것 (readOnlyRootFilesystem: true)
- 권한 상승 허용은 false로 설정 (allowPrivilegeEscalation: false)
- 모든 capabilities는 제거하고 필요한 항목만 명시적으로 추가

## 네트워킹
- 서비스 포트는 프로토콜에 따라 명확히 이름 지정 (예: http, https, grpc)
- Ingress 리소스는 TLS를 사용하고, HTTP 요청은 HTTPS로 리디렉션해야 함
- 네임스페이스 간 트래픽 제한을 위해 NetworkPolicy 사용

## 예시: 컨테이너 포트 설정
yaml
ports:
• name: http
  containerPort: 8080
  protocol: TCP
EOF
```

### 4.2 컨텍스트로 추가:

```
/context add kubernetes_standards.md
```

**현재 컨텍스트 보기**

```
/context show
```

**예시**

```
[test_k8s_profile] > /context add kubernetes_standards.md

Added 1 path(s) to profile context.

[test_k8s_profile] > /context show

🌍 global:
    .amazonq/rules/**/*.md 
    README.md 
    AmazonQ.md 

👤 profile (test_k8s_profile):
    kubernetes_standards.md (1 match)

1 matched file in use:
👤 /home/ec2-user/kubernetes_standards.md (~480 tkns)

Total: ~480 tokens
```

이 명령을 실행하면 글로벌 및 프로파일 수준에서 컨텍스트 파일과 매칭되는 현재 파일 패턴을 확인할 수 있습니다.

출력 결과는 test\_k8s\_profile 프로파일에 kubernetes\_standards.md 파일을 설정한 덕분에 1개의 컨텍스트 파일이 매칭되었음을 보여줍니다. 글로벌 수준에서는 일반적인 프로젝트 규칙에 사용할 수 있는 사전 정의된 매칭 규칙이 존재합니다.

> ⚠️ 주의 성능 저하가 감지된다면, 컨텍스트에서 사용되는 전체 토큰 수를 확인합니다.  컨텍스트 크기가 약 200,000 토큰에 근접하면 /compact 명령어를 사용하여 정상 동작을 유지하는 것이 좋습니다.

### 4.3 컨텍스트와 함께 프롬프트 실행

이제 앞서 생성한 Kubernetes 배포 파일(nginx-deployment.yaml)을 설정한 컨텍스트 기준에 맞춰 업데이트해봅니다.

Amazon Q CLI는 변경된 내용을 diff 형식으로 보여줍니다.

```
nginx-deployment.yaml 파일을 Kubernetes 모범 사례에 맞게 수정해줘.
```

💡 출력 결과를 보면, 프롬프트에 명시하지 않았음에도 불구하고 컨텍스트 파일에 정의된 규칙과 베스트 프랙티스가 자동으로 적용된 코드가 생성됩니다.

```
현재 nginx-deployment.yaml 파일을 확인하고 Kubernetes 모범 사례에 맞게 수정하겠습니다.


🛠️  Using tool: fs_read (trusted)
 ⋮ 
 ● Reading file: /home/ec2-user/nginx-deployment.yaml, all lines
 ⋮ 
 ● Completed in 0.0s



🛠️  Using tool: fs_read (trusted)
 ⋮ 
 ● Reading file: /home/ec2-user/kubernetes_standards.md, all lines
 ⋮ 
 ● Completed in 0.0s



🛠️  Using tool: fs_write (trusted)
 ⋮ 
 ● Path: nginx-deployment.yaml

   1,  1: apiVersion: apps/v1
   2,  2: kind: Deployment
   3,  3: metadata:
   4,  4:   name: nginx-deployment
   5,  5:   labels:
   6,  6:     app: nginx
+      7:     environment: dev
+      8:     team: platform-engineering
+      9:     cost-center: cc-12345
   7, 10: spec:
   8, 11:   replicas: 3
+     12:   strategy:
+     13:     type: RollingUpdate
+     14:     rollingUpdate:
+     15:       maxSurge: 25%
+     16:       maxUnavailable: 25%
   9, 17:   selector:
  10, 18:     matchLabels:
  11, 19:       app: nginx
  12, 20:   template:
  13, 21:     metadata:
  14, 22:       labels:
  15, 23:         app: nginx
+     24:         environment: dev
+     25:         team: platform-engineering
+     26:         cost-center: cc-12345
  16, 27:     spec:
+     28:       securityContext:
+     29:         runAsNonRoot: true
+     30:         runAsUser: 101
+     31:         runAsGroup: 101
+     32:         fsGroup: 101
  17, 33:       containers:
  18, 34:       - name: nginx
  19, 35:         image: nginx:1.14.2
+     36:         imagePullPolicy: IfNotPresent
  20, 37:         ports:
- 21    :         - containerPort: 80
+     38:         - name: http
+     39:           containerPort: 80
+     40:           protocol: TCP
  22, 41:         resources:
  23, 42:           limits:
- 24    :             cpu: "0.5"
- 25    :             memory: "512Mi"
+     43:             cpu: "1000m"
+     44:             memory: "1Gi"
  26, 45:           requests:
- 27    :             cpu: "0.2"
- 28    :             memory: "256Mi"
+     46:             cpu: "100m"
+     47:             memory: "128Mi"
+     48:         securityContext:
+     49:           allowPrivilegeEscalation: false
+     50:           readOnlyRootFilesystem: true
+     51:           capabilities:
+     52:             drop:
+     53:               - ALL
+     54:         livenessProbe:
+     55:           httpGet:
+     56:             path: /
+     57:             port: http
+     58:           initialDelaySeconds: 10
+     59:           periodSeconds: 10
+     60:           timeoutSeconds: 5
+     61:           failureThreshold: 3
+     62:         readinessProbe:
+     63:           httpGet:
+     64:             path: /
+     65:             port: http
+     66:           initialDelaySeconds: 5
+     67:           periodSeconds: 5
+     68:           timeoutSeconds: 3
+     69:           failureThreshold: 3
+     70:         volumeMounts:
+     71:         - name: nginx-cache
+     72:           mountPath: /var/cache/nginx
+     73:         - name: nginx-pid
+     74:           mountPath: /var/run
+     75:       volumes:
+     76:       - name: nginx-cache
+     77:         emptyDir: {}
+     78:       - name: nginx-pid
+     79:         emptyDir: {}

Updating: nginx-deployment.yaml

 ⋮ 
 ● Completed in 0.0s



🛠️  Using tool: fs_write (trusted)
 ⋮ 
 ● Path: nginx-deployment.yaml

  80,  80: ---
  81,  81: apiVersion: v1
  82,  82: kind: Service
  83,  83: metadata:
  84,  84:   name: nginx-service
+      85:   labels:
+      86:     app: nginx
+      87:     environment: dev
+      88:     team: platform-engineering
+      89:     cost-center: cc-12345
  85,  90: spec:
  86,  91:   selector:
  87,  92:     app: nginx
  88,  93:   ports:
- 89     :   - port: 80
- 90     :     targetPort: 80
+      94:   - name: http
+      95:     port: 80
+      96:     targetPort: http
  91,  97:   type: ClusterIP
+      98: ---
+      99: apiVersion: networking.k8s.io/v1
+     100: kind: NetworkPolicy
+     101: metadata:
+     102:   name: nginx-network-policy
+     103:   labels:
+     104:     app: nginx
+     105:     environment: dev
+     106:     team: platform-engineering
+     107:     cost-center: cc-12345
+     108: spec:
+     109:   podSelector:
+     110:     matchLabels:
+     111:       app: nginx
+     112:   ingress:
+     113:   - from:
+     114:     - podSelector: {}
+     115:     ports:
+     116:     - port: 80
+     117:       protocol: TCP

Updating: nginx-deployment.yaml

 ⋮ 
 ● Completed in 0.0s



🛠️  Using tool: fs_read (trusted)
 ⋮ 
 ● Reading file: /home/ec2-user/nginx-deployment.yaml, all lines
 ⋮ 
 ● Completed in 0.0s




nginx-deployment.yaml 파일을 Kubernetes 모범 사례에 맞게 수정했습니다. 주요 변경 사항은 다음과 같습니다:

### 1. 라벨 추가
• 모든 리소스에 표준 라벨 추가:
  • app: nginx
  • environment: dev
  • team: platform-engineering
  • cost-center: cc-12345

### 2. 배포 구성 개선
• 롤링 업데이트 전략 명시적 설정 (maxSurge: 25%, maxUnavailable: 25%)
• 리소스 요청 및 제한 조정:
  • CPU 요청: 100m, 제한: 1000m
  • 메모리 요청: 128Mi, 제한: 1Gi
• 이미지 풀 정책을 "IfNotPresent"로 설정
• 포트에 명확한 이름 지정 (http)

### 3. 컨테이너 보안 강화
• 컨테이너를 root가 아닌 사용자로 실행 (runAsUser: 101)
• 읽기 전용 루트 파일 시스템 설정 (readOnlyRootFilesystem: true)
• 권한 상승 비허용 (allowPrivilegeEscalation: false)
• 모든 capabilities 제거

### 4. 상태 확인 추가
• liveness 프로브 추가: 컨테이너가 살아있는지 확인
• readiness 프로브 추가: 컨테이너가 요청을 처리할 준비가 되었는지 확인

### 5. 볼륨 마운트 추가
• nginx가 읽기 전용 파일 시스템에서도 작동하도록 필요한 디렉터리에 emptyDir 볼륨 마운트:
  • /var/cache/nginx
  • /var/run

### 6. 서비스 구성 개선
• 서비스에도 표준 라벨 추가
• 포트에 명확한 이름 지정
• targetPort를 이름으로 참조

### 7. 네트워크 정책 추가
• 기본적인 NetworkPolicy 추가하여 트래픽 제한
• 같은 네임스페이스 내의 파드만 nginx에 접근 가능하도록 설정

이제 이 YAML 파일은 Kubernetes 모범 사례와 조직의 표준을 준수하며, 보안성과 안정성이 향상되었습니다.
```

### 4.4 컨텍스트 제거

컨텍스트에서 파일을 제거하려면 다음 명령어를 사용합니다:

```bash
/context rm kubernetes_standards.md
```

컨텍스트 상태를 다시 확인합니다:

```bash
/context show
```

출력 결과에는 이제 글로벌 또는 프로파일 수준에서 매칭되는 파일이 없기 때문에, 현재 컨텍스트가 설정되어 있지 않음을 확인할 수 있습니다.

```bash
[test_k8s_profile] > /context rm kubernetes_standards.md

Removed 1 path(s) from profile context.

[test_k8s_profile] > /context show

🌍 global:
    .amazonq/rules/**/*.md 
    README.md 
    AmazonQ.md 

👤 profile (test_k8s_profile):
    <none>

No files in the current directory matched the rules above.
```

#### 워크숍 마무리를 위해 기본 프로파일로 전환합니다.

```bash
/profile set default
```

출력 결과는 기본 프로파일로 성공적으로 전환되었음을 보여줍니다.

```bash
[test_k8s_profile] > /profile set default

Switched to profile: default

> 
```

***

## 5. 글로벌 컨텍스트 설정

글로벌 컨텍스트는 모든 프로파일에 공통 적용되므로, 자주 사용하는 지침이나 규칙을 공유할 때 유용합니다. 글로벌 컨텍스트를 관리하는 방법은 여러 가지가 있지만, 여기서는 .amazonq/rules 디렉터리를 사용하는 방식을 소개합니다.

```bash
!mkdir -p .amazonq/rules
```

### 5.1 예시 1: AWS 및 EKS 설정

Q CLI가 항상 지정된 AWS 리전과 EKS 클러스터를 사용하도록 글로벌 컨텍스트를 설정합니다:

```
!cat > .amazonq/rules/aws.md << 'EOF'
    AWS와 관련된 모든 작업에는 항상 리전 'ap-northeast-2'를 사용하세요.
    EKS 또는 Kubernetes와 관련된 모든 작업에는 항상 \`eks-workshop\`이라는 클러스터를 사용하세요.
EOF
```

이제 아래와 같은 질문을 하면 이 설정이 자동으로 반영됩니다:

```
ap-northeast-2 리전에 있는 EKS 클러스터를 확인해보겠습니다.


🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: eks
Operation name: list-clusters
Profile name: default
Region: ap-northeast-2
Label: List EKS clusters in ap-northeast-2 region
 ⋮ 
 ● Completed in 0.562s



🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: eks
Operation name: describe-cluster
Parameters: 
- name: "eksworkshop"
Profile name: default
Region: ap-northeast-2
Label: Get details of the eksworkshop cluster
 ⋮ 
 ● Completed in 0.673s



🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: eks
Operation name: list-nodegroups
Parameters: 
- clusterName: "eksworkshop"
Profile name: default
Region: ap-northeast-2
Label: List nodegroups in the eksworkshop cluster
 ⋮ 
 ● Completed in 0.605s



🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: eks
Operation name: describe-nodegroup
Parameters: 
- clusterName: "eksworkshop"
- nodegroupName: "managed-ng-private-01"
Profile name: default
Region: ap-northeast-2
Label: Get details of the managed-ng-private-01 nodegroup
 ⋮ 
 ● Completed in 0.660s



🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: eks
Operation name: describe-nodegroup
Parameters: 
- nodegroupName: "managed-ng-public-01"
- clusterName: "eksworkshop"
Profile name: default
Region: ap-northeast-2
Label: Get details of the managed-ng-public-01 nodegroup
 ⋮ 
 ● Completed in 0.630s






ap-northeast-2 리전에 다음과 같은 EKS 클러스터가 있습니다:

## EKS 클러스터 정보

클러스터 이름: eksworkshop
• **상태**: ACTIVE (활성 상태)
• **버전**: Kubernetes 1.31
• **생성 시간**: 2025년 5월 8일 12:18:47 UTC
• **플랫폼 버전**: eks.25

## 노드 그룹 (2개)

### 1. managed-ng-private-01 (프라이빗 노드 그룹)
• **상태**: ACTIVE
• **인스턴스 타입**: m6i.xlarge
• **용량 타입**: ON_DEMAND (온디맨드)
• **노드 수**: 최소 3개, 최대 6개, 현재 3개
• **AMI 타입**: AL2_x86_64 (Amazon Linux 2)
• **서브넷**: 프라이빗 서브넷 3개
• **라벨**: nodegroup-type=managed-backend-workloads
• **생성 도구**: eksctl

### 2. managed-ng-public-01 (퍼블릭 노드 그룹)
• **상태**: ACTIVE
• **인스턴스 타입**: m6i.xlarge
• **용량 타입**: ON_DEMAND (온디맨드)
• **노드 수**: 최소 3개, 최대 6개, 현재 3개
• **AMI 타입**: AL2_x86_64 (Amazon Linux 2)
• **서브넷**: 퍼블릭 서브넷 3개
• **라벨**: nodegroup-type=managed-frontend-workloads
• **생성 도구**: eksctl

## 네트워크 구성
• **VPC ID**: vpc-0f37cd2f69f5248bf
• **퍼블릭 액세스**: 활성화 (0.0.0.0/0에서 접근 가능)
• **프라이빗 액세스**: 비활성화
• **서비스 CIDR**: 172.20.0.0/16

## 보안 구성
• **시크릿 암호화**: KMS 키를 사용하여 활성화됨
• **OIDC 발급자**: 구성됨
• **로깅**: API, 감사, 인증자, 컨트롤러 매니저, 스케줄러 로그 활성화

이 클러스터는 백엔드 워크로드용 프라이빗 노드 그룹과 프론트엔드 워크로드용 퍼블릭 노드 그룹으로 구성되어 있으며, 모두 정상적으로 작동 중입니다.
```

### 5.2 예시 2: Markdown 출력 형식 개선 (glow 유틸리티 사용)

Q CLI가 생성한 Markdown 내용을 보기 좋게 출력하도록 glow 유틸리티를 사용하게 설정할 수 있습니다.

* 먼저 glow CLI를 GitHub 릴리즈에서 설치합니다 (설치 경로는 /usr/local/bin):

```
GitHub 릴리스를 통해 charmbracelet의 glow CLI 바이너리를 설치하세요.
설치 위치는 /usr/local/bin 디렉터리를 사용하세요.

```

* 아래는 실행 결과입니다.&#x20;

```
GitHub 릴리스를 통해 charmbracelet의 glow CLI 바이너리를 설치하겠습니다. 먼저 최신 릴리스를 확인하고 적절한 바이너리를 다운로드한 후 /usr/local/bin 디렉터리에 설치하겠습니다.


🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: uname -sm

Linux x86_64

 ⋮ 
 ● Completed in 0.1s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
curl -s https://api.github.com/repos/charmbracelet/glow/releases/latest | grep -o '"tag_name": ".*"' | cut -d'"' -f4

v2.1.0

 ⋮ 
 ● Completed in 0.311s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
mkdir -p ~/glow_temp && cd ~/glow_temp && curl -L -o glow.tar.gz https://github.com/charmbracelet/glow/releases/download/v2.1.0/glow_2.1.0_linux_x86_64.tar.gz

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 6103k  100 6103k    0     0  6458k      0 --:--:-- --:--:-- --:--:-- 6458k

 ⋮ 
 ● Completed in 0.951s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
cd ~/glow_temp && tar -xzf glow.tar.gz && ls -la

total 6120
drwxrwxr-x.  3 ec2-user ec2-user      56 May  8 18:09 .
drwx------. 16 ec2-user ec2-user   16384 May  8 18:09 ..
-rw-rw-r--.  1 ec2-user ec2-user 6250450 May  8 18:09 glow.tar.gz
drwxrwxr-x.  4 ec2-user ec2-user      85 May  8 18:09 glow_2.1.0_Linux_x86_64

 ⋮ 
 ● Completed in 0.111s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
cd ~/glow_temp/glow_2.1.0_Linux_x86_64 && ls -la

total 16888
drwxrwxr-x. 4 ec2-user ec2-user       85 May  8 18:09 .
drwxrwxr-x. 3 ec2-user ec2-user       56 May  8 18:09 ..
-rw-r--r--. 1 ec2-user ec2-user     1080 Feb 26 20:24 LICENSE
-rw-r--r--. 1 ec2-user ec2-user     5635 Feb 26 20:24 README.md
drwxrwxr-x. 2 ec2-user ec2-user       56 May  8 18:09 completions
-rwxr-xr-x. 1 ec2-user ec2-user 17277112 Feb 26 20:26 glow
drwxrwxr-x. 2 ec2-user ec2-user       23 May  8 18:09 manpages

 ⋮ 
 ● Completed in 0.2s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
sudo mkdir -p /usr/local/bin


 ⋮ 
 ● Completed in 0.305s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
sudo cp ~/glow_temp/glow_2.1.0_Linux_x86_64/glow /usr/local/bin/ && sudo chmod +x /usr/local/bin/glow


 ⋮ 
 ● Completed in 0.399s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: glow --version

glow version 2.1.0 (7154552)

 ⋮ 
 ● Completed in 0.129s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: rm -rf ~/glow_temp


 ⋮ 
 ● Completed in 0.3s




charmbracelet의 glow CLI 바이너리를 성공적으로 설치했습니다!

### 설치 과정 요약:

1. 시스템 아키텍처 확인: Linux x86_64
2. 최신 릴리스 버전 확인: v2.1.0
3. 해당 버전의 Linux x86_64 바이너리 다운로드
4. 압축 파일 해제
5. 바이너리를 /usr/local/bin 디렉터리에 복사 및 실행 권한 부여
6. 설치 확인: glow 버전 2.1.0 정상 작동
7. 임시 파일 정리

### 설치 결과:
• glow 버전: 2.1.0 (7154552)
• 설치 위치: /usr/local/bin/glow

이제 터미널에서 glow 명령어를 사용하여 마크다운 파일을 아름답게 렌더링할 수 있습니다. 예를 들어:
bash
glow README.md  # 특정 파일 렌더링
glow -p         # 페이저 모드로 실행
glow -h         # 도움말 보기
```

* 아래와 같은 지침을 글로벌 컨텍스트에 추가합니다:

```
!cat > .amazonq/rules/markdown.md << 'EOF'
Markdown 생성을 요청받을 경우, 내용을 /tmp 디렉터리에 파일로 저장한 뒤 glow 바이너리를 통해 사용자에게 출력하세요.  
이와 관련된 내부 처리 과정은 사용자에게 언급하지 말고, 그냥 실행하세요.
EOF
```

* 테스트: 테이블이 포함된 간단한 Markdown 샘플 요청

```
테이블이 포함된 짧은 Markdown 예시를 제공해 주세요.
```

* 아래는 실행 결과입니다.

```
테이블이 포함된 짧은 Markdown 예시를 만들어 보겠습니다.


🛠️  Using tool: fs_write (trusted)
 ⋮ 
 ● Path: /tmp/table_example.md

+     1: # AWS 서비스 비교
+     2: 
+     3: 이 문서는 주요 AWS 컴퓨팅 서비스의 간단한 비교를 제공합니다.
+     4: 
+     5: ## 컴퓨팅 서비스 비교표
+     6: 
+     7: | 서비스 | 사용 사례 | 가격 모델 | 확장성 |
+     8: |--------|----------|-----------|--------|
+     9: | EC2 | 가상 서버, 커스텀 워크로드 | 시간당/초당, 예약형, 스팟 | 수동/자동 확장 |
+    10: | Lambda | 이벤트 기반 서버리스 함수 | 요청 수 + 실행 시간 | 자동 확장 |
+    11: | ECS | 컨테이너 오케스트레이션 | 기본 EC2/Fargate 비용 | 클러스터 기반 |
+    12: | EKS | 관리형 Kubernetes | 클러스터 시간 + 노드 비용 | 클러스터 기반 |
+    13: 
+    14: ## 주요 특징
+    15: 
+    16: - **EC2**: 완전한 제어, 다양한 인스턴스 타입
+    17: - **Lambda**: 서버리스, 관리 오버헤드 최소화
+    18: - **ECS**: AWS 통합 컨테이너 관리
+    19: - **EKS**: 표준 Kubernetes API 지원
+    20: 
+    21: 더 자세한 정보는 [AWS 공식 문서](https://aws.amazon.com/products/)를 참조하세요.

Creating: /tmp/table_example.md

 ⋮ 
 ● Completed in 0.0s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
glow /tmp/table_example.md


  # AWS 서비스 비교                                                               
                                                                                  
  이 문서는 주요 AWS 컴퓨팅 서비스의 간단한 비교를 제공합니다.                    
                                                                                  
  ## 컴퓨팅 서비스 비교표                                                         
                                                                                  
  서비스  │사용 사례                   │가격 모델                 │확장성         
  ────────┼────────────────────────────┼──────────────────────────┼───────────────
  EC2     │가상 서버, 커스텀 워크로드  │시간당/초당, 예약형, 스팟 │수동/자동 확장 
  Lambda  │이벤트 기반 서버리스 함수   │요청 수 + 실행 시간       │자동 확장      
  ECS     │컨테이너 오케스트레이션     │기본 EC2/Fargate 비용     │클러스터 기반  
  EKS     │관리형 Kubernetes           │클러스터 시간 + 노드 비용 │클러스터 기반  
                                                                                  
  ## 주요 특징                                                                    
                                                                                  
  • **EC2**: 완전한 제어, 다양한 인스턴스 타입                                    
  • **Lambda**: 서버리스, 관리 오버헤드 최소화                                    
  • **ECS**: AWS 통합 컨테이너 관리                                               
  • **EKS**: 표준 Kubernetes API 지원                                             
                                                                                  
  더 자세한 정보는 AWS 공식 문서 https://aws.amazon.com/products/를 참조하세요.   

⋮ 
 ● Completed in 0.17s


# AWS 서비스 비교

이 문서는 주요 AWS 컴퓨팅 서비스의 간단한 비교를 제공합니다.

## 컴퓨팅 서비스 비교표

| 서비스 | 사용 사례 | 가격 모델 | 확장성 |
|--------|----------|-----------|--------|
| EC2 | 가상 서버, 커스텀 워크로드 | 시간당/초당, 예약형, 스팟 | 수동/자동 확장 |
| Lambda | 이벤트 기반 서버리스 함수 | 요청 수 + 실행 시간 | 자동 확장 |
| ECS | 컨테이너 오케스트레이션 | 기본 EC2/Fargate 비용 | 클러스터 기반 |
| EKS | 관리형 Kubernetes | 클러스터 시간 + 노드 비용 | 클러스터 기반 |

## 주요 특징

• **EC2**: 완전한 제어, 다양한 인스턴스 타입
• **Lambda**: 서버리스, 관리 오버헤드 최소화
• **ECS**: AWS 통합 컨테이너 관리
• **EKS**: 표준 Kubernetes API 지원

더 자세한 정보는 [AWS 공식 문서](https://aws.amazon.com/products/)를 참조하세요.
```

### 5.3  **대화 기록 초기화**

대화 기록을 초기화하려면 /clear 명령어를 사용하면 됩니다.

새로운 주제를 시작할 때나 컨텍스트 한도에 근접했을 때 이 명령을 사용하는 것이 권장됩니다.

```
/clear

```

**실행 결과**

```
> /clear

Are you sure? This will erase the conversation history and context from hooks for the current session. [y/n]:

> y

Conversation history cleared.
```

## 6.`/compact` 로 대화 요약하기

Amazon Q와의 대화가 길어지면 컨텍스트 한도에 가까워질 수 있습니다.

이 경우, Amazon Q는 대화 기록을 요약(/compact) 하라는 경고 메시지를 표시할 수 있습니다.

**대화 요약 명령어:**

```
/compact
```

이 명령을 사용하면 중요한 맥락을 잃지 않고도 대화를 계속할 수 있습니다.

요약 과정에서는 이전 대화를 정리해 핵심 정보만 유지하면서도 Amazon Q가 문맥 인식 능력을 지속적으로 유지할 수 있도록 돕습니다.



언제 /compact 를 사용해야 할까?

* 메모리 사용량 경고 메시지가 표시될 때
* 대화가 오래 지속된 경우
* 같은 세션 내에서 새로운 주제를 시작하려 할 때
* 복잡한 도구 작업을 완료한 직후

#### 작동 방식

* AI가 대화를 요약하여 핵심 정보만 추출
* 중요한 코드, 명령, 툴 실행 내역은 유지
* 기존 대화 기록은 정리되어 공간 확보
* 이후 응답 시, 요약된 맥락을 바탕으로 응답 제공

💡 Tip: 장시간 세션 중에는 /compact 명령을 주기적으로 사용하면, 응답 품질을 유지하면서 컨텍스트 한도를 피하는 데 효과적입니다.
