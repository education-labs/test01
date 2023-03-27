# helm

#### Cloud9 Terminal 창 > root계정으로 진행

#### 기본 chart생성

```bash
# 폴더 생성 및 이동
```

```bash
mkdir /home/ec2-user/environment/helm
cd /home/ec2-user/environment/helm
```

```bash
# [user명]이름을 가진 helm chart 생성
```

```bash
helm create $USERNAME
```

```bash
# 결과(예시)
Creating user01
```

#### helm chart 기본 구조

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

#### values.yaml 수정

```bash
# 7번째 라인부터
image:
  repository: public.ecr.aws/l6m2t8p7/docker-2048
  pullPolicy: Always
  # Overrides the image tag whose default is the chart appVersion.
  tag: "latest"
 
# 17번째 라인부터
serviceAccount:
  create: false
 
# 39번째 라인부터
service:
  type: NodePort
 
# 43번째 라인부터
ingress:
  enabled: true
  className: "alb"
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
  hosts:
    - host:
      paths:
        - path: /
          pathType: Prefix
 
# 60번째 라인부터
resources:
  requests:
    cpu: 1
    memory: 1Gi
```

#### 필요 없는 파일 삭제

```bash
rm -rf \$USERNAME/templates/tests
```

#### template/NOTES.txt 수정

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

```bash
export LB_NAME=$(kubectl get ingress $(kubectl get ingress -o=jsonpath={.items[*].metadata.name}) -o=jsonpath={.status.loadBalancer.ingress[*].hostname})
echo "http{{ if $.Values.ingress.tls }}s{{ end }}://$LB_NAME{{ .path }}"
```

#### Helm command로 배포

```bash
# helm 배포
```

```bash
helm upgrade -i $USERNAME $USERNAME --wait
```

```bash
# 결과(예시 - $USERNAME=user01 일 때)
Release "user01" has been upgraded. Happy Helming!
NAME: user01
LAST DEPLOYED: Tue Mar  7 13:54:45 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Get the application URL by running these commands:
  export LB_NAME = $(kubectl get ingress $(kubectl get ingress -o=jsonpath={.items[*].metadata.name}) -o=jsonpath={.status.loadBalancer.ingress[*].hostname})
  http://$LB_NAME/
 
# 결과 확인
```

```bash
kubectl get all
```

```bash
# 정상 결과
NAME                          READY   STATUS    RESTARTS   AGE
pod/user01-7c587c59cc-swprv   1/1     Running   0          9m29s
 
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/user01       NodePort    172.20.57.190   <none>        80:30143/TCP   9m29s
service/kubernetes   ClusterIP   172.20.0.1      <none>        443/TCP        50m
 
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/user01   1/1     1            1           9m29s
 
NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/user01-7c587c59cc   1         1         1       9m29s
```

#### Helm command 결과창에 안내된 것 처럼 배포된 프로그램의 LB주소를 획득한다.

```bash
export LB_NAME = $(kubectl get ingress $(kubectl get ingress -o=jsonpath={.items[*].metadata.name}) -o=jsonpath={.status.loadBalancer.ingress[*].hostname})
export LB_NAME = $(kubectl get ingress $(kub 
echo "http://$LB_NAME/"
```

#### 결과창에 나온 LB주소를 browser에 입력하여 잘 배포 되었는지 확인한다.

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
