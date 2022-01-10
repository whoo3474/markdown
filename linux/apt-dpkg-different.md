> 공식 문서와 블로그, 검색 등을 통해 제가 원하는 자료를 만들고있습니다.
> 되도록 이미지에대한 정확한 출처를 밝히고자 하지만,
> 책이나 PDF를 인용하여 사용한듯한 곳에서는 정확한 출처를 찾을수가 없었습니다.


# 리눅스 패키지란?
> 출처 : https://bradbury.tistory.com/227
> 출처 : https://gamsungcoding.tistory.com/entry/Linux-%EB%A6%AC%EB%88%85%EC%8A%A4Linux-%ED%8C%A8%ED%82%A4%EC%A7%80-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0


리눅스 패키지(Linux Package)란 리눅스 시스템에서 소프트웨어를 실행하는데 필요한 파일들(실행 파일, 설정 파일, 라이브러리 등)이 담겨 있는 설치 파일 묶음이다.

패키지는 종류는 소스 패키지(Source Package)와 바이너리 패키지(Binary Package)가 있다.

소스 패키지(Source Package)는 말 그대로 소스 코드(C언어..등)가 들어 있는 패키지로 컴파일 과정(configure,make,make install 명령어)을 통해 바이너리 파일로 만들어야 실행할 수 있다.

즉, 소스 패키지는 설치할 때 컴파일 작업도 진행되므로 설치 시간이 길고 컴파일 작업 과정에서 오류가 발생할 수 있다.


바이너리 패키지(Binary Package)는 성공적으로 컴파일된 바이너리 파일이 들어있는 패키지이다.

이미 컴파일이 되어 있으니 소스 패키지에 비해 설치 시간도 짧고 오류가 발생할 가능성도 적다.

따라서 리눅스의 기본 설치 패키지들은 대부분 바이너리 패키지이다.


---------------------------------------------



리눅스 배포판에 따라서 서로 다른 패키지 형식을 지원하는데 대부분 다음의 3가지 중 하나를 지원한다
 
- Debian 계열 (Debian, Ubuntu 등) : .deb 파일
- RedHat 계열 (RedHat, Fedora, CentOS) : .rpm 파일
- openSUSE 계열 : openSUSE를 위해 특별히 빌드된 .rpm 파일

Ubuntu에서는 /var/cache/apt/archives 디렉터리에 다양한 .deb 파일들이 보관되어 있다. 이러한 패키지를 관리하기 위해선 패키지 관리 도구를 사용하는데, 일반적으로 다음 두 유형의 패키지 관리 도구가 사용된다

저수준 툴(low-level tools) : 실제 패키지의 설치, 업데이트, 삭제 등을 수행
고수준 툴(high-level toos) : 의존성의 해결, 패키지 검색 등의 기능을 제공


아래 표는 리눅스 배포판 별로 저수툰/고수준 패키지 관리 도구를 나타내었다



|구분|저수준 툴|고수준 툴|
|:---:|:--------:|:--------:|
|데비안계열|dpkg|apt-get / apt|
|레드헷계열|rpm|yum|
|openSUSE|rpm|zypper|


------------------------------------------------


## 저장소가 기록된 파일
- /etc/apt/sources.list 파일

![image](https://user-images.githubusercontent.com/23617635/141925578-804c59c0-6217-4e03-a93c-d875eae7ecb5.png)



#### source.list에 적혀있는 정보
```shell
deb http://www.deb-multimedia.org buster main none-free

[deb or deb-src] [repository url] [distribution] [component]
```
- deb or deb-src : 바이너리 패키지 저장소(Binary Package Repositories)와 소스 패키지 저장소(Source Package Repositories) 중 어떤 저장소를 사용하는지를 의미

- repository url : 해당 저장소의 주소를 의미

- distribution : 릴리즈하는 리눅스 버전 이름을 의미

- component : 
    main(표준으로 제공되는 무료 오픈소스 소프트웨어),
    restricted(공식적으로 지원하는 사유(유료) 소프트웨어),
    universe(커뮤니티에 의해 유지되고 지원되는 오픈소스(무료) 소프트웨어),
    multiverse(공식적으로 지원되지 않는 사유(유료) 소프트웨어)를 의미



------------------------------------------------


# apt와 dpkg의 차이
>ubuntu, debian ..등 데비안 계열에서 사용
## dpkg
dpkg는 window환경에서는 exe 파일과 비슷한 설치파일이며,
deb 확장명으로 불린다.
이를 package라 부르며, dpkg로 파일을 설치할 때는, *.deb설치파일이 이미 작업환경에서 존재하고 있을때 아래와 같이 설치할 수 있다.

```shell
minhan$ dpkg --help

 설치
dpkg -i 패키지파일이름.deb

삭제
dpkg -r 패키지이름 : 패키지만 삭제
dpkg -P 패키지이름 : 설정파일까지 삭제

조회
dpkg -l 패키지이름 : 패키지를 간략히 조회
dpkg -L 패키지이름 : 패키지에 의해 소유된 파일까지 조회
dpkg --info 패키지이름.deb : 패키지 파일에 대한 정보를 보여줌
```
dpkg는 해당 패키지만 설치를 진행하고 해당 패키지에 종속되서 설치되야하는 프로그램을 같이 설치해주지는 않는다.
(A패키지가 설치되기 위해서 B패키지가 필요할경우, 해결이 까다로움)
이를 해결하기위하여 apt-get을 사용한다.


## apt-get
저장소의 url가 /etc/apt/sources.list 에 작성되어있다면 인터넷을 통해서 해당 저장소에서 파일을 다운로드해서 설치한다. 
이 방식은 dpkg와 달리 종속된 프로그램이 만약 작업환경에 미설치되어있다면 추가 수동설치 필요없이 자동으로 설치해준다.


```shell
# apt-get은 패키지 설치 담당
minhan$ apt-get --help

# 패키지 설치
apt-get install -f 패키지이름 
> -f는 --fix-broken 옵션으로, 의존성 관련 문제를 해결할때 사용

#새 패키지 목록 업데이트(sources.list 최신화)
apt-get update

# 패키지를 설치/업그레이드하여 시스템 업그레이드(sources.list 토대로 설치된 패키지 최신화)
apt-get upgrade

#패키지 삭제
apt-get remove/purge 패키지이름

#사용하지 않는 패키지 제거
apt-get autoremove

#내려받은 파일 제거
apt-get clean (or) apt-get autoclean


#  apt-cache는 패키지 검색을 담당
minhan$ apt-cache --help

#패키지 정보 보기
apt-cache show 패키지 이름

#패키지 의존성 확인
apt-cache depends 패키지이름

#패키지의 역의존성 확인
apt-cache rdepends 패키지이름
```

![이미지](https://media.vlpt.us/images/broccoliindb/post/a5515b0c-a521-4a64-8a87-ae8cd312d932/apt-get.png)



>**apt와 apt-get의 차이** (apt, apt-get은 데비안계열에서 사용)
APT는 Advanced Packaging Tool의 약자이다.
apt 와 apt-get는 내부적인 동작 차이는 거의 없기에, 뭘사용해도 상관이 없다.
다만, 좀 더 가독성이 좋고, 메세지 안내가 나오거나(apt), 
좀더 세부적인 옵션과 스크립트 작성을 할떄 유리한것(apt-get) 
차이정도 인지하고 선택하면 된다.
(나는 apt를 사용하고자 한다.)

> script 파일, 예를들어 Dockerfile 등에서 apt를 사용하면,
> ***WARNING: apt does not have a stable CLI interface. Use with caution in scripts.***
> 이라는 경고문구가 나온다.
> apt 명령어는 사용자와의 상호작용에 중점을 둔 CLI고, script내에서는 사용하기에는 주의가 필요하다는 것이므로, apt-get을 이용해서 사용하면 된다.
 


------------------------------------------

# rpm과 yum의 차이
>출처: https://wookoa.tistory.com/365 [Wookoa]

>centos, redhat, fedora ..등 레드헷 계열에서 사용

### RPM (Redhat Package Manager)
RPM이 나오기 전 초기의 리눅스에서는 모든 패키지를 설치할때 직접 컴파일한 후에 수동으로 설치해야 했다. 물론 컴파일은 위한 소스 파일은 tar, gzip으로 묶여있었다. 그렇기 때문에 패키지 설치에 많인 시간이 필요하게 되었고, 이를 극복하고자 RedHat 사에서 패키지 관리 프로그램인 RPM을 개발하게 되었다. 마치, Windows 계열의 setup.exe 파일과 비슷하게 자동으로 설치해주는 프로그램이다. RPM 패키지는 컴파일되어 설치한 실행파일, 설정파일, 라이브러리 등을 하나로 묶어놓은 파일을 말한다. 대표적인 특징은 아래와 같다.

1) YUM과 마찬가지로 패키지 인스톨 프로그램이다.
2) 인터넷이 연결되어있지 않아도 설치가 가능하지만, rpm 확장자로된 패키지 프로그램이 필요하다.
3) 패키지 사이에 의존하고 있는 패키지까지 자동으로 설치되지 않는다.
4) 기본 사용 명령어
- 설치: rpm -Uvh [패키지명]
- 삭제: rpm -e [패키지명]
- 설치확인: rpm -qa [패키지명]
- 상세정보: rpm -qi [패키지명]



### YUM (Yellodog Updater Modified)
YUM은 RPM 기반의 시스템을 위한 자동 업데이터 및 패키지 설치, 제거 프로그램이다. 듀크 대학교의 Linux@DUKE 프로젝트의 일부분으로 개발되었다. RPM과 다르게 인터넷 사용이 필수이기 때문에 패키지간 의존성 문제를 해결할 수 있다. 예를들어 A 패키지를 설치하기전에 필요한 B 패키지까지 모두 한번에 설치가 가능하며, 의존도를 자동으로 찾고 알아서 설치해준다. 대표적인 특직은 아래와 같다.

1) RPM과 마찬가지로 패키지 인스톨 프로그램이다.
2) 인터넷에서 바로 설치가 가능하기 때문에 패키지 프로그램이 따로 필요없다.
3) 패키지 사이에 의존하고 있는 패키지까지 자동으로 설치가 가능하다.
4) 기본 사용 명령어
- 설치: yum install [패키지명]
- 삭제: yum remove [패키지명]
- 업그레이드: yum update [패키지명]
- 목록: yum list [패키지명]
- yum 데이터베이스 동기화 업데이트: yum update


