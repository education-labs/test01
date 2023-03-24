# 배포 및 확인

#### Cloud9 Terminal 창 > root계정으로 진행

#### terraform command로 배포

```bash
# plan
terraform plan
 
# plan 결과
Plan: 1 to add, 0 to change, 0 to destroy.
 
# Apply
terraform apply
 
# apply 결과
module.eks.kubernetes_config_map_v1_data.aws_auth[0]: Creating...
module.eks.kubernetes_config_map_v1_data.aws_auth[0]: Creation complete after 0s [id=kube-system/aws-auth]
 
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

#### 결과 확인

```bash
kubectl -n kube-system get configmap aws-auth -o jsonpath='{.data.mapUsers}'
```

```bash
# 결과
- "groups":
  - "system:masters"
  "userarn": "arn:aws:iam::[ACCOUNT_ID]:user/[USER명]"
  "username": "[USER명]"
```

참고(직접 수정방법) - [https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/add-user-role.html](https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/add-user-role.html)
