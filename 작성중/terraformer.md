#terraformer

terraformer는 기존의 인프라를 terraform으로 관리하기 위해서 
terraform import를 사용 하고자 하였으나, state만 가져오기에,
직접 tf파일을 작성하기에는 이건 좀 큰일이구나 싶을 때 찾아서 사용하였다.

terraforming 이라는 AWS 전용 terraform import 도구가 있었는데
```
Project Status (2021-12-11): No longer actively maintained
```
이라고 한다.

아무래도 멀티 클라우드 시대이니, 범용적으로 사용하기에도 좋고, 사용법도 편리한 terraformer를 선택하게 되었다.

https://github.com/GoogleCloudPlatform/terraformer

이 주소로 들어가서 각자의 환경에 맞게 설치를 진행하면 되는데, 나는 맥북으로 진행하고 있기에,
```
brew install terraformer
```
brew로 설치를 진행하였다.

자신이 terraform import를 하려했던 provider에 맞도록 download를 진행하여야 하는데,
적당한 tf파일을 만들고, 아래의 코드를 넣은후, 
```
#provider.tf
provider "aws" {
  region = "ap-northeast-2" # Please use the default region ID
}
```

명령어를 치면 알아서 provider를 설치해준다.
```shell
terraform init
```

그 이후 내가 가져오고싶은 resource를 **,** 표시로 구분하여 명령어를 입력하면,
generated폴더가 생기고, 그 내부에 폴더별로 깔끔하게 가져와진다.

```
terraformer import aws --resources=subnet,vpc,s3 --regions=<REGION> --profile=<PROFILE>
```
상단의 명령어는, subnet,vpc,s3를 가져온다.

상세하게 태그를 구분해서 가져오고 싶을때는

```
terraformer import aws --resources=s3 --filter="Name=tags.Abc" --regions=eu-west-1
```

이렇게 입력하면 tag가 Abc인 s3리소스를 가져온다.


혹시, 리소스가 잘 안받아지는데? 내가 가진 리소스를 지원하는건지 모르겠다 싶을때는

>https://github.com/GoogleCloudPlatform/terraformer/tree/master/docs

여기서 본인의 환경에 맞는 파일을 선택해서 찾아보면된다.