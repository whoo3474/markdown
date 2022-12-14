# github Actions의 cache를 지워보자

사람은 알수없는 에러를 마주하게되면, 다양한 생각을 하며 에러의 원인을 찾아보게되고,
구글과 스택오버플로우를 뒤적거리지만 답이 안나올때는 정말 다양한 행동을 하게 되는것같다.

그러하여 나도 아. 혹시 캐시가 남아서 그런건 아닐까? 하고 뜬금없이 깃허브 캐시를 지우려고 하였다.

참고로 깃허브 캐시를 일정 용량 이상 차면, 오래된 순으로 알아서 삭제되므로 굳이 삭제할필요는 없긴 할것이다.


> https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows#managing-caches

여기에 나오는 코드를 깃허브 액션으로 실행시켜주었다.
아래가 제공되는 코드이다.
이것을 나의 레포지토리에 ./github/workflows/delete-cache.yaml 파일을 만들어서 붙여넣기해준다.
```
name: cleanup caches by a branch
on:
  pull_request:
    types:
      - closed
  workflow_dispatch:

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v3
        
      - name: Cleanup
        run: |
          gh extension install actions/gh-actions-cache
          
          REPO=$            //채워줘야함
          BRANCH=$          //채워줘야함

          echo "Fetching list of cache key"
          cacheKeysForPR=$(gh actions-cache list -R $REPO -B $BRANCH | cut -f 1 )

          ## Setting this to not fail the workflow while deleting cache keys. 
          set +e
          echo "Deleting caches..."
          for cacheKey in $cacheKeysForPR
          do
              gh actions-cache delete $cacheKey -R $REPO -B $BRANCH --confirm
          done
          echo "Done"
        env:
          GH_TOKEN: $       //채워줘야함
```

여기서 우리는 REPO 와 BRANCH, GH_TOKEN을 넣어줘야한다.
참고로 레포는 "[HOST/]OWNER/REPO" 포맷을 맞춰줘야한다.
나는 아래와같이 값을 넣어주었다.
```
REPO=openknowl/miniintern-client-v2
BRANCH=k8s-prod
GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

코드를 보면 알겠지만 단순히 gh actions-cache list 명령어로 리스트를 가져오고
for문으로 리스트를 돌려서  gh actions-cache delete 명령어로 하나씩 지워주는 코드이다.

이 명령어가 궁금하면

>https://docs.github.com/en/rest/actions/cache#delete-github-actions-caches-for-a-repository-using-a-cache-key

여기에서 javascript, github cli, cURL 로 된 명령어를 확인할수있다.
Gibhub API가 제공하는것을 사용할뿐이다.

## 지우기전의 캐시들
![image](https://user-images.githubusercontent.com/23617635/207623748-045b835f-cdcd-4217-8be6-9507fd94804a.png)


## 지워지는 모습들
![image](https://user-images.githubusercontent.com/23617635/207624196-6520b460-320a-4a2a-8b0c-a409772f27fc.png)
