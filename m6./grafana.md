# grafana

#### Cloud9 Terminal 창 > root계정으로 진행

#### grafana 설치

```
# 디렉토리 생성
```

```bash
mkdir /home/ec2-user/environment/grafana
```

```bash
# yaml파일 생성​
cat << EoF > /home/ec2-user/environment/grafana/grafana.yaml​
datasources:​
  datasources.yaml:​
    apiVersion: 1​
    datasources:​
    - name: Prometheus​
      type: prometheus​
      url: http://prometheus-server.prometheus.svc.cluster.local​
      access: proxy​
      isDefault: true​
EoF
```

```
# helm repo add
```

```bash
helm repo add grafana https://grafana.github.io/helm-charts
```

```
# grafana 배포용 namespace 생성
```

```bash
kubectl create namespace grafana
```

```
# helm을 이용하여 배포
```

```bash
helm upgrade -i grafana grafana/grafana --namespace grafana --set persistence.storageClassName="gp2" --set persistence.enabled=true --set adminPassword='admin123' --values /home/ec2-user/environment/grafana/grafana.yaml --set service.type=LoadBalancer --wait
```

```
# 배포 확인
```

```bash
kubectl get all -n grafana
```

#### 배포 확인

```
# 결과
NAME                          READY   STATUS    RESTARTS   AGE
pod/grafana-85c4d67b6-zvsxf   1/1     Running   0          2m2s
 
NAME              TYPE           CLUSTER-IP      EXTERNAL-IP                                                                   PORT(S)        AGE
service/grafana   LoadBalancer   172.20.96.250   a816063d499024e50a7a8caf9b78c60d-267075833.ap-northeast-2.elb.amazonaws.com   80:30680/TCP   2m2s
 
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1/1     1            1           2m2s
 
NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-85c4d67b6   1         1         1       2m2s
```

#### grafana 연결

```
# LB 주소 확인
```

```bash
echo "http://"`kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'`
```

```
# 결과값을 브라우저에 입력
```

#### grafana login

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

#### Data source확인&#x20;

좌측 메뉴

Configuration > Data sources

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

#### eks 내부 dns에 의해 prometheus namespace에 있는 prometheus-server 서비스 연결 확인

#### (EKS 내부 DNS : <서비스명>.<네임스페이스명>.svc.cluster.local)

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

#### Dashboards import하여 cluster 모니터링을 확인한다.&#x20;

좌측 메뉴&#x20;

Dashboards > + import 클릭

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

#### Import via grafana.com 에서 3119입력 후 load

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

#### prometheus 부분에 prometheus 입력 후 Import

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

#### Cluster monitoring Dashboard

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

#### Dashboards import하여 pod 모니터링을 확인한다.&#x20;

좌측 메뉴

Dashboards > + import 클릭

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

#### Import via grafana.com 에서 6417입력 후 load

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

#### prometheus 부분에 prometheus 입력 후 Import

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

#### pod monitoring Dashboard&#x20;

namespace 부분에 default를 입력하면 배포한 솔루션이 보인다

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
