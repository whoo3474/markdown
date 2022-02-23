> 출처 : https://tech.ssut.me/what-even-is-a-container/


------------------------------------------------
docker events
docker stats
docker system df

------------------------------------------------


# 컨테이너를 다룰때 주의할점
신뢰할수 있는 사용자만 컨테이너 런타임(e.g. Docker)을 실행할 수 있도록 제한한다.
`sudo usermod -aG docker <your-user>` 이거 금지

컨테이너의 프로세스가 root user로 실행하지 않도록 한다.
```dockerfile
RUN groupadd -r author && useradd -r -g author hoony
USER hoony
```
호스트의 디렉토리를 컨테이너가 직접 접근할 수 있도록 마운트 하지 않는다.


# 컨테이너 기술
## namespaces
VM에서는 각 게스트 머신별로 독립적인 공간을 제공하고 서로가 충돌하지 않도록 하는 기능을 갖고 있습니다. 리눅스에서는 이와 동일한 역할을 하는 namespaces 기능을 커널에 내장하고 있습니다. 글을 쓰는 시점을 기준으로 현재 리눅스 커널에서는 다음 6가 지 namespace를 지원하고 있습니다:

- mnt (파일시스템 마운트): 호스트 파일시스템에 구애받지 않고 독립적으로 파일시스템을 마운트하거나 언마운트 가능
- pid (프로세스): 독립적인 프로세스 공간을 할당
- net (네트워크): namespace간에 network 충돌 방지 (중복 포트 바인딩 등)
- ipc (SystemV IPC): 프로세스간의 독립적인 통신통로 할당
- uts (hostname): 독립적인 hostname 할당
- user (UID): 독립적인 사용자 할당
![image](https://user-images.githubusercontent.com/23617635/154421431-1236f521-d1e0-4b54-9412-731372af69ee.png)
![image](https://user-images.githubusercontent.com/23617635/154421522-da101164-e4d2-4ae1-b25d-78f4eab2ab89.png)
![image](https://user-images.githubusercontent.com/23617635/154421660-bbd355fd-01dd-42c8-8c34-fdc35ae04dac.png)

![image](https://user-images.githubusercontent.com/23617635/154421814-718974e0-9d6f-4cc9-86e9-ed2ae80878d4.png)

>https://blog.hyojun.me/9?category=972261

## cgroups (Control Groups)
cgroups(Control Groups)는 자원(resources)에 대한 제어를 가능하게 해주는 리눅스 커널의 기능입니다. cgroups는 다음 리소스를 제어할 수 있습니다:

- 메모리
- CPU
- I/O
- 네트워크
- device 노드(/dev/)


