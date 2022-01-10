> 이 페이지는 "https://mermaid-js.github.io/mermaid/#/README" 참고하여 작성합니다.

마크다운으로 문서화를 하는 버릇을 들이면서, 나에게 필요한 다이어그램이나 차트, 이미지 등이 필요한것같다 생각을 종종 하기도 하였었다.
그러다가 찾아본것이 Markdown에서 사용 가능한 Mermaid를 찾았다.

# Mermaid
Mermaid를 사용하면 텍스트와 코드를 사용하여 다이어그램과 시각화를 만들 수 있습니다.

Markdown에서 영감을 받은 텍스트 정의를 렌더링하여 다이어그램을 동적으로 만들고 수정하는 Javascript 기반 다이어그램 및 차트 작성 도구입니다.


# Tistory 적용

```
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
```
해당 코드를 tistory의 html편집기를 열어서 붙여넣고. 사용하면 된다.

다만. codeBlock으로 작성은 그만두었다.
tistory의 codeBlock은 pre태그 안의 code태그가 language-mermaid 클래스를 가지게 되는데, tistory의 기본 스타일만 적용된다.

mermaid의 홈페이지에 들어가보면, 

>Doing so will command the mermaid parser to look for the div tags with class="mermaid". From these tags mermaid will try to read the diagram/chart definitons and render them into svg charts.

mermaid클래스를 가진 div태그를 찾아서 svg로 렌더링을 한다고 되어있기 때문에, 이것을 따라가려고 한다.
다행히 markdown은 HTML태그를 그대로 사용하여 적용할수 있다.
애초에 markdown은 Plain text로 작성된 파일(.md)를 HTML로 변경해 보여 주는 매우 쉽고 문서를 간결하게 정히할 수 있는 문법 이기 때문이다.
구글 등에 tistory로 mermaid 적용법을 찾아도 다른 마땅한 방법이 없었다.

```
<div class="mermaid">
pie title NETFLIX
         "Time spent looking for movie" : 90
         "Time spent watching it" : 10
</div>
```

<div class="mermaid">
pie title NETFLIX
         "Time spent looking for movie" : 90
         "Time spent watching it" : 10
</div>


