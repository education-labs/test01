# 배포 및 확인

#### Cloud9 Terminal 창 > root계정으로 진행

#### 완성 후 아래 커맨드로 eks 배포(약 15분 소요)

```bash
# eks module이 추가되었으므로 init을 다시함
```

```bash
terraform init
```

```bash
# edu.auto.tfvars의 variable값을 이용하여 plan값 확인
```

```bash
terraform plan
```

```bash
# plan 결과(구성요소에 따라 변할 수 있음)
Plan: 30 to add, 0 to change, 0 to destroy
 
# edu.auto.tfvars의 variable 값을 이용하여 apply
```

```bash
terraform apply
```

```bash
# apply 결과(구성요소에 따라 변할 수 있음)
 Apply complete! Resources: 30 added, 0 changed, 0 destroyed.
```



#### aws cli command로 kubectl config 파일을 업데이트 하여 kubectl로 노드의 상태를 확인한다.

```bash
# kubectl 권한 획득
```

```bash
aws eks update-kubeconfig --region ap-northeast-2 --name $USERNAME --role-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):role/makingeks
```

```bash
# kubectl cmd로 접근 확인
```

```bash
kubectl get nodes
```

```bash
# node 2개가 보이면 완료
```

