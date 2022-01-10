
서버로 ssh접속을 할때는, 암호를 사용하거나, private,public 키로 접근이 가능하다.

## ssh접속시 전체적인 진행상황
1. 클라이언트에서 private, public 키를 생성한다. `ssh-keygen`
2. 타겟서버에 public key를 복사해 놓는다. public key는 자유롭게 공유하여도 된다.
3. private key는 클라이언트만 가지고 있어야하며, 누구에게도 복사해 주거나 공개하면 안된다.
4. 공유된 public key로 메세지를 암호화 하고, 그 암호화된 메세지는 그 쌍이 되는 private key로만 해석할수 있다.
5. 클라이언트에서 타겟서버로 public key를 공유한다 `ssh-copy-id -i 공개키경로 사용자@타겟서버ip`
6. 클라이언트에서 타겟서버로 ssh접속 `ssh 사용자@타겟서버ip`
7. 클라이언트에서 타겟서버 최초 접속시 **RSA key fingerprint**로 접속여부를 확인하는 차원으로 (yes/no)를 물어본다.
8. 여기서 yes를 할시에는 **~/.ssh/known_hosts** 파일에 해단 RSA key정보가 등록되며, 다음접속 부터는 물어보지 않는다.
9. no를 할시에는 접속이 불가능해진다.

---

## ssh접속 흐름
![ssh key authentication](https://2.bp.blogspot.com/-KFIKbVe38GY/VRugz3LpcHI/AAAAAAAAEUY/_zcGcOwpp90/s1600/DB30E7AC-42F8-4876-9BA4-04CB4623571F.png)
1. 클라이언트가 서버에 SSH 연결을 요청한다.
2. 서버는 랜덤 챌린지(랜덤 데이터 스트링)를 생성해 클라이언트에게 보낸다.
3. 클라이언트는 서버로 부터 받은 챌린지(C)를 자신이 가지고 있는 private 키로 암호화해서 암호화 된 메시지를 서버로 보낸다.
4. 서버는 클라이언트에서 받은 암호화 된 메시지를 public 키로 해석한 후 그 결과를 2단계에서 자신이 클라이언트에게 보낸 랜덤 챌린지와 일치하는지 확인한다. (public 키로 해석할 수 있는 메시지는 그 쌍이 되는 private 키를 가진 사람만이 만들 수 있기 때문) 일치하면 클라이언트가 인증된 것이다. 
(이미지 및 설명 출처 : https://arsviator.blogspot.com/2015/04/ssh-ssh-key.html)

---

## 주요 명령어 설명
### ssh
- a: 인증 에이전트의 전송을 불허
- c: 세션을 암호화하는데 사용할 암호 해독기를 선택한다.
  idea가 기본값이고,  arcfour가 가장 빠르며, 
  none은 rlogin이나 rsh(암호화 없음)를 사용
- e: 세션에 대한 이스케이프 문자를 설정
- f: 인증과 전송이 설정된 후에 백그라운드에서 ssh를 설정 
- i: RSA 인증을 위한 비밀 키를 읽어 올 아이덴티티 파일을 선택
- k: Kerberos 티켓의 전송을 불허
- l: 원격 시스템에 사용할 로그인 이름을 설정
- n: ssh가 백그라운드에서 실행될 때 사용되는 /dev/nulls 로 부터의 stdin을 재지정
- o: 구성 파일의 형식을 따르는 사용자 정의 옵션 사용  -p 원격 호스트에 있는 연결할 포트 설정  
- q: 모든 메시지를 억제하는 quiet 모드를 활성화한다.
- P: 특권이 부여되지 않은 포트를 사용   -t pseudo-tty 할당을 강제 . 
- p: port번호 설정
- v: (디버깅에 유용한) 자세한 정보 표시 모드를 활성화 
- x: X11 전송을 불가능하게 한다. 
- C: 모든 데이터의 압축을 요구한다. 
- L: 지정된 원격 호스트와 포트에 전송할 로컬 포트 설정
- R: 로컬 호스트와 지정된 포트로 전송될 원격 포트 설정
(ssh 명령어 출처: https://experiences.tistory.com/33 [IT경험])

### ssh-keygen
- b bits	생성할 키 비트수 지정. 최소 768비트가 필요하며 디폴트 값으로 2048비트로 설정되어 있음	
- C comment	주석 입력 가능. 서버에 따라 특별한 용도로 사용 하기도 함	
- t	어떠한 암호화 방식을 사용 할 것인지를 지정	
`$ssh-keygen -t rsa → rsa암호화 방식으로 키 생성`
- f	저장할 파일명 지정. 경로 지정 가능	
(ssh-keygen 명령어 출처: https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_ssh-keygen [제타위키])

### ssh-keyscan
- t : 암호화하는 타입이다. 특별한 경우가 아니라면 가장 자주 사용하는 rsa 타입으로 설정하면 되겠다.
- b : 암호의 bit수를 의미하며 default값은 2048이다.
- f : 저장할 파일 위치와 이름.
- C : 주석 부분으로 역할이 특별히 없다.
  
### ssh-copy-id


---

클라이언트에서 테스트를 하기위하여, docker로 컨테이너를 client-ubuntu와 server-ubuntu로 실행시켜 준다.

```shell
docker run -d --name client-ubuntu ubuntu sleep infinity
docker run -d --name server-ubuntu ubuntu sleep infinity
```


docker컨테이너를 접속하여 줄것인데,
attach로 접속하면 처음 도커를 run하였을때의 환경이 포그라운드로 보여지게 되기때문에,
/bin/bash 와 같은 쉘로 작동시킨것이 아니라하면, attach를 활용할수없다.
예를 들어 pm2나 node 명령어로 웹서버를 작동 시켰다면, 해당 명령어의 결과들이 보이게 된다.
그렇기에, 실행되고 있는 컨테이너에 새로운 명령을 할수있는 **exec**을 사용하여 접속한다. 

```shell
## ex) docker exec -it (컨테이너 ID 또는 이름) /bin/bash

docker exec -it client-ubuntu /bin/bash
docker exec -it server-ubuntu /bin/bash
```
/bin/bash가 안될때는 /bin/sh를 사용한다.
alpline 버전일경우에는, bash쉘이 없을수가 있다.

---

## 잠깐 sudo와 su 설명
sudo와 su 모두 root권한으로 명령을 내리는데 사용된다.
```shell
sudo 명령어
```
일반 사용자가 root권한을 잠시 빌려서 명령을 실행

```shell
su 명령어
```
현재 사용자를 로그아웃하지 않은 상태에서 다른 사용자의 계정으로 전환하는 명령어


```shell
su - 명령어
```
다른 사용자의 계정으로 완전히 전환하고, 전환한 사용자의 환경설정을 불러오는 명령어

> **su** 와 **su -**의 차이
> su 명령어는 현재 계정의 환경변수를 유지한 채, 대상 계정으로 전환한다.
> 반면에 su - 명령어는 다른 사용자의 계정으로 전환하고, 환경 변수까지 그 계정의 상태로 완전히 전환한다.\

---

기본적으로 docker hub의 ubuntu 이미지를 사용하면, ssh, vim, sudo 등의 명령어가 설치되어 있지않다.
이를 설치해주기 위하여 apt를 사용한다.
패키지를 설치하기위하여 **apt** 또는 **apt-get**라는 우분투에서 쓰이는 데비안 계열의 패키지를 관리하는데 쓰이는 도구가 존재한다.
간단한 예로, apt는 설치 진행 상황을 프로그레스바로 보여주거나, 업그레이드를 할수있는 패키지 숫자도 보여주지만, 
apt-get은 별도의 옵션을 사용하여야 가능하다.
apt는 사람들이 자주쓰는 사용자 친화적인 기능을 기본적으로 탑재한것이다.
apt-get은 오래전부터 존재해왔기 때문에, 더 안정적이고 호환성이 높기때문에 script를 작성할때는 유리하다.
각각 장단점이 있고, 내부 동작의 차이는 거의 없기때문에, 둘중 아무거나 사용하면 된다.
```shell

sudo apt update
sudo apt upgrade

apt install ssh
apt install openssh-server
```

---

ssh를 이용하여, 클라이언트에서 원격서버로 접속시에는 **ssh-keygen**으로 키값을 만들어줘야한다.

```
## client-ubuntu

ssh-keygen -t rsa -b 4096 -C minhan_a@naver.com
```
이후 질문들은, 어디에 저장할지 등을 적는것이기에, enter로 통과한다.

> 단! 질문중 file이름을 설정하는것이 있는데, 기본값이  **~/.ssh/id_rsa** 로 되어있기때문에,
> 기존에 사용하고있던 ssh key가 있다면 덮어쓰기가 되기 떄문에, 원하는 이름을 적어도 좋다.

```
## client-ubuntu
ssh-copy-id -i ~/path/id_rsa[private_key_path] user_name@remote_ip
```
만약 i없이 진행하면 **~/.ssh/id_rsa**를 default 위치로 진행하게된다.
ssh-copy-id 를 사용하여 server의 **~/.ssh/authorized_keys**에 public key를 저장된다.
이로써 client가 ssh 명령으로 접속을 시도하면 server에서는 상대방이 private key를 가졌는지 테스트를 통해 확인하고 연결 여부를 결정하게 되는 것이다.

```
cat ~/path/id_rsa[private_key_path] | ssh user_name@remote_ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys
```
(직접 copy 진행하기 출처 : https://danthetech.netlify.app/Backend/configure-ssh-key-based-authentication-on-a-linux-server)


ssh 테스트를 위하여, 각 컨테이너에서, 기본 22번을 사용하는 ssh포트를 10022번으로 변경해준다.
그리고 ssh password를 이용한 접속을 막기위한 설정도 해준다.

```shell
##vi /etc/ssh/sshd_config

Port 10022
PasswordAuthentication no
```

저장하여 설정을 끝내었으면, 다음 명령어로 서비스를 재구동한다.

```shell
systemctl restart sshd
```

---

# 방화벽 부분은 새로 작성 해야겠다. 

ufw vs iptables : 데비안 계열
firewalld vs iptables : 레드헷 계열

포트번호를 바꾸었으니, 해당하는 포트를 iptables이나 firewalld로 방화벽을 열어준다.

ubuntu는 기본적으로 UFW 라는 iptables용 방화벽 구성 도구가 있다.

iptables는 CentOS 7 이전에 기본 방화벽으로 설치되었었다.

firewalld는 iptables의 업그레이드 버전
정적 방화벽인 iptables와 달리, 방화벽 설정을 자유롭게 하며, 즉시 적용 가능한 동적 방화벽.
변경시 즉시 적용하는  네트워크 중단이 발생하지 않는다.


Iptables는 커널 수준의 IP 필터링 메커니즘입니다. IP 패킷에 대한 라우팅 결정 등을 할 수 있습니다.

UFW(Uncomplicated Firewall)는 iptables 위에 구현된 단순화된 방화벽 메커니즘입니다. 
UFW는 유연하지 않지만 일반적인 시나리오에 대해 구성하기가 더 쉽습니다.

UFW는 기본 기본 방화벽을 제공하며 기본 서비스를 쉽게 켜고 끌 수 있습니다. 
IPtables를 사용하려면 TCP/IP 연결, 더 복잡한 프로토콜(예: NFS)을 이해해야 하며 여전히 복잡할 수 있습니다.

UFW는 iptables가 수행하는 일부 기능을 수행할 수 없지만 훨씬 단순화된 방화벽이며 필요한 기능을 제공하는 경우 구성이 훨씬 간단하고 실제로 원하는 작업을 수행하고 있다는 확신을 갖기가 더 쉽습니다.

```shell
## filrewalld 설치
sudo apt install firewalld -y


sudo firewall-cmd --version
```


---

접속을 진행한다
```shell
ssh -p 10022 -i 키경로 (사용자)@(서버IP)
```


ssh 접속시, 유저명과 서버 IP, 그리고 이름이 바뀐 key 파일명을 적어주기가 귀찮다면,
client에서 ~/.ssh/config 을 이런식으로 저장해준다.
```shell
# ~.ssh/config

Host server
	HostName 172.0.0.1
	User minhan
	Port 10022
	IdentityFile ~/.ssh/id_rsa (또는 바뀐 파일명)
```

config파일을 처음만들었다면, 400권한을 부여한다
```
sudo chmod 400 ~/.ssh/config
```

이제부터
```shell
ssh server
```
로 접속하면 된다.
