# aws cli update​

#### Cloud9 Terminal 창 > root계정으로 진행​

root계정으로 변경

```bash
sudo su -
```

패키지 업데이트

```shell
yum update -y
yum upgrade
```

aws cli update

```bash
pip3 install --upgrade --user awscli
echo 'export PATH=$HOME/.local/bin:$PATH'>> ~/.bash_profile
source ~/.bash_profile
```

버전 확인

```bash
aws --version
```

예상 결과

```bash
aws-cli/1.27.69 Python/3.11.2 Linux/4.14.133-
113.105.amzn2.x86_64 botocore/1.13​
```

참고 - [https://docs.aws.amazon.com/cli/v1/userguide/install-linux-](https://docs.aws.amazon.com/cli/v1/userguide/install-linux-al2017.html)[al2017.htm](https://docs.aws.amazon.com/cli/v1/userguide/install-linux-al2017.html)
