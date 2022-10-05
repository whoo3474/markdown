Dockerfile build를 하다가 RUN으로 폴더 이동하다가 원하는데로 작동을 하지 않을때

아래는 무슨짓을 써서라도 packages/app 폴더로 가서 작업을 하려고 한 의지가 보이는 Dockerfile이다.

```dockerfile
FROM node:16.13.2-alpine3.15
WORKDIR /usr/src/app
COPY . .
RUN pwd
RUN ls -al
RUN cd packages/app
RUN pwd
RUN ls -al
RUN cd packages/app && ls -al && pwd
RUN pwd

...

```

아래는 그 결과이다.

```
Step 1/13 : FROM node:16.13.2-alpine3.15
16.13.2-alpine3.15: Pulling from library/node
Status: Downloaded newer image for node:16.13.2-alpine3.15
---> 23990429c0d7
Step 2/13 : WORKDIR /usr/src/app
---> Running in 4d81cdd6b483
Removing intermediate container 4d81cdd6b483
---> b36000ab2520
Step 3/13 : COPY . .
---> aef92719b16b
Step 4/13 : RUN pwd
---> Running in 64e6ff194ca3
/usr/src/app
Removing intermediate container 64e6ff194ca3
---> 772dc46fd8ee
Step 5/13 : RUN ls -al
---> Running in 130d94584f87
total 2996
drwxr-xr-x    1 root     root          4096 Aug 26 04:29 .
drwxr-xr-x    1 root     root          4096 Aug 26 04:29 ..
-rw-r--r--    1 root     root           134 Aug 26 04:29 .editorconfig
-rw-r--r--    1 root     root          3307 Aug 26 04:29 .eslintrc.json
drwxr-xr-x    8 root     root          4096 Aug 26 04:29 .git
drwxr-xr-x    3 root     root          4096 Aug 26 04:29 .github
-rw-r--r--    1 root     root           338 Aug 26 04:29 .gitignore
drwxr-xr-x    2 root     root          4096 Aug 26 04:29 .husky
-rw-r--r--    1 root     root             7 Aug 26 04:29 .nvmrc
-rwxr-xr-x    1 root     root       2188908 Aug 26 04:29 .pnp.cjs
-rw-r--r--    1 root     root          8667 Aug 26 04:29 .pnp.loader.mjs
-rw-r--r--    1 root     root           106 Aug 26 04:29 .prettierrc
drwxr-xr-x    2 root     root          4096 Aug 26 04:29 .vscode
drwxr-xr-x    6 root     root          4096 Aug 26 04:29 .yarn
-rw-r--r--    1 root     root           248 Aug 26 04:29 .yarnrc.yml
-rw-r--r--    1 root     root          4331 Aug 26 04:29 CONVENTION.md
-rw-r--r--    1 root     root           237 Aug 26 04:29 Dockerfile
-rw-r--r--    1 root     root           619 Aug 26 04:29 README.md
-rw-r--r--    1 root     root          2301 Aug 26 04:29 package.json
drwxr-xr-x    4 root     root          4096 Aug 26 04:29 packages
-rw-r--r--    1 root     root           129 Aug 26 04:29 tsconfig.json
-rw-r--r--    1 root     root        777389 Aug 26 04:29 yarn.lock
Removing intermediate container 130d94584f87
---> 4c29166b23c7
Step 6/13 : RUN cd packages/app
---> Running in fcf4799d3543
Removing intermediate container fcf4799d3543
---> 8d5725e4f5ad
Step 7/13 : RUN pwd
---> Running in a***a37ebb679
/usr/src/app
Removing intermediate container a***a37ebb679
---> 94d29ee3188f
Step 8/13 : RUN ls -al
---> Running in acce4f1e6dfd
total 2996
drwxr-xr-x    1 root     root          4096 Aug 26 04:29 .
drwxr-xr-x    1 root     root          4096 Aug 26 04:29 ..
-rw-r--r--    1 root     root           134 Aug 26 04:29 .editorconfig
-rw-r--r--    1 root     root          3307 Aug 26 04:29 .eslintrc.json
drwxr-xr-x    8 root     root          4096 Aug 26 04:29 .git
drwxr-xr-x    3 root     root          4096 Aug 26 04:29 .github
-rw-r--r--    1 root     root           338 Aug 26 04:29 .gitignore
drwxr-xr-x    2 root     root          4096 Aug 26 04:29 .husky
-rw-r--r--    1 root     root             7 Aug 26 04:29 .nvmrc
-rwxr-xr-x    1 root     root       2188908 Aug 26 04:29 .pnp.cjs
-rw-r--r--    1 root     root          8667 Aug 26 04:29 .pnp.loader.mjs
-rw-r--r--    1 root     root           106 Aug 26 04:29 .prettierrc
drwxr-xr-x    2 root     root          4096 Aug 26 04:29 .vscode
drwxr-xr-x    6 root     root          4096 Aug 26 04:29 .yarn
-rw-r--r--    1 root     root           248 Aug 26 04:29 .yarnrc.yml
-rw-r--r--    1 root     root          4331 Aug 26 04:29 CONVENTION.md
-rw-r--r--    1 root     root           237 Aug 26 04:29 Dockerfile
-rw-r--r--    1 root     root           619 Aug 26 04:29 README.md
-rw-r--r--    1 root     root          2301 Aug 26 04:29 package.json
drwxr-xr-x    4 root     root          4096 Aug 26 04:29 packages
-rw-r--r--    1 root     root           129 Aug 26 04:29 tsconfig.json
-rw-r--r--    1 root     root        777389 Aug 26 04:29 yarn.lock
Removing intermediate container acce4f1e6dfd
---> b484501a2ae5
Step 9/13 : RUN cd packages/app && ls -al && pwd
---> Running in dc67785803aa
total 40
drwxr-xr-x    4 root     root          4096 Aug 26 04:29 .
drwxr-xr-x    4 root     root          4096 Aug 26 04:29 ..
-rw-r--r--    1 root     root           231 Aug 26 04:29 .env.production
-rw-r--r--    1 root     root           271 Aug 26 04:29 .gitignore
-rw-r--r--    1 root     root          1807 Aug 26 04:29 craco.config.ts
-rw-r--r--    1 root     root          2025 Aug 26 04:29 package.json
drwxr-xr-x    2 root     root          4096 Aug 26 04:29 public
drwxr-xr-x    5 root     root          4096 Aug 26 04:29 src
-rw-r--r--    1 root     root           544 Aug 26 04:29 tsconfig.json
-rw-r--r--    1 root     root           249 Aug 26 04:29 tsconfig.paths.json
/usr/src/app/packages/app
Removing intermediate container dc67785803aa
---> c9b9770f37c4
Step 10/13 : RUN pwd
---> Running in d993e5a6b846
/usr/src/app
Removing intermediate container d993e5a6b846
---> fa72b134b023
```

무슨 짓을 하더라도 WORKDIR 로 다시 돌아온다.
이러한 결과는 *Dockerfile은 docker run + docker commit의 래퍼*일 뿐이기 때문이다.

```yaml
FROM node:16.13.2-alpine3.15
(생략 ...)
RUN cd packages/app
RUN pwd
RUN ls -al
(생략 ...)
```
위의 도커파일은 아래와 같다.

```shell
CID=$(docker run node:16.13.2-alpine3.15 cd packages/app); ID=$(docker commit $CID)
CID=$(docker run $ID pwd); ID=$(docker commit $CID)
CID=$(docker run $ID ls -al); ID=$(docker commit $CID)
```

매번 RUN을 할때마다 새 컨테이너를 생성하므로 pwd는 WORKDIR로 설정한'/usr/src/app'가 나온다.


그러므로 이동등의 행동을 Dockerfile에서 할때는

`RUN cd packages/app && ls -al && pwd && cat .env.production`
이렇게 한줄로 적어서 무언가를 할수밖에 없다.