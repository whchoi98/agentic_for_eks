---
description: 'update : 2025.05.07'
---

# Amazon Q Developer CLI 도구

***

## 1. 사용 가능한 도구

Amazon Q Developer CLI는 시스템 및 AWS 리소스와 상호작용할 수 있도록 여러 내장 도구를 제공합니다. 이를 통해 Amazon Q는 파일을 읽거나, 명령을 실행하거나, AWS API 호출을 수행할 수 있습니다.\


다음 도구들이 기본적으로 제공됩니다:

<table data-header-hidden><thead><tr><th width="153.9921875"></th><th></th><th></th></tr></thead><tbody><tr><td>도구</td><td>일반 설명</td><td>Kubernetes Workflow에서의 역할</td></tr><tr><td>fs_read</td><td>시스템의 파일과 디렉터리를 읽음</td><td>Kubernetes YAML 파일과 구성 분석</td></tr><tr><td>fs_write</td><td>시스템의 파일 생성 및 수정</td><td>매니페스트, Helm 차트, 스크립트 생성 및 업데이트</td></tr><tr><td>execute_bash</td><td>시스템에서 Bash 명령 실행</td><td>kubectl 및 기타 셸 명령 실행</td></tr><tr><td>use_aws</td><td>AWS CLI를 사용하여 AWS 서비스와 상호작용</td><td>EKS 및 기타 AWS 서비스 제어</td></tr><tr><td>report_issue</td><td>AWS에 문제를 보고하는 브라우저 창 열기</td><td>운영 중 발생한 문제 보고</td></tr></tbody></table>

***

## 2. 도구 권한 관리

채팅 세션에서 `/tools` 명령어를 통해 Amazon Q가 어떤 도구를 사용할 수 있을지 설정할 수 있습니다.

### **2.1 권한 상태**

도구 권한은 두 가지 상태 중 하나를 가질 수 있습니다:

* 신뢰됨(Trusted): Amazon Q가 도구를 사용할 때 매번 확인하지 않고 바로 실행 가능
* 요청 시 승인(Per-request): 매번 사용자 승인을 받아야 도구 사용 가능

기본적으로 fs\_read만 신뢰됨으로 설정되어 있으며, 나머지는 사용 시마다 확인을 요청합니다.

### 2.2 현재 권한 보기

아래 명령을 채팅에 입력하여 현재 도구 권한 상태를 확인할 수 있습니다:

```
/tools
```

***

## 3. 개별 도구 권한 설정

특정 도구를 신뢰할 때:

```
/tools trust fs_read
```

도구의 신뢰를 해제하고 승인 요청 방식으로 되돌릴 때:

```
/tools untrust execute_bash
```

***

## 4. 모든 도구 권한 일괄 설정

모든 도구를 한 번에 신뢰하여 원활한 환경 구성:

```
/tools trustall
```

초기 설정으로 되돌릴 때:

```
/tools reset
```

> ⚠️ 경고: /tools trustall 명령어는 모든 도구를 확인 없이 사용할 수 있게 하므로 보안상 위험할 수 있습니다.

***

## 5. 도구 권한 명령 요약

| 명령어      | 설명                          |
| -------- | --------------------------- |
| help     | 도구 관련 도움말 표시                |
| trust    | 특정 도구를 세션 동안 신뢰함            |
| untrust  | 도구를 승인 요청 방식으로 변경           |
| trustall | 모든 도구를 신뢰함 (/acceptall의 대체) |
| reset    | 도구 권한을 기본값으로 초기화            |

***

## 6. 도구 사용 예시

### **6.1 파일 시스템 작업**

워크스페이스 내 파일을 분석하거나 생성할 수 있습니다.

예시:

```
이 워크스페이스에 어떤 파일이 있지?
```

예시에 대한 결과:

```
현재 워크스페이스에 있는 파일을 확인해보겠습니다.


🛠️  Using tool: fs_read (trusted)
 ⋮ 
 ● Reading directory: /home/ec2-user with maximum depth of 0
 ⋮ 
 ● Completed in 0.0s


현재 워크스페이스에 있는 파일 및 디렉터리는 다음과 같습니다:

1. 방금 생성한 파일:
   • nginx-deployment.yaml (Kubernetes nginx 배포 YAML 파일)

2. 구성 파일 및 디렉터리:
   • .bash_profile, .bashrc, .bash_logout (Bash 쉘 설정)
   • .zshrc, .zprofile (Zsh 쉘 설정)
   • .aws 디렉터리 (AWS CLI 구성)
   • .kube 디렉터리 (Kubernetes 구성)
   • .ssh 디렉터리 (SSH 키 및 구성)

3. 도구 및 유틸리티:
   • helm-v3.16.4-linux-amd64.tar.gz (Helm 패키지)
   • linux-amd64 디렉터리
   • .kubectl_completion, .helm_completion (쉘 자동완성 스크립트)
   • .fzf 디렉터리 (Fuzzy Finder 도구)

4. Amazon Q 관련:
   • q.zip
   • q 디렉터리
   • .amazon-q.dotfiles.bak 디렉터리

5. 기타:
   • environment 디렉터리
   • ec2_vscode 디렉터리

이 중에서 방금 생성한 nginx-deployment.yaml 파일이 Kubernetes nginx 배포를 위한 YAML 파일입니다.
```

예시. 파이썬 스크립트를 생성해 보세요:

```
ap-northeast-2 리전에 있는 EKS 노드를 나열하는 간단한 파이썬 스크립트를 이 디렉터리에 생성해줘.
```

예시에 대한 결과

```
ap-northeast-2 리전에 있는 EKS 노드를 나열하는 파이썬 스크립트를 생성하겠습니다.


🛠️  Using tool: fs_write (trusted)
 ⋮ 
 ● Path: /home/ec2-user/list_eks_nodes.py

+     1: #!/usr/bin/env python3
+     2: import boto3
+     3: import sys
+     4: 
+     5: def list_eks_clusters(region='ap-northeast-2'):
+     6:     """List all EKS clusters in the specified region"""
+     7:     eks_client = boto3.client('eks', region_name=region)
+     8:     try:
+     9:         response = eks_client.list_clusters()
+    10:         return response['clusters']
+    11:     except Exception as e:
+    12:         print(f"Error listing EKS clusters: {e}")
+    13:         return []
+    14: 
+    15: def list_nodegroups(cluster_name, region='ap-northeast-2'):
+    16:     """List all nodegroups in a specific EKS cluster"""
+    17:     eks_client = boto3.client('eks', region_name=region)
+    18:     try:
+    19:         response = eks_client.list_nodegroups(clusterName=cluster_name)
+    20:         return response['nodegroups']
+    21:     except Exception as e:
+    22:         print(f"Error listing nodegroups for cluster {cluster_name}: {e}")
+    23:         return []
+    24: 
+    25: def describe_nodegroup(cluster_name, nodegroup_name, region='ap-northeast-2'):
+    26:     """Get details about a specific nodegroup"""
+    27:     eks_client = boto3.client('eks', region_name=region)
+    28:     try:
+    29:         response = eks_client.describe_nodegroup(
+    30:             clusterName=cluster_name,
+    31:             nodegroupName=nodegroup_name
+    32:         )
+    33:         return response['nodegroup']
+    34:     except Exception as e:
+    35:         print(f"Error describing nodegroup {nodegroup_name} in cluster {cluster_name}: {e}")
+    36:         return None
+    37: 
+    38: def list_ec2_instances(instance_ids, region='ap-northeast-2'):
+    39:     """Get details about EC2 instances"""
+    40:     ec2_client = boto3.client('ec2', region_name=region)
+    41:     try:
+    42:         response = ec2_client.describe_instances(InstanceIds=instance_ids)
+    43:         return response['Reservations']
+    44:     except Exception as e:
+    45:         print(f"Error describing EC2 instances: {e}")
+    46:         return []
+    47: 
+    48: def main():
+    49:     region = 'ap-northeast-2'
+    50:     print(f"Listing EKS nodes in region: {region}")
+    51:     
+    52:     # Get all EKS clusters
+    53:     clusters = list_eks_clusters(region)
+    54:     if not clusters:
+    55:         print("No EKS clusters found in the region.")
+    56:         return
+    57:     
+    58:     print(f"Found {len(clusters)} EKS clusters")
+    59:     
+    60:     # For each cluster, get nodegroups and their details
+    61:     for cluster_name in clusters:
+    62:         print(f"\nCluster: {cluster_name}")
+    63:         
+    64:         nodegroups = list_nodegroups(cluster_name, region)
+    65:         if not nodegroups:
+    66:             print(f"  No nodegroups found in cluster {cluster_name}")
+    67:             continue
+    68:         
+    69:         print(f"  Found {len(nodegroups)} nodegroups")
+    70:         
+    71:         for nodegroup_name in nodegroups:
+    72:             nodegroup = describe_nodegroup(cluster_name, nodegroup_name, region)
+    73:             if not nodegroup:
+    74:                 continue
+    75:             
+    76:             print(f"\n  Nodegroup: {nodegroup_name}")
+    77:             print(f"    Status: {nodegroup.get('status', 'Unknown')}")
+    78:             print(f"    Instance Type: {nodegroup.get('instanceTypes', ['Unknown'])[0]}")
+    79:             print(f"    Capacity Type: {nodegroup.get('capacityType', 'Unknown')}")
+    80:             print(f"    Desired Size: {nodegroup.get('scalingConfig', {}).get('desiredSize', 'Unknown')}")
+    81:             print(f"    Min Size: {nodegroup.get('scalingConfig', {}).get('minSize', 'Unknown')}")
+    82:             print(f"    Max Size: {nodegroup.get('scalingConfig', {}).get('maxSize', 'Unknown')}")
+    83:             
+    84:             # Get instance IDs if available
+    85:             instance_ids = []
+    86:             for resource in nodegroup.get('resources', {}).get('autoScalingGroups', []):
+    87:                 if 'name' in resource:
+    88:                     asg_name = resource['name']
+    89:                     print(f"    ASG Name: {asg_name}")
+    90:             
+    91:             # If there are instances in the health section, list them
+    92:             if 'health' in nodegroup and 'issues' in nodegroup['health']:
+    93:                 print(f"    Health Issues: {len(nodegroup['health']['issues'])}")
+    94:                 for issue in nodegroup['health']['issues']:
+    95:                     print(f"      - {issue.get('code')}: {issue.get('message')}")
+    96: 
+    97: if __name__ == "__main__":
+    98:     main()

Creating: /home/ec2-user/list_eks_nodes.py

 ⋮ 
 ● Completed in 0.0s



🛠️  Using tool: execute_bash
 ⋮ 
 ● I will run the following shell command: 
chmod +x /home/ec2-user/list_eks_nodes.py


Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> t

 ⋮ 
 ● Completed in 0.1s


ap-northeast-2 리전에 있는 EKS 노드를 나열하는 파이썬 스크립트를 생성했습니다. 스크립트 이름은 list_eks_nodes.py이며 실행 권한을 부여했습니다.

스크립트 기능:
• ap-northeast-2 리전의 모든 EKS 클러스터를 나열합니다.
• 각 클러스터의 노드 그룹을 나열합니다.
• 각 노드 그룹에 대한 상세 정보를 표시합니다:
  • 상태
  • 인스턴스 타입
  • 용량 타입(온디맨드/스팟)
  • 원하는 노드 수, 최소 및 최대 노드 수
  • Auto Scaling Group 이름
  • 상태 이슈(있는 경우)

스크립트 실행 방법:
bash
./list_eks_nodes.py


필요한 권한:
• 이 스크립트를 실행하려면 AWS 계정에 다음 권한이 필요합니다:
  • eks:ListClusters
  • eks:ListNodegroups
  • eks:DescribeNodegroup
  • ec2:DescribeInstances

AWS 자격 증명이 올바르게 구성되어 있는지 확인하세요. 필요한 경우 AWS CLI를 사용하여 자격 증명을 구성할 수 있습니다:
bash
aws configure

```

fs\_read, fs\_write 도구가 사용되며, 신뢰되지 않은 경우 사전 승인을 요청합니다.

***

## **7.명령 실행**

### 7.1 EKS 관리 

kubectl을 활용해 클러스터와 상호작용하는 명령도 실행할 수 있습니다.

* 예시:

```
ap-northeast-2에 있는 내 Kubernetes 클러스터에서 실행 중인 파드를 보여줘.
```

execute\_bash 도구가 사용되며, 신뢰되지 않았다면 사전 확인을 요청합니다.

* 예시에 대한 결과:

```
ap-northeast-2 리전에 있는 Kubernetes 클러스터의 실행 중인 파드를 확인하기 위해서는 kubectl 명령어를 사용해야 합니다. 먼저 현재 구성된 컨텍스트를 확인하고 파드를 조회해보겠습니다.


🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
kubectl config current-context

i-0f1e10a1cd00fb1cb@eksworkshop.ap-northeast-2.eksctl.io

 ⋮ 
 ● Completed in 0.32s



🛠️  Using tool: execute_bash (trusted)
 ⋮ 
 ● I will run the following shell command: 
kubectl get pods --all-namespaces

NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE
kube-system   aws-node-5p5tp                       2/2     Running   0          5h1m
kube-system   aws-node-8dtqr                       2/2     Running   0          5h
kube-system   aws-node-cfd6n                       2/2     Running   0          5h
kube-system   aws-node-cv94n                       2/2     Running   0          5h
kube-system   aws-node-fbfxh                       2/2     Running   0          5h
kube-system   aws-node-rbdtg                       2/2     Running   0          5h1m
kube-system   aws-node-shgdd                       2/2     Running   0          5h1m
kube-system   aws-node-tkvm5                       2/2     Running   0          5h
kube-system   aws-node-v4jdv                       2/2     Running   0          5h1m
kube-system   aws-node-xhgjp                       2/2     Running   0          5h
kube-system   aws-node-z7stp                       2/2     Running   0          5h1m
kube-system   aws-node-zdpmc                       2/2     Running   0          5h1m
kube-system   coredns-fd6b6c769-lgcqg              1/1     Running   0          5h3m
kube-system   coredns-fd6b6c769-rc57k              1/1     Running   0          5h3m
kube-system   ebs-csi-controller-7df458c96-qsnmt   6/6     Running   0          4h56m
kube-system   ebs-csi-controller-7df458c96-tw69g   6/6     Running   0          4h56m
kube-system   ebs-csi-node-6n6nv                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-6qfp9                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-clv97                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-czcxl                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-ff989                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-fsk4v                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-gzrmv                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-hghqs                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-hhtnn                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-hpjnv                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-r4zc5                   3/3     Running   0          4h56m
kube-system   ebs-csi-node-tqvjp                   3/3     Running   0          4h56m
kube-system   kube-proxy-49jtx                     1/1     Running   0          5h1m
kube-system   kube-proxy-8jccc                     1/1     Running   0          5h1m
kube-system   kube-proxy-b2crn                     1/1     Running   0          5h
kube-system   kube-proxy-f5b2s                     1/1     Running   0          5h
kube-system   kube-proxy-kzzv8                     1/1     Running   0          5h1m
kube-system   kube-proxy-m4s86                     1/1     Running   0          5h
kube-system   kube-proxy-pbxms                     1/1     Running   0          5h
kube-system   kube-proxy-qtqxn                     1/1     Running   0          5h1m
kube-system   kube-proxy-scdms                     1/1     Running   0          5h
kube-system   kube-proxy-wzfb9                     1/1     Running   0          5h
kube-system   kube-proxy-xmw8x                     1/1     Running   0          5h1m
kube-system   kube-proxy-zbmxf                     1/1     Running   0          5h1m
kube-system   metrics-server-554d7599f4-5sjrs      1/1     Running   0          5h3m
kube-system   metrics-server-554d7599f4-swn57      1/1     Running   0          5h3m

 ⋮ 
 ● Completed in 0.598s




현재 ap-northeast-2 리전에 있는 Kubernetes 클러스터(eksworkshop.ap-northeast-2.eksctl.io)에서 실행 중인 파드 목록입니다:

현재 모든 파드는 kube-system 네임스페이스에서 실행 중이며, 모두 Running 상태입니다:

1. 네트워킹 관련 파드:
   • aws-node: 12개 (VPC CNI 플러그인)
   • kube-proxy: 12개 (네트워크 프록시)

2. DNS 관련 파드:
   • coredns: 2개 (클러스터 DNS 서비스)

3. 스토리지 관련 파드:
   • ebs-csi-controller: 2개 (EBS CSI 컨트롤러)
   • ebs-csi-node: 12개 (EBS CSI 노드 에이전트)

4. 모니터링 관련 파드:
   • metrics-server: 2개 (클러스터 메트릭 수집)

현재 기본 시스템 파드만 실행 중이며, 사용자 애플리케이션 파드는 보이지 않습니다. 이전에 생성한 nginx-deployment.yaml 파일을 적용하지 않았거나, 다른 네임스페이스에 배포되었을 수 있습니다.

특정 네임스페이스의 파드만 보려면 다음 명령어를 사용할 수 있습니다:
bash
kubectl get pods -n <namespace>


모든 네임스페이스의 파드를 보는 명령어는 이미 실행했습니다:
bash
kubectl get pods --all-namespaces
```

### **7.2 AWS CLI 통합**

AWS 리소스와 상호작용할 수 있습니다.

* 예시:

```
ad-ap-northeast-2에 있는 EKS 클러스터들을 모두 나열해줘.
```

* 예시 결과:

```
ap-northeast-2 리전에 있는 EKS 클러스터들을 나열해보겠습니다.


🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: eks
Operation name: list-clusters
Parameters: 
Profile name: default
Region: ap-northeast-2
Label: List EKS clusters in ap-northeast-2 region
 ⋮ 
 ● Completed in 0.578s



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
 ● Completed in 0.693s



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
 ● Completed in 0.607s




ap-northeast-2 리전에서 다음과 같은 EKS 클러스터를 찾았습니다:

### EKS 클러스터 정보

클러스터 이름: eksworkshop
• **상태**: ACTIVE
• **버전**: 1.31
• **생성 시간**: 2025-05-08 12:18:47 UTC
• **플랫폼 버전**: eks.25

### 네트워크 구성
• **VPC ID**: vpc-0f37cd2f69f5248bf
• **서브넷**: 6개 서브넷 구성
• **퍼블릭 액세스**: 활성화 (0.0.0.0/0에서 접근 가능)
• **프라이빗 액세스**: 비활성화
• **서비스 CIDR**: 172.20.0.0/16
• **IP 패밀리**: IPv4

### 노드 그룹 (2개)
1. managed-ng-private-01: 프라이빗 노드 그룹
2. managed-ng-public-01: 퍼블릭 노드 그룹

### 보안 구성
• **암호화**: 시크릿 암호화 활성화 (KMS 키 사용)
• **OIDC 발급자**: 구성됨
• **로깅**: API, 감사, 인증자, 컨트롤러 매니저, 스케줄러 로그 활성화

### 추가 정보
• **클러스터는 eksctl로 생성됨** (태그 정보에서 확인)
• **인증 모드**: API_AND_CONFIG_MAP
• **업그레이드 정책**: EXTENDED 지원 타입

이 클러스터는 현재 정상적으로 작동 중이며(ACTIVE 상태), 프라이빗 및 퍼블릭 노드 그룹을 모두 포함하고 있습니다.
```

이 경우 aws cli 도구가 사용되며, 신뢰되지 않은 경우 확인을 요청합니다.

***

## 8. 도구 권한 관리 모범 사례

* **Trust selectively**: 자주 사용하는 도구만 신뢰 상태로 설정하세요.
* **Review actions**: Amazon Q가 수행하려는 작업을 항상 검토한 후 승인하세요.
* **Context matters**: 운영 환경의 민감도를 고려하여 도구 신뢰 여부를 결정하세요.
* **Use per-request for sensitive operations**: AWS CLI 호출이나 셸 명령 실행처럼 민감한 작업은 요청 승인 상태로 유지하는 것이 좋습니다.

