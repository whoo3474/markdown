# Vagrant
```
### 베이그런트란 
베이그런트(Vagrant)는 포터블 가상화 소프트웨어 개발 환경 (예: 개발 생산성 증가를 위해 가상화의 소프트웨어 구성 관리의 단순화를 시도하는 버추얼박스, 하이퍼-V, 도커 컨테이너, VM웨어, AWS)의 생성 및 유지보수를 위한 오픈 소스 소프트웨어 제품의 하나이다.

테스트를 위한, 사용자의 요구에 맞게 시스템 자원을 할당, 배치, 배포해 두었다가 필요시 시스템을 즉시 사용할 수 있는 상태로 미리 준비된 가상머신들의 생성과 삭제 등의 관리가 가능하도록 코드로 작성하는 툴을 말한다.
```
## Vagrant 설치
```mac
brew install vagrant
vagrant plugin install vagrant-vbguest
```
## Vagrant Cloud
> 필요한 Vagrant 이미지들을 찾아서 사용할수 있는곳.
> https://app.vagrantup.com/boxes/search

## Vagrant 명령어
```shell
vagrant init
```
###### vagrant를 Provisioning 하기 위한 초기 파일(Vagrantfile)을 생성합니다.

```shell
vagrant up
```
###### 작성된 Vagrantfile을 바탕으로 provisioning을 진행합니다. 

```shell
vagrant halt
```
###### vagrant에 관리하는 가상머신을 종료합니다.

```shell
vagrant destroy
```
###### vagrant에서 관리하는 가상머신을 삭제합니다.

```shell
vagrant ssh
```
###### vagrant에서 관리하는 가상머신에 ssh로 접속합니다.

```shell
vagrant provision
```
###### vagrant에서 관리하는 가상 머신의 설정을 변경하고 적용합니다.