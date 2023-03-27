# 패키지 설치

#### Cloud9 Terminal 창 > root계정으로 진행​

#### kubectl

kubectl 다운로드

<pre class="language-bash"><code class="lang-bash"><strong>curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.24.9/2023-01-11/bin/linux/amd64/kubectl
</strong></code></pre>

실행권한 부여

<pre class="language-bash"><code class="lang-bash"><strong>chmod +x ./kubectl
</strong></code></pre>

path 추가

```bash
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
```

설치 확인

```bash
kubectl version --short --client
```

예상 결과

```bash
Flag --short has been deprecated, and will be removed in the future. The --short output will become the default.​
Client Version: v1.24.9-eks-49d8fe8​
Kustomize Version: v4.5.4​
```

참고 - [https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/install-kubectl.html](https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/install-kubectl.html)



#### terraform

```bash
yum install -y yum-utils
```

```bash
yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
```

```bash
yum -y install terraform
```

설치 확인

```bash
terraform version
```

예상 결과

<pre><code><strong>Terraform v1.3.9​
</strong>on linux_amd64​
</code></pre>

참고 - [https://developer.hashicorp.com/terraform/downloads?product\_intent=terraform](https://developer.hashicorp.com/terraform/downloads?product\_intent=terraform)​



#### helm

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 > get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh
```

설치 확인

<pre class="language-bash"><code class="lang-bash"><strong>helm version --short | cut -d + -f 1
</strong></code></pre>

예상 결과

```
v3.11.1​
```

참고 - [https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/helm.html](https://docs.aws.amazon.com/ko\_kr/eks/latest/userguide/helm.html)



#### jq

```bash
yum install jq -y
```

설치 확인

```bash
jq --help
```

```bash
jq - commandline JSON processor [version 1.5]
Usage: jq [options] <jq filter> [file...]
 
        jq is a tool for processing JSON inputs, applying the
        given filter to its JSON text inputs and producing the
        filter's results as JSON on standard output.
        The simplest filter is ., which is the identity filter,
        copying jq's input to its output unmodified (except for
        formatting).
        For more advanced filters see the jq(1) manpage ("man jq")
        and/or https://stedolan.github.io/jq
 
        Some of the options include:
         -c             compact instead of pretty-printed output;
         -n             use `null` as the single input value;
         -e             set the exit status code based on the output;
         -s             read (slurp) all inputs into an array; apply filter to it;
         -r             output raw strings, not JSON texts;
         -R             read raw strings, not JSON texts;
         -C             colorize JSON;
         -M             monochrome (don't colorize JSON);
         -S             sort keys of objects on output;
         --tab  use tabs for indentation;
         --arg a v      set variable $a to value <v>;
         --argjson a v  set variable $a to JSON value <v>;
         --slurpfile a f        set variable $a to an array of JSON texts read from <f>;
        See the manpage for more options
```

####

#### 실습에서 자주 사용할 환경변수 구성

\[학생코드]에 학생코드 입력

```bash
echo 'export USERNAME=[학생코드]' >> ~/.bashrc​
```

```bash
source ~/.bashrc
```

확인

```bash
echo $USERNAME
```
