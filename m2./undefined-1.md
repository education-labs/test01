# 배포

#### Cloud9 Terminal 창 > root계정으로 진행

#### terraform init

```bash
terraform init
```

```bash
# Init 결과
Initializing modules...
Downloading registry.terraform.io/terraform-aws-modules/vpc/aws 3.19.0 for vpc...
- vpc in .terraform/modules/vpc
 
Initializing the backend...
 
Initializing provider plugins...
- Finding hashicorp/aws versions matching ">= 3.73.0, >= 4.47.0"...
- Finding hashicorp/tls versions matching ">= 3.0.0"...
- Finding hashicorp/kubernetes versions matching ">= 2.10.0"...
- Finding latest version of hashicorp/helm...
- Installing hashicorp/aws v4.57.1...
- Installed hashicorp/aws v4.57.1 (signed by HashiCorp)
- Installing hashicorp/tls v4.0.4...
- Installed hashicorp/tls v4.0.4 (signed by HashiCorp)
- Installing hashicorp/kubernetes v2.18.1...
- Installed hashicorp/kubernetes v2.18.1 (signed by HashiCorp)
- Installing hashicorp/helm v2.9.0...
- Installed hashicorp/helm v2.9.0 (signed by HashiCorp)
 
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.
 
Terraform has been successfully initialized!
 
You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.
 
If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

#### terraform plan 후 terraform apply로 배포

<pre class="language-bash"><code class="lang-bash"><strong># plan 명령어로 생성될 자원 확인
</strong></code></pre>

```bash
terraform plan -var-file="edu.auto.tfvars"
```

```bash
# -var-file옵션으로 terraform에서 사용할 변수값이 입력된 파일을 변경해가며 사용할 수 있다.
# auto.tfvars로 끝나는 파일은 terraform에서 자동으로 load한다.
# -var-file="edu.auto.tfvars" 를 생략해도 자동으로 load하므로 본 프로젝트에선 이후 생략한다.
# plan 결과
Plan: 16 to add, 0 to change, 0 to destroy.
 
# apply로 배포 수행
```

```bash
terraform apply
```

```bash
# Apply 결과
Apply complete! Resources: 16 added, 0 changed, 0 destroyed.
```
