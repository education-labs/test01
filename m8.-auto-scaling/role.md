# role 생성

#### Cloud9 Terminal 창 > root계정으로 진행

#### 오토스케일링을 하기 위해 eks node에 붙여줄 role을 만든다.&#x20;

(이름: 학생코드-KarpenterInstanceNodeRole)



```
# [학생코드]에 학생코드 입력
```

```bash
echo 'export USERNAME=devops-user05' >> ~/.bashrc
source ~/.bashrc
```

```
# 확인
```

```bash
echo $USERNAME
```

```
# 작업 폴더 생성
```

```bash
mkdir /home/ec2-user/environment/karpenter
cd /home/ec2-user/environment/karpenter/
```

```
# Policy 파일 생성
```

```bash
echo '{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}' > node-trust-policy.json
```

```
# iam role 생성
```

```bash
aws iam create-role --role-name $USERNAME-KarpenterInstanceNodeRole \ --assume-role-policy-document file://node-trust-policy.json
```

```
# 확인
```

```bash
aws iam get-role --role-name $USERNAME-KarpenterInstanceNodeRole | jq -r .Role.AssumeRolePolicyDocument
```

#### 생성 결과

```
# 결과
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

#### 이 Role에 \[AmazonEKSWorkerNodePolicy, AmazonEKS\_CNI\_Policy, AmazonEC2ContainerRegistryReadOnly, AmazonSSMManagedInstanceCore] 정책들을 붙여준다.

```bash
aws iam attach-role-policy --role-name $USERNAME-KarpenterInstanceNodeRole \
--policy-arn arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy

aws iam attach-role-policy --role-name $USERNAME-KarpenterInstanceNodeRole \
--policy-arn arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy

aws iam attach-role-policy --role-name $USERNAME-KarpenterInstanceNodeRole \
--policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly

aws iam attach-role-policy --role-name $USERNAME-KarpenterInstanceNodeRole \
--policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore
```

```
# 결과 확인
```

```bash
aws iam list-attached-role-policies --role-name $USERNAME-KarpenterInstanceNodeRole
```

#### 생성 결과

```
# 결과                                                                                                                            
{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonSSMManagedInstanceCore",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
        },
        {
            "PolicyName": "AmazonEKS_CNI_Policy",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy"
        },
        {
            "PolicyName": "AmazonEC2ContainerRegistryReadOnly",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly"
        },
        {
            "PolicyName": "AmazonEKSWorkerNodePolicy",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy"
        }
    ]
}
```

#### scaling될 Node에 붙일 Instance profile을 만들고 학생코드-KarpenterInstanceNodeRole를 붙인다

```bash
aws iam create-instance-profile \
--instance-profile-name $USERNAME-KarpenterInstanceProfile

aws iam add-role-to-instance-profile \
--instance-profile-name $USERNAME-KarpenterInstanceProfile \
--role-name $USERNAME-KarpenterInstanceNodeRole
```

```
# 확인
```

```bash
aws iam get-instance-profile --instance-profile-name $USERNAME-KarpenterInstanceProfile | jq -r .InstanceProfile.Roles[0].RoleName
```

```
# 결과(예시)
user01-KarpenterInstanceNodeRole
```

