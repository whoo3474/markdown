

# docker commit
```shell
$docker commit --help
Usage:  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
Create a new image from a container is changes
Options:
  -a, --author string    작성자 (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -c, --change list      생성된 이미지에 Dockerfile 적용
  -m, --message string   Commit 메세지(주석
  -p, --pause            컨테이너 일시 중지 (default true)
```

# docker images
```shell
$docker images --help

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]
List images
Options:
  -a, --all             모든 이미지를 표시합니다.
  -f, --filter filter   출력 결과 필터를 설정합니다.
      --format string   주어진 템플릿 형식을 사용하여 출력
      --no-trunc        내용을 생략하지 않습니다
  -q, --quiet           이미지 ID만 출력합니다.
```

# docker tag
```shell
$docker tag --help

Usage:  docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```

# docker pull
```shell
Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Pull an image or a repository from a registry

Options:
  -a, --all-tags                저장소에서 태그가 있는 모든 이미지 다운로드
      --disable-content-trust   이미지 확인 건너뛰기 (default true)
      --platform string         서버가 다중 플랫폼 지원이 가능한 경우 플랫폼 설정
  -q, --quiet                   자세한 출력x
```

# docker rmi
```
$docker rmi --help

Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Options:
  -f, --force      강제로 이미지 삭제
      --no-prune   태그 없는 부모는 삭제 제외
```

# docker push
```shell
$docker push --help

Usage:  docker push [OPTIONS] NAME[:TAG]

Push an image or a repository to a registry

Options:
  -a, --all-tags                태그가 지정된 모든 이미지를 저장소에 푸시
      --disable-content-trust   이미지 서명 건너뛰기. (default true)
  -q, --quiet                   자세한 출력 x
```

# docker login

```shell
$docker login --help

Log in to a Docker registry or cloud backend.
If no registry server is specified, the default is defined by the daemon.

Usage:
  docker login [OPTIONS] [SERVER] [flags]
  docker login [command]

Available Commands:
  azure       Log in to azure

Flags:
  -h, --help              로그인 도움말
  -p, --password string   패스워드
      --password-stdin    STDIN에서 암호 가져오기
  -u, --username string   유저이름

Use "docker login [command] --help" for more information about a command.
```

# docker run

```shell
docker run -it -d -e AUTHOR=minhan --name minhan_container --restart=always ubuntu sleep infinity

-i : interactive, 컨테이너의 입력 및 출력 등 상호작용하겠다는 뜻
-t : pseudo-tty 로 터미널과같은 환경을 사용하겠다는 뜻
-d : detached모드이며, background 실행
-e : 컨테이너 환경변수 설정
--name : 컨테이너 이름 설정

-w : 컨테이너 시작 디렉토리 설정
-v : 호스트와 공유할 데이터 볼륨 설정
--net : 컨테이너의 네트워크 모드 설정

--restart
컨테이너 종료 시 적용할 재시작 옵션
no :container 재시작하지 않음(default)
on-faulure: 컨테이너가 exit code가 0이 아닐때만 재시작 (최대 시도횟수 설정)
always : exit code와 상관없이 container 항상 재시작
unless-stopped: 컨테이너가 정지 상태가 아니라면 항상 재시작한다.
```