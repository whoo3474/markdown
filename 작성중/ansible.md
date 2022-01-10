> 여러 블로그와 책, DevOps를 위한 IaC 클라우드 인프라 자동화 전문가 과정, Ansible을 이용한 AWS클라우드 DevOps자동화 구현 수업을 참고하여 작성하였습니다.
> 개인 공부와 기록을 위해 적는 글이므로 ,혹시 불편한 부분이 있다면 댓글을 달아주시면 감사하겠습니다.


# 인프라 엔서블

## 이것을 왜 쓰는걸까?
Iac(infrastructure as code: 코드형 인프라)는 인프라를 웹 인터페이스 및 대화형식의 도구를 사용해 수동적으로 인프라를 구성하는 것이 아닌, 시스템이 읽을 수 있는 인프라 정의 파일을 통해 인프라의 구성 관리 및 배포를 자동화 하는것을 일컫는다.
물리적 하드웨어 뿐만 아니라, 가상 컴퓨터, 클라우드 등 관련 리소스를 IaC를 통해 구성 관리 및 배포할수 있다.
ping 테스트, 점검 자동화, 환경 확인 및 설정에 사용할수있다.
가상화 테스트 환경을 위해 **베이그런트**를 주로 사용한다.

> asible은 agent가 없다. (정확히는 관리하고자하는 노드들에 설치되어있는 파이썬을 사용함)
> agent가 없다는것은 관리하고자하는 노드들에 따로 설치 진행을 하지 않아도 된다는 것이다. (Python이 없을시에는 Python을 설치해줘야함)


---

### IoC의 장점?
- 비용 절감
  사람의 노력적인 측면에서 인프라 관리를 수동적으로 하지 않음으로써 다른 생산적인 작업에 노력을 집중할수있다.
- 안정성
  수동으로 구성할 때와 같은 사람의 실수와 관련 위험을 제거할 수 있다.
- 코드화 및 버전관리
  표준화된 포맷과 규칙으로 작성된 코드 문서를 통해 누구나 읽을 수 있고, 확인할 수 있다.
  또한 코드는 변경 사항 이력을 남길수 있어 추후 문제 발생 시 어떤 부분이 변경되어 발생된 문제인지 확인이 쉽다.
- 재사용성
  인프라를 코드화 하고 리소스를 그룹 및 모듈화 해서 필요 시 필요한 부분을 재사용 할 수 있다.

---

### 프로비저닝(Provisioning)이란?

>"프로비저닝은 사용자의 요구에 맞게 시스템 자원을 할당, 배치, 배포해 두었다가 필요 시 시스템을 즉시 사용할 수 있는 상태로 미리 준비해 두는 것을 말한다."-위키피디아


프로비저닝(Provisioning) 이란 의미는 영어 직역한 그대로 "제공하는것" 이다. 
어떤 종류의 서비스든 사용자 혹은 비즈니스 요구사항에 맞게 시스템 자체를 제공 하는 것을 프로비저닝이라고 합니다.
즉,  제공해줄 수 있는 것은 인프라 자원이나 서비스, 또는 장비가 될 수도 있다.


---
### 멱등성
#### 멱등성(idempotence) 이란?
>"멱등법칙 또는 멱등성(冪等性, 영어: idempotent)은 수학이나 전산학에서 연산의 한 성질을 나타내는 것으로, 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질을 의미한다. 멱등법칙의 개념은 추상대수학과 함수형 프로그래밍의 여러 부분에서 사용하고 있다. 멱등성의 개념은 적용되는 곳에 따라 여러 의미를 가진다."-위키피디아


연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질을 멱등성(idempotence) 이라 한다. puppet, chef, ansible 등은 모두 이런 특성을 가지고 있다. 
쉽게 말하면, rest api의 경우 get, head,put, delete 메소드는 멱등성을 가지고 있다. 그러나 post는 상태를 변화시키기 때문에 멱등성이 없다. (status 변화를 준다.) 


#### ansible 툴에서의 멱등성이란?

여러번 ansible 툴을 사용하더라도 동일한 결과값을 나올 수 있도록 제공되는 형태여야 한다. 즉 매번 다른 결과가 나오거나 에러가 나온다면 비멱등성(non-idempotent) 하다고 할 수 있다. ansible 툴의 거의 대부분의 모듈은 멱등성을 제공한다. 또한 멱등성을 제공하기 위해서는 조건절을 제공하고 있다. 

원래 제공한 ansible 모듈은 대부분 멱등성를 제공하나 개발자/시스템 운영자가 만든 ansible 스크립트는 멱등성를 제공하지 못할 수 있다. 따라서 멱등성이 깨지지 않도록 주의해야 한다. 


출처: https://knight76.tistory.com/entry/ansible-멱등성idempotent-용어-이해하기 [김용환 블로그(2004-2020)]
---
---

---
### ansible 용어 설명
1) 제어노드
   - 제어노드는 Ansible이 설치된 모든 호스트다. 제어노드에서 ansible 또는 ansible-playbook 명령을 이용하여 작업을 실행할수있다.
   - 그러나 Window는 제어노드로 사용이 불가능하다.
2) 관리노드
   - 관리노드는 Ansible로 관리하는 호스트 및 네트워크 장치다. Ansible은 관리노드에 설치되지 않는다.
3) 인벤토리
   - 인벤토리(Inventory)는 관리 노드의 목록으로, 관리 노드에 대한 호스트 이름이나 IP 주소와 같은 정보를 지정한다.
   - 여러 관리 노드를 그룹으로 조직화 할 수 있고, 중첩 그룹을 사용할 수 있다.
4) 플러그인
   - Ansible의 핵심 기능을 확장할 수 있도록 다양한 플러그인들을 제공한다.
   -  Action, Become, Cache, Callback, Connection, Httpapi, Inventory, Netconf, Lookup, Shell, Strategy, Vars
   -  > https://docs.ansible.com/ansible/latest/plugins/plugins.html
5) 모듈
   - 모듈(Module)은 Ansible을 실행하는 Python 코드 단위이다.
   - 각 모듈은 호스트에 패키지를 설치 및 관리하고, 데이터베이스의 사용자를 관리하고, 네트워크 장치의 VLAN 인터페이스를 관리할 수 있는 등 약 3000개의 모듈이 잇다.
   - 하나의 모듈은 하나의 작업을 실행 할 수 있고, 플레이북을 이용해 여러 모듈을 선언해 여러 작업을 수행할 수 있다.
6) 작업
   - 작업(Task)는 Ansible의 작업 실행 단위이다.
   - 하나의 모듈이 하나의 작업이 되며, Ad-hoc 명령을 통해 단일 작업을 실행하거나, 플레이북을 작성해 여러 작업을 실행할 수 있다.
7) Ad-hoc 명령
   - ansible 명령을 터미널 명령어로 단일 작업을 실행할 수 있다.
8) 플레이
   - 플레이(Play)는 특정 관리 노드를 대상으로한 순서가 지정된 작업 목록이다.
9)  플레이북
   - 플레이북(Playbook)은 하나 이상의 플레이를 가지고 있는 YAML 파일이다.
   - ansible-playbook 명령을 이용해 플레이북을 실행할 수 있다.


---

## ansible 설치하기

```mac
brew install ansible
```

``` ubuntu
# Ansible 설치
sudo apt update
sudo apt install -y software-properties-common
sudo apt-add-repository -y -u ppa:ansible/ansible
sudo apt install -y ansible

#Ansible 명령 쉘 자동완성
```

---

*참고 - ansible 설정파일 참조 순서*
1. 현재 디렉토리의 ansible.cfg
2. $HOME/.ansible.cfg
3. /etc/ansible/ansible.cfg
 
---

## ansible-galaxy란?
>https://galaxy.ansible.com/

각 디렉토리 및 파일의 설명은 다음과 같다.

#### defaults
이 디렉토리의 main.yml 파일에는 role이 사용될 때 덮어쓸 수 있는 role 변수의 기본값이 포함되어 있다. 이러한 변수는 우선 순위가 낮으며 플레이에서 변경하고 사용자 지정할 수 있다.
이러한 변수는 role을 사용하는 플레이를 작성하는 사람에게 할 일을 정확하게 사용자 지정하거나 제어할 방법을 제공하기 위한 것이다. default 변수는 해당 값을 재정의하려는 경우에만 사용해야 한다.

#### files
이 디렉토리에는 role 작업에서 참조하는 일반적인 정적 파일이 있다.

#### handlers
이 디렉토리의 main.yml 파일에는 role의 핸들러 정의가 들어있다.

#### meta
이 디렉토리의 main.yml 파일에는 작성자, 라이센스, 플랫폼 및 role 종속성 등을 포함한 role의 메타데이터 정보가 들어있다.

#### tasks
이 디렉토리의 main.yml 파일에는 role의 task들을 정의한 내용이 포함되어 있다.

> task 수행시 어떤 결과를 저장하고 다음 단계에서 사용할수 있는, **register** 모듈을 사용하여 변수에 저장할수있다.

> 특정 조건에서만 task를 진행하고 싶을경우, when 모듈을 통해 조건문을 달수있다.

> delegate_to 모듈을 통해 작업 위임이 가능하다.(다른 호스트 서버에서 작업할수 있다.)
> run_once와 자주 사용된다. 한번만 작업하면 되는 경우가 많아서.

> gather_facts 엔서블에서의 facts 란 클라이언트 서버 정보(OS버전 등)이다. 가져온다면 속도가 느려짐.
> 필요할경우에만 사용

#### templates
이 디렉토리에는 role에서 참조하는 jinja2 템플릿이 들어있다.

#### tests
이 디렉토리에는 role을 테스트하는데 사용할 수 있는 인벤토리와 test.yml 플레이북이 포함될 수 있다. 테스트용이므로 따로 사용하지 않는다면 무시해도 무방하다.

#### vars
이 디렉토리의 main.yml파일은 role의 변수 값을 정의한다. 종종 이러한 변수는 role 내에서 내부 목적으로 사용된다. 이러한 변수는 우선순위가 높으며 플레이북에서 사용될 때 변경되지 않는다.  vars 변수의 의도는 ROLE의 내부 기능에 의해 사용되는 변수를 정의하도록 하는 것이다.

#### README.md
사용자가 읽을 수 있는 role의 기본 설명, 사용방법, 설명서, 예제, 작업하기 위해 보유할 수 있는 비 ansible 요구사항 등을 제공한다.

#### main.yml
위에서 명시한 각 디렉토리에 내용을 넣을 경우, 이 이름을 사용해서 넣는다. 각 파일의 내용에서 들여쓰기는 그냥 처음부터 시작한다.  해당 role을 참조하는 파일의 들여쓰기 수준만큼 쓰는 것이 아니다.

### 참고
- 여기 명시된 각 디렉토리의 이름은 바꾸면 안된다. 또한 그 아래의 yml파일도 main.yml 로 해야한다. 모두 동일하다.
- 이러한 디렉토리는 필수가 아니며, 필요한 디렉토리만 있으면 된다.

 
---

# 접속시 권한(ansible.cfg)

> 출처: https://watch-n-learn.tistory.com/76

ansible.cfg에는 크게 2가지 섹션이 있다. 
- [default]  : ansible 작업의 기본 환경설정 값을 구성
- [privilege_escalation] : 관리 호스트에서 ansible이 권한 에스컬레이션을 수행하는 방법을 구성

### [defalult]

|설정값|설명|
|-----|---|
|inventory = ./inventory|***사용할 인벤토리 파일의 경로*** 정적 인벤토리 파일 또는 여러개의 정적 인벤토리 파일이 있는 디렉토리를 명시 동적 인벤토리 스크립트를 포함하는 디렉토리 명시|
|remote_user = user|***관리 호스트에 연결할 사용자의 이름. 관리 호스트에서 사용하므로 관리 호스트에 해당 유저가 존재해야 한다.*** 이 구문을 따로 명시하지 않으면 ansible은 ansible 명령을 실행하는 로컬 사용자명을 사용하여 관리 호스트에 연결한다. 따라서 다른 원격 사용자를 지정하려면 여기에 해당 유저명을 넣는다.또한 여기서 명시된 유저를 사용하여 [privilege_escalation]을 적용한다. (물론 명시하지 않으면 ansible 명령을 실행하는 로컬 사용자 이름을 사용한다)|
|ask_pass = false|***ssh암호를 요청하는 메시지 표시 여부를 결정*** 제어노드에서 관리 호스트에 연결할 사용자에 대해 인증할 수 있는 키가 구성된 경우 따로 비밀번호를 물어보지 않고 자동으로 로그인된다. 이런 경우에는 ssh 암호를 요청할 필요가 없으므로 false로 설정한다. default는 false 이다. 위 경우가 아닌, 비밀번호를 입력해야 하도록 설정된경우, true로 설정하여 로컬 사용자에게 원격 사용자가 사용하는 암호를 요청하는 메시지가 표시하도록 해야 한다. 만약 비밀번호를 써야하는데 false로 되어있으면, 아예 비밀번호를 입력할 기회 자체가 없어진다.|


### [privilege_escalation] 

|설정값|설명|
|-----|---|
|become = true|***remote_user로 연결한 유저를 sudo 또는 su를 사용하여 특정 상급 유저로 전환할지 여부***사용하는 모듈에 따라 원래 유저의 권한만으로 충분할 수도 있고, 상급유저(root)의 권한이 필요할 수도 있다. 이에 따라 true/false가 달라질 것이다. 상급 유저로 전환하려면, true를 설정한다. default 값은 false이다.다만, 이렇게 설정해도 ansible 명령 또는 ansible-playbook 실행 시 다양한 방법으로 이를 재정의할 수 있다. 예를 들어 특정 ansible-playbook은 권한 상승이 필요없는 경우 false로 재정의할 수 있다. 이 방법은 이후에 다시 설명한다.|
|become_method = sudo|***상급 유저로 전환하는 방법을 명시한다.*** default는 sudo이고, su는 옵션이다. 일반적으로 sudo 그대로 사용한다.|
|become_user = root|***상급 유저로 전환할 때, 어떤 유저로 전환할지 명시한다.***default는 root 이다.|
|become_ask_pass = false|***become_method의 암호를 요청하는 메시지 표시 여부***default는 false 이다. 상급 유저로 전환할 때 sudoers 설정을 따로 하지 않아 상급유저의 암호를 입력해야 한다면, 이 값을 true로 해야 한다. 위 [inventory] 항목에서 remote_user 로 설정한 유저가 sudo 권한이 있다면 이 값은 false로 하면 된다.|

> 구성파일 내에서 주석 사용하기
> - ansible .cfg 구성파일은 #와 ; 로 주석을 사용할 수 있다.
> - \# 는 줄 맨앞에 넣으며 전체 줄을 주석처리한다.
> - ; 는 해당 줄에서 ; 의 오른쪽에 있는 모든 내용을 주석처리한다.


```
- hosts: all
  become: yes
  become_user: user1

- hosts: all
  remote_user: user1
```
--become-user <BECOME_USER>
이 사용자로 작업을 실행합니다 (기본값 = root).

become_user : 플레이북을 실행할 사용자를 의미하며, 원격 사용자는 플레이북을 원격 서버에서 실행합니다.
remote_user: user1 = Log in as foofoo on that remote server.


> ansible_user 와 remote_user는 같은 설정값이다. 둘중 하나만 선택하여 수정하여도 됌
```shell
MAGIC_VARIABLE_MAPPING = dict(
   connection       = ('ansible_connection',),
   remote_addr      = ('ansible_ssh_host', 'ansible_host'),
   remote_user      = ('ansible_ssh_user', 'ansible_user'),
   port             = ('ansible_ssh_port', 'ansible_port'),
```


>add_host는 실행 중에 Ansible 플레이북의 메모리 내 인벤토리에 호스트와 그룹을 동적으로 추가할 수 있는 Ansible 모듈입니다. 
---

## 에드혹 or 에드훅

```shell
ansible all - m ping
```
에드훅은 터미널에서 한줄의 명령어로 실행하는것을 말한다.
여기서 all 은 인벤토리의 모든 host들을 뜻한다.
192.168.30.1 같은 인벤토리안의 host 주소를 적어주어도 된다.

```
-m : 사용할 모듈을 적는다
-a : argument, 전달할 명령어
-k : 
-i : default는 /etc/ansible/hosts, 값을 지정할시에는 그 안의 인벤토리들을 사용하겠다
—list-hosts : 인벤토리안의 호스트들을 보여줌
-c : ssh 프로토콜을 이용하지 않는다
```

---
---

## 인벤토리


---
---
## 모듈(module)
### 모듈이란?

- #### local
```shell
ansible localhost -c local -m lineinfile -a "path=inventory.list line=192.168.30.10"
```

---
- #### copy
```shell
ansible all -m copy -a "src=~/test.txt dest=~/directory/test.txt"
```
src : 시작할 위치
dest: 도착할 위치

---
- #### apt
centos 에서 패키지 관리할때 사용

- #### yum
ubuntu에서 패키지 관리할때 사용
>혹시 관리하는 호스트들이 ubuntu와 centos가 혼용되어있으면, 인벤토리를 따로 관리하면 좋음.
명령어가 다른부분이 있으므로.

---
- #### shell
ansible all -m shell -a "ls -a"
shell 명령어를 치듯 아무거나 사용 가능

---
- #### blockinfile : 파일안에 블록으로 텍스트를 추가한다

---
- #### lineinfile : 파일안에 라인을 추가한다.
 path : 파일을 정해서
 line : 이 라인을 넣을것이다.

---
- #### replace
 
---
- #### service

---
- #### mount

---
- #### get_url

---
- #### command

---
- #### file

---
- #### setup

---
- #### user

---
- #### timezone
name : 타임존의 위치 (ex- Asia/Seoul)

---
- #### action
when : 언제

---
- #### docker_container


---

- #### ec2
aws ec2를 프로비져닝하기위한 모듈
region, instance_type, image, exact_count(생성할 갯수), count_tag, group(security_group), key_name(pem), user_data(생성시 실행할 명령어), instance_tags(태그), vpc_subnet_id

```yaml
---
- name: Install nginx on the nodes
  hosts: all
  become: yes

  tasks:
    - name: install epel-release for centOS
      action: "{{ ansible_pkg_mgr }} name=epel-release state=latest"
      when: ansible_distribution == 'CentOS'

# setup 에 있는 정보중에 ansible_distribution 변수를 찾고, 그 밑의 ansible_pkg_mgr을 찾는다.
# {{}} 중괄호로 변수를 감싸면, 엔서블 실행시 값으로 변경됌
```


---
---

## Playbook(플레이북)
indent(띄어쓰기)는 스페이스 2번, 탭은 하지마시오.
yaml 문법으로 작성한다.
indent에 따라, 배열, 객체, 문자열 등으로 구분된다.
```yaml
---
- name: Install nginx on Ubuntu
  hosts: Ubuntu
  gather_facts: no
  become: yes
  become_user :root

  tasks:
    - name: install nginx web server
      apt: pkg=nginx state=present update_cache=yes
    - name : Upload default index.html
      get_url: url=https://www.nginx.com dest=/usr/share/nginx/html mode=0644 validate_certs=no
      
  
# gather_facts : ip, os, hosts ...등의 변수 x
# become :
# hosts :  ? inventory를 지정 (ex- all, centos, ubuntu ...등)
# tasks : 
```



---
---
### 예시
#### ansible docker

#### aws ec2 instance생성
```yaml
#vi ec2.yml

---
- name: launch webservers instance
  hosts: localhost
  tasks:
  - name: start the instance
    ec2:
      region: ap-northeast-2
      ## region 타입
      image: ami-081511b9e3af53902
      ## instance image 타입
      instance_type: t2.micro
      ## instance 타입
      exact_count: 1
      ## 생성할 instance 갯수
      count_tag: { type: web }
      vpc_subnet_id: subnet-~~~~~~~~~~~~~~
      ## 생성된 subnet_id를 연동한다
      assign_public_ip: True
      ## public ip를 할당한다
      user_data: "{{ lookup('file', 'user_data.sh') }}" 
      ## user_data 파일을 실행하겠다.
      instance_tags: { Name: WEB01 }
      ## 태그 
      group: SG-WEB
      ## security group
      key_name: 20210326
      wait: yes
```
```shell
#vi user_data.sh

#!/bin/bash
yum install -y httpd
systemctl start httpd && sudo systemctl enable httpd
echo "WEB01" > /var/www/html/index.html
```

##### aws inventory 추가
```shell
#vi /etc/ansible/hosts

[aws]
ec2-instance ansible_host=123.123.123.123 ansible_user=ec2-user ansible_ssh_private_key_file=/home/ec2-user/20210917.pem
```

##### aws inventory 확인
```shell
ansible aws -m ping
```

#### docker 설치
```yaml
#vi file.yml

- name: confugure yum repo for docker
  hosts: ec2-instance
  become: yes
  become_user: root
  tasks:
    - name: Install docker
      shell: "amazon-linux-extras install docker -y"
    - name: enable Docker services
      service:
        name: "docker"
        state: started
        enabled: yes
    - name: install pip
      yum:
        name: python-pip
        state: present
    - name: Installing Docker SDK for python3
      command: pip install docker
    - name: launching apache server in docker
      docker_container:
        name: webserver
        image: nginx
        ports:
        - 80:80
        state: started
```

#### ec2 삭제
```yaml
#vi ec2_remove.yml

- name: Terminate instances
  hosts: localhost
  tasks:
    - name: Terminate instances that were previously launched
      ec2:
        region: ap-northeast-2
        state: 'absent'
        instance_ids: 'i-~~~~~~~~~~~~~~'
```

#### docker 삭제
```shell
ansible localhost -m docker_container -a "name=test-ghost image=ghost ports=8000:2368"
```