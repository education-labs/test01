# karpenter

#### Cloud9 Terminal 창 > root계정으로 진행

#### 앞으로 명령어 수행을 위해 shell에서 환경 변수를 선언한다.

```bash
CLUSTER_NAME=$USERNAME

CLUSTER_ENDPOINT="$(aws eks describe-cluster --name ${CLUSTER_NAME} --region ap-northeast-2 --query "cluster.endpoint" --output text)"

OIDC_ENDPOINT="$(aws eks describe-cluster --name ${CLUSTER_NAME} --region ap-northeast-2 --query "cluster.identity.oidc.issuer" --output text)"

AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query 'Account' --output text)
```

```
# 확인
```

```bash
echo $CLUSTER_NAME
echo $CLUSTER_ENDPOINT
echo $OIDC_ENDPOINT
echo $AWS_ACCOUNT_ID
```

```
# 각각 값이 잘 들어갔는지 확인
```

#### Karpenter Controller가 수행할 role과 policy를 만들고 이를 붙여준다.(1/2)

```bash
echo "{
    \"Version\": \"2012-10-17\",
    \"Statement\": [
        {
            \"Effect\": \"Allow\",
            \"Principal\": {
                \"Federated\": \"arn:aws:iam::${AWS_ACCOUNT_ID}:oidc-provider/${OIDC_ENDPOINT#*//}\"
            },
            \"Action\": \"sts:AssumeRoleWithWebIdentity\",
            \"Condition\": {
                \"StringEquals\": {
                    \"${OIDC_ENDPOINT#*//}:aud\": \"sts.amazonaws.com\",
                    \"${OIDC_ENDPOINT#*//}:sub\": \"system:serviceaccount:karpenter:karpenter\"
                }
            }
        }
    ]
}" > controller-trust-policy.json
```

```bash
aws iam create-role --role-name ${CLUSTER_NAME}-KarpenterControllerRole --assume-role-policy-document file://controller-trust-policy.json
```

#### Karpenter Controller가 수행할 role과 policy를 만들고 이를 붙여준다.(2/2)

```bash
echo '{
    "Statement": [
        {
            "Action": [
                "ssm:GetParameter",
                "iam:PassRole",
                "ec2:DescribeImages",
                "ec2:RunInstances",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeLaunchTemplates",
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceTypes",
                "ec2:DescribeInstanceTypeOfferings",
                "ec2:DescribeAvailabilityZones",
                "ec2:DeleteLaunchTemplate",
                "ec2:CreateTags",
                "ec2:CreateLaunchTemplate",
                "ec2:CreateFleet",
                "ec2:DescribeSpotPriceHistory",
                "pricing:GetProducts"
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Sid": "Karpenter"
        },
        {
            "Action": "ec2:TerminateInstances",
            "Condition": {
                "StringLike": {
                    "ec2:ResourceTag/Name": "*karpenter*"
                }
            },
            "Effect": "Allow",
            "Resource": "*",
            "Sid": "ConditionalEC2Termination"
        }
    ],
    "Version": "2012-10-17"
}' > controller-policy.json
```

```bash
aws iam put-role-policy --role-name ${CLUSTER_NAME}-KarpenterControllerRole --policy-name ${CLUSTER_NAME}-KarpenterControllerPolicy --policy-document file://controller-policy.json
```

#### Karpenter가 node를 생성하고 subnet과 security group을 할당하기 위해서는 권한 뿐만 아니라 알맞은 태그가 필요하므로 태그를 만들어준다.(subnet)

```
# subnet에 tag 추가
```

```bash
for NODEGROUP in $(aws eks list-nodegroups --cluster-name ${CLUSTER_NAME} --region ap-northeast-2 --query 'nodegroups' --output text); do aws ec2 create-tags --region ap-northeast-2 --tags "Key=karpenter.sh/discovery,Value=${CLUSTER_NAME}" --resources $(aws eks describe-nodegroup --cluster-name ${CLUSTER_NAME} --region ap-northeast-2 --nodegroup-name $NODEGROUP --query 'nodegroup.subnets' --output text )
done
```

```
# 확인
```

```bash
aws ec2 describe-subnets --region ap-northeast-2 --filters "Name=tag:karpenter.sh/discovery,Values=$CLUSTER_NAME" --query "Subnets[*].Tags[].Value"
```

```
# 결과(예시) - 순서 상관 없이 아래 4개가 나오면 됨
[
    "user01",
    "user01-devops-vpc-private-ap-northeast-2c",
    "user01-devops-vpc-private-ap-northeast-2a",
    "user01"
]
```

```
# NODE 그룹명 변수 지정
```

```bash
NODEGROUP=$(aws eks list-nodegroups --cluster-name ${CLUSTER_NAME} --region ap-northeast-2 --query 'nodegroups[0]' --output text)
```

```
# 결과 확인
```

```bash
echo $NODEGROUP
```

```
# 결과(예시)
test-2023XXXXXX
```

```
# managed node group launch_template 확인
```

```bash
LAUNCH_TEMPLATE=$(aws eks describe-nodegroup --cluster-name ${CLUSTER_NAME} --region ap-northeast-2 --nodegroup-name ${NODEGROUP} --query 'nodegroup.launchTemplate.{id:id,version:version}' --output text | tr -s "\t" ",")
```

```
# If your setup uses the security groups in the Launch template of a managed node group, then :
# Get node group's security group
```

```bash
SECURITY_GROUPS=$(aws ec2 describe-launch-template-versions --region ap-northeast-2 --launch-template-id ${LAUNCH_TEMPLATE%,*} --versions ${LAUNCH_TEMPLATE#*,} --query 'LaunchTemplateVersions[0].LaunchTemplateData.[NetworkInterfaces[0].Groups||SecurityGroupIds]' --output text)
```

```
# set tag
```

```bash
aws ec2 create-tags --region ap-northeast-2 --tags "Key=karpenter.sh/discovery,Value=${CLUSTER_NAME}" --resources ${SECURITY_GROUPS}
```

```
# 결과 확인
```

```bash
aws ec2 describe-security-groups --region ap-northeast-2 --group-ids $SECURITY_GROUPS | jq -r .SecurityGroups[].Tags[]
```

```
# 결과(예시)
{
  "Key": "kubernetes.io/cluster/user01",
  "Value": "owned"
}
{
  "Key": "Name",
  "Value": "user01-node"
}
{
  "Key": "karpenter.sh/discovery",
  "Value": "user01"
}
```

#### karpenter가 내부 kubernetes 자원에 접근하기 위해 aws-auth에 다음 내용을 추가한다. (5. 권한 부여에서는 terraform으로 진행했지만 이 과정에서는 kubectl을 이용하여 직접 수정한다.)&#x20;

#### ${AWS\_ACCOUNT\_ID}과 ${USERNAME}은 직접 값을 변경하여 입력한다. 단, \{{EC2PrivateDNSName\}}는 변경하면 안된다.

```bash
kubectl edit configmap aws-auth -n kube-system
```

#### 아래 값을 mapRoles 하위에 입력

```bash
- "groups":
      - "system:bootstrappers"
      - "system:nodes"
      "rolearn": "arn:aws:iam::${AWS_ACCOUNT_ID}:role/${USERNAME}-KarpenterInstanceNodeRole"
      "username": "system:node:{{EC2PrivateDNSName}}"
```

#### 변경한 결과

```
# 결과 확인
```

```bash
kubectl describe configmap aws-auth -n kube-system
```

```
# 결과(예시)
Name:         aws-auth
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>
 
Data
====
mapAccounts:
----
[]
 
mapRoles:
----
- "groups":
  - "system:bootstrappers"
  - "system:nodes"
  "rolearn": "arn:aws:iam::XXXXXXXXXXXX:role/test-eks-node-group-20230313093417589000000001"
  "username": "system:node:{{EC2PrivateDNSName}}"
- "groups":
  - "system:bootstrappers"
  - "system:nodes"
  "rolearn": "arn:aws:iam::XXXXXXXXXXXX:role/user01-KarpenterInstanceNodeRole"
  "username": "system:node:{{EC2PrivateDNSName}}"
 
mapUsers:
----
- "groups":
  - "system:masters"
  "userarn": "arn:aws:iam::995895514601:user/user01"
  "username": "user01"
 
 
BinaryData
====
 
Events:  <none>
```

