

# Kubernetes 개요
쿠버네티스는 컨테이너화된 워크로드와 서비스를 관리하기 위한 이식성이 있고, 확장가능한 오픈소스 플랫폼이다. 쿠버네티스는 선언적 구성과 자동화를 모두 용이하게 해준다. 쿠버네티스는 크고, 빠르게 성장하는 생태계를 가지고 있다. 쿠버네티스 서비스, 기술 지원 및 도구는 어디서나 쉽게 이용할 수 있다.

쿠버네티스란 명칭은 키잡이(helmsman)나 파일럿을 뜻하는 그리스어에서 유래했다. K8s라는 표기는 "K"와 "s"와 그 사이에 있는 8글자를 나타내는 약식 표기이다. 구글이 2014년에 쿠버네티스 프로젝트를 오픈소스화했다. 쿠버네티스는 프로덕션 워크로드를 대규모로 운영하는 15년 이상의 구글 경험과 커뮤니티의 최고의 아이디어와 적용 사례가 결합되어 있다.

![image](https://user-images.githubusercontent.com/23617635/150284719-a6094f9e-88b8-4567-9ef5-45e5662462c0.png)

**전통적인 배포 시대**: 초기 조직은 애플리케이션을 물리 서버에서 실행했었다. 한 물리 서버에서 여러 애플리케이션의 리소스 한계를 정의할 방법이 없었기에, 리소스 할당의 문제가 발생했다. 예를 들어 물리 서버 하나에서 여러 애플리케이션을 실행하면, 리소스 전부를 차지하는 애플리케이션 인스턴스가 있을 수 있고, 결과적으로는 다른 애플리케이션의 성능이 저하될 수 있었다. 이에 대한 해결책은 서로 다른 여러 물리 서버에서 각 애플리케이션을 실행하는 것이 있다. 그러나 이는 리소스가 충분히 활용되지 않는다는 점에서 확장 가능하지 않았으므로, 물리 서버를 많이 유지하기 위해서 조직에게 많은 비용이 들었다.

**가상화된 배포 시대**: 그 해결책으로 가상화가 도입되었다. 이는 단일 물리 서버의 CPU에서 여러 가상 시스템 (VM)을 실행할 수 있게 한다. 가상화를 사용하면 VM간에 애플리케이션을 격리하고 애플리케이션의 정보를 다른 애플리케이션에서 자유롭게 액세스 할 수 없으므로, 일정 수준의 보안성을 제공할 수 있다.

가상화를 사용하면 물리 서버에서 리소스를 보다 효율적으로 활용할 수 있으며, 쉽게 애플리케이션을 추가하거나 업데이트할 수 있고 하드웨어 비용을 절감할 수 있어 더 나은 확장성을 제공한다. 가상화를 통해 일련의 물리 리소스를 폐기 가능한(disposable) 가상 머신으로 구성된 클러스터로 만들 수 있다.

각 VM은 가상화된 하드웨어 상에서 자체 운영체제를 포함한 모든 구성 요소를 실행하는 하나의 완전한 머신이다.

**컨테이너 개발 시대**: 컨테이너는 VM과 유사하지만 격리 속성을 완화하여 애플리케이션 간에 운영체제(OS)를 공유한다. 그러므로 컨테이너는 가볍다고 여겨진다. VM과 마찬가지로 컨테이너에는 자체 파일 시스템, CPU 점유율, 메모리, 프로세스 공간 등이 있다. 기본 인프라와의 종속성을 끊었기 때문에, 클라우드나 OS 배포본에 모두 이식할 수 있다.

컨테이너는 다음과 같은 추가적인 혜택을 제공하기 때문에 인기가 있다.

- 기민한 애플리케이션 생성과 배포: VM 이미지를 사용하는 것에 비해 컨테이너 이미지 생성이 보다 쉽고 효율적임.
- 지속적인 개발, 통합 및 배포: 안정적이고 주기적으로 컨테이너 이미지를 빌드해서 배포할 수 있고 (이미지의 불변성 덕에) 빠르고 효율적으로 롤백할 수 있다.
- 개발과 운영의 관심사 분리: 배포 시점이 아닌 빌드/릴리스 시점에 애플리케이션 컨테이너 이미지를 만들기 때문에, 애플리케이션이 인프라스트럭처에서 분리된다.
- 가시성(observability): OS 수준의 정보와 메트릭에 머무르지 않고, 애플리케이션의 헬스와 그 밖의 시그널을 볼 수 있다.
개발, 테스팅 및 운영 환경에 걸친 일관성: 랩탑에서도 클라우드에서와 동일하게 구동된다.
클라우드 및 OS 배포판 간 이식성: Ubuntu, RHEL, CoreOS, 온-프레미스, 주요 퍼블릭 클라우드와 어디에서든 구동된다.
- 애플리케이션 중심 관리: 가상 하드웨어 상에서 OS를 실행하는 수준에서 논리적인 리소스를 사용하는 OS 상에서 애플리케이션을 실행하는 수준으로 추상화 수준이 높아진다.
느슨하게 커플되고, 분산되고, 유연하며, 자유로운 마이크로서비스: 애플리케이션은 단일 목적의 머신에서 모놀리식 스택으로 구동되지 않고 보다 작고 독립적인 단위로 쪼개져서 동적으로 배포되고 관리될 수 있다.
- 리소스 격리: 애플리케이션 성능을 예측할 수 있다.
- 자원 사용량: 리소스 사용량: 고효율 고집적.



## Container 개념
컨테이너는 데스크탑, 기존의 IT 또는 클라우드 등 어디서나 실행될 수 있도록 애플리케이션 코드가 해당 라이브러리 및 종속 항목과 함께 패키징되어 있는 소프트웨어 실행 유닛입니다.

이를 수행하기 위해 컨테이너는 OS의 기능(Linux 커널의 경우, 이름 공간 및 cgroups 프리미티브)을 활용하여 프로세스를 격리하고 해당 프로세스가 액세스할 수 있는 CPU, 메모리 및 디스크의 양을 제어하는 운영 체제(OS) 가상화의 형식을 활용합니다.

가상 머신과는 달리 컨테이너는 모든 인스턴스에 게스트 OS를 포함할 필요가 없으며, 그 대신 호스트 OS의 기능과 리소스를 간편하게 활용할 수 있습니다. 따라서 컨테이너는 소형이고, 빠르며, 이식성이 뛰어납니다.

컨테이너는 FreeBSD Jails 및 AIX Workload Partitions 등의 버전으로 수십 년 전에 처음 소개되었지만, 최신 개발자들 중 대부분은 Docker의 소개에 따라 2013년에 최신 컨테이너 시대가 도래했다고 기억합니다.

## Container VS Virtual Machine
컨테이너를 보다 잘 이해하는 한 가지 방법은 기존의 가상 머신(VM)과의 차이점을 살펴보는 것입니다. 온프레미스에 있건 혹은 클라우드에 있건 이와 무관하게, 기존의 가상화에서는 하이퍼바이저를 활용하여 물리적 하드웨어를 가상화합니다. 그리고 각각의 VM에는 애플리케이션, 이와 연관된 라이브러리 및 종속 항목과 함께 OS가 실행해야 하는 하드웨어의 가상 사본인 게스트 OS가 포함됩니다.

기반 하드웨어를 가상화하는 대신, 컨테이너는 운영 체제(일반적으로 Linux)를 가상화함으로써 각 개별 컨테이너에는 오직 애플리케이션과 함께 해당 라이브러리와 종속 항목만 포함됩니다. 게스트 OS의 부재는 컨테이너가 경량이며 빠르고 포터블한 이유를 설명합니다.

## Container Orchestration
컨테이너 오케스트레이션은 컨테이너의 배포, 관리, 확장, 네트워킹을 자동화합니다. 수백 또는 수천 개의 Linux® 컨테이너와 호스트를 배포하고 관리해야 하는 기업에서는 컨테이너 오케스트레이션을 활용할 수 있습니다. 

컨테이너 오케스트레이션은 컨테이너를 사용하는 어떤 환경에서든 사용할 수 있습니다. 또한 재설계할 필요 없이 각기 다른 환경 전반에 동일한 애플리케이션을 배포하는 데에도 도움이 됩니다. 컨테이너에 마이크로서비스를 구현하면 스토리지, 네트워킹, 보안과 같은 서비스를 간편하게 오케스트레이션할 수 있습니다. 

컨테이너는 마이크로서비스 기반 애플리케이션에 이상적인 애플리케이션 배포 유닛 및 독립적인 실행 환경을 제공합니다. 동일한 하드웨어의 마이크로서비스에서 애플리케이션의 여러 부분들을 독립적으로 실행시키고 개별 요소 및 라이프사이클을 더욱 효과적으로 제어할 수 있습니다.

컨테이너 오케스트레이션을 사용해 다음과 같은 태스크를 자동화하고 관리할 수 있습니다.

- 프로비저닝 및 배포
- 구성 및 일정 조정 
- 리소스 할당
- 컨테이너 가용성
- 인프라 전반의 워크로드 밸런싱을 기반으로 컨테이너 스케일링 또는 제거
- 로드 밸런싱 및 트래픽 라우팅 
- 컨테이너 상태 모니터링
- 실행될 컨테이너를 기반으로 애플리케이션 설정
- 컨테이너 간 상호 작용의 보안 유지

현재 DEIS, RANCHER, MESOS, MARATHON, Nomad, Docker SWARM, Kubernetes 와 같은 다양한 Container Orchestration이 존재합니다.
그 중에서 **Kurbernetes**가 사실상 표준이 되었습니다.

## Kubernetes 기능
컨테이너는 애플리케이션을 포장하고 실행하는 좋은 방법이다. 프로덕션 환경에서는 애플리케이션을 실행하는 컨테이너를 관리하고 가동 중지 시간이 없는지 확인해야 한다. 예를 들어 컨테이너가 다운되면 다른 컨테이너를 다시 시작해야 한다. 
이 문제를 시스템에 의해 처리하고자 쿠버네티스가 필요한것이다.
쿠버네티스는 분산 시스템을 탄력적으로 실행하기 위한 프레임 워크를 제공한다. 애플리케이션의 확장과 장애 조치를 처리하고, 배포 패턴 등을 제공한다. 
예를 들어, 쿠버네티스는 시스템의 카나리아 배포를 쉽게 관리 할 수 있다.

쿠버네티스는 다음을 제공한다.
- **서비스 디스커버리와 로드 밸런싱** 쿠버네티스는 DNS 이름을 사용하거나 자체 IP 주소를 사용하여 컨테이너를 노출할 수 있다. 컨테이너에 대한 트래픽이 많으면, 쿠버네티스는 네트워크 트래픽을 로드밸런싱하고 배포하여 배포가 안정적으로 이루어질 수 있다.
- **스토리지 오케스트레이션** 쿠버네티스를 사용하면 로컬 저장소, 공용 클라우드 공급자 등과 같이 원하는 저장소 시스템을 자동으로 탑재 할 수 있다.
- **자동화된 롤아웃과 롤백** 쿠버네티스를 사용하여 배포된 컨테이너의 원하는 상태를 서술할 수 있으며 현재 상태를 원하는 상태로 설정한 속도에 따라 변경할 수 있다. 예를 들어 쿠버네티스를 자동화해서 배포용 새 컨테이너를 만들고, 기존 컨테이너를 제거하고, 모든 리소스를 새 컨테이너에 적용할 수 있다.
- **자동화된 빈 패킹(bin packing)** 컨테이너화된 작업을 실행하는데 사용할 수 있는 쿠버네티스 클러스터 노드를 제공한다. 각 컨테이너가 필요로 하는 CPU와 메모리(RAM)를 쿠버네티스에게 지시한다. 쿠버네티스는 컨테이너를 노드에 맞추어서 리소스를 가장 잘 사용할 수 있도록 해준다.
- **자동화된 복구(self-healing)** 쿠버네티스는 실패한 컨테이너를 다시 시작하고, 컨테이너를 교체하며, '사용자 정의 상태 검사'에 응답하지 않는 컨테이너를 죽이고, 서비스 준비가 끝날 때까지 그러한 과정을 클라이언트에 보여주지 않는다.
- **시크릿과 구성 관리 쿠버네티스**를 사용하면 암호, OAuth 토큰 및 SSH 키와 같은 중요한 정보를 저장하고 관리 할 수 있다. 컨테이너 이미지를 재구성하지 않고 스택 구성에 시크릿을 노출하지 않고도 시크릿 및 애플리케이션 구성을 배포 및 업데이트 할 수 있다.




## Kurnernetes Architecture
쿠버네티스를 배포하면 클러스터를 얻는다.

쿠버네티스 클러스터는 컨테이너화된 애플리케이션을 실행하는 노드라고 하는 워커 머신의 집합. 모든 클러스터는 최소 한 개의 워커 노드를 가진다.

워커 노드는 애플리케이션의 구성요소인 파드를 호스트한다. 컨트롤 플레인은 워커 노드와 클러스터 내 파드를 관리한다. 프로덕션 환경에서는 일반적으로 컨트롤 플레인이 여러 컴퓨터에 걸쳐 실행되고, 클러스터는 일반적으로 여러 노드를 실행하므로 내결함성과 고가용성이 제공된다.


아래는 모든 컴포넌트가 함께 있는 쿠버네티스 클러스터 다이어그램을 보여준다.
![쿠버네티스의 컴포넌트](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)


## Kubernetes 활용 예시
Kubernetes는 세계적으로 유명한 기업들에서 사용되고 있으며, 다양한 분야에서 활용이 되고 있다.
![image](https://user-images.githubusercontent.com/23617635/150284487-69edb9da-9c56-48a9-aa74-0ed8f2c97859.png)
