---
description: 'update : 2025.05.07'
---

# Agentic/MCP for EKS

Agentic for EKS는 Amazon Q Developer CLI와 Model Context Protocol(MCP)을 활용하여 Amazon EKS 환경에서 에이전트 기반 자동화와 운영 효율화를 실현하는 워크샵입니다.

이 리포지토리는 다음과 같은 목표를 지향합니다:

* EKS 클러스터 인벤토리 조회 자동화
* 마이크로서비스 애플리케이션 배포 및 업데이트 (TBD)
* 컨텍스트 기반 지능형 상호작용 (예: YAML 수정, 다이어그램 생성, 리소스 분석)
* Playwright를 통한 브라우저 액세스 및 문서 요약 자동화
* Mermaid 기반 토폴로지 시각화와 MkDocs 통합

#### 📚 포함된 내용

* Amazon Q CLI 세션 자동화 명령어
* .amazonq/rules/ 기반 컨텍스트 템플릿
* mcp.json 예제 구성 (Kubernetes + Playwright)
* Kustomize 기반 샘플 애플리케이션
* Markdown 다이어그램 출력 예시

#### 🧪 주요 유즈케이스

* EKS 클러스터 상태 점검 (pods, deployments, IAM, addons 등)
* YAML 기반 애플리케이션 업데이트 및 롤링 배포
* 클러스터 토폴로지 다이어그램 자동 생성
* AWS Best Practices 웹 문서 요약 요청

***

> 이 워크샵은 Amazon EKS 환경에서 LLM 기반 운영 자동화를 실험하고자 하는 모든 개발자, DevOps 엔지니어, SRE를 위한 학습 리소스를 제공합니다.

