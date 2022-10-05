>https://jwcheong0420.github.io/2020/02/13/using-sysctls-in-k8s/

sysctl : 커널의 변수 값을 제어하여 시스템 최적화를 할수있는 명령
시스템의 /proc/sys 디렉토리 밑에 있는 커널 매개변수를 제어한다
이 디렉토리 밑으로 존재하는 디렉토리를 구분하는 '/'는 sysctl 명령 이용시에는 '.'으로 대체한다

```
/proc/sys/net/ipv4/icmp_echo_ignore_all -> net.ipv4.icmp_echo_ignore_all
```

커널 매개변수 변경시 vi, echo, sysctl 을 사용해도 재부팅하면 값이 초기화됌
재부팅하여도 값을 유지하려면 /etc/sysctl.conf 에 등록하거나
/etc/rc.d/rc.local 에 등록하면된다.


/proc/sys/net/ipv4/icmp_echo_ignore_all 값 출력
```
sysctl -n net.ipv4.icmp_echo_ignore_all
```

/proc/sys/net/ipv4/icmp_echo_ignore_all 값 0으로 설정
```
sysctl -w net.ipv4.icmp_echo_ignore_all=0
```

/etc/sysctl.conf 를 비롯하여 커널 매개 변수와 연관된 환경 설정 파일을 읽어 들여서 적용
```
sysctl --system
```