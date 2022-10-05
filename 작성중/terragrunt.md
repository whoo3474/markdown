
# terragrunt Quick start 요약

>https://terragrunt.gruntwork.io/docs/getting-started/quick-start/

DRY는 Don't Repeat Yourself(자신을 반복하지 말 것)의 약자입니다.

간단히, Terragrunt는 Terraform에서 반복되는 폴더와 작업들을 줄이고자 사용하는 툴입니다.
Terragrunt는 테라폼 코드를 DRY하고 유지보수가 편하게 만들어줍니다.

PROD, QA, STAGE, DEV .. 몇개의 환경이 있던 상관없이 
환경마다 중복되는 Provider, Backend, input variables 등을 Terragrunt로 한번만 정의해서 사용할수 있습니다.

Terragrunt는 root 디렉터리에서 Terraform 상태를 한 번 관리하고 모든 하위 모듈에서 상속하는 방법을 정의합니다.

각 모듈에서 개별적으로 실행하는 대신, root경로에서 모든 모듈에 대해 하나의 명령을 실행합니다.
```shell
$terragrunt run-all apply
$terragrunt run-all destroy
$terragrunt run-all plan
$terragrunt run-all output
```



-------------------------------------------------------------
### 설치해보자
> https://terragrunt.gruntwork.io/docs/getting-started/install/

설치는 상단의 링크에서 확인 하셔도 되고,
저는 brew로 설치를 합니다.
```
brew install terragrunt
```

-------------------------------------------------------------
### 백엔드와 프로바이더의 중복을 없에보자.
아래와 같은 폴더 구조를 가지고 있고,

```
stage
├── frontend-app
│   └── main.tf
└── mysql
    └── main.tf
```

각각의 main.tf의 파일안에 중복되는 값이 들어가있을때,
```
# stage/frontend-app/main.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "stage/frontend-app/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "my-lock-table"
  }
}
```
```
# stage/mysql/main.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "stage/mysql/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "my-lock-table"
  }
}
```

Terragrunt를 사용하여, root경로의 폴더에 단일 terragrunt.hcl 파일을 추가하고 각 모듈 폴더에 terragrunt.hcl 파일을 추가합니다.
```
stage
├── terragrunt.hcl
├── frontend-app
│   ├── main.tf
│   └── terragrunt.hcl
└── mysql
    ├── main.tf
    └── terragrunt.hcl
```

그리고 중복되는 backend값을 root경로의 terragrunt.hcl파일에 작성해줍니다.
```
# stage/terragrunt.hcl
remote_state {
  backend = "s3"
  generate = {
    path      = "backend.tf"
    if_exists = "overwrite_terragrunt"
  }
  config = {
    bucket = "my-terraform-state"

    key = "${path_relative_to_include()}/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "my-lock-table"
  }
}

generate "provider" {
  path = "provider.tf"
  if_exists = "overwrite_terragrunt"
  contents = <<EOF
provider "aws" {
  assume_role {
    role_arn = "arn:aws:iam::0123456789:role/terragrunt"
  }
}
EOF
}
```

조금 모양이 달라졌는데, terragrunt는 terraform과 같은 구성언어를 씁니다. (HCL)
backend는 remote_state 블럭을 사용합니다.
provider는 generate 블럭을 사용합니다.

generate.path는 만들어줄 파일명을, generate.if_exists는 만약 하위 모듈 폴더에 generate.path에 해당하는 파일이 있으면 어떻게 할지 옵션을 넣는곳입니다.
###### if_exists 옵션
- overwirte : 다 무시하고 덮어씌어버립니다.
- overwrite-terragrunt : terragrunt로 만들어진 파일만 덮어씌여집니다. 
- skip : 기존파일이 있다면 그대로 놔둡니다.
- error : 에러와 함께 종료됩니다.

그리고 config가 기존에 중복되던 부분인데, 나머지 부분은 다 동일하지만, path_relative_to_include를 통하여
root폴더에 작성된 코드가 하위 모듈 폴더에 내려갈때마다 바뀌는 위치값을 넣어줍니다.
이와 같이 단순히 중복되는 정적인 코드 뿐만아니라 동적으로 변하는 코드로 작성할수도 있습니다.


root경로에서는 하위 폴더별 중복되는 코드를 작성하였다면,
각 하위 폴더에서는 내가 상위 경로의 코드를 상속 받겠다 라는 코드를 작성해야합니다.

```
# stage/mysql/terragrunt.hcl
include "root" {
  path = find_in_parent_folders()
}
# stage/frontend-app/terragrunt.hcl
include "root" {
  path = find_in_parent_folders()
}
```


그후 root경로에서 모든 폴더를 init하겠다는 명령어를 입력합니다.
```shell
$terragrunt run-all init
```

이후 terragrunt.hcl를 설정한 폴더들에 정상적으로 파일들이 생성되었는지 확인합니다.
위의 예시에서는 backend.tf 와 provider.tf 두개가 생성되었어야 합니다.


--------------------------------

### 유용할만한 내장 함수

>https://terragrunt.gruntwork.io/docs/reference/built-in-functions/

위에서 사용한 내장함수들이 이해가 안될수 있기에, 설명을 추가합니다.
terragrunt.hcl로 파일 경로를 작성할때, terragrunt가 임시폴더를 생성하여 작동하기에,
사용자가 직접 상대경로나 절대경로를 입력하면 오류가 난다고 한다.
그래서 경로를 나타낼때는 아래의 내장 함수들은 사용하는것이 좋다.

##### find_in_parent_folders()
```
# 주로 include을 사용할때 쓴다. 상위 폴더의 첫번쨰 절대 경로를 반환함
# include란 해당 위치에 있는 파일을 그대로 가져와서 사용한다는 뜻으로 이해하면 됌

include "root" {
  path = find_in_parent_folders()
}
```
##### path_relative_to_include() & path_relative_from_include()

```
# include 블럭의 path와 현재의 terragrunt.hcl와의 상대경로를 나타낸다.
# 주로 find_in_parent_folders() 와 쓰일수있다.
```

```
├── sources
|  ├── mysql
|  |  └── \*.tf
└── terragrunt
  ├── mysql
  |  └── terragrunt.hcl
  └── terragrunt.hcl
```
이와 같은 상황에서, sources의 mysql을 module로 사용하고자 할때

```
# /terragrunt/mysql/terragrunt.hcl
include "root" {
  path = find_in_parent_folders()
}
# /terragrunt/terragrunt.hcl
terraform {
  source = "${path_relative_from_include()}/../sources//${path_relative_to_include()}"
}
```

/terragrunt/mysql/terragrunt.hcl 에서 바로 상단의 terragrunt.hcl을 include하므로
/terragrunt/terragrunt.hcl의 코드를 가져오게된다.

그 결과는 ***../../../sources//mysql*** 로 된다.
("${path_relative_from_include()}/../sources//${path_relative_to_include()}" 의 결과)

상위의 terragrunt.hcl와 mysql/terragrunt.hcl 에서의 상대적인 위치는 ***path_relative_to_include()***가 ***mysql***로 들어가게 되고, 상위의 terragrunt.hcl 와 mysql/terragrunt.hcl으로부터 위치는  ***path_relative_from_include()***가 ***../../***로 표현이 된다.


##### get_terragrunt_dir()
현재 terragrunt.hcl의 절대경로 위치
```
/terraform-code
├── common.tfvars
├── frontend-app
│   └── terragrunt.hcl
```
```
terraform {
  source = "git::git@github.com:foo/modules.git//frontend-app?ref=v0.0.3"

  extra_arguments "custom_vars" {
    commands = [
      "apply",
      "plan",
      "import",
      "push",
      "refresh"
    ]

    # With the get_terragrunt_dir() function, you can use relative paths!
    arguments = [
      "-var-file=${get_terragrunt_dir()}/../common.tfvars"
    ]
  }
}
```
***/terraform-code/frontend-app/../common.tfvars***

##### get_parent_terragrunt_dir()
현재 terragrunt.hcl의 루트폴더 절대경로 위치

```
/terraform-code
├── terragrunt.hcl
├── common.tfvars
├── app1
│   └── terragrunt.hcl
├── tests
│   ├── app2
│   |   └── terragrunt.hcl
│   └── app3
│       └── terragrunt.hcl
```
```
terraform {
  extra_arguments "common_vars" {
    commands = [
      "apply",
      "plan",
      "import",
      "push",
      "refresh"
    ]

    arguments = [
      "-var-file=${get_parent_terragrunt_dir()}/common.tfvars"
    ]
  }
}
```
***/terraform-code/common.tfvars***


---------------------------------------
### Terragrunt의 Input Value로 TF 환경변수 설정을 해보자
공통적인 변수를 사용하고자 하는경우에는, Terragrunt의 input 블럭을 이용해 환경변수로 만들수 있습니다.

```
#terragrunt.hcl

inputs = {
  instance_type  = "t2.micro"
  instance_count = 10

  tags = {
    Name = "example-app"
  }
}
```

inputs 블럭에 값을 입력한후, Terragrunt 명령어를 실행시키면, 아래와 같은 결과가 나옵니다.

```
$ terragrunt apply

# 위와 아래는 같은 의미입니다.

TF_VAR_instance_type="t2.micro" \
TF_VAR_instance_count=10 \
TF_VAR_tags='{"Name":"example-app"}' \
terraform apply
```

다만 기존에 사용자가 ***TF_VAR_xxx*** 같은 환경변수를 설정하였다면, terragrunt는 override하지 않습니다.
variable 블럭을 만들어 주는것이 아닙니다.
환경변수를 잠시 설정해주는것입니다.


--------------------------------
### Terraform CLI 명령어의 중복을 없에보자
때때로 terraform 명령어를 사용할때, 추가 CLI인수를 전달해야할때가 있습니다.
terragrunt.hcl 파일에서 ***extra_arguments*** 블럭을 사용하여 특정 명령어애 대한 특정 CLI인수를 전달하도록 구성할수 있습니다.
```
terraform {
  # Force Terraform to keep trying to acquire a lock for
  # up to 20 minutes if someone else already has the lock
  extra_arguments "retry_lock" {
    commands = [
      "init",
      "apply",
      "refresh",
      "import",
      "plan",
      "taint",
      "untaint"
    ]

    arguments = [
      "-lock-timeout=20m"
    ]

    env_vars = {
      TF_VAR_var_from_environment = "value"
    }
  }
}
```
상단의 terragrunt.hcl 를 구성하였고, 하단의 명령어를 입력해봅니다.
```
$ terragrunt apply

# 현재 설정에서 위와 아래의 명령어는 같습니다

terraform apply -lock-timeout=20m
```

하나 이상의 extra_arguments 블럭을 생성하여 조건이 부합하는 모든 extra_arguments블럭을 작동시킬수도 있습니다.


#### CLI 명령어 실행시 파일 지정
```
/my/tf
├── terragrunt.hcl
├── prod.tfvars
├── us-west-2.tfvars
├── backend-app
│   ├── main.tf
│   ├── dev.tfvars
│   └── terragrunt.hcl
├── frontend-app
│   ├── main.tf
│   ├── us-east-1.tfvars
│   └── terragrunt.hcl
```

```
terraform {
  extra_arguments "conditional_vars" {
    commands = [
      "apply",
      "plan",
      "import",
      "push",
      "refresh"
    ]

    required_var_files = [
      "${get_parent_terragrunt_dir()}/terraform.tfvars"
    ]

    optional_var_files = [
      "${get_parent_terragrunt_dir()}/${get_env("TF_VAR_env", "dev")}.tfvars",
      "${get_parent_terragrunt_dir()}/${get_env("TF_VAR_region", "us-east-1")}.tfvars",
      "${get_terragrunt_dir()}/${get_env("TF_VAR_env", "dev")}.tfvars",
      "${get_terragrunt_dir()}/${get_env("TF_VAR_region", "us-east-1")}.tfvars"
    ]
  }
```


```
$ terragrunt run-all apply
[backend-app]  terraform apply -var-file=/my/tf/terraform.tfvars -var-file=/my/tf/backend-app/dev.tfvars
[frontend-app] terraform apply -var-file=/my/tf/terraform.tfvars -var-file=/my/tf/frontend-app/us-east-1.tfvars

$ TF_VAR_env=prod terragrunt run-all apply
[backend-app]  terraform apply -var-file=/my/tf/terraform.tfvars -var-file=/my/tf/prod.tfvars
[frontend-app] terraform apply -var-file=/my/tf/terraform.tfvars -var-file=/my/tf/prod.tfvars -var-file=/my/tf/frontend-app/us-east-1.tfvars

$ TF_VAR_env=prod TF_VAR_region=us-west-2 terragrunt run-all apply
[backend-app]  terraform apply -var-file=/my/tf/terraform.tfvars -var-file=/my/tf/prod.tfvars -var-file=/my/tf/us-west-2.tfvars
[frontend-app] terraform apply -var-file=/my/tf/terraform.tfvars -var-file=/my/tf/prod.tfvars -var-file=/my/tf/us-west-2.tfvars
```

--------------------------------
### 느낀점

다른건 모르겠고
backend와 provider를 공통으로 적용시켜줄수있는것이 가장 좋은것같다.

그외에 사용할만한것은 한꺼번에 삭제하고 한꺼번에 plan, apply하는것.
CLI로 PLAN, APPLY, DESTROY 등을 할때 argument를 자동설정 해주는것, 뭐 내가 원하는 var-file 설정이라거나.
모듈이나 input value에 terragrunt input블럭으로 value를 넣어줄수 있는 것

안그래도 terraform, kubernetes, aws 등을 사용하느라 러닝커브가 높은거같은데,
이런저런 서드파티 들을 붙이기에는 부담이 되는듯하여 너무 깊게는 사용하지 말아야하나 고민이다.


다른 폴더에 있는것을 terraform_remote_state로 사용하고 있는데, dependency로도 사용가능하더라, 근데 필요한지 모르겠어서 나중에 다시봐야겠다.
```
The terragrunt dependency block acts as a replacement for the terraform remote state resource. The remote state resource requires you to write terraform code to refer to another statefile, be it in an S3 bucket, local file, whatever.

If you are using terraform/terragrunt the way that terragrunt says you should, then your terraform plans will only consist of calling a single tf module with inputs, not writing raw terraform resources. Inside a module you don't want to write a reference to another statefile, because that reduces the re-usability of your module. Instead, your module only has inputs and outputs.

With terragrunt, this is possible. Terragrunt just takes the output of 1 module and passes it as input to another module. You can chain outputs and inputs together without having to write terraform code to source from other statefiles. This increases the re-usability of your modules and reduces the amount of code you have to write.
```
라고 레딧에서 답변이 되어있다.

