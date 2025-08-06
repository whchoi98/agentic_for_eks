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
/tools trust-all
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

예시 프롬프트:

```
이 워크스페이스에 어떤 파일이 있지?
```

예시에 대한 결과:

```
현재 워크스페이스에는 다음과 같은 파일들이 있습니다:

## 주요 디렉토리 구조:

홈 디렉토리 (/home/ec2-user):
• .aws/ - AWS CLI 설정
• .kube/ - Kubernetes 설정
• environment/ - 개발 환경 파일들
• output/ - 출력 파일들
• q/ - Amazon Q CLI 관련 파일들
• 기타 설정 파일들 (.bashrc, .bash_profile 등)

environment/myeks/ 디렉토리:
• CloudFormation 템플릿들: EKSVPC.yml, EKSVPC3AZ.yml 등 다양한 VPC 구성
• eksctl 설정 파일들: eksworkshop-cluster.yaml, whchoi-cluster.yaml 등
• Kubernetes 매니페스트들: 
  • nginx-deployment.yaml (방금 생성한 파일)
  • calico.yaml, aws-k8s-cni.yaml 등 네트워킹 관련
  • alb-ingress-controller.yaml 등 인그레스 관련
• 하위 디렉토리들:
  • alb-controller/ - ALB 컨트롤러 관련
  • calico_demo/ - Calico 네트워킹 데모
  • csi/ - Container Storage Interface 관련
  • fargate/ - Fargate 관련 설정
  • helm-chart-demo/ - Helm 차트 데모
  • ingress/ - 인그레스 설정들
  • istio/ - Istio 서비스 메시 관련
  • karpenter/ - Karpenter 오토스케일링 관련
  • Storage/ - 스토리지 관련 설정

output/ 디렉토리:
• eks-security-best-practices.md - EKS 보안 모범 사례 문서

이 워크스페이스는 EKS 클러스터 관리와 Kubernetes 개발을 위한 다양한 리소스들이 잘 정리되어 있는 환경입니다.
```

예시. 파이썬 스크립트를 생성 프롬프트:

```
ap-northeast-2 리전에 있는 EKS 노드를 나열하는 간단한 파이썬 스크립트를 이 디렉터리에 생성해줘.
```

예시에 대한 결과:

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

생성된 python code를 실행해 봅니다.

{% hint style="info" %}
boto3 가 설치되어 있지 않기 때문에, Shell 에서 에러가 발생하면, Q CLI를 활용해서 설치해 봅니다.
{% endhint %}

boto3가 설치가 되면 python code를 실행해 봅니다.

```
$ python3 ~/environment/myeks/list_eks_nodes.py
🎯 EKS 노드 목록 조회 도구
⏰ 실행 시간: 2025-08-06 02:06:57

🔍 ap-northeast-2 리전의 EKS 클러스터 및 노드 정보를 조회합니다...

================================================================================
📋 총 1개의 EKS 클러스터를 발견했습니다.

🏗️  클러스터: eksworkshop
------------------------------------------------------------
   상태: ACTIVE
   버전: 1.31
   엔드포인트: https://ACA8ACA476298B673DF87D7BED190995.gr7.ap-northeast-2.eks.amazonaws.com
   생성일: 2025-08-05 15:39:37

   📦 노드 그룹 (2개):
     • managed-ng-private-01
       - 상태: ACTIVE
       - 인스턴스 타입: m6i.xlarge
       - AMI 타입: AL2_x86_64
       - 용량 타입: ON_DEMAND
       - 스케일링: 최소 3, 원하는 3, 최대 6
       - 서브넷: subnet-0955e022578e4fd64, subnet-0401c81b9e6502b7a, subnet-0b145c54494bce17a
       - 실행 중인 인스턴스 (3개):
         * i-0806a5637a35e051c (running) - m6i.xlarge
         * i-0838c0643fd4e1008 (running) - m6i.xlarge
         * i-09cd6efe259c436a8 (running) - m6i.xlarge

     • managed-ng-public-01
       - 상태: ACTIVE
       - 인스턴스 타입: m6i.xlarge
       - AMI 타입: AL2_x86_64
       - 용량 타입: ON_DEMAND
       - 스케일링: 최소 3, 원하는 3, 최대 6
       - 서브넷: subnet-04638ee0e421dec5d, subnet-01e79cf458cdaefe9, subnet-0d171dd83e589cee6
       - 실행 중인 인스턴스 (3개):
         * i-066454061f7e7f3bc (running) - m6i.xlarge
         * i-0d1d3808520924b43 (running) - m6i.xlarge
         * i-0c2e973d4b7ab65b2 (running) - m6i.xlarge

================================================================================

✅ 조회 완료!
```

***

## **7.명령 실행**

### 7.1 EKS 관리 

kubectl을 활용해 클러스터와 상호작용하는 명령도 실행할 수 있습니다.

* 예시 프롬프트:

```
ap-northeast-2에 있는 내 Kubernetes 클러스터에서 실행 중인 파드를 보여주세요.
결과는 ~/output 디렉토리에 markdown으로 저장해 주세요.
```

execute\_bash 도구가 사용되며, 신뢰되지 않았다면 사전 확인을 요청합니다.

* 결과 예시:

### **7.2 AWS CLI 통합**

AWS 리소스와 상호작용할 수 있습니다.

* 예시 프롬프트:

```
ap-northeast-2에 있는 EKS 클러스터들을 모두 나열해줘.
```

* 결과 예시:
  * [markdown](https://www.notion.so/whchoi98/eks-pods-status-24704ef7e60e80bca095f0b87059c1c3?source=copy_link)

```
ap-northeast-2 리전의 EKS 클러스터에서 실행 중인 파드 현황을 조회하여 /home/ec2-user/output/eks-pods-status.md 파일에 저장했습니다.

## 주요 발견사항:

클러스터 현황:
• 클러스터명: eksworkshop
• Kubernetes 버전: v1.31.11-eks-3abbec1
• 총 노드: 12개 (모두 Ready 상태)
• 총 파드: 42개 (모두 Running 상태)

파드 구성:
• AWS VPC CNI: 12개 (네트워킹)
• kube-proxy: 12개 (서비스 프록시)
• EBS CSI: 14개 (스토리지 - 컨트롤러 2개 + 노드 드라이버 12개)
• CoreDNS: 2개 (DNS 서비스)
• Metrics Server: 2개 (메트릭 수집)

클러스터 상태:
• ✅ 모든 시스템 구성요소 정상 작동
• ✅ 재시작 없이 안정적 운영 (10시간 가동)
• ✅ 고가용성 구성 (중요 구성요소 다중 인스턴스)
• ✅ 퍼블릭/프라이빗 노드 그룹 균형 배치

현재 클러스터는 시스템 파드만 실행 중인 깨끗한 상태로, 애플리케이션 워크로드를 배포할 준비가 완료되어 있습니다. 모든 핵심 구성요소가 정상적으로 작동하고 있어 안정적인 운영 환경을 제공하고 있습니다.
```

이 경우 aws cli 도구가 사용되며, 신뢰되지 않은 경우 확인을 요청합니다.

***

## 8. 도구 권한 관리 모범 사례

* **Trust selectively**: 자주 사용하는 도구만 신뢰 상태로 설정하세요.
* **Review actions**: Amazon Q가 수행하려는 작업을 항상 검토한 후 승인하세요.
* **Context matters**: 운영 환경의 민감도를 고려하여 도구 신뢰 여부를 결정하세요.
* **Use per-request for sensitive operations**: AWS CLI 호출이나 셸 명령 실행처럼 민감한 작업은 요청 승인 상태로 유지하는 것이 좋습니다.

