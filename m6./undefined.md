# 배포 및 확인

#### Cloud9 Terminal 창 > root계정으로 진행

#### terraform으로 배포한다.

```bash
# 모듈이 추가 되었으므로 init명령어 수행
terraform init
 
# plan
terraform plan
 
# 결과
Plan: 4 to add, 0 to change, 0 to destroy.
 
# apply
terraform apply
 
# apply결과
Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
```



#### EKS > Cluster > 학생코드

#### aws 콘솔상의 추가기능에서 Amazon EBS CSI 드라이버가 있는 것들 확인한다.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
