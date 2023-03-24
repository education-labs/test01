# prometheus

#### Cloud9 Terminal 창 > root계정으로 진행

#### prometheus를 배포한다.

```bash
# 배포를 위한 namespace구성
```

```bash
kubectl create namespace prometheus
```

```bash
# 결과
namespace/prometheus created
```

```bash
# helm repo add
```

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

```bash
# 결과
"prometheus-community" has been added to your repositories
 
# helm을 이용해 배포
```

```bash
helm upgrade -i prometheus prometheus-community/prometheus \ --namespace prometheus \ --set alertmanager.persistentVolume.storageClass="gp2",server.persistentVolume.storageClass="gp2" \ --wait
```

```bash
# 배포 결과 확인
```

<pre class="language-bash"><code class="lang-bash"><strong>kubectl get all -n prometheus
</strong></code></pre>

#### 배포 결과

```
# 커맨드결과
NAME                                                    READY   STATUS    RESTARTS   AGE
pod/prometheus-alertmanager-0                           1/1     Running   0          53s
pod/prometheus-kube-state-metrics-6fcf5978bf-sljxt      1/1     Running   0          53s
pod/prometheus-prometheus-node-exporter-c2kns           1/1     Running   0          53s
pod/prometheus-prometheus-node-exporter-fvcg7           1/1     Running   0          53s
pod/prometheus-prometheus-pushgateway-fdb75d75f-25txb   1/1     Running   0          53s
pod/prometheus-server-5c985f5fb5-hh42q                  2/2     Running   0          53s
 
NAME                                          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/prometheus-alertmanager               ClusterIP   172.20.35.121   <none>        9093/TCP   53s
service/prometheus-alertmanager-headless      ClusterIP   None            <none>        9093/TCP   53s
service/prometheus-kube-state-metrics         ClusterIP   172.20.26.91    <none>        8080/TCP   53s
service/prometheus-prometheus-node-exporter   ClusterIP   172.20.196.26   <none>        9100/TCP   53s
service/prometheus-prometheus-pushgateway     ClusterIP   172.20.10.0     <none>        9091/TCP   53s
service/prometheus-server                     ClusterIP   172.20.127.78   <none>        80/TCP     53s
 
NAME                                                 DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/prometheus-prometheus-node-exporter   2         2         2       2            2           <none>          53s
 
NAME                                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/prometheus-kube-state-metrics       1/1     1            1           53s
deployment.apps/prometheus-prometheus-pushgateway   1/1     1            1           53s
deployment.apps/prometheus-server                   1/1     1            1           53s
 
NAME                                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/prometheus-kube-state-metrics-6fcf5978bf      1         1         1       53s
replicaset.apps/prometheus-prometheus-pushgateway-fdb75d75f   1         1         1       53s
replicaset.apps/prometheus-server-5c985f5fb5                  1         1         1       53s
 
NAME                                       READY   AGE
statefulset.apps/prometheus-alertmanager   1/1     53s

```

#### prometheus에 포트포워딩

```
# prometheus 연결
```

```sh
kubectl port-forward -n prometheus deploy/prometheus-server 8080:9090
```

```
# 결과 -> 아래 같은 결과가 나옴
Forwarding from 127.0.0.1:8080 -> 9090
Forwarding from [::1]:8080 -> 9090
```

#### cloud9에서 Prometheus 연결&#x20;

#### Tools > Preview > Preview Running Application

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

#### status > targets로 확인 가능

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>
