
실무 환경에서 NextJS 동적 배포할때는 아무 걱정없이 yarn start로 포트열고 마음편하게 사용했었는데,
정적 페이지를 배포할일이 생겼다.

ReactJS를 사용할때는 마음편하게 yarn build만 하고 index.html만 바라보게하고 있으면 잘 해결 되었었다.
NextJS에서는 yarn build 후, yarn export를 하면 모든 페이지가 .html 확장자를 달고 나오게 되었다.

예를 들어서,
```
naver.com
naver.com/login
naver.com/history
```
라는 경로들이 존재한다면, 나의 빌드 파일들에서도
index.html, login.html, history.html 이렇게 남아있어서 해당 파일들을 불러서 화면에 보여줘야 했다.


사전 작업으로는
1. S3 를 생성한다
2. ACL비활성화, 모든 퍼블릭 엑세스 차단으로 설정한다.
3. 정적 웹 사이트 호스팅설정을 해주며, 인덱스 문서는 index.html로 해준다.
4. cloudfront 생성하는데, OAI설정을 체크하고 S3와 연동 및 버킷 정책을 변경해준다.
(이때 알아서 cloudfront를 principal, condition 으로 지정후, s3:getObject action과 연결할 s3 resource에 대한 policy를 생성해줘서 편함)
5. 덤으로 ACM생성을 해서 SSL 인증서도 세팅한다.
6. Origin Shield도 한번 설정해보자.
7. 403, 404 오류페이지는 HTTP 200 응답코드 및 /index.html 응답페이지 경로로 설정한다.
8. 캐쉬 정책은 본인이 원하는데로 편하게 AWS에서 지원해주는 정책을 사용한다.
9. Route53으로 도메인을 A레코드 별칭을 사용해 cloudfront를 가르킨다.
10. 마지막으로 Github Actions를 작성할때, yarn build, yarn export를 진행후, s3 , cloudfront 등의 env 변수 혹은 상수들을 github actions secret으로 설정하고, aws cli로 s3배포와 cloudfront 무효화를 진행해준다.
   ```
   # github actions에서 주요하게 사용될 aws cli
    aws s3 cp --recursive --region ap-northeast-2 out ${{ secrets.AWS_PAGE_S3 }} 
    aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.AWS_CLOUDFRONT }} --paths "/*"
   ```

배포 순서는 
1. 개발자가 코드를 작성 후, git push를 진행한다.
2. github actions에 설정하였기 때문에, 빌드 및 s3 배포, 캐쉬 무효화 까지 진행되서 배포 완료된다.




자 이제 여기서 문제가 발생한다.
처음에 그냥 메뉴를 여기저기 클릭하면서 이동해도 별 문제가 없을수 있는데, 새로고침하면 문제가 생긴다.
nextjs에서 route에 대해서 console을 찍으면 자꾸 "/" 경로라고 나온다.

이때 해야할것은 함수를 써야한다.
이전에는 cloudfront에서 커스텀한 함수를 쓰고싶으면 람다를 사용했었는데, 함수라는것이 새로 생겼다.




```js
function handler(event) {
    var request = event.request;
    var uri = request.uri;

    var suffix = '.html';
    var appendToDirs = 'index.html';
    var removeTrailingSlash = false;

    var regexSuffixless = /\/[^/.]+$/; // e.g. "/some/page" but not "/", "/some/" or "/some.jpg"
    var regexTrailingSlash = /.+\/$/; // e.g. "/some/" or "/some/page/" but not root "/"
    // var dynamicRouteRegex = /\/subpath\/\b[0-9a-f]{8}\b-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-\b[0-9a-f]{12}\b/; // e.g /urs/some-uuid; // e.g. '/subpath/uuid'
    var dynamicRouteRegex = /^(.+)\/\d+\/?$/; // e.g. /post/123

    if(uri.match(dynamicRouteRegex)) {
        request.uri = uri.replace(dynamicRouteRegex, '$1/[id].html')
    }
    
    // Append ".html" to origin request
    if (suffix && uri.match(regexSuffixless)) {
        request.uri = uri + suffix;
    }
    
    // Append "index.html" to origin request
    if (appendToDirs && uri.match(regexTrailingSlash)) {
        request.uri = uri + appendToDirs;
    }

    // Redirect (301) non-root requests ending in "/" to URI without trailing slash
    if (removeTrailingSlash && uri.match(/.+\/$/)) {
        var request = {
            // body: '',
            // bodyEncoding: 'text',
            headers: {
                'location': [{
                    key: 'Location',
                    value: uri.slice(0, -1)
                 }]
            },
            status: '301',
            statusDescription: 'Moved Permanently'
        };
        return request
    }

    // If nothing matches, return request unchanged
    return request;
};
```


<img width="1202" alt="image" src="https://user-images.githubusercontent.com/23617635/220567517-61924eb9-6ef9-4922-aee5-656d08d56c5a.png">
함수 생성하기를 누르면
<img width="781" alt="image" src="https://user-images.githubusercontent.com/23617635/220567600-6f990e38-3e94-4636-8f18-b7c3af36abb8.png">
이름만 적고 저장한다.
<img width="1039" alt="image" src="https://user-images.githubusercontent.com/23617635/220567701-0a6b6fdf-ff12-4815-8463-67463ffd3063.png">
개발에 함수코드를 넣고 변경사항 저장을 누른다.
그리고 빌드 , 테스트, 게시 에서 게시를 눌르는 화면에서 함수 게시를 한다.
<img width="1048" alt="image" src="https://user-images.githubusercontent.com/23617635/220568116-8606cd5d-4e6e-4c4e-b73f-3f16d9a4a3eb.png">
<img width="1061" alt="image" src="https://user-images.githubusercontent.com/23617635/220568251-a683dc71-79ef-4ed3-
ba6a-35b319f2a83a.png">

여기서 연결 추가를 누르고, 원하는 cloudfront와 연결하면 끝!

<img width="1106" alt="image" src="https://user-images.githubusercontent.com/23617635/220568363-1f3f1015-e704-414a-8300-5ff446617c54.png">

