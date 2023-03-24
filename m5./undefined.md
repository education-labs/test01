# 배포 및 확인

#### Cloud9 Terminal 창 > root계정으로 진행

#### terraform command로 배포한다.

```bash
## init
terraform init
 
# plan
terraform plan
 
# 결과
Plan: 5 to add, 0 to change, 0 to destroy.
 
#배포
terraform apply
 
# 결과
Apply complete! Resources: 5 added, 0 changed, 0 destroyed.
```

#### 배포 결과 확인

```bash
# 결과 확인
```

```bash
kubectl get deployment aws-load-balancer-controller -n kube-system
```

```bash
# 아래와 같으면 완료                                                                                                                                                  
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
aws-load-balancer-controller   2/2     2            2           2m59s
```

참고(terraform 이외의 설치 방법) - [AWS Load Balancer Controller ](https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/aws-load-balancer-controller.html)[추가 기능 설치 ](https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/aws-load-balancer-controller.html)[- Amazon EKS](https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/aws-load-balancer-controller.html)
