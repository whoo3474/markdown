
> https://jonnung.dev/posts/2021-01-28-aws-iam-role/
> https://whchoi98.gitbook.io/aws-iam/iam-policy#iam

> 인증 : 로그인
> 인가 : 권한 확인


# 역할 (Roles)

# 정책 (Policies)
정책은 권한을 부여하는 방법이다. 하나 이상의 AWS 리소스에 대한 어떤 작업을 수행할 수 있는지 허용 규칙을 JSON 형식으로 작성된다.
이렇게 만들어진 정책이 IAM 사용자와 그룹, 역할에 연결된다.

# 그룹 (Groups)
IAM 그룹은 다수의 IAM 사용자를 모아놓은 개념이다.
그룹이 필요한 이유는 IAM 사용자마다 매번 정책을 직접 연결해줘야 하는 번거로움과 관리 포인트를 줄일 수 있기 때문이다.
예를 들어 backend_developers라는 그룹에 S3와 Cloudwatch에 Read Only 정책을 연결한다면, 이 그룹에 속한 IAM 사용자들에게 자동으로 이 정책이 적용되는 것이다.

# 사용자 (Users)
IAM 사용자는 실제 사용자 단 한 명을 의미한다.
하지만 AWS 계정을 처음 만들었을 때 만든 루트 계정(email)이랑 완전 다르다는 점을 알아야 한다. 왜냐하면 루트 계정은 모든 권한을 갖고 있고, IAM 사용자는 단지 루트 계정에 의해 만들어졌을 뿐이다.
AWS에서는 루트 계정을 직접 사용하지 말고, 어드민 권한(AdministratorAccess)을 부여한 IAM 사용자를 따로 만들어서 사용하는 것을 권장하고 있다.


# IAM Policy의 구조
```json
{
    "Statement":[{
        "Effect":"Allow or Deny",
        "Principal":"principal",
        "Action":"action",
        "Resource":"arn",
        "Condition":{
            "condition":{
                "key":"value"
            }
        }
    }]
}
```

## Effect
- 명시된 정책에 대한 허용 혹은 차단

## principal
- 접근을 허용 혹은 차단하고자 하는 대상(보안 주체)
- 정책의 Principal 요소를 사용하여 리소스에 대한 액세스가 허용되거나 거부되는 보안 주체를 지정합니다. IAM 자격 증명 기반 정책에서는 Principal 요소를 사용할 수 없습니다. IAM 역할을 위한 신뢰 정책 및 리소스 기반 정책에서는 사용할 수 있습니다. 리소스 기반 정책은 리소스에 직접 삽입할 수 있는 정책입니다. 예를 들어 Amazon S3 버킷 또는 AWS KMS key에 정책을 삽입할 수 있습니다.
```yaml
"Principal": "AWS":"arn:aws:iam::123456789012:user/username"
```

## Action
- 허용 혹은 차단하고자하는 접근 타입
```yaml
"Action": "Action":"s3:GetObject" 
```

## Resource
- 요청의 목적지가 되는 서비스
```yaml
"Resouce": "arn:aws:sqs:us-west-2:123456789012:queue1"
```

## Condition
- 명시된 조건 유효하다고 판단될 수 있는 조건
```yaml
"StringEqualsIfExists": {"aws:RequestTag/project":["Pickles"]}
```

------------------------------
> https://musma.github.io/2019/11/05/about-aws-iam-policy.html
> 너무 좋은글이라 가져옴

# IAM Policy의 종류

정책(Policy) 앞에 달리는 수식어는 다양합니다.

- 연결 대상이 어디인가에 따라
  - 자격 증명 주체(User, Group, Role)에
    - 자격증명-기반 정책(Id-Based Policy)
- 리소스에
  - 리소스-기반 정책(Resource-Based Policy)
- Role의 신뢰 관계
  - 신뢰 정책 (사실 Role을 리소스로 본다면, 특별한 의미의 리소스-기반 정책이라고 할 수 있음)
- 누가 만들었냐에 따라
  - 내가
    - 고객 관리 정책
  - AWS가 만든 빌트인 정책
    - AWS 관리 정책
    - 직무 기반 정책
- IAM Role에 인라인으로 선언되었으면
  - 인라인 정책

뭐가 많지만 진짜 중요한 두 가지 유형은 아래와 같습니다.

- 자격증명-기반 정책(Id-Based Policy)
- 리소스-기반 정책(Resource-Based Policy)

정책이라는 것은 사실 **“(어떤 Condition을 만족할 때,) 어떤 Resource에 대해서 어떤 Action을 할 수 있게 허용/불허(Effect)한다.”**라는 의미를 담은 것이지요.

그런데 위의 문장 성분 중에서 뭐가 빠졌습니까?

네, 주어(Subject)가 빠졌습니다. 이것을 보안 주체(Principal)라고 합니다.

완전한 정책이 되려면, **“(어떤 Principal이) (어떤 Condition을 만족할 때,) 어떤 Resource에 대해서 어떤 Action을 할 수 있게 허용/불허(Effect)한다.”** 이렇게 되어야 합니다.


-----------------------------
 
>https://dev.classmethod.jp/articles/iam-3/

|Principal	|Resource|	Action	|Effect	|Condition|
|-----------|-------|---------|-------|-----------|
|이대상에대해서	|이 Resource 에 대한|	특정 행동을|	불허하거나 허용한다.|	조건에 따라서|

- 알아두면 좋은것

  - 나열되는 요소들의 순서는 중요하지 않습니다.(예를 들어 Resource 요소는 Action 요소 앞에 올 수 있습니다.)
  - 정책에서 Condition 요소는 지정하지 않아도 됩니다.
  - 동일한 정책 문에서 (Action/NotAction,Principal/NotPrincipal,Resource/NotResource) 둘 다 사용할 수 없습니다.



# identity-based Policies
>**자격증명-기반 정책(Id-Based Policy)**
 자격증명-기반 정책(Id-Based Policy)으로 사용할 때는, 그 정책에 연결된 보안 주체(AWS 계정, IAM 사용자, 그룹, 역할 혹은 연합 인증된 사용자, Cognito User Identity 등이 될 수 있음)가 암묵적인 Principal이 됩니다.
그래서 여러분이 자주 보던 정책의 Statement에서는 대개 Principal 속성이 없었다는 것을 깨달았을 것입니다.
>- IAM 메뉴의 정책 리스트에 나오는 정책들은 모두 자격증명-기반 정책(Id-Based Policy)입니다.

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "dynamodb:*",
    "Resource": "arn:aws:dynamodb:us-east-2:123456789012:table/Books"
  }
}
```
- 이 정책의 내용은 (AWS계정에 속한User,Group,Role에 연결했을때)사용자가 us-east-2 리전 내의 123456789012 계정에서 Books 테이블의 모든 Amazon DynamoDB 작업(dynamodb:*)을 수행할 수 있도록 허용정책입니다.

- 원래 ARN은 arn:aws:(서비스 Prefix):(AWS 리전 이름):(AWS 계정):(리소스 한정자) 이런 순서로 적습니다.
- (AWS 리전 이름), (AWS 계정)은 암묵적으로 생략이 가능한 경우가 있습니다.


|Principal	|Resource|	Action	|Effect	|Condition|
|-----------|-------|---------|-------|-----------|
|연결하는(user,group,role)|	이 Resource 에 대한|	DynamoDB행동을|	허용한다(Allow).|	(조건없음)|

**Resource** : us-east-2 리전 내의 123456789012 계정에서 Books 테이블의

이번 정책을 보시면 알 수 있겠지만 Principal이 빠져있습니다. Resource-based Policies와 다르게 연결하는 사용자를 암묵적으로 지정하기 때문에 설정하지 않는다고 합니다.

**arn이란?**: Amazon Resource 이름 AWS 리소스를 고유하게 식별합니다.


# Resource-based Policies
>리소스-기반 정책(Resource-Based Policy)
자격증명 기반 정책은 ‘이 정책과 연결된 보안주체는 어떤 대상에 대해 이러이러한 권한이 부여된다’는 의미였습니다.
그런데 반대로 객체(대상)를 중심으로 생각을 해보면 어떨까요? (마치 능동태를 수동태로 바꾸듯이)
> - ~~‘이 정책과 연결된 보안주체는 어떤 대상에 대해 이러이러한 권한이 부여된다’~~
> - ‘이 대상에 대해 이러이러한 권한이 누구누구누구에게 부여된다’
>
>리소스-기반 정책은 AWS의 서비스 중에서 일부 서비스에서 사용됩니다.
예를 들어, S3의 버킷의 권한 메뉴에 들어가면 버킷 정책을 편집할 수 있습니다.
리소스-기반 정책을 사용하는 서비스 중에서 대표적으로 S3가 있습니다.


```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Principal": {
        "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity E36E6CTWWW86E5"
    },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::*"
  }
}
```
- S3 모든버킷에 업로드된 모든 오브젝트를 읽을 권한(s3:GetObject)를 CloudFront Origin Access Identity E36E6CTWWW86E5에 부여한다.

|Principal	|Resource|	Action	|Effect	|Condition|
|-----------|-------|---------|-------|-----------|
|cloudfront의 user/E36E6CTWWW86E5에게| 모든 s3에 대한|	오브젝트를 읽을 권한을|	허용한다(Allow).|	(조건없음)|

------------------------------



|  | 자격증명-기반 정책(Id-Based Policy)	|리소스-기반 정책(Resource-Based Policy)|
|-------|--------------------------|-----------------|
|연결 대상	|사용자, 그룹, 역할 등|보안주체(Principal)가 될 수 있는 대상|리소스|
|의미|	( 이 정책이 연결된 보안주체는 )( 특정 조건(Condition) 하에 )이 리소스(Resource)에 대한특정 행동(Action)을 허용/불허(Effect)|	( 특정 조건(Condtion)하에 ) 이 리소스(Resource)에 대한 특정 행동(Action)을 특정 보안주체(들)(Principal)에게 허용/불허(Effect)|
|특징1|	Principal 속성이 안 들어감|	Principal 속성이 들어감|
|특징2|	IAM Policy 메뉴에서 관리(혹은 IAM Role에 인라인으로)|일부 AWS 서비스의 리소스에서 자체 관리 (대표적으로 S3, SQS 등)|
특징3|	모든 AWS 서비스에 연관|	일부 공유 자원 성격을 가진 서비스에서 사용(대표적으로 S3, SQS 등)|





-----------------------------------



# IAM 권한 할당 원리의 이해
## 권한 할당 원칙#1 - 명시적 Deny, 묵시적 Deny
작성하지않으면 Deny - 묵시적 Deny
작성하여 Deny - 명시적 Deny

## 권한 할당 원칙#2 - 명시적 허용
 

? Permission Policy?


----------------

# Permission Boundary

>https://wisen.co.kr/pages/blog/blog-detail.html?idx=9162



Organizations SCP (조직 SCP) 
Access control lists (액세스 제어 목록 ACL)
Session policies (세션 정책)
>https://memory-hub.tistory.com/14
>https://velog.io/@koo8624/AWS-Security-IAM-Deep-Dive