# eks.tf 파일 생성

#### 아래와 같이 eks.tf 파일을 생성하고 module 자원명이 eks인 모듈을 요구사항에 부합하도록 작성한다.

```bash
cd /home/ec2-user/environment/eks
```

```bash
cat > eks.tf <<EOF
module "eks"{
  source  = "terraform-aws-modules/eks/aws"                         # 모듈명
  version = "~>19.10.0"                                             # 모듈버전
  ######아래를 작성한다#####
 
}
EOF
```

