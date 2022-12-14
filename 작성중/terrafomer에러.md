##### 현재 최신 버전
terrafomer : v0.8.22
terraform : v1.3.1

### 본문

terraformer를 aws에서 사용할때,
```shell
$terraformer import aws --resources=s3 --profile=minhan --regions=ap-northeast-2
```
를 사용하여 s3 폴더가 만들어졌다고 해보자.
s3 폴더로 이동하여 terraform init을 터미널에 입력해본다.
```shell
$terraform init

Initializing the backend...
╷
│ Error: Invalid legacy provider address
│ 
│ This configuration or its associated state refers to the unqualified provider "aws".
│ 
│ You must complete the Terraform 0.13 upgrade process before upgrading to later versions.
```
이러한 에러가 뜬다.
```
$terraform version
Terraform v1.2.5
```
나는 분명 0.13버전 이상의 테라폼 버전을 쓰고있는데 이게 무슨일일까 싶다.

```yaml
#/generated/aws/s3
{
    "version": 3,
    "terraform_version": "0.12.31",
...
```
s3의 terraform.tfstate가 terraform_version을 0.12버전으로 사용되고 있기 때문에 나오는 에러이다.
현재 최신 버전의 terraformer는 import할때 기본적으로 ***Terraform v0.12***을 사용하고 있어서 생기는 문제이다.

```shell
$terraform state replace-provider -auto-approve "registry.terraform.io/-/aws" "hashicorp/aws"
```
provider를 replace해주면 

```yaml
{
  "version": 4,
  "terraform_version": "1.2.5",
...
```
현재 나의 terraform 버전으로 ***terraform.tfstate***의 구조 및 provider가 변경된다.


이제 plan이던 apply던 진행하면 된다.
```shell
$terraform init
$terraform plan
$terraform apply
```


