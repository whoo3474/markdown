# Harbor란

CNCF의 졸업 프로젝트이다.

Private Rgeistry는 public한 docker hub와는 다르게, 개인적으로만 사용하도록 개인 서버에 구축하는 도커 이미지 저장소이다. 회사 사내에서 사용하거나 개인적으로 만든 이미지를 올리고 관리하기 위해서 사용된다.

Harbor는 Docker 컨테이너로 실행되기 때문에 Docker 환경이있는 모든 서버에 쉽게 설치할 수 있으며 개발자에게 오프라인 설치 프로그램을 제공하기 때문에 보안 고려 사항 또는 기타 요인이있는 상황(공용 인터넷에 연결할 수 없는 경우을 의미합니다.)에서 유용 할 수 있습니다.

Docker를 기업환경에서 사용하려면 반드시 필요한것이 Prvaite Docker Registry이다.
개발자가 만든 Docker 컨테이너 이미지를 저장하고 공유하면서 신뢰할 수 있는 저장소가 필수이기 때문입니다.
관리 측면과 보안 측면에서 여러 가지 문제점이 있어서 개선이 필요한 상황이었습니다. 
특히 이미지를 효율적으로 관리하기 위한 UI, 이미지 보안을 향상시키기 위한 접근 제어 기능이 필요하다고 사용자들이 계속 요구하고 있는 상황이었습니다.

--------------------------------
# 다른 도커 레포지토리는?
## 클라우드
|이름|제공 업체|유료여부|
|---|------|------|
|[DockerHub](https://hub.docker.com/)|Docker|ㅇ|
|[GitHub Container Registry](https://github.com/features/packages)|Github|ㅇ|
|[ECR](https://aws.amazon.com/ko/ecr/)|AWS|ㅇ|
|[Azure Container Registry](https://azure.microsoft.com/ko-kr/services/container-registry/)|Azure|ㅇ|ㅇ|
|[Google Cloud Container Registry](https://cloud.google.com/container-registry)|Google Cloud|ㅇ|
|[Naver Cloud Platform Container Registry](https://www.ncloud.com/product/compute/containerRegistry)|Naver Cloud Platform|ㅇ|

...그만 알아보자.
왠만한 클라우드 플랫폼들은 도커 저장소를 운영하고 있다고 생각하면 된다.

>cloud 서비스들은 public Registry이다 이자 private Registry이다.
무료정책들이 존재하나, 각각의 정책이 다르므로 확인하여야함

>Docker Hub 정책 변경으로 6개월간 Push나 Pull이 없던 이미지는 삭제된다고 합니다.
백업 차원에서 GitHub의 Packages에 도커 이미지를 Push 해두는것이 좋습니다.


## 사설(직접 로컬에 설치)
|이름|제공 업체|
|---|-------|
|[registry](https://hub.docker.com/_/registry)|Docker|
|[GitLab Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)|GitLab|

Harbor 외에 registry와 GitLab 이 가장 많이 사용된다.
이외에 Nexus, Portus 등이 있다.

---------------------------------------------
# docker-compose
는 설치해야하지만, 맥북과 윈도우는, Docker를 설치하면서 함께 자동 설치된다.

만약 Ubuntu라면
```shell
#도커설치
# https://docs.docker.com/engine/install/ubuntu/
# 그런데 실습환경을 다시 구축하고자 apt-get remove를 하는데 설치가 안되어있다고 뜬다.
$ sudo apt-get remove docker docker-engine docker.io containerd runc
$ sudo apt-get update
$ sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
# Docker의 GPG Key인증

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
#sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"

$ sudo apt-get update



$ sudo apt-get install docker-ce docker-ce-cli containerd.io
$ sudo docker run hello-world

# docker그룹에 유저를 추가시킨다
$ sudo usermod -aG docker $USER 
# 추가시킨후 도커서비스를 재시작해서 적용한다
$ sudo systemctl restart docker

#docker-compose설치
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose -v
```

###  컴포즈 명령어

#### docker-compose -v
```shell
# 현재 설치된 docker-compose 버전을 확인한다.
$ docker-compose -v
```

#### docker-compose ps
```shell
#현재 동작중인 컨테이너들의 상태를 확인할 수 있습니다.
docker-compose ps
```

#### docker-compose up
```shell
#컨테이너를 생성 및 실행 합니다.
$ docker-compose up

#도커 컴포즈 컨테이너들 백그라운드로 띄우기
$ docker-compose up -d

#다른 경로에 있는 도커 컴포즈 파일 사용 도커 컴포즈로 다른 이름이나 경로의 파일을 
#Docker Compose 설정 파일로 사용하고 싶다면 -f 옵션으로 명시
$ docker-compose -f /app/docker-compose.yml up

#여러 개의 도커 컴포즈 설정 파일을 사용할 수 있으며, 
#이 때는 나중에 나오는 설정이 앞에 나오는 설정보다 우선으로 적용된다.
$ docker-compose -f docker-compose.yml -f docker-compose-test.yml up
```

#### docker-compose logs
```shell
#컨테이너들의 로그를 출력 합니다.
$ docker-compose logs
```

#### docker-compose run
```shell
#docker-compose up 명령어를 이용해 생성 및 실행된 컨테이너에서 
#임의의 명령을 실행하기 위해 사용 합니다.
#컨테이너들을 모두 삭제할 경우 docker-compose start가 아닌, 
#docker-compose up으로 다시 컨테이너들을 생성 해주어야 합니다.

$ docker-compose run

# docker-compose run [서비스명] [명령]
$ docker-compose run redis /bin/bash
```

#### docker-compose kill
```shell
#실행중인 컨테이너를 강제로 정지 시킵니다. 
#-s옵션을 사용하여 시그널을 지정해줄 수 있는데, 
#아래 코드에서는 SIGINT를 사용 하였습니다. 
#-s 옵션을 사용하지 않고 docker-compose kill만 사용할 경우 SIGKILL 이 전송 됩니다.
#kill 뒤에 서비스를 지정하여 특정 서비스만 kill할 수 있습니다.

$ docker-compose kill
```

#### docker-compose rm
```shell
$ docker-compose rm

#docker-compose로 생성한 컨테이너들을 일괄 삭제 합니다. 
#(삭제 전, 관련 컨테이너들을 종료 시켜두어야 합니다.)
```


#### docker-compose down
```shell
#도커 컴포즈 컨테이너들 내리기

$ docker-compose down
```


#### docker-compose restart
```shell
#도커 컴포즈 컨테이너들 다시 시작하기

$ docker-compose restart
```

#### docker-compose ps
```shell
#도커 컴포즈 컨테이너들 상태 확인

$ docker-compose ps
```

#### docker-compose config
```shell
#도커 컴포즈 설정을 확인 (주로 -f 옵션으로 여러 개의 설정 파일을 사용할 때,
#최종적으로 어떻게 설정이 적용되는지 확인)

$ docker-compose config
```


#### docker-compose port
```shell
# 서비스 프라이빗 포트 번호의 설정을 확인할 수 있습니다.
# docker-compose port [서비스명] [프라이빗 포트 번호]
$ docker-compose port nginx 80
```


---------------------------------------------

# Harbor 설치하기



---------------------------------------------
# 도커 명령어 사용하여 Habor에 Push하기
![도커 라이프사이클](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbnJWPJ%2Fbtq6vTanLpQ%2F0wlETgQa34Sxj1yk6kKyuk%2Fimg.png)
> 그림의 출처는 찾을수가 없었으나, 가져온곳만 적음
> https://nearhome.tistory.com/76

보다싶이, 이미지를 만드는 방법은 실행중인 컨테이너를 commit하는 방법이있고,
Dockerfile을 build하는 방법이있다.

>***주의!*** 컨테이너는 정지(stop)후 재시작의 경우는 기존의 변경사항이 유지되지만,
종료(rm)을 한후에 다시 시작(run)하면 변경사항이 삭제되어있다.

이번 실습은 컨테이너 내부에 새로운 라이브러리나 패키지를 설치하거나 설정을 변경하거나 파일을 추가하는 등의 변경사항이 있다면, 이 변경을 반영한 이미지 그대로 저장하고자한다.
commit 명령어는 실행중인 컨테이너에서 사용가능하다.
실행중인 컨테이너를 커밋해보자.

#### docker 이미지 실행시켜보기
```
# 로컬 컴퓨터
docker run -d --name server ubuntu
docker exec -it server /bin/bash

# docker 내부
cd ~
apt update
apt -y install net-tools
ifconfig
touch DockerCommitTest
```

#### docker commit 명령어
```shell
docker commit -a "Minhan" -m "HelloWorld!" ubuntu ubuntu/server:1.0
```

#### 커밋된 이미지 확인
```shell
docker images -a
```

#### 이미지에 태그달기 (commit시 원하는 태그를 걸었다면 사용 안해도됌)
```shell
docker tag [이미지 이름]:[version] [이미지 이름]:[version]
```

#### Harbor에 로그인
```shell
docker login
```

#### Tag 가 적용되어 있는 Image 를 Harbor 에 Push
```shell
docker push [도커허브ID]/[이미지 이름]
```

#### 이미지 지우기
```shell
docker rmi [도커허브ID]/[이미지 이름]
```

#### 이미지 다운로드받기
```shell
docker pull 
```

#### pull한 이미지 확인
```shell
docker images -a
```
### 컨테이너 실행시켜보기
```shell
docker run -it minhan /bin/sh
```
