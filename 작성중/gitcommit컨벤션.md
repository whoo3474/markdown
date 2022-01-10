1. 슬랙 알림은 하도록 하자
(
 -  풀리퀘트
 -  코멘트
 -  배포 시작
 -  배포 후(성공,실패)
 -  
).. 어디까지 보고싶어? 슬랙으로는 다 가능할듯

2. 테스트도 진행하고싶은데 님들 테스트는 작동시켜도됌? (lint, cypress ...등)
테스트는 나중에 하자

<!-- 백엔드는 jest
프론트엔드는 나중에 (cypress -->

>eslint 테스트(x)
https://github.com/marketplace/actions/super-linter

>패스트레인 테스트(제외)
https://github.com/marketplace/actions/fastlane-action

>docker push
https://github.com/marketplace/actions/publish-docker


3. github template라는게 있데, 우리 만들어볼래?
   
4. 다른 애들이 commit 하는거 한번 봐봐, 우리도 이렇게 할까?
5. commit 하는 방식 정하자.
5.1. commit과 PR은 최대한 작은 단위로 쪼개고 싶다.
>https://medium.com/prnd/%ED%97%A4%EC%9D%B4%EB%94%9C%EB%9F%AC-%EA%B0%9C%EB%B0%9C%ED%8C%80-%EB%AA%A8%EB%91%90%EA%B0%80-%ED%96%89%EB%B3%B5%ED%95%9C-%EA%B0%9C%EB%B0%9C-pr%EA%B4%80%EB%A6%AC-%EB%B0%A9%EB%B2%95-7%EA%B0%80%EC%A7%80-1d4cd5d091f0

>https://www.conventionalcommits.org/ko/v1.0.0/

5.2. commit을 할때 컨벤션이 있으면 좋겠다.
>https://velog.io/@bky373/Git-Git-%EC%BB%A4%EB%B0%8B-%EB%A9%94%EC%8B%9C%EC%A7%80-%EC%BB%A8%EB%B2%A4%EC%85%98

5.3. commit template를 쓰자.
솔직히 지금 Commit은 이해하기도 힘들고 활용이 안되어있다고 생각함.
>git config commit.template ./.github/git-commit-template.txt


6. 유의적 버전
>https://semver.org/lang/ko/

7. pull request도 템플릿을 사용했으면 좋겠어. (라벨도 붙여야함)
8. 어떤 라벨을 쓸지 정해야한다.

코드상의 문제, 기능상의 문제 등이 있을시에
notion에서는 그러한일을 어떻게 해결했는지 모른다. 우리의 지식은
code 그 자체로 끝이나버림.. 과거 이력에 대해서  어떤 일들이 있었고, 어떻게 해결나갔는지 이해하기 힘듬
문제를 해결한것에 대한 데이터베이스가 쌓였으면 하는데..
직관적으로 찾아보고 확인할수있게 github에서 관리하면 안될까? -> ISSUE를 쓰기엔 부담스러우면, PR이라도 잘해보자.
디자인적인 부분 말고, 기능적인 부분만이라도.




현재 배포시 불편한점 건의받습니다.

----------------------------------------


CODEOWNERS
자동 리뷰어 설정

PULL_REQUEST_TEMPLATE.md 
https://blog.dramancompany.com/2021/11/%eb%93%9c%eb%9d%bc%eb%a7%88%ec%95%a4%ec%bb%b4%ed%8d%bc%eb%8b%88%ec%97%90%ec%84%9c-pull-request-%ed%8e%b8%eb%a6%ac%ed%95%98%ea%b2%8c-%ec%82%ac%ec%9a%a9%ed%95%98%eb%8a%94-%eb%b2%95/

https://lookingfor.tistory.com/entry/github-auto-assign2


이슈이슈이슈 핫잇휴

지라를 통해 일해야 하는 이유
https://abelog.netlify.app/development/%EA%B0%9C%EB%B0%9C%EC%9E%90%EA%B0%80-jira-tickets%EB%A5%BC-%ED%86%B5%ED%95%B4-%EC%9D%BC%ED%95%B4%EC%95%BC%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0/



-----------------
github issue를 작성하면 notion 글을 자동 생성 해주는것도 있네?

https://github.com/marketplace/actions/issue-to-notion?fbclid=IwAR1At-GRotyq6Bt9f2ct9Ryqf-yy3HEeLmimaO0noeImB8FXEnc0c1hHnLs

https://www.notion.so/aebe312a066c465494fb1eb6997060b0?v=ecc88a0f24e84f7ea16c2eee3741886f&p=39c2ceb3fde44a57b1986181421a7aed





--------------------------------



얘네보면 commit할때 앞에 prefix로 적어두는것들이 있음


https://github.com/twbs/bootstrap/commits/main



https://github.com/EbookFoundation/free-programming-books/commits?author=davorpa

https://github.com/reduxjs/redux/issues/4212

--------------------------------



--------------------------------

git style guide

https://udacity.github.io/git-styleguide/


--------------------------------


github template repository 생성도 가능
-> 어디다써먹냐?

https://velog.io/@bgm537/Github%EC%9D%98-%EC%83%88%EB%A1%9C%EC%9A%B4-%EA%B8%B0%EB%8A%A5-Template-repository-%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-fsjwpt0x00



------------------------------

이것도 github template 예시

https://velog.io/@modolee/github-initial-settings