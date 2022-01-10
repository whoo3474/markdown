> 출처 : https://argo-cd.readthedocs.io/en/stable/


>https://platform9.com/blog/argo-cd-vs-tekton-vs-jenkins-x-finding-the-right-gitops-tooling/

>참고
https://cwal.tistory.com/22

>GitOps란
https://duksoo.tistory.com/entry/GitOps-%EB%9E%80
푸시기반 배포, 풀기반 배포

# Argocd

## ArgoCD는 무엇인가?
Argo CD는 Kubernetes용 선언적 GitOps 지속적 전달 툴(cd)입니다.

## 왜 ArgoCD인가?
애플리케이션 정의, 구성 및 환경은 선언적이어야 하며 버전이 제어되어야 합니다. 
애플리케이션 배포 및 수명 주기 관리는 자동화되고 감사 가능하며 이해하기 쉬워야 합니다.

![image](https://user-images.githubusercontent.com/23617635/143388529-31fa8350-3c69-412a-a1bd-4783abb6b02a.png)
>출처  : https://ichi.pro/ko/gitlab-ci-mich-argocdleul-sayonghaneun-kubernetes-ui-gitops-173863326692699

![image](https://user-images.githubusercontent.com/23617635/143388748-a476f952-d0ca-496d-9632-21b65b7d47ea.png)
> 출처  : https://news.mynavi.jp/itsearch/article/devsoft/5154


# push 기반 배포

# pull 기반 배포


>https://snowdeer.github.io/linux/2018/01/22/ubuntu-16p04-using-snap-package/

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get install docker-ce docker-ce-cli containerd.io -y

sudo apt-get update -y
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common -y

sudo usermod -aG docker $USER && newgrp docker

docker ps -a

sudo apt install curl

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb

sudo dpkg -i minikube_latest_amd64.deb
minikube start --driver=docker



kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'