# 폴더 생성 및 변수 값 설정

#### Cloud9 Terminal 창 > root계정으로 진행

#### 작업 폴더 생성

```bash
mkdir /home/ec2-user/environment/eks
chown -R ec2-user:ec2-user /home/ec2-user/environment/eks
cd /home/ec2-user/environment/eks
```

#### 파일 다운로드



#### 주어진 소스에서 variables.tf에 선언된 name과 vpc, subnet cidr에 대한 값을 edu.auto.tfvars파일에서 변경

```bash
##### 학생코드 값을 입력하세요 #####
name = "학생코드"
vpc_cidr = "10.[학생번호].0.0/16"
myip = "x.x.x.x/32" # myip.com에서 Your IP address is부분을 /32를 붙여 cidr값으로 표현
```

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>
