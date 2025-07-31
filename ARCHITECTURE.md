# EKS 클러스터 아키텍처

## 개요
AWS EKS 기반 DevSecOps 플랫폼 아키텍처

## 인프라 구성

### 네트워크
- **VPC**: 10.0.0.0/16
- **가용 영역**: 3개 (ap-northeast-1a, 1c, 1d)
- **퍼블릭 서브넷**: 10.0.101.0/24, 10.0.102.0/24, 10.0.103.0/24
- **프라이빗 서브넷**: 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24
- **NAT 게이트웨이**: 활성화

### EKS 클러스터
- **클러스터명**: devsecops-eks
- **버전**: 1.28
- **리전**: ap-northeast-1 (도쿄)
- **엔드포인트**: 퍼블릭 액세스 활성화

### 노드 그룹
- **인스턴스 타입**: t3.medium
- **최소 노드**: 1개
- **최대 노드**: 3개
- **희망 노드**: 2개
- **배치**: 프라이빗 서브넷

## 애드온 구성

### AWS Load Balancer Controller
- **버전**: 1.6.2
- **네임스페이스**: kube-system
- **기능**: ALB/NLB 자동 프로비저닝

### EBS CSI Driver
- **버전**: 2.25.0
- **네임스페이스**: kube-system
- **기능**: 영구 볼륨 관리

### External DNS
- **버전**: 1.13.1
- **네임스페이스**: kube-system
- **기능**: Route53 DNS 자동 관리

## IAM 역할

### AWS Load Balancer Controller
- **역할명**: aws-load-balancer-controller
- **서비스 계정**: aws-load-balancer-controller
- **권한**: ELB 생성/관리, EC2 네트워크 조회

### EBS CSI Controller
- **역할명**: AmazonEKS_EBS_CSI_DriverRole
- **서비스 계정**: ebs-csi-controller-sa
- **권한**: EBS 볼륨 관리

### External DNS
- **역할명**: external-dns
- **서비스 계정**: external-dns
- **권한**: Route53 레코드 관리

## 보안 구성
- **OIDC 프로바이더**: 활성화
- **IRSA**: 모든 서비스 계정에 적용
- **네트워크**: 프라이빗 서브넷에 워커 노드 배치
- **엔드포인트**: 퍼블릭 API 서버 액세스

## 배포 명령어
```bash
cd terraform
terraform init
terraform plan
terraform apply
```

## kubectl 설정
```bash
aws eks --region ap-northeast-1 update-kubeconfig --name devsecops-eks
```