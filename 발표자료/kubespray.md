

> 이 글은 "https://kubernetes.io/ko/docs" 와 "https://www.redhat.com/ko/topics/containers/kubernetes-architecture", 여러 블로그, 강의들을 총합하여 한곳에 모아 공부하고자 적고있습니다.
> 출처등은 적고있지만, 부족하다면 말씀 부탁드립니다.

# 쿠버네티스란?
쿠버네티스는 컨테이너화된 워크로드와 서비스를 관리하기 위한 이식성이 있고, 확장가능한 오픈소스 플랫폼이다. 쿠버네티스는 선언적 구성과 자동화를 모두 용이하게 해준다. 쿠버네티스는 크고, 빠르게 성장하는 생태계를 가지고 있다. 쿠버네티스 서비스, 기술 지원 및 도구는 어디서나 쉽게 이용할 수 있다.

쿠버네티스란 명칭은 키잡이(helmsman)나 파일럿을 뜻하는 그리스어에서 유래했다. K8s라는 표기는 "K"와 "s"와 그 사이에 있는 8글자를 나타내는 약식 표기이다. 구글이 2014년에 쿠버네티스 프로젝트를 오픈소스화했다. 쿠버네티스는 프로덕션 워크로드를 대규모로 운영하는 15년 이상의 구글 경험과 커뮤니티의 최고의 아이디어와 적용 사례가 결합되어 있다.

![쿠버네티스란](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)

-----------------------------------------

# 쿠버네티스가 필요한 이유
컨테이너는 애플리케이션을 포장하고 실행하는 좋은 방법이다. 프로덕션 환경에서는 애플리케이션을 실행하는 컨테이너를 관리하고 가동 중지 시간이 없는지 확인해야 한다. 예를 들어 컨테이너가 다운되면 다른 컨테이너를 다시 시작해야 한다. 이 문제를 시스템에 의해 처리한다면 더 쉽지 않을까?

그것이 쿠버네티스가 필요한 이유이다! 쿠버네티스는 분산 시스템을 탄력적으로 실행하기 위한 프레임 워크를 제공한다. 애플리케이션의 확장과 장애 조치를 처리하고, 배포 패턴 등을 제공한다. 예를 들어, 쿠버네티스는 시스템의 카나리아 배포를 쉽게 관리 할 수 있다.

쿠버네티스는 다음을 제공한다.

- **서비스 디스커버리와 로드 밸런싱** : 쿠버네티스는 DNS 이름을 사용하거나 자체 IP 주소를 사용하여 컨테이너를 노출할 수 있다. 컨테이너에 대한 트래픽이 많으면, 쿠버네티스는 네트워크 트래픽을 로드밸런싱하고 배포하여 배포가 안정적으로 이루어질 수 있다.
- **스토리지 오케스트레이션** : 쿠버네티스를 사용하면 로컬 저장소, 공용 클라우드 공급자 등과 같이 원하는 저장소 시스템을 자동으로 탑재 할 수 있다.
- **자동화된 롤아웃과 롤백** : 쿠버네티스를 사용하여 배포된 컨테이너의 원하는 상태를 서술할 수 있으며 현재 상태를 원하는 상태로 설정한 속도에 따라 변경할 수 있다. 예를 들어 쿠버네티스를 자동화해서 배포용 새 컨테이너를 만들고, 기존 컨테이너를 제거하고, 모든 리소스를 새 컨테이너에 적용할 수 있다.
- **자동화된 빈 패킹(bin packing)** : 컨테이너화된 작업을 실행하는데 사용할 수 있는 쿠버네티스 클러스터 노드를 제공한다. 각 컨테이너가 필요로 하는 CPU와 메모리(RAM)를 쿠버네티스에게 지시한다. 쿠버네티스는 컨테이너를 노드에 맞추어서 리소스를 가장 잘 사용할 수 있도록 해준다.
- **자동화된 복구(self-healing)** : 쿠버네티스는 실패한 컨테이너를 다시 시작하고, 컨테이너를 교체하며, '사용자 정의 상태 검사'에 응답하지 않는 컨테이너를 죽이고, 서비스 준비가 끝날 때까지 그러한 과정을 클라이언트에 보여주지 않는다.
- **시크릿과 구성 관리** : 쿠버네티스를 사용하면 암호, OAuth 토큰 및 SSH 키와 같은 중요한 정보를 저장하고 관리 할 수 있다. 컨테이너 이미지를 재구성하지 않고 스택 구성에 시크릿을 노출하지 않고도 시크릿 및 애플리케이션 구성을 배포 및 업데이트 할 수 있다.


------------------------------------------------------

# 쿠버네티스 설계 원칙
쿠버네티스 클러스터의 설계는 쿠버네티스 구현 상세 정보에 설명된 대로 3가지 원칙에 기반을 두고 있습니다.

쿠버네티스 클러스터가 갖춰야 할 특성은 다음과 같습니다.

- 보안성: 최신 보안 모범 사례를 따라야 합니다.
- 사용 편이성: 몇 가지 간단한 명령으로 작동할 수 있어야 합니다. 
- 확장 가능성: 하나의 제공업체만을 선호해서는 안 되고 구성 파일을 통해 사용자 정의할 수 있어야 합니다.

>https://kubernetes.io/docs/reference/setup-tools/kubeadm/implementation-details/

------------------------------------------------------

# 쿠버네티스의 컴포넌트(구성요소)
쿠버네티스를 배포하면 클러스터를 얻는다.

쿠버네티스 클러스터는 컨테이너화된 애플리케이션을 실행하는 노드라고 하는 워커 머신의 집합. 모든 클러스터는 최소 한 개의 워커 노드를 가진다.

워커 노드는 애플리케이션의 구성요소인 파드를 호스트한다. 컨트롤 플레인은 워커 노드와 클러스터 내 파드를 관리한다. 프로덕션 환경에서는 일반적으로 컨트롤 플레인이 여러 컴퓨터에 걸쳐 실행되고, 클러스터는 일반적으로 여러 노드를 실행하므로 내결함성과 고가용성이 제공된다.

여기서는 완전히 작동하는 쿠버네티스 클러스터를 갖기 위해 필요한 다양한 컴포넌트들에 대해 요약하고 정리한다.

아래는 모든 컴포넌트가 함께 있는 쿠버네티스 클러스터 다이어그램을 보여준다.

![쿠버네티스의 컴포넌트](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)
>쿠버네티스 공식홈페이지 사진


![쿠버네티스의 컴포넌트2](https://www.redhat.com/cms/managed-files/kubernetes_diagram-v3-770x717_0.svg)
>레드헷 공식홈페이지 사진

## 컨트롤 플레인 컴포넌트(마스터)
컨트롤 플레인 컴포넌트는 클러스터에 관한 전반적인 결정(예를 들어, 스케줄링)을 수행하고 클러스터 이벤트(예를 들어, 디플로이먼트의 replicas 필드에 대한 요구 조건이 충족되지 않을 경우 새로운 파드를 구동시키는 것)를 감지하고 반응한다.

컨트롤 플레인 컴포넌트는 클러스터 내 어떠한 머신에서든지 동작할 수 있다. 그러나 간결성을 위하여, 구성 스크립트는 보통 동일 머신 상에 모든 컨트롤 플레인 컴포넌트를 구동시키고, 사용자 컨테이너는 해당 머신 상에 동작시키지 않는다. 여러 VM에서 실행되는 컨트롤 플레인 설정의 예제를 보려면 kubeadm을 사용하여 고가용성 클러스터 만들기를 확인해본다.


### kube-apiserver
API 서버는 쿠버네티스 API를 노출하는 쿠버네티스 컨트롤 플레인 컴포넌트이다. API 서버는 쿠버네티스 컨트롤 플레인의 프론트 엔드로, 내부 및 외부 요청을 처리합니다.

쿠버네티스 API 서버의 주요 구현은 kube-apiserver 이다. kube-apiserver는 수평으로 확장되도록 디자인되었다. 즉, 더 많은 인스턴스를 배포해서 확장할 수 있다. 여러 kube-apiserver 인스턴스를 실행하고, 인스턴스간의 트래픽을 균형있게 조절할 수 있다.

REST 호출이나 kubectl 커맨드라인 인터페이스 또는 kubeadm과 같은 기타 CLI(command-line interface)를 통해 API에 액세스할 수 있습니다.


### kube-scheduler
클러스터가 양호한 상태인가? 새 컨테이너가 필요하다면 어디에 적합한가? 쿠버네티스 스케줄러는 이러한 것들을 주로 다룹니다.

노드가 배정되지 않은 새로 생성된 파드 를 감지하고, 실행할 노드를 선택하는 컨트롤 플레인 컴포넌트입니다.

스케줄링 결정을 위해서 고려되는 요소는 CPU 또는 메모리와 같은 리소스에 대한 개별 및 총체적 요구 사항, 하드웨어/소프트웨어/정책적 제약, 어피니티(affinity) 및 안티-어피니티(anti-affinity) 명세, 데이터 지역성, 워크로드-간 간섭, 데드라인와 같은 클러스터의 상태를 고려합니다. 그런 다음 포드를 적절한 컴퓨팅 노드에 예약합니다.

> **affinity, anti-affinity 란?**
> kubenetes 환경에서 작업하다보면, 특정 node에 pod가 쏠리는 현상이 발생할 수 있으며, 이를 방지하기위해서 설정하는것.
> nodeSelector 보다 더 많은 매칭 규칙을 제공하는 확장된 방식으로, 특정 노드에 pod를 위치시키게 고정하거나 또는 제외시킬 수 있습니다.
>affinity는 Node affinity와 Pod affinity 두가지가 있습니다.
>
> 이와 같은 방식으로 특정 노드 선택과 노드필터링이 있습니다.
> 특정 노드 선택은 가장 원시적인 방법으로 nodeName과 nodeSelector를 pod의 spec에 명시하여 사용합니다.
>  노드필터링(Taint,Toleration)은 해당 노드에 Pod가 할당되거나 실행되는것을 막을수 있습니다.
> 참고 : https://bcho.tistory.com/1346?category=731548
> https://blog.naver.com/alice_k106/221511412970

### kube-controller-manager
컨트롤러 프로세스를 실행하는 컨트롤 플레인 컴포넌트.

논리적으로, 각 컨트롤러는 분리된 프로세스이지만, 복잡성을 낮추기 위해 모두 단일 바이너리로 컴파일되고 단일 프로세스 내에서 실행된다.

>컨트롤러는 실제로 클러스터를 실행하고 쿠버네티스 controller-manager에는 여러 컨트롤러 기능이 하나로 통합되어 있습니다. 
하나의 컨트롤러는 스케줄러를 참고하여 정확한 수의 포드가 실행되게 합니다. 
포드에 문제가 생기면 또 다른 컨트롤러가 이를 감지하고 대응합니다. 
컨트롤러는 서비스를 포드에 연결하므로 요청이 적절한 엔드포인트로 이동합니다. 
또한 계정 및 API 액세스 토큰 생성을 위한 컨트롤러가 있습니다.

여러 컨트롤러란 아래를 말합니다.

- 노드 컨트롤러: 노드가 다운되었을 때 통지와 대응에 관한 책임을 가진다.
- 레플리케이션 컨트롤러: 시스템의 모든 레플리케이션 컨트롤러 오브젝트에 대해 알맞은 수의 파드들을 유지시켜 주는 책임을 가진다.
- 엔드포인트 컨트롤러: 엔드포인트 오브젝트를 채운다(즉, 서비스와 파드를 연결시킨다.)
서비스 어카운트 & 토큰 컨트롤러: 새로운 네임스페이스에 대한 기본 계정과 API 접근 토큰을 생성한다.



### cloud-controller-manager
클라우드별 컨트롤 로직을 포함하는 쿠버네티스 컨트롤 플레인 컴포넌트이다. 클라우드 컨트롤러 매니저를 통해 클러스터를 클라우드 공급자의 API에 연결하고, 해당 클라우드 플랫폼과 상호 작용하는 컴포넌트와 클러스터와만 상호 작용하는 컴포넌트를 구분할 수 있게 해 준다.
cloud-controller-manager는 클라우드 제공자 전용 컨트롤러만 실행한다. 자신의 사내 또는 PC 내부의 학습 환경에서 쿠버네티스를 실행 중인 경우 클러스터에는 클라우드 컨트롤러 매니저가 없다.

kube-controller-manager와 마찬가지로 cloud-controller-manager는 논리적으로 독립적인 여러 컨트롤 루프를 단일 프로세스로 실행하는 단일 바이너리로 결합한다. 수평으로 확장(두 개 이상의 복제 실행)해서 성능을 향상시키거나 장애를 견딜 수 있다.

다음 컨트롤러들은 클라우드 제공 사업자의 의존성을 가질 수 있다.

- 노드 컨트롤러: 노드가 응답을 멈춘 후 클라우드 상에서 삭제되었는지 판별하기 위해 클라우드 제공 사업자에게 확인하는 것
- 라우트 컨트롤러: 기본 클라우드 인프라에 경로를 구성하는 것
- 서비스 컨트롤러: 클라우드 제공 사업자 로드밸런서를 생성, 업데이트 그리고 삭제하는 것


### etcd
모든 클러스터 데이터를 담는 쿠버네티스 뒷단의 저장소로 사용되는 일관성·고가용성 키-값 저장소입니다.
설정 데이터와 클러스터의 상태에 관한 정보는 키-값 저장소 데이터베이스인 etcd에 상주합니다. 내결함성을 갖춘 분산형 etcd는 클러스터에 관한 궁극적 정보 소스(Source Of Truth, SOT)가 되도록 설계되었습니다.
쿠버네티스 클러스터에서 etcd를 뒷단의 저장소로 사용한다면, 이 데이터를 백업하는 계획은 필수입니다.



## 노드 컴포넌트(슬레이브)
쿠버네티스 클러스터에는 최소 1개 이상의 컴퓨팅 노드가 필요하지만 일반적으로 여러 개가 있습니다. pod는 노드에서 실행하도록 예약되고 오케스트레이션됩니다. 
클러스터의 용량을 확장해야 한다면 노드를 더 추가하면 됩니다.

노드 컴포넌트는 동작 중인 pod를 유지시키고 쿠버네티스 런타임 환경을 제공하며, 모든 노드 상에서 동작합니다.

![노드](https://d33wubrfki0l68.cloudfront.net/5cb72d407cbe2755e581b6de757e0d81760d5b86/a9df9/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg)

### pod
pod는 쿠버네티스 오브젝트 모델에서 가장 작고 단순한 유닛으로, 애플리케이션의 단일 인스턴스를 나타냅니다. 
각 pod는 컨테이너 실행 방식을 제어하는 옵션과 함께 컨테이너 하나 또는 긴밀히 결합된 일련의 컨테이너로 구성되어 있습니다.
pod를 퍼시스턴트 스토리지에 연결하여 스테이트풀(stateful) 애플리케이션을 실행할 수 있습니다.

![파드](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)

### 컨테이너 런타임 엔진
컨테이너 실행을 위해 각 컴퓨팅 노드에는 컨테이너 런타임 엔진이 있습니다. 
그중 한 가지 예가 Docker입니다. 
하지만 쿠버네티스는 rkt, CRI-O와 같은 다른 Open Container Initiative 호환 런타임도 지원합니다.

### kubelet
각 컴퓨팅 노드에는 컨트롤 플레인과 통신하고, 파드에서 컨테이너가 확실하게 동작하도록 관리하는 매우 작은 애플리케이션인 kubelet이 있습니다. 

kubelet은 컨테이너가 포드에서 실행되게 하며, 컨트롤 플레인에서 노드에 작업을 요청하는 경우 이를  
실행합니다.

Kubelet은 다양한 메커니즘을 통해 제공된 파드 스펙(PodSpec)의 집합을 받아서 컨테이너가 해당 파드 스펙에 따라 정상적인 동작하는 것을 확실히 합니다. 
Kubelet은 쿠버네티스를 통해 생성되지 않는 컨테이너는 관리하지 않습니다.


### kube-proxy
kube-proxy는 클러스터의 각 노드에서 실행되는 쿠버네티스 네트워킹 서비스를 용이하게 하기 위한 네트워크 프록시로, 쿠버네티스의 서비스 개념의 구현부입니다.

kube-proxy는 노드의 네트워크 규칙을 유지 관리합니다. 
이 네트워크 규칙이 내부 네트워크 세션이나 클러스터 바깥에서 파드로 네트워크 통신을 할 수 있도록 해줍니다.

kube-proxy는 운영 체제에 가용한 패킷 필터링 계층이 있는 경우, 이를 사용하고, 그렇지 않다면, 트래픽 자체를 포워드(forward)합니다.

>kube-proxy는 운영 체제의 패킷 필터링 계층에 의존하거나 트래픽 자체를 전달하여 클러스터 내부 또는 외부의 네트워크 통신을 처리합니다.



-------------------------------------------------------------

# 쿠버네티스 시작점들
## Kubeadmin
Kubeadm 은 Kubernetes를 시작 하는 "어려운 방법" 입니다. 이 솔루션을 사용하면 모범 사례를 준수하는 최소 실행 가능한 Kubernetes 클러스터를 부트스트랩할 수 있다. 
클러스터 최소 크기는 두 개의 노드로 구성된다.


>메모리: 2GB
CPU: 2(마스터 전용)



#### kubeadm, kubelet 및 kubectl 설치
모든 머신에 다음 패키지들을 설치한다.

- kubeadm: 클러스터를 부트스트랩하는 명령이다.
- kubelet: 클러스터의 모든 머신에서 실행되는 파드와 컨테이너 시작과 같은 작업을 수행하는 컴포넌트이다.
- kubectl: 클러스터와 통신하기 위한 커맨드 라인 유틸리티이다.

> kubeadm은 kubelet 또는 kubectl 을 설치하거나 관리하지 않으므로, kubeadm이 설치하려는 쿠버네티스 컨트롤 플레인의 버전과 일치하는지 확인해야 한다. 그렇지 않으면, 예상치 못한 버그 동작으로 이어질 수 있는 버전 차이(skew)가 발생할 위험이 있다. 그러나, kubelet과 컨트롤 플레인 사이에 하나의 마이너 버전 차이가 지원되지만, kubelet 버전은 API 서버 버전 보다 높을 수 없다. 예를 들어, 1.7.0 버전의 kubelet은 1.8.0 API 서버와 완전히 호환되어야 하지만, 그 반대의 경우는 아니다.

> https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm

>**Kubectl**은 쿠버네티스 클러스터를 제어하기 위한 커맨드 라인 도구이다. 구성을 위해, kubectl 은 config 파일을 $HOME/.kube 에서 찾는다. KUBECONFIG 환경 변수를 설정하거나 --kubeconfig 플래그를 설정하여 다른 kubeconfig 파일을 지정할 수 있다.



## minikube
MiniKube는 일반적으로 누군가가 시작하려고 할 때 찾을수있는 첫 번째 Kubernetes 기술이다.
가벼운 쿠버네티스 구현체이며, 로컬 머신에 VM을 만들고 **하나의 노드**로 구성된 간단한 클러스터를 생성한다. Minikube는 리눅스, 맥, 그리고 윈도우 시스템에서 구동이 가능하다.
(Kubernetes 공식 문서는 miniKube를 사용 하여 첫 번째 클러스터 를 배포하는 자습서를 제공한다.)

>로컬에 설치 하는 매우 간단한 솔루션 이며 학습 및 테스트용으로 설계되었다. 시작되면 단일 노드 클러스터 (가장 작은 크기)를 배포한다.

> !!윈도우에서 VM으로 가상 컴퓨터를 띄우는것과 다름없다.
> 실환경에서는 사용하지 말것.

>**호스트 시스템의 최소 요구 사항:**
CPU: 2
메모리: 2GB
디스크 공간: 20GB
이것은 kubectl 명령줄에 익숙해지는 가장 쉬운 방법 입니다.

이 솔루션의 불편한 점은 다른 노드를 추가할 수 없고 결과적으로 아키텍처가 실제로 작동하는 방식과 Kubernetes의 모든 기능을 발견 할 수 없다는 것 입니다.

> https://kubernetes.io/ko/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/



## kubespray
Kubespray는 프로비저닝 및 오케스트레이션을위한 기판으로 Ansible을 사용하여 베어 메탈 및 대부분의 클라우드에서 실행된다.
Kubespray는 Ansible 플레이북, 인벤토리, 프로비저닝 도구와 일반적인 운영체제, 쿠버네티스 클러스터의 설정 관리 작업에 대한 도메인 지식의 결합으로 만들어졌다. 

> https://kubernetes.io/ko/docs/setup/production-environment/tools/kubespray/




## Kind 
Kind 는 Kubernetes 클러스터를 로컬로 배포하는 또 다른 도구이다. 
로컬로 배포하는 것의 독특한점은 클러스터가 Docker 컨테이너 내부에 배포 된다는 것 이다.

>단일 노드
1개의 마스터와 여러 개의 워커

초보자의 가장 큰 불편은 클러스터가 도커 컨테이너 내부에 배치되기 때문에 클러스터에 접근하기 위한 네트워크 관리 가 더 어렵다는 것이다.

## k3s
K3S 는 Rancher에서 개발한 가벼운 Kubernetes 버전 입니다 . 소규모 서버, IoT 어플라이언스 등의 프로덕션 사용을 위해 만들어졌습니다. 바이너리는 50MB 미만이며 매우 작은 VM에서 실행할 수 있습니다. 최소 요구 사항은 다음과 같다.

>리눅스 3.10+
서버당 512MB 램
노드당 75MB 램
200MB의 디스크 공간
x86_64, ARMv7, ARM64

가벼운 버전이란, 더 적은 기능을 의미한다.

모든 부가 기능이 제거되었다
기본 저장소는 etcd3 대신 SQLlite3이다.
자동 TLS 관리
기본적으로 사용되는 컨테이너 런타임이 포함되어 있다.

따라서 K3S 는 더 작은 VM에서 Kubernetes를 테스트할 수 있으므로 랩톱이 제한적인 경우 Kubeadm 에 대한 매우 좋은 대안이다.

또한 K3S 설치 및 클러스터 배포 가 매우 쉽다.
그리고 다중 마스터로 클러스터를 생성 할 수 있다.

## kops
kops는 자동화된 프로비저닝 시스템


## microk8s

## 기타
EKS, GKS ...등이 있음

-------------------------


# 설치전 체크사항
- 호환되는 리눅스 머신
- RAM 2GB 이상
- 2core 이상의 CPU
- 클러스터 간의 네트워크 연결
- 모든 노드들이 고유한 호스트 이름, MAC 주소, product_uuid
> MAC 주소 확인 : ip link 혹은 ifconfig -a
  product_uuid : sudo cat /sys/class/dmi/id/product_uuid
- 특정 포트들이 사용 중이지 않아야 함
- 스왑 메모리 비활성화
> swapoff
- iptables 가 bridge된 트래픽을 올바르게 보게 하기
>"sysctl" 구성에서 "net.bridge.bridge-nf-call-iptables" 가 1로 설정되어 있는지 확인해보면 됩니다.

-------------------------

## 생소한 명령어 모음
### 모듈관련
> 리눅스의 모듈은 리눅스 커널에서 필요한 시스템 관리기능들을 모두 커널에 포함시키지 않고, 필요에 따라서 추가하여 사용할 수 있도록 구혀된 매커니즘이다. 
리눅스 모듈 확인 및 관리하기 위해서는 아래와 같은 명령어들이 존재한다.
##### lsmod 
- 현재 시스템에 설치되어 있는 모듈들의 목록을 볼 수 있다.

##### modprobe
- 리눅스 프로그램으로서 적재 가능 커널 모듈을 리눅스 커널에 추가하거나 커널로부터 제거하는데 사용된다.

---
 
### Master Node

|프로토콜|방향|포트 범위|목적|사용자|
|------|---|------|---|----|
|TCP|인바운드|6443|쿠버네티스 API 서버|모두|
|TCP|인바운드|2379-2380|etcd 서버 클라이언트 API|kube-apiserver, etcd|
|TCP|인바운드|10250|kubelet API	|자체, 컨트롤 플레인|
|TCP|인바운드|10251|kube-scheduler|자체|
|TCP|인바운드|10252|kube-controller-manager|자체|
 

### Worker Node

|프로토콜|방향|포트 범위|목적|사용자|
|------|---|------|---|----|
|TCP	|인바운드	|10250	|kubelet API	|자체, 컨트롤 플레인|
|TCP	|인바운드	|30000-32767	|NodePort |서비스	|모두|


위에 해당하는 포트들은 사용 중이지 않아야 한다.

또한 파드에 컨테이너를 실행하기 위해 컨테이너 런타임을 사용하는데, 기본적으로 쿠버네티스는 CRI(Container Runtime Interface)를 사용하여 인터페이스 한다. 인터페이스를 지정하지 않으면 소켓 목록을 검색하여 자동으로 런타임을 자동으로 감지한다. 런타임 소켓 경로는 아래와 같다.

 
|런타임	|유닉스 도메인 소켓 경로|
|------|----------------|
|Docker	|/var/run/dockershim.sock|
|containerd	|/run/containerd/containerd.sock|
|CRI-O	|/var/run/crio/crio.sock|



-------------------------

# 쿠버네티스 설치

 

# kubespray 설치

```shell
docker 설치

```

ip설정
vi /etc/netplan/50-cloud-init.yaml
```
network:
    ethernets:
        eth0:
                addresses:
                        - 172.30.1.1/24
                gateway4: 172.30.1.254
                nameservers:
                        addresses: [168.126.63.1, 168.126.63.2]
                #dhcp4: true
                #optional: true
    version: 2
~
```

```shell
#swap 끄기
# 방화벽 끄기
swapoff -a

vi /boot/firmware/cmdline.txt
 cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1


sudo vi /etc/docker/daemon.json
```
{
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
                "max-size": "100m"
        },
        "storage-driver": "overlay2"
}
```

sudo vi /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main

sudo vi /etc/sysctl.conf
net.ipv4.ip_forward=1

# 각 서버의 호스트서버 연결
vi /etc/hosts

hostname set-hostname [hostname]

# ssh 접속 확인

```

```shell
# ansible ..등등 설치
sudo pip3 install -r requirements.txt

# ansible 작동 확인
ansible -i host.yaml -m ping all
```



 vi roles/bootstrap-os/tasks/bootstrap-debian.yml


 



```shell
ansible-playbook -i kubespray/inventory/cluster/inventory.ini \
-v --become --become-user=root kubespray/cluster.yml -e ignore_assert_errors=yes
```

https://nirsa.tistory.com/181

------------------------------

## 주로 사용하는 약어
```shell
kubectl api-resources
```
|구분|	이름|	약어	|오브젝트 이름(Kind)|
|---|-----|-----|----------------|
|자주 사용하는 명령어|	nodes	|no	|Node|
||namespaces	|ns|	Namespace|
||deployments|	deploy	|Deployment|
||pods	|po|	Pod|
||services	|svc	|Service|
|앞선 포스팅에서 다룬 명령어	|replicasets|	rs|	ReplicaSet|
||ingresses	|ing|	Ingress|
||configmaps	|cm|	ConfigMap|
||horizontalpodautoscalers|	hpa	|HorizontalPodAutoscaler|
||daemonsets	|ds|	DaemonSet|
||persistentvolumeclaims|	pvc	|PersistentVolumeClaim|
||persistentvolumes	|pv|	PersistentVolume|
||statefulsets	|sts|	StatefulSet|
|참고 명령어	|replicationcontrollers|	rc|	ReplicationController|
||resourcequotas	|quota	|ResourceQuota|
||serviceaccounts	|sa|	ServiceAccount|
||cronjobs|	cj|	CronJob|
||events	|ev|	Event|
||storageclasses|	sc|	StorageClass|
||endpoints	|ep|	Endpoints|
||limitranges	|limits|	LimitRange|