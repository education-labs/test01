# efk.tf 적용 및 배포

#### Cloud9 Terminal 창 > root계정으로 진행

#### terraform을 통해 opensearch domain과 k8s 내부의 fluent-bit에서 사용할 role과 policy, kubernetes serviceaccount(irsa)를 만든다.&#x20;

#### opensearch는 기본적으로 access policy를 설정하도록 강제하는데, opensearch에 접근할 사용자의 로컬과 cloud9, eks 내부의 fluent-bit에서 접근을 허용하기 위해 ip를 추가한다.&#x20;

#### 주어진 efk.tf 파일로 배포한다.(domain은 만들어지는데 약 20분 정도 소요된다.)

```
# plan
terraform plan
 
# plan 결과
Plan: 6 to add, 0 to change, 0 to destroy.
 
# Apply
terraform apply
 
# apply 결과
Apply complete! Resources: 6 added, 0 changed, 0 destroyed.
```
