# CI/CD with EKS

## 프로젝트 개요

AWS EKS 기반 Spring 애플리케이션의 클라우드 네이티브 배포 파이프라인을 구축합니다.

![아키텍처 다이어그램](/docs/images/deploy_eks.jpg)
- **프로젝트명**: deploy_eks
- **목표 클라우드**: AWS EKS

---

## Phase 1: 환경 세팅

### 목표
AWS 계정 설정, CLI 구성, EKS 클러스터 생성 등 인프라 기반 환경을 구성합니다.

### 작업 내용

- AWS CLI, kubectl, eksctl 등 로컬 개발 도구 설치 및 구성
- IAM 사용자/역할 설정 및 자격 증명 구성
- EKS 클러스터 생성을 위한 기본 네트워크(VPC, 서브넷) 구성

---

## Phase 2: EKS 내부 구성

### 목표
클러스터 내부의 핵심 리소스와 정책을 설정하여 워크로드 실행 환경을 준비합니다.

### 작업 내용

- 네임스페이스 전략 수립 및 생성
- 서비스 어카운트 및 IRSA(IAM Roles for Service Accounts) 설정
- 클러스터 애드온 설치 (CoreDNS, kube-proxy, VPC CNI)

---

## Phase 3: 배포 아키텍처 구성

### 목표
CI/CD 파이프라인의 전체 흐름과 배포 전략을 설계합니다.

### 작업 내용

- 전체 배포 아키텍처 다이어그램 설계
- GitHub Actions → ECR → ArgoCD → EKS 흐름 정의
- 이미지 태그 전략 수립 (Git SHA 기반)
- 환경별(dev/staging/prod) 배포 분기 전략 설계

---

## Phase 4: K8S 리소스 생성

### 목표
애플리케이션 구동에 필요한 Kubernetes 리소스를 정의합니다.

### 작업 내용

- Deployment, Service, Ingress 매니페스트 작성
- ConfigMap / Secret을 통한 환경 변수 관리
- PVC를 활용한 영속 스토리지 구성
- Health check 엔드포인트 연동 (livenessProbe, readinessProbe)

---

## Phase 5: GitHub Actions Workflow

### 목표
코드 푸시 시 자동으로 빌드, 테스트, 이미지 푸시가 수행되는 CI 파이프라인을 구성합니다.

### 작업 내용

- GitHub Actions 워크플로우 작성
  - Build → Test → Docker Image Build → ECR Push
- ECR 레포지토리 생성 및 이미지 관리 정책 설정
- GitOps 매니페스트 자동 업데이트 연동

---

## Phase 6: AutoScale 적용

### 목표
트래픽 변화에 따른 자동 스케일링을 설정하여 안정성과 비용 효율성을 확보합니다.

### 작업 내용

- HPA(Horizontal Pod Autoscaler) 설정
  - CPU/Memory 기반 파드 오토스케일링
  - 최소/최대 레플리카 수 정의
- Karpenter 설치 및 구성
  - 노드 수준 오토스케일링 자동화
  - Provisioner 정책 설정 (인스턴스 타입, 용량 타입)

---

## Phase 7: ArgoCD 구성

### 목표
GitOps 방식의 CD를 구현하여 Git 저장소 기반 자동 배포 체계를 완성합니다.

### 작업 내용

- ArgoCD 설치 및 초기 설정
- Git 저장소 연동 및 Application 리소스 등록
- Sync Policy 설정 (자동/수동 동기화)
- 배포 상태 모니터링 및 롤백 전략 수립

---

## Phase 8: Observability 구성

### 목표
메트릭 수집 및 시각화를 통해 클러스터와 애플리케이션 상태를 모니터링합니다.

### 작업 내용

- Prometheus 설치 및 메트릭 수집 구성
- Grafana 대시보드 구성
  - 클러스터 리소스 현황 (CPU, Memory, Network)
  - 애플리케이션 성능 지표 (요청 수, 응답 시간, 에러율)
- 알림 규칙 설정 및 Slack 연동

---

## 부록: ALB 구성

### 목표
AWS Application Load Balancer를 통해 외부 트래픽을 클러스터 내부 서비스로 라우팅합니다.

### 작업 내용

- AWS Load Balancer Controller 설치
- Ingress 리소스와 ALB 연동 설정
- SSL/TLS 인증서 적용 (ACM 연동)
- Route53 DNS 레코드 연결

---
