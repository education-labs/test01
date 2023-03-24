# M2. 기반환경 구성​

#### Architecture​

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

#### 요구사항

**terraform-aws-modules/vpc/aws" 모듈을 사용하여 기반환경 구성**

* terraform-aws-modules/vpc/aws 모듈 버전 : 3.19&#x20;
* vpc명 : 학생코드-devops-vpc&#x20;
* vpc cidr : 10.\[학생번호].0.0/16&#x20;
* DNS 호스트네임 : 활성화&#x20;
* DNS 확인 : 활성화&#x20;
* 사용할 subnet 영역 :&#x20;
  * ap-northeast-2a,&#x20;
  * ap-northeast-2c&#x20;
* private subnet cidr :&#x20;
  * 10.\[학생번호].1.0/24,&#x20;
  * 10.\[학생번호].2.0/24&#x20;
* public subnet cidr :&#x20;
  * 10.\[학생번호].11.0/24,&#x20;
  * 10.\[학생번호].12.0/24&#x20;
* private subnet에 단일 nat gateway : 사용&#x20;
* public subnet에는&#x20;
  * key : "kubernetes.io/role/elb",&#x20;
  * value : 1&#x20;
* 의 tag를 부여(lb배포를 위함)

