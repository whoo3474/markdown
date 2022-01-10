- [kubenetes를 관리하는 명령어](#kubenetes를-관리하는-명령어)
    - [init 이나 join을 잘못되었을 경우 리셋](#init-이나-join을-잘못되었을-경우-리셋)
    - [token이 만료되었을경우](#token이-만료되었을경우)
    - [kubeadmin 설치 순서](#kubeadmin-설치-순서)
- [kubernetes pod 개념](#kubernetes-pod-개념)
  - [pause란?](#pause란)
  - [cgroups](#cgroups)
  - [ipc란?](#ipc란)
  - [pid란?](#pid란)


# kubenetes를 관리하는 명령어
- kubeadm: 클러스터를 부트스트랩 하는 명령
- kubelet: 클러스터의 모든 시스템에서 실행되는 구성 요소로, 창 및 컨테이너 시작과 같은 작업을 실행
- kubectl: 클라이언트 명령어


sudo swapoff -a 
는 리부트하면 원상복구된다.


node들은 모두 개별적인 hostname을 가져야한다.
hostnamectl -h
hostnamectl set-hosename [hostname]
vim /etc/hosts 도 변경

### init 이나 join을 잘못되었을 경우 리셋
>kubeadmin reset
-- ${home}/config 삭제하고
다시 init

### token이 만료되었을경우
>kubeadm token create --print-join-command
kubeadm token list

### kubeadmin 설치 순서
>(master) init -> (master) config -> (nodes) join -> (master) addon cni 추가

>https://www.inflearn.com/course/%EB%8D%B0%EB%B8%8C%EC%98%B5%EC%8A%A4-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EB%A7%88%EC%8A%A4%ED%84%B0/lecture/37834?tab=curriculum&speed=2



# kubernetes pod 개념
- 한 컨테이너에서 여러 프로세스를 실행하는 것은 권장하지 않음
  - 컨테이너가 실행 중이라도, 메인 프로세스를 제외한 다른 프로세스들이 실행 중인 상태를 보장할 수 없음.
- Kubernetes Pod의 특정 컨테이너가 종료되면, Kubelet이 restartPolicy에 따라 컨테이너를 재시작한다
- Pod을 어떻게 구성할지 판단 기준
  - 컨테이너들이 꼭 같은 노드에 실행되어야 하는가?
  - 해당 컨테이너들이 같은 개수로 수평 확장되어야 하는가?
  - 컨테이너들을 하나의 그룹으로 함께 배포해야 하는가?
- Pod의 컨테이너 간 격리
  - Host와 공유되는 namespace -> cgroup, user
  - 같은 Pod의 컨테이너 간 공유되는 namespace -> ipc,net
  - 컨테이너 별로 격리되는 namespace -> mount, uts, pid
    - pd namespace 공유는 optional
- Pause Container?
  - 컨테이너 간 공유될 IPC, Network namespace를 생성하고 유지
  - PID namespace공유시 Zombie precess reaping 역할도 수행

-------------------------------------------------
## pause란?

>https://ikcoo.tistory.com/10
이해가 잘된다.

container관리를 위한 linux namespace상속
부모 Container(pause container)를 생성하여, 같은 Pod의 container들이 pause의 namespace(Container의 IPC, Network, Pid)를 상속받음

즉 한 Pod 안에 생성되는 모든 Container 들은 부모 프로세스가 pause container인

리눅스의 namespace에 속하기 때문에 네트워크와 볼륨을 공유하고 서로 localhost로 통신할 수 있음


![image](https://user-images.githubusercontent.com/23617635/144201916-697a8f3d-3b03-471c-b4fc-4c0a8ff6dc72.png)

-------------------------------------------------

## cgroups

-------------------------------------------------
## ipc란?

## pid란?

>https://ssup2.github.io/onebyone_container/3.1.PID_Namespace/




