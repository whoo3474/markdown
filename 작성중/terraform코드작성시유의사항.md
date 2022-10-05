terraform 코드 (ex- main.tf)
terraform 상태 (ex- terraform.tfstate)
terraform 실제 원격지의 인프라 (ex- aws의 실제resource(s3, ec2 ... etc))

----------------------------
```
#terraform.tfstate
...
"resources": [
 {
      "mode": "managed",
      "type": "aws_instance", //여기 확인!
      "name": "web",          //여기 확인!
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 1,
          "attributes": {
            "ami": "ami-0b9954ae1a8f15194",
            "arn": "arn:aws:ec2:ap-northeast-2:457525610080:instance/i-05030f",
            "associate_public_ip_address": true,
            "availability_zone": "ap-northeast-2b",
            "capacity_reservation_specification": [
              {
                "capacity_reservation_preference": "open",
                "capacity_reservation_target": []
              }
            ],
            "cpu_core_count": 1,
            "cpu_threads_per_core": 2,
            "credit_specification": [
              {
                "cpu_credits": "unlimited"
              }
            ],
            "disable_api_stop": false,
            "disable_api_termination": false,
            "ebs_block_device": [],
            "ebs_optimized": false,
            "enclave_options": [
              {
                "enabled": false
              }
            ],
            "ephemeral_block_device": [],
            "get_password_data": false,
            "hibernation": false,
            "host_id": null,
            "host_resource_group_arn": null,
            "iam_instance_profile": "",
            "id": "i-05030f",  //여기 확인!
...
```

-----------------------------------------
## terraform reflesh 동작 순서

상태와 실제 원격지의 인프라는
ID값으로 서로를 체크하며 
refresh로 실제 원격지의 인프라의 값을 상태와 비교하여 상태를 변경시킨다.
```
# 상단의 terraform.tfstate 참고 ("id": "i-05030f")
resources[?].instances[?].attributes.id 
```

refresh할때 id값을 비교하여, 해당하는 원격지의 리소스를 확인후,
해당 리소스의 attributes의 값을 가져온다.
resources[].type
resources[].name
같은게 바뀌어도 신경 안씀

보통 output이나 data를 가져올떄 간단하게 쓴다

```shell
$terraform plan --refresh-only
$terraform apply --refresh-only
```

-----------------------------------------
## terraform plan & terraform apply 동작 순서
코드와 상태는
```hcl
# main.tf
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"

  tags = {
    Name = "HelloWorld222222"
  }
}
```
resourceType.resourceName(위의 aws_instance.web)으로 서로를 체크하며
apply, plan을 작동시킨다.
```
# 상단의 terraform.tfstate 참고 ("type": "aws_instance" , "name": "web")
resources[?].type
resources[?].name
```
plan과 apply할떄, type과 name이 같은것을 찾은후
refresh(***refresh 동작순서 참고***)하여 상태값을 원격지의 인프라와 동일하게 만든후,
```
Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
```
코드에 있는 값이 상태값과 비교하여 어떻게 변할지 알려주고,
Enter a value로 yes or no 를 선택하여 변경사항을 적용시키거나 반려할수있다.


