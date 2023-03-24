# 클러스터 확인 및 eks.tf 수정

#### AWS 콘솔에서 Terraform으로 생성한 클러스터에 접근을 한다.

1\. [https://ap-northeast-2.console.aws.amazon.com/eks/home?region=ap-northeast-2#/clusters](https://ap-northeast-2.console.aws.amazon.com/eks/home?region=ap-northeast-2)\
2\. 생성한 클러스터 클릭

#### 다음과 같은 메시지 확인 가능

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

#### 생성한 유저/역할만 기본적으로 접근이 가능하다. 본 과정에선 makingeks 역할로 생성하여 유저 계정 권한으로는 볼 수 없다

#### eks.tf의 terraform eks module에 아래 정보 추가

```
# aws-auth configmap # M4. 권한부여
manage_aws_auth_configmap = true
aws_auth_users = [
  {
    userarn = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${var.name}"
    username = "${var.name}"
    groups   = ["system:masters"]
  },
```
