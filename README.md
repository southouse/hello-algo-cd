# hello-argo-cd

## EKS 클러스터 생성 (on Fargate)
```bash
eksctl create cluster --name eks-cluster --region ap-northeast-1 --fargate
```

## Argo CD 설치
```bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## IAM OIDC 생성 
- https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/enable-iam-roles-for-service-accounts.html

## Helm 설치 
- https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/helm.html
## AWS Load Balancer Controller 추가 기능 설치 
- https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/aws-load-balancer-controller.html

## Argo CD 설정
```bash
# Admin 패스워드
kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d; echo
```

### Argo CD Rollout 설치
- https://github.com/argoproj/argo-rollouts
```bash
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts \
-f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```

### Argo CD 관련 명령어
```bash
kubectl argo rollouts list rollouts # rollout 리스트
kubectl argo rollouts get rollout ${rollout_name} # rollout 조회
```

## EKS에서의 Fargate 환경
- `namespace`를 추가할 때 마다 Fargate 프로파일을 지정해줘야 함
- `DaemonSets` 리소스를 지원하지 않음
- Fargate에서 실행되는 Pod는 인터넷 게이트웨이가 지원되지 않아 NAT 게이트웨이가 있는 환경에서만 가능
- 파드 당 최대 16 vCPU, 32~120GB(8GB 단위)까지 사용 가능
- vCPU 및 메모리 조합을 지정하지 않으면 사용 가능한 가장 작은 조합(.25 vCPU 및 0.5GB 메모리)이 사용
- vCPU와 메모리의 조합이 있기 때문에 둘 중 하나라도 조합을 넘으면 다음 상위 조합으로 사용
  - ex. 1vCPU, 9GB 메모리를 요청하면 자동으로 2vCPU, 9GB로 태스크를 생성