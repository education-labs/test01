# M3. EKS 환경 구성

#### Architecture

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

#### 요구사항

* terraform-aws-module/eks/aws 모듈 사용&#x20;
* terraform-aws-module/eks/aws 모듈 버전 : 19.10.0&#x20;
* module 자원명 : eks&#x20;
* eks 클러스터명 : 학생코드&#x20;
* eks cluster버전 : 1.24&#x20;
* eks cluster 접속 : cloud9 vpc의 nat gateway의 public ip만 public access 허용하도록 구성&#x20;
* eks 생성할 vpc : ''M2.기반환경구성'' 에서 생성한 '학생코드-devops-vpc'&#x20;
* eks 생성할 subnet : 해당 vpc의 ap-northeast-2a, ap-northeast-2c의 private subnet&#x20;
* eks manged node group 생성&#x20;
  * managed node group ami type : AL2\_X86\_64&#x20;
  * 최소 사이즈 : 2&#x20;
  * 최대 사이즈 : 4&#x20;
  * desired size : 2
