> 출처 : https://tech.ssut.me/what-even-is-a-container/


------------------------------------------------
docker events
docker stats
docker system df

------------------------------------------------
# 컨테이너 기술
## namespaces
VM에서는 각 게스트 머신별로 독립적인 공간을 제공하고 서로가 충돌하지 않도록 하는 기능을 갖고 있습니다. 리눅스에서는 이와 동일한 역할을 하는 namespaces 기능을 커널에 내장하고 있습니다. 글을 쓰는 시점을 기준으로 현재 리눅스 커널에서는 다음 6가 지 namespace를 지원하고 있습니다:

- mnt (파일시스템 마운트): 호스트 파일시스템에 구애받지 않고 독립적으로 파일시스템을 마운트하거나 언마운트 가능
- pid (프로세스): 독립적인 프로세스 공간을 할당
- net (네트워크): namespace간에 network 충돌 방지 (중복 포트 바인딩 등)
- ipc (SystemV IPC): 프로세스간의 독립적인 통신통로 할당
- uts (hostname): 독립적인 hostname 할당
- user (UID): 독립적인 사용자 할당


## cgroups (Control Groups)
cgroups(Control Groups)는 자원(resources)에 대한 제어를 가능하게 해주는 리눅스 커널의 기능입니다. cgroups는 다음 리소스를 제어할 수 있습니다:

- 메모리
- CPU
- I/O
- 네트워크
- device 노드(/dev/)


