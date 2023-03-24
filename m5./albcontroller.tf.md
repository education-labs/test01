# albcontroller.tf 적용

#### Cloud9 Terminal 창 > root계정으로 진행

#### eks cluster에서 컨트롤러를 통해 lb를 배포하려면 public subnet tag에 "kubernetes.io/role/elb"=1 값이 있어야한다 (M2에서 미리 설정함)&#x20;

#### 제공받은 albcontroller.tf를 사용한다.

```bash
# aws 공식 module을 통해 load balancer controller에 대한 role을 생성한다.
module "role-lb" {
  source    = "terraform-aws-modules/iam/aws//modules/iam-role-for-service-accounts-eks"
  role_name = "role-${var.name}-lb-controller"
  attach_load_balancer_controller_policy = true
  oidc_providers = {
    main = {
      provider_arn               = module.eks.oidc_provider_arn
      namespace_service_accounts = ["kube-system:aws-load-balancer-controller"]
    }
  }
}
 
# kubernetes pod가 aws에 lb를 생성하기 위해, 위에서 생성한 role을 할당 받을 service account를 생성한다.
resource "kubernetes_service_account" "sa-lb" {
  metadata {
    name = "aws-load-balancer-controller"
    namespace = "kube-system"
    labels = {
        "app.kubernetes.io/name"= "aws-load-balancer-controller"
        "app.kubernetes.io/component"= "controller"
    }
    annotations = {
      "eks.amazonaws.com/role-arn" = module.role-lb.iam_role_arn
      "eks.amazonaws.com/sts-regional-endpoints" = "true"
    }
  }
}

```

```bash
# helm을 통해, ap-northeast-2 region의 aws-load-balancer-controller image를 가져와서 배포한다.
# 이 배포는 tag와 service account 생성 이후에 이뤄져야 한다.
resource "helm_release" "lb" {
  name       = "aws-load-balancer-controller"
  repository = "https://aws.github.io/eks-charts"
  chart      = "aws-load-balancer-controller"
  namespace  = "kube-system"
  depends_on = [
    kubernetes_service_account.sa-lb,
  ]
  set {
    name  = "region"
    value = var.region
  }
  set {
    name  = "vpcId"
    value = module.vpc.vpc_id
  }
  set {
    name  = "image.repository"
    value = "602401143452.dkr.ecr.${var.region}.amazonaws.com/amazon/aws-load-balancer-controller"
  }
  set {
    name  = "serviceAccount.create"
    value = "false"
  }
  set {
    name  = "serviceAccount.name"
    value = "aws-load-balancer-controller"
  }
  set {
    name  = "clusterName"
    value = module.eks.cluster_name
  }
}
EOF

```

