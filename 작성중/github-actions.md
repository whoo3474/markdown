

# githubactions에서 dockerfile build 할떄 주의사항

github actions 에서의 step 작업은 항상 루트 파일로 이동한다.

따라서 각각의 스텝마다
작업을 할 공간으로 이동하거나, working-directory를 지정해주어야 한다.

```yaml
name: React CICD

on:
  push:
    branches: ["develop"]

jobs:
  working-directory-test:
    runs-on: ubuntu-18.04
    env:
      working-directory: ./packages/app
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: 'Create env file111'
        run: |
          pwd
          ls -al
          cd ./packages/app
          touch .env.production
          echo REACT_APP_LOCAL111=${{ secrets.REACT_APP_LOCAL }} >> .env.production
          cat .env.production

      - name: 'Create env file222'
        run: |
          pwd
          ls -al
          cd ./packages/app
          echo REACT_APP_LOCAL222=${{ secrets.REACT_APP_LOCAL }} >> .env.production
          cat .env.production

      - name: 'Create env file333'
        working-directory: /packages/app
        run: |
          pwd
          ls -al
          echo REACT_APP_LOCAL333=${{ secrets.REACT_APP_LOCAL }} >> .env.production
          cat .env.production
          
```

설정을 해석해보자면,
develop 브런치에서 push이벤트가 발생하면,
ubuntu-18.04 환경에서 github actions 가 작동하게 됩니다.
env파일로 미리 working-directory 를 선언해줍니다.
Checkout actions를 사용하여 나의 레포지토리를 가져와 줍니다.

첫번째 step에서 
현재 작업 위치를 확인하고 ./packages/app 폴더로 이동합니다.
.env.production 파일을 생성하여 github actions secrets의 변수를 입력해 주고,
작업 위치의 폴더와 파일들을 확인후, .env.productnio을 읽어줍니다.

```yaml
- name: 'Create env file111'
  run: |
    pwd
    cd ./packages/app
    touch .env.production
    echo REACT_APP_LOCAL111=${{ secrets.REACT_APP_LOCAL }} >> .env.production
    pwd
    ls -al
    cat .env.production
```

아래가 작업 내용입니다.
```
/home/runner/work/MY_TEST_REPO/MY_TEST_REPO
/home/runner/work/MY_TEST_REPO/MY_TEST_REPO/packages/app
total 40
drwxr-xr-x 4 runner docker 4096 Aug 26 03:58 .
drwxr-xr-x 4 runner docker 4096 Aug 26 03:58 ..
-rw-r--r-- 1 runner docker   42 Aug 26 03:58 .env.production
-rw-r--r-- 1 runner docker  271 Aug 26 03:58 .gitignore
-rw-r--r-- 1 runner docker 1807 Aug 26 03:58 craco.config.ts
-rw-r--r-- 1 runner docker 2025 Aug 26 03:58 package.json
drwxr-xr-x 2 runner docker 4096 Aug 26 03:58 public
drwxr-xr-x 5 runner docker 4096 Aug 26 03:58 src
-rw-r--r-- 1 runner docker  544 Aug 26 03:58 tsconfig.json
-rw-r--r-- 1 runner docker  249 Aug 26 03:58 tsconfig.paths.json
REACT_APP_LOCAL111=***
```

두번째도 같은 작업이되, 변수명만 다르게 하여 .env.production에 넣어줍니다.
```yaml
- name: 'Create env file222'
  run: |
    pwd
    cd ./packages/app
    touch .env.production
    echo REACT_APP_LOCAL222=${{ secrets.REACT_APP_LOCAL }} >> .env.production
    pwd
    ls -al
    cat .env.production
```

```
/home/runner/work/MY_TEST_REPO/MY_TEST_REPO
/home/runner/work/MY_TEST_REPO/MY_TEST_REPO/packages/app
total 40
drwxr-xr-x 4 runner docker 4096 Aug 26 03:58 .
drwxr-xr-x 4 runner docker 4096 Aug 26 03:58 ..
-rw-r--r-- 1 runner docker   84 Aug 26 03:58 .env.production
-rw-r--r-- 1 runner docker  271 Aug 26 03:58 .gitignore
-rw-r--r-- 1 runner docker 1807 Aug 26 03:58 craco.config.ts
-rw-r--r-- 1 runner docker 2025 Aug 26 03:58 package.json
drwxr-xr-x 2 runner docker 4096 Aug 26 03:58 public
drwxr-xr-x 5 runner docker 4096 Aug 26 03:58 src
-rw-r--r-- 1 runner docker  544 Aug 26 03:58 tsconfig.json
-rw-r--r-- 1 runner docker  249 Aug 26 03:58 tsconfig.paths.json
REACT_APP_LOCAL111=***
REACT_APP_LOCAL222=***
```

분명히 첫번째 step에서 cd ./packages/app 으로 폴더를 이동하였음에도 불구하고, 
또 다시 이동하여서 작업을 해야 하는것 을 알수있습니다.


깜빡 할수도있고, 생각처럼 작동하지 않아서 이게 왜 이러지 싶을수가 있습니다.
그럴땐 진작에 *working-directory*를 설정해주면 마음이 편해집니다.

```yaml
- name: 'Create env file333'
  working-directory: ${{ env.working-directory }}
  run: |
    echo  env.working-directory=${{ env.working-directory }}
    pwd
    ls -al
    echo REACT_APP_LOCAL333=${{ secrets.REACT_APP_LOCAL }} >> .env.production
    cat .env.production
```

```
env.working-directory=./packages/app
/home/runner/work/MY_TEST_REPO/MY_TEST_REPO/packages/app
total 40
drwxr-xr-x 4 runner docker 4096 Aug 26 03:58 .
drwxr-xr-x 4 runner docker 4096 Aug 26 03:58 ..
-rw-r--r-- 1 runner docker   84 Aug 26 03:58 .env.production
-rw-r--r-- 1 runner docker  271 Aug 26 03:58 .gitignore
-rw-r--r-- 1 runner docker 1807 Aug 26 03:58 craco.config.ts
-rw-r--r-- 1 runner docker 2025 Aug 26 03:58 package.json
drwxr-xr-x 2 runner docker 4096 Aug 26 03:58 public
drwxr-xr-x 5 runner docker 4096 Aug 26 03:58 src
-rw-r--r-- 1 runner docker  544 Aug 26 03:58 tsconfig.json
-rw-r--r-- 1 runner docker  249 Aug 26 03:58 tsconfig.paths.json
REACT_APP_LOCAL111=***
REACT_APP_LOCAL222=***
REACT_APP_LOCAL333=***
```

-------------------------------------


step마다 working-directory를 설정하는것이 불편하다면
default로 전체로 설정해주거나
```yaml
name: Node.js CI

on:
  push:
    branches: [ frontend-develop ]

defaults:
  run:
    working-directory: ./app

jobs:
  working-directory-test:
    defaults:
      run:
        working-directory: ./app
    steps:
      - uses: actions/checkout@v1
```

각각의 job에 설정해줄수 있습니다.
```yaml
name: CI

on: [push]

jobs:
  working-directory-test:
    defaults:
      run:
        working-directory: ./app
    steps:
      - uses: actions/checkout@v1

```