# EKS 시작하기

> 시작시 참고 진행
> 
https://catalog.us-east-1.prod.workshops.aws/v2/workshops/9c0aa9ab-90a9-44a6-abe1-8dff360ae428/ko-KR/30-setting/100-aws-cloud9

https://aws-diary.tistory.com/43?category=753089

뭘 설치해서 사용할수 있는가?

>https://coffeewhale.com/kubernetes/cluster/eks/2020/09/03/k8s-eks/

## awscli
awscli는 AWS의 여러 서비스들을 커멘드 인터페이스를 통해 접근할 수 있게 해주는 툴입니다. 파이썬으로 개별되어 pip로 설치합니다. 뒤에서 살펴 볼 aws-iam-authenticator가 사용자의 신원을 확인하는 용도로 사용됩니다. (aws sts get-caller-identity)

## eksctl
eksctl은 weaveworks에서 개발한 Amazon EKS CLI 툴입니다. EKS 클러스터를 손쉽게 구축할 수 있게 도와주는 툴입니다. 재밌는 것은 이 툴은 AWS에서 만든 것이 아니라 쿠버네티스 Network Provider 중 하나인 weavenet를 만든 회사, Weaveworks에서 개발했다는 점입니다. eksctl 툴 하나로 EKS 노드가 생성될 VPC와 관련한 subnet, Security Group, AutoScaling 설정까지 완벽하게 EKS 클러스터를 구축할 수 있습니다.

# aws-iam-authenticator
aws-iam-authenticator은 AWS IAM 정보를 활용하여 쿠버네티스 클러스터 사용자 인증(Authentication)을 할 수 있도록 도와주는 툴입니다. 내부적으로 awscli를 이용하여 쿠버네티스에 현재 사용자의 IAM 정보( User나 Role)를 전달합니다. EKS에는 이 IAM 정보와 매핑되는 쿠버네티스 사용자가 존재하고 해당 사용자로 쿠버네티스에 접근을 할 수 있게 됩니다. 사용자 인증에 대한 자세한 내용은 저의 블로그 k8s 인증 완벽 이해 시리즈를 참고하시기 바랍니다. aws-iam-authenticator도 재밌게도 원래는 heptio라는 회사에서 개발한 툴입니다. 현재는 kubernetes-sigs(special interest group)에서 관리합니다. EKS는 기본적으로 AWS IAM을 이용하여 k8s RBAC과 연동합니다. 이때 필요한 것이 aws-iam-authenticator라는 녀석입니다.




#  Instance Type 별 최대 Pod수 확인
>https://github.com/awslabs/amazon-eks-ami/blob/master/files/eni-max-pods.txt

# Instance 별 최대 ENI 개수를 확인
>https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI

AWS CNI를 쓰는경우, 네트워크 인터페이스(인스턴스의 프라이빗 IPv4)마다, 생성될수있는 프라이빗(인스턴스의 보조 프라이빗 IPv4) IPv4를 가지는 POD수가 다르다.
>https://ikcoo.tistory.com/160

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAqV2S%2Fbtq37Bba0uK%2FRE1leERAX5b1kFuL75RMHk%2Fimg.png)

~~~t2.small 또는~~~ t2.medium 사용 생각중(15개 팟) 

m5.large의 경우 한 worker node당 
3 x 10 = 30임으로 
최대 30개의 Pod가 배포될 수 있음
하지만 실제 운영환경에서는
현재 모든 Node에 kube-proxy와 AWS-node Pod가 동작하고 있고
특정 2개의 Node에는 Core-dns Pod가 배포되어있으니
30 - 3= 27로
m5.large 인스턴스 유형인 worker node에 최대 배포할 수 있는 Pod수는 27개가 됨.




Instance types : t3.medium ( 이보다 낮은 유형 선택시 성능에 문제 생길 수 있음) !?

secondary ipv4 할당 vs ipv4 prefix 위임
무슨 차이지?