# python-devsecops-app

Flask 기반 DevSecOps 파이프라인 및 EKS 배포 프로젝트

## 프로젝트 구성

### 애플리케이션
- **Flask 웹 앱**: 간단한 "Hello, World!" 서비스
- **Docker**: 컨테이너화된 배포
- **Python 3.11**: 런타임 환경

### DevSecOps 파이프라인

#### GitHub Actions 워크플로우
**빌드 & 배포**
- Docker 이미지 빌드 및 Docker Hub 푸시
- 태그: `zziwon/flask-devsecops:latest`

**보안 스캔**
- **Bandit**: Python SAST (정적 코드 분석)
- **Safety**: Python SCA (의존성 취약점 검사)
- **Trivy**: 컨테이너 이미지 스캔

**보고서**
- GitHub Artifacts에 스캔 결과 저장
- SARIF 형식으로 GitHub Security 탭 연동

### EKS 인프라 (Terraform)

#### 클러스터 구성
- **리전**: ap-northeast-1 (도쿄)
- **버전**: EKS 1.28
- **노드**: t3.medium (1-3개)

#### 네트워크
- **VPC**: 10.0.0.0/16
- **가용영역**: 3개
- **서브넷**: 퍼블릭/프라이빗 분리

#### 애드온
- **AWS Load Balancer Controller**: ALB/NLB 관리
- **EBS CSI Driver**: 영구 볼륨
- **External DNS**: Route53 DNS 자동화

## 실행 방법

### 로컬 실행
```bash
pip install -r requirements.txt
python app.py
```

### Docker 실행
```bash
docker build -t flask-app .
docker run -p 5000:5000 flask-app
```

### EKS 배포
```bash
cd terraform
terraform init
terraform plan
terraform apply
```

### kubectl 설정
```bash
aws eks --region ap-northeast-1 update-kubeconfig --name devsecops-eks
```

## 파이프라인 실행 순서
1. 코드 푸시 → GitHub Actions 트리거
2. 보안 스캔 (Bandit, Safety, Trivy)
3. Docker 이미지 빌드 & 푸시
4. Terraform으로 EKS 인프라 배포 (수동)
5. 애플리케이션 배포