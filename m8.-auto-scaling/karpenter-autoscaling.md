# karpenter 배포 및 autoscaling

#### Cloud9 Terminal 창 > root계정으로 진행

#### helm으로 deploy할 karpenter version을 환경변수로 선언한다.

```bash
export KARPENTER_VERSION=v0.25.0
```

#### helm을 통해 karpenter를 배포한다.

```
# 배포할 namespace 생성
```

```bash
kubectl create namespace karpenter
```

```
# helm배포
```

```bash
helm upgrade -i karpenter oci://public.ecr.aws/karpenter/karpenter --version ${KARPENTER_VERSION} --namespace karpenter --set settings.aws.defaultInstanceProfile=${CLUSTER_NAME}-KarpenterInstanceProfile --set settings.aws.clusterEndpoint="${CLUSTER_ENDPOINT}" --set settings.aws.clusterName=${CLUSTER_NAME} --set serviceAccount.annotations."eks\.amazonaws\.com/role-arn"="arn:aws:iam::${AWS_ACCOUNT_ID}:role/${CLUSTER_NAME}-KarpenterControllerRole" --set controller.resources.requests.cpu=1 --set controller.resources.requests.memory=1Gi --set controller.resources.limits.cpu=1 --set controller.resources.limits.memory=1Gi --set affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[0].matchExpressions[0].key="karpenter.sh/provisioner-name" --set affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[0].matchExpressions[0].operator="DoesNotExist" --set affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[1].matchExpressions[0].key="eks.amazonaws.com/nodegroup" --set affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[1].matchExpressions[0].operator="In" --set affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[1].matchExpressions[0].values[0]=${NODEGROUP} --version ${KARPENTER_VERSION} --wait
```

```
# 결과
Release "karpenter" does not exist. Installing it now.
Pulled: public.ecr.aws/karpenter/karpenter:v0.25.0
Digest: sha256:f9e7662f6f083b6db321e4285fd5b0692ee6d9d8b4e4c09f2a579d8177acb16d
NAME: karpenter
LAST DEPLOYED: Mon Mar 13 13:58:59 2023
NAMESPACE: karpenter
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

```
# 배포 확인
```

```bash
kubectl get all -n karpenter
```

#### 배포 결과

```
# 결과 확인
kubectl get all -n karpenter
 
# 결과
NAME                             READY   STATUS    RESTARTS   AGE
pod/karpenter-7684c57f5f-765dj   1/1     Running   0          21h
pod/karpenter-7684c57f5f-8tnbg   1/1     Running   0          21h
 
NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)            AGE
service/karpenter   ClusterIP   172.20.77.206   <none>        8080/TCP,443/TCP   21h
 
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/karpenter   2/2     2            2           21h
 
NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/karpenter-7684c57f5f   2         2         2       21h
```

#### node provisioning을 위한 provisioner를 생성한다.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: karpenter.sh/v1alpha5
kind: Provisioner
metadata:
    name: default
spec:
    ttlSecondsAfterEmpty: 60
    requirements:
    - key: node.kubernetes.io/instance-type
      operator: In
      values: ["t3.xlarge"]
    providerRef:
        name: default
---
apiVersion: karpenter.k8s.aws/v1alpha1
kind: AWSNodeTemplate
metadata:
    name: default
spec:
    subnetSelector:
        karpenter.sh/discovery: "${CLUSTER_NAME}"
    securityGroupSelector:
        karpenter.sh/discovery: "${CLUSTER_NAME}"
EOF
```

#### auto-scaler가 설치되어 있는 경우 삭제한다.

```bash
kubectl scale deploy/cluster-autoscaler -n kube-system --replicas=0
```

#### M5에서 배포한 예제 소스의 replicas 숫자를 조정하여 node autoscaling이 되는지 확인한다.

```
# helm chart 폴더로 이동
```

```bash
cd /home/ec2-user/environment/helm
```

```
# chart의 replicas수를 조정하여 배포
```

```bash
helm upgrade -i $USERNAME $USERNAME --set replicaCount=9 --wait
```

```
# node 상태 확인
```

```bash
kubectl get nodes
```

```
# 결과 예시
NAME                                            STATUS   ROLES    AGE   VERSION
ip-10-0-1-64.ap-northeast-2.compute.internal    Ready    <none>   8h    v1.24.10-eks-48e63af
ip-10-0-2-140.ap-northeast-2.compute.internal   Ready    <none>   31m   v1.24.10-eks-48e63af
ip-10-0-2-160.ap-northeast-2.compute.internal   Ready    <none>   8h    v1.24.10-eks-48e63af
ip-10-0-2-35.ap-northeast-2.compute.internal    Ready    <none>   31m   v1.24.10-eks-48e63af
```
