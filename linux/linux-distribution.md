> 공식 문서와 블로그, 검색 등을 통해 제가 원하는 자료를 만들고있습니다.
> 되도록 이미지에대한 정확한 출처를 밝히고자 하지만,
> 책이나 PDF를 인용하여 사용한듯한 곳에서는 정확한 출처를 찾을수가 없었습니다.

# 리눅스의 종류?
![image](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Linux_Distribution_Timeline.svg/298px-Linux_Distribution_Timeline.svg.png)
>https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%BC:Linux_Distribution_Timeline.svg

보시다싶이 리눅스의 종류는 엄~청 많다.(사진:2021년 6월23일 기준)

## 배포판 버전 확인
 
 > 출처:   https://zetawiki.com/

```shell
grep . /etc/*-release
```

```shell
cat /etc/*-release | uniq
```

### 실행예시 (Ubuntu 18.04.1)
```shell
minhan:~# grep . /etc/*-release
/etc/lsb-release:DISTRIB_ID=Ubuntu
/etc/lsb-release:DISTRIB_RELEASE=18.04
/etc/lsb-release:DISTRIB_CODENAME=bionic
/etc/lsb-release:DISTRIB_DESCRIPTION="Ubuntu 18.04.1 LTS"
/etc/os-release:NAME="Ubuntu"
/etc/os-release:VERSION="18.04.1 LTS (Bionic Beaver)"
/etc/os-release:ID=ubuntu
/etc/os-release:ID_LIKE=debian
/etc/os-release:PRETTY_NAME="Ubuntu 18.04.1 LTS"
/etc/os-release:VERSION_ID="18.04"
/etc/os-release:HOME_URL="https://www.ubuntu.com/"
/etc/os-release:SUPPORT_URL="https://help.ubuntu.com/"
/etc/os-release:BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
/etc/os-release:PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
/etc/os-release:VERSION_CODENAME=bionic
/etc/os-release:UBUNTU_CODENAME=bionic
```


### 실행예시 (CentOS 6.5)
```shell
[minhan]# grep . /etc/*-release
/etc/centos-release:CentOS release 6.5 (Final)
/etc/lsb-release:LSB_VERSION=base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch:graphics-4.0-amd64:graphics-4.0-noarch:printing-4.0-amd64:printing-4.0-noarch
/etc/redhat-release:CentOS release 6.5 (Final)
/etc/system-release:CentOS release 6.5 (Final)

```


### 실행예시 (Fedora 11 Leonidas)
```shell
[minhan]# cat /etc/*-release | uniq
Fedora release 11 (Leonidas)

```

### 실행예시 (Oracle Enterprise Linux Server 5.5)
```shell
[rminhan]# cat /etc/*-release | uniq
Enterprise Linux Enterprise Linux Server release 5.5 (Carthage)
Red Hat Enterprise Linux Server release 5.5 (Tikanga)

```

### 실행예시 (SUSE Linux Enterprise Server 11)
```shell
minhan:~ # cat /etc/*-release | uniq
SUSE Linux Enterprise Server 11 (x86_64)
VERSION = 11
PATCHLEVEL = 1
LSB_VERSION="core-2.0-noarch:core-3.2-noarch:core-4.0-noarch:core-2.0-x86_64:core-3.2-x86_64:core-4.0-x86_64"
```

---------------------------------------------------------------------

## 데비안 계열
- 데비안에서 파생된 리눅스 계열을 지칭한다.
  

Konoppix
Corel
Lindows
Ubuntu
Linux Mint
Elementary OS
BackTrack
Kali LInux 등

> Ubuntu계열도 따로 존재한다

# 레드헷계열
- 레드햇에서 파생된 리눅스 계열
- 널리 사용되고 있음(주로 서버용)
- 리눅스 rpm과 yum 사용

Red Hat Enterprise Linux
Fedora
CentOS
AnNyung Linux: 안녕 리눅스. 정확히는 AnNyung LInux 2(CentOS 6 기반)와 AnNyung LInux 3 (CentOS 7 기반).
Oracle Linux 등
