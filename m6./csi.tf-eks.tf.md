# csi.tf 생성 및 eks.tf 수정

#### Cloud9 Terminal 창 > root계정으로 진행

#### ebs-csi-driver에서 필요한 iam role과 그에 연결되는 serviceaccount를 정의하는 csi.tf를 아래와 같이 작성

```bash
cd /home/ec2-user/environment/eks/
```

```bash
# csi.tf 생성
cat >csi.tf <<EOF
module "ebs_csi_driver_irsa" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-role-for-service-accounts-eks"
 
  role_name             = "${var.name}_AmazonEKS_EBS_CSI_DriverRole"
  attach_ebs_csi_policy = true
   
  oidc_providers = {
    main = {
      provider_arn               = module.eks.oidc_provider_arn
      namespace_service_accounts = ["kube-system:ebs-csi-controller-sa"]
    }
  }
}
EOF
```

#### eks.tf 파일에서 ebs-csi-driver cluster addon부분을 추가한다.

```bash
module "eks" {
  #... 아래 부분 추가
  cluster_addons = {
    aws-ebs-csi-driver = {
      resolve_conflicts = "OVERWRITE"
      service_account_role_arn = module.ebs_csi_driver_irsa.iam_role_arn
      most_recent = true
    }
  }
  #... 위 부분 추가
```
