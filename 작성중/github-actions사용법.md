>출처:
>https://docs.github.com/en/actions
>https://velog.io/@ggong/Github-Action%EC%97%90-%EB%8C%80%ED%95%9C-%EC%86%8C%EA%B0%9C%EC%99%80-%EC%82%AC%EC%9A%A9%EB%B2%95
>https://meetup.toast.com/posts/286
>https://velog.io/@adam2/GitHub-Actions-%EB%A1%9C-%ED%92%80%EB%A6%AC%ED%80%98%EC%8A%A4%ED%8A%B8-test-%EA%B2%80%EC%A6%9D%ED%95%98%EA%B8%B0

>https://docs.github.com/en/actions/learn-github-actions/contexts#job-status-check-functions

---------------------------------------------
# workflow 작성법


---
```shell

name: minhan

on:
  push:
      branches: [development]

jobs:
    build:
        name: build
        runs-on: ubuntu-latest
        defaults:
        run:
            shell: bash
            working-directory: ./mongodb

        env:
            working-directory: ./dir1

        strategy:
            #fail-fast는
            fail-fast: false
            #matrix는
            matrix:
                node-version: [14.x]
                #이런식으로도 사용 가능
                #python-version: [3.7]

        steps:
            #actions/checkout@v2
            #이제 각 스텝 별로 액션을 수행하게 되며 마찬가지로 name 항목은 액션에서 각 스텝 항목의 이름으로 표시되는 부분이다. uses로 사용할 액션(미리 만들어진 패키지)을 지정하여 main 브랜치를 체크아웃했다.
            - uses: actions/checkout@v2

            - name: Use Node.js ${{ matrix.node-version }}
              uses: actions/setup-node@v1
              #with는 액션에 옵션을 추가하는것
              with:
                node-version: ${{ matrix.node-version }}

            - name: yarn install
              run: yarn install
            
            - name: yarn build
              run : yarn build

            - name: dir1 directory
              run : |
                ls -al
              working-directory: ${{ env.working-directory }}


```


## (name) : / -> name
선택사항으로 적는다.
workflow의 이름을 지정하며, 지정한 이름은 해당 레포지토리의 actions 페이지에 표현된다.
name을 지정하지 않으면 workflow 파일이 저장된 경로 이름으로 자동 지정된다.
```shell
name: minhan
```

-----------------------------------
## (on) : / -> on
필수 필드이다.
workflow를 수행하는 이벤트를 지정해야한다.
wiki를 생성하거나, fork를 하거나, issues를 작성, 라벨추가, 풀리퀘를 날리거나, 풀리퀘에 리뷰를 달거나, 다른 workflow가 실행되거나
해당 레포지토리에서 github로 할수있는 대부분은 이벤트 트리거로 받을수 있다고 생각하고 찾아 쓰면 된다.

>https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows

### 단일 이벤트 트리거

```shell
# 해석: 아무 브런치에서 push하면 작동
on: push
```

### 복수 이벤트 트리거
- check_run : workflow 실행
- deployment: 배포 이벤트가 발생할 때
- issue_comment: 이슈에 코멘트를 남길 때
- pull_request: 풀리퀘 관련 이벤트
- pull_request_review_comment
- push
- schdule: POSIX crone 구문을 사용해 특정 시간에 workflow가 실행되도록 예약
- repository_dispatch: 외부 이벤트가 발생한 후 workflow를 트리거하기위해서 사용

```shell
# 해석: 아무 브런치에서 push 또는 pull_request 하면 작동
on: [push, pull_request, issue_comment]
```

### 복합 이벤트 트리거
branches / branches-ignore 또는 tags / tags-ignore 는 같이쓸수 없다.
그 대신 느낌표를 사용할수있다.
```shell
on:
  push:
    branches:
      - main
    branches-ignore:
      - master
    # 잘못된 branches 나누기
    tags:
      - v1
    tags-ignore:
      - v1.*
    # 잘못된 tags 나누기

  pull_request:
    branches:
      - main
  # 브런치가 main일때만 pull_request 이벤트를 받는다.
```

```shell
on:
  push:
    branches:
      - 'releases/**'
      - '!releases/**-alpha'

```

### 파일경로 이벤트 트리거
>[on.<push|pull_request>.paths](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onpushpull_requestpaths)

업데이트된 파일 경로를 이벤트 트리거로서 지정할 수도 있다. 
아래의 예시는 docs/ 폴더 아래의 파일들에 대해서 작업을하고 push를 하면 workflow를 수행하지 않는다. 
docs/ 폴더 밖에서 적어도 하나의 파일이 수정되었을때 push 했을 경우 workflow가 수행된다.
```shell
# 경로 무시
on:
  push:
    paths-ignore:
      - 'docs/**'
```
```shell
# 경로 포함
on:
  push:
    paths:
      - '**.js'
```



### (schedule) : / -> on -> schedule
POSIX cron 구문을 사용하여 특정 UTC 시간에 실행되도록 워크플로를 예약할 수 있습니다.
예약된 워크플로는 기본 또는 기본 분기의 최신 커밋에서 실행됩니다. 
예약된 워크플로를 실행할 수 있는 가장 짧은 간격은 5분에 한 번입니다.

```shell
# 매일 5시30분, 17시30분에 workflow를 트리거한다
on:
  schedule:
    - cron:  '30 5,17 * * *'
```

```shell
#Cron 구문에는 공백으로 구분된 5개의 필드가 있으며 각 필드는 시간 단위를 나타냅니다.
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of the month (1 - 31)
│ │ │ ┌───────────── month (1 - 12 or JAN-DEC)
│ │ │ │ ┌───────────── day of the week (0 - 6 or SUN-SAT)
│ │ │ │ │
│ │ │ │ │
│ │ │ │ │
* * * * *
```


|기호|	설명|	예시|
|----|----|----|
|*|	모든 값	|`* * * * *` 매일 1분마다 실행됩니다.|
|,|	값 목록 구분 기호|`	2,10 4,5 * * *` 매일 4시와 5시 2분과 10분에 실행됩니다.|
|-|	값 범위	|`0 4-6 * * *` 4시, 5시, 6시 0분에 실행됩니다.|
|/|	단계 값|`20/15 * * * *` 20분부터 59분까지(20분, 35분, 50분) 15분마다 실행됩니다.|

----------------------------------------

## (jobs) : / -> jobs
워크플로 파일 에서 실행되는 모든 작업을 함께 그룹화 합니다.
```shell
# job이름을 build-job으로 하고, runner(가상서버)의 os를 ubuntu-latest(최신)로 설정
jobs:
  build-job:
    runs-on: ubuntu-latest

  install-job:
    runs-on: ubuntu-latest

```

### 의존적인 작업 구성
기본적으로 작업은 병렬적으로 수행된다. 다른 작업이 완전히 끝난 후에 작업을 실행시키고 싶다면 needs 키워드를 통해 작업이 의존성을 갖도록 지정하면 된다.
```shell
jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - run: ./setup_server.sh
  build:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - run: ./build_server.sh
  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: ./test_server.sh
```



### (matrix) : / -> jobs -> {job} -> strategy -> matrix
빌드 매트릭스 활용하기
워크 플로우가 다양한 OS, 플랫폼, 언어의 여러 조합에서 테스트를 실행하려는 경우 빌드 매트릭스를 활용하면 된다. 빌드 옵션을 배열로 받는 strategy 키워드를 사용하면 된다.

```shell
jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        # 다양한 버전의 Ubuntu, Node.js를 이용하여 작업을 여러번 실행
	    	os: [ubuntu-16.04, ubuntu-18.04]
        node: [6, 8, 10]
    steps:
      - uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node }}
```

### 종속성 캐싱
GIthub의 러너는 각 작업에서 새로운 환경으로 실행되므로 작업들이 종속성을 재사용하는 경우 파일들을 캐싱하여 성능을 높이면 된다. 캐시를 생성하면 해당 저장소의 모든 워크 플로우에서 사용 가능하다.
```shell
jobs:
  example-job:
    steps:
      - name: Cache node modules
        uses: actions/cache@v2
        env:
          cache-name: cache-node-modules
        with:
          # `~/.npm` 디렉토리를 캐시해 성능을 높인다
          path: ~/.npm
          key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-build-${{ env.cache-name }}-
```

------------------------------------


## (default) : / -> jobs -> {job} -> defaults

## (전역적인 working-directory) : / -> jobs -> {job} -> defaults -> run -> working-directory

```shell
# runner의 shell을 변경하고, 명령어를 진행하는 폴더를 변경한다.
defaults:
  run:
    shell: bash
    working-directory: ./mongodb
```

> 개별 작업 폴더를 지정하는 방법도 있다
```shell
    - name: dir1 directory
        run : |
          ls -al
        working-directory: ./mongodb
```

------------------------------------


## (전역적인 env) : / -> env
## (jobs env) : / -> jobs -> {job} -> env
>https://docs.github.com/en/actions/learn-github-actions/environment-variables#default-environment-variables

```
env:
AZURE_WEBAPP_NAME: webAppMainPage-fora22
AZURE_WEBAPP_PACKAGE_PATH: './build/'     
NODE_VERSION: '14.0'  
```

### Default env
기본적으로 변수로 사용할수있는 변수들이 정의되어있다

------------------------------------

## (container) : / -> jobs -> {job} -> container

------------------------------------

## (services) : / -> jobs -> {job} -> services

------------------------------------

## (step) : / -> jobs -> {job} -> steps -> {step}

------------------------------------

# (if) :  / -> jobs -> {job} -> steps -> {step} -> if

------------------------------------

### (지역적인 env) : / -> jobs -> {job} -> steps -> {step} -> env

------------------------------------

### (지역적인 working-directory) : / -> jobs -> {job} -> steps -> {step} -> working-directory

```shell
    - name: dir1 directory
        run : |
          ls -al
        working-directory: ./mongodb
```

------------------------------------

## (Actions) : / -> jobs -> {job} -> steps -> uses
액션은 남들이 만들어놓은 패키지같은 개념으로 받아들여진다.
예를들어, workout의 runner에서 github 레포지토리로 checkout하고 싶다면 **actions/checkout@v2** 을 사용하면 된다.

```shell
- uses: actions/checkout@v2
```
이 액션은 GitHub Actions의 workflow가 레포지토리에 접근 할 수 있도록 $GITHUB_WORKSPACE에 레포지토리를 복사해 주는 일을 해준다. 예를 들어, /home/runner/work/my-repo-name/my-repo-name 경로로 레포지토리를 복사해준다.

## setup-node action
```shell
jobs:
  example-job:
    runs-on: unbuntu-latest
    steps:
    # steps의 

```


--------------------------------------

# secrets
워크 플로우가 비밀번호나 인증서 같은 민감한 정보를 사용한다면 Github에 secret으로 저장하여 환경 변수로 사용 가능하다.
```shell
jobs:
  example-job:
    runs-on: unbuntu-latest
    steps:
      - name: Retrieve secret
        # 환경변수로 저장하고
        env:
          super_secret: ${{ secrets.SUPERSECRET }}
        # 저장한 환경변수를 활용한다.
        run: |
          example-command "$super_secret"
```


-------------------------------------

# Context
> https://docs.github.com/en/actions/learn-github-actions/contexts