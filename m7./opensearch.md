# OpenSearch

#### Amazon OpenSearch Service > Domains > usercode

#### 현상태로는 fluentbit에서 opensearch로 데이터를 전송할 수 없다.&#x20;

#### fluent-bit에서 opensearch에 접근하기 위해서 IRSA 뿐만 아니라, opensearch 내부 database 접근에 대한 허용 요청을 하기 위해서 master user를 생성해야 한다.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

#### 아래와 같이master\_user\_name과 master\_user\_password를 설정한다. (약 40분 소요)

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

#### Cloud9 Terminal 창 > root계정으로 진행

#### 다음과 같은 명령어를 opensearch에 보내서 fluent-bit에서 opensearch에 접근할 수 있도록 한다.

```
# 생성한 master username과 password를 변수 선언
```

```bash
MASTER_USER_NAME=test
MASTER_USER_PASSWORD=Test123@
```

```
# opensearch domain endpoint 확인
```

```bash
OPENSEARCH_DOMAIN_ENDPOINT=$(aws opensearch describe-domain --domain-name $USERNAME --region ap-northeast-2 | jq -r .DomainStatus.Endpoint)
```

```
# opensearch role arn 확인
```

```bash
OPENSEARCH_ROLE_ARN=$(aws iam get-role --role-name $USERNAME-role-opensearch | jq -r .Role.Arn)
```

```
# opensearch internal db에 접근할 수 있게 하는 명령어
```

```bash
curl -sS -u "$MASTER_USER_NAME:$MASTER_USER_PASSWORD" \​
-X PATCH https://$OPENSEARCH_DOMAIN_ENDPOINT/_opendistro/_security/api/rolesmapping/all_access?pretty \​
-H 'Content-Type: application/json' \​
-d '[{"op": "add", "path": "/backend_roles", "value": [ "$OPENSEARCH_ROLE_ARN" ]}]'
```

```
# 결과
{
  "status" : "OK",
  "message" : "'all_access' updated."
}
```

#### kubectl로 전달받은 fluent-bit.yaml을 배포한다. clusterrole, clusterrolebinding, configmap, daemonset 등을 설치한다. (kubectl apply -f fluent-bit.yaml)&#x20;

#### 단, 파일의 daemonset부분 에서 spec.template.spec.containers.env에 알맞은 값을 넣어주어야 한다. (endpoint는 https://를 생략한 후 넣어줘야 한다. - 다음장 참조)

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

#### Amazon OpenSearch Service > Domains > usercode

#### Domain endpoint에서 https://를 제거한 값을 위 daemonset부분에 넣는다.

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

#### Cloud9 Terminal 창 > root계정으로 진행

#### kubectl apply -f fluent-bit.yaml 결과

```bash
# 결과 확인
kubectl get all -n logging
 
# 결과
NAME                   READY   STATUS    RESTARTS   AGE
pod/fluent-bit-b5nq6   1/1     Running   0          14s
pod/fluent-bit-vmczj   1/1     Running   0          14s
 
NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/fluent-bit   2         2         2       2            2           <none>          14s
```

#### Amazon OpenSearch Service > Domains > usercode

#### Kibana URL을 선택하여 Kibana에 접속한다.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

#### side menu의 stack management > Index patterns로 접속해서 data가 잘 들어와있는지 확인한다

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

#### fluent-bit daemonset 환경 변수(spec.template.spec.containers.env)에서 index로 설정한 eks-log가 data로 들어온 것을 확인할 수 있다.&#x20;

#### index pattern name을 eks-log로 하고 timefield를 @timestamp로 하여 index pattern 생성 작업을 완료한다.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

#### side menu의 kibana → discover 에서 date 설정을 today로 해주면 들어온 log를 확인할 수 있다

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>
