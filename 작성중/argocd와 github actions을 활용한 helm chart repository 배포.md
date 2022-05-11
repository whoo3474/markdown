나는 가끔 눈물을 흘린다..
내가 왜 helm을 써가지고는..
근데 이미 갈만큼 갔고, kustomize보다는 helm의 기능이 마음에 들기때문에 helm으로 쭉 갈것이다.

![image](https://user-images.githubusercontent.com/23617635/166405846-cb23a508-ae60-4427-a1ff-5382ee423c03.png)


아니 그런데 kusotmize로 github actions를 배포하는 방법은 많이 한글 블로그에 나와있는데
Helm으로는 argocd를 helm으로 배포하는 방법밖에 안나와있었다.

게다가 Helm으로 아무리 automatic sync를 하려고해도 작동을 안해서
value.yaml 에서 부터 Chart.yaml, template/*.yaml 등을 수정해보기도 하고,
수정할때마다 
```
helm package .
helm index repo .
```
를 해서 commit 하고 push해줬는데 안되었다 ㅎㅎㅎㅎ.

한참을 찾다가, 얘네는 git의 변경된 commit을 3분마다 sync해주거나, github webhook으로 신호를 즉각적으로 보내주어서 sync해주는것을 확인하였다.
다만, 그건 git이고.. kustomize나 일반 yaml파일..
Helm은 유감스럽게도 .tar형식의 helm 패키지를 사용하기 때문인지 자동 업데이트가 안된다..
이건 공식 문서에도 나와있지 않다.



모든 회사나 개인의 상황에 따라 쿠버네티스의 생태계를 관리하는 툴이나 코드 작성방식, 파일/폴더 관리 방식이 달라진다고 이해하고있다.
그렇기 때문에, 여기에서는 나의 주변 상황에 맞는, 좀더 편한 환경으로 구축을 진행하고자 한다.


백엔드, 프론트 엔드 등의 레포지토리를 따로 두고, Helm chart github page를 위한 레포지토리를 각각 따로 생성한다.

백엔드, 프론트 엔드에서는
빌드의 실패/성공 여부에따라 슬랙 알림을 보내고,
github actions가 성공/실패 되었을때, 슬랙 알림을 보내면서,
Helm repository에 helm chart 변경을 위한 pull request를 보낼것이다.
Helm github actions에서는 편의상 pull request를 이벤트 트리거로 걸어놓고 github actions를 작동시킬것이며,
이 actions에는 helm chart lint/test 를 진행하고 각각의 성공 여부를 슬랙 알림을 보낼것이다.
테스트가 완료되면 helm package를 만들어 자동으로 github page를 배포하는 actions를 사용할것이고, 성공 여부를 슬랙 알림을 보낼것이다.
마지막으로는 argocd CLI를 통해서 
```shell
argocd app set <app name> --helm-set image.tag<사용하고자하는 value의 tag name>=v1.0.2<바꾸고자 하는 tag name>

#values-dev.yaml
image:
  tag: v1.0.1
```
내가 작동 시키고있는 argocd에게 직접 cli로 literal value key/value를 보내서 변경시킬것이다.

사실 별도의 github를 바라보며 sync를 해주지 않고, 별도의 argocd cli로 값을 보내 자동화 하는것에 대한 의문은 남지만,
이것 이외에는 
Chart.yaml의 Cahrt version을 업데이트하여 helm package를 배포하고, argocd에서 업데이트된 chart version을 변경하여 sync해주는 번거로운 작업 밖에 없었다.

**이 생각은, 개발자가 배포를 하는 순간부터, 배포되는 과정을 manual적인 과정없이 자동화 하고자 함이다.**
DEV에서만 자동배포로 진행되고, PROD환경에서는 sync는 메뉴얼한 과정을 추가한다.





어떻게 어떻게 진행을 하다보니,

github actions에서 
```
docker/build-push-action
```
을 사용해서 내 앱을 도커 이미지로 만들고 레지스트리에 이미지를 푸쉬하고


새로운 helm chart를 보관하기위한 레포지토리를 생성해서
자동으로
helm package .
helm index repo .
로 패키지를 만들고, index.yaml을 업데이트하여 올려주는 
```
stefanprodan/helm-gh-pages
```
를 사용하고

```
helm/kind-action
helm/chart-testing-action
```
등을 사용하여 test를 하고, 
