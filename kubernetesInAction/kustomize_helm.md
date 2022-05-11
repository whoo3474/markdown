> 출처:
>  https://www.jacobbaek.com/1172
> https://wookiist.dev/159


# Helm 과 Kustomize

둘다 비슷한 역할을 하지만,

Kustomize 수동으로 하는 부분이 존재하지만,
Helm은 이마저도 자동화 해놓았다.

개발을 했다가 Devops로 전향을 해서 그런지, helm에서는 go template를 사용하여, if나 반복문, 변수설정등을 사용하는게 오히려 편하게 느껴졌다.


애플리케이션을 동적으로 배포하려고 사용하는것.



# Kustomize
## Kustomize 란?
![kustomize](https://kustomize.io/images/header_templates.png)
> https://kustomize.io/

```shell
## kustomization.yaml 파일을 생성
kustomize create --autodetect
```

```shell
##kustomize 를 수정함
kustomize edit set image quay.io

```

```shell
## 출력 결과물을 만듬
kustomize build
```

```shell
kubectl apply -k 


```

---------------------

# Helm
## Helm이란?
Helm은 brew, apt-get 와 yaml 같은 저장소를 가지고있다.

![image](https://assets.cloudacademy.com/bakery/media/uploads/entity/blobid1-ed9f8e01-0402-4fcd-887f-25b8f50888f2.png)
> https://helm.sh/

Helm stable repository를 보면 대부분 Deprecation이 되어있다.
>https://helm.sh/ko/blog/charts-repo-deprecation/
>https://www.cncf.io/blog/2020/10/07/important-reminder-for-all-helm-users-stable-incubator-repos-are-deprecated-and-all-images-are-changing-location/

관리하는 차트들의 수가 늘어나고, 인기가 많아지게 되면서 유지가 점점 어려워짐으로써, 지원 중단을 하게되었다.

많은 글들에서 아직은 stable repository를 등록해서 사용하는 예시가 많기 때문에, 실습 용도로는 사용을 하되, 이후 다른 레포지토리를 사용해야한다.
```shell
helm repo remove stable

helm repo add bitnami https://charts.bitnami.com/bitnami
```


```shell
brew install helm
```

```shell
## 공식 helm 헬름 stable 차트 레포지토리 이다.
helm repo add stable https://charts.helm.sh/stable
```

```shell
## 해당 레포지토리 안에서 차트들의 목록을 확인할수 있다.
helm search repo stable
```

예를들어 stable에 없는 차트들을 다운로드 받아서 사용하고 싶다면,
```shell
# argocd의 레포지토리를 추가하고
helm repo add argo https://argoproj.github.io/argo-helm

# install을 진행하면 된다.
helm install my-release argo/argo-cd
```


## helm degug
![image](https://user-images.githubusercontent.com/23617635/154198654-a6284ecb-6dab-432f-a9c6-4d142a829385.png)
```shell
#템플릿 렌더링을 테스트하고 싶지만 실제로 아무것도 설치하지 않으려고 사용.
#YAML 유효성 검사와 함께 K8S 사양에 대해 매니페스트 파일을 확인하고 렌더링하는 틸러로 차트를 보냅니다.
helm install --dry-run --debug
```

![image](https://user-images.githubusercontent.com/23617635/154198626-f0af0fa8-55f5-4465-8dd0-ccda09272df3.png)

```shell
helm lint
#매니페스트만 생성하고 YAML 파일이 유효한지 확인합니다. 그러나 생성된 매니페스트가 유효한 Kubernetes 리소스인지 확인하지 않습니다.
helm template 
```