> 출처 : https://tech.ssut.me/what-even-is-a-container/


------------------------------------------------
docker events
docker stats
docker system df

------------------------------------------------
# 도커 멀티스테이지 nextjs 도움 많이됌
https://www.nicovak.com/posts/nextjs-docker-multistage
https://www.koyeb.com/tutorials/how-to-dockerize-and-deploy-a-next-js-application-on-koyeb
https://dev.to/techworld_with_nana/top-8-docker-best-practices-for-using-docker-in-production-1m39
------------------------------------------------
>출처 : https://medium.com/banksalad/docker-best-practices-8b4f28ab3a65
# 도커 파일 빌드할때 주의점
## exec form
```
# shell form
CMD yarn start

# exec form
CMD ["node", "server.js"]
```
shell form의 경우 command shell을 호출하기 때문에 위의 커맨드는 /bin/sh -c node server.js 로 실행되게 됩니다. 
이 경우 SIGTERM같은 Signal들이 executable (위의 예시에서는 node)로 전달되지 않을 수 있기 때문에, Graceful Shutdown을 코드 레벨에서 처리했다 하더라도 실제로 작동하지 않을 수 있습니다. 
따라서 exec form 사용이 권장됩니다.

## latest 태그 사용을 지양하기
위에서 언급한 것처럼 Base Docker Image의 latest태그의 의미 자체가 빌드되는 시점의 최신 버전이기 때문에 빌드 시점마다 다른 내용을 가리킬 수 있습니다. 예를 들면 이 글을 작성하는 시점의 python:latest는 3.7.3버전을 가리키지만, 새로운 버전이 릴리즈되면 latest 태그는 새롭게 릴리즈된 버전을 가리키게 됩니다.

그리고 운영의 관점에서 보면 정확하게 어떤 버전의 이미지가 실행되고 있는지를 바로 확인하기 어렵고, Kubernetes를 사용하는 경우 latest 태그를 사용했을 때 imagePullPolicy 설정값에 따라 새로운 이미지를 못 불러오거나, 이미지를 노드에 Cache 하지 않아 매번 Registry에서 불러오는 비효율을 야기할 수 있습니다.

따라서 latest 태그 사용을 최대한 지양하는 게 좋습니다.

## dependency 버전을 명시하기
위에서 언급한 상황뿐만 아니라 Dependency Hell을 피하기 위해 빌드, 실행을 위한 Dependency의 버전을 명시하는 게 중요합니다. yarn.lock, Pipfile.lock과 같은 Lockfile을 활용하면 더 좋겠죠?


> 출처 : https://cloud.google.com/architecture/best-practices-for-building-containers?hl=ko#signal-handling
## Docker 빌드 캐시 최적화

Docker 빌드 캐시는 컨테이너 이미지 빌드를 가속화할 수 있습니다. 이미지는 레이어별로 빌드되며 Dockerfile에서는 각 명령이 결과 이미지에 레이어를 만듭니다. 가능하면 빌드 중에 Docker는 이전 빌드의 레이어를 다시 사용하고 잠재적으로 비용이 많이 드는 단계를 건너뜁니다. Docker는 모든 이전 빌드 단계에서 사용된 경우에만 빌드 캐시를 사용할 수 있습니다. 이 동작은 일반적으로 빌드가 빨라지는 장점이 있지만 몇 가지 경우를 고려해야 합니다.

예를 들어 Docker 빌드 캐시를 최대한 활용하려면 Dockerfile 맨 아래에 자주 변경되는 빌드 단계를 배치해야 합니다. Docker를 맨 위에 배치하면 Docker는 자주 변경되지 않는 다른 빌드 단계에 대해 빌드 캐시를 사용할 수 없습니다. 새 Docker 이미지는 대개 새 버전의 소스 코드마다 작성되므로 Dockerfile에서 소스 코드를 가능한 한 늦게 이미지에 추가합니다. 다음 다이어그램에서 STEP 1을 변경하면 Docker는 FROM debian:9 단계의 레이어만 다시 사용한다는 것을 알 수 있습니다. 하지만 STEP 3를 변경하면 Docker는 STEP 1 및 STEP 2의 레이어를 다시 사용할 수 있습니다.


레이어 재사용은 또 다른 결과를 초래합니다. 빌드 단계가 로컬 파일 시스템에 저장된 모든 종류의 캐시에 의존하는 경우 이 캐시는 동일한 빌드 단계에서 생성되어야 합니다. 이 캐시가 생성되지 않으면 빌드 단계가 이전 빌드에서 나온 오래된 캐시로 실행될 수 있습니다. 이러한 동작을 가장 일반적으로 볼 수 있는 경우는 apt 또는 yum:과 같은 패키지 관리자입니다. 패키지 설치와 동일한 RUN 명령어로 저장소를 업데이트해야 합니다.

다음 Dockerfile에서 두 번째 RUN 단계를 변경하면 apt-get update 명령어가 다시 실행되지 않고 이전의 apt 캐시가 남게 됩니다.

![image](https://cloud.google.com/architecture/images/bp-containers-optimization.svg?hl=ko)

```Dockerfile
FROM debian:9

RUN apt-get update
RUN apt-get install -y nginx
```
그 대신, 하나의 RUN 단계에서 2개의 명령을 병합하세요.

```Dockerfile
FROM debian:9

RUN apt-get update && \
    apt-get install -y nginx
```

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


