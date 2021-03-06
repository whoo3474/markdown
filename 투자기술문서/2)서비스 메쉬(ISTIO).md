# Service Mesh 개요
## Microservice 와 Monolithic
![service](https://www.researchgate.net/profile/Anna-Rozeva/publication/337245580/figure/fig1/AS:837788945625095@1576755858555/Monolithic-Architecture-versus-Microservices-Architecture-image-taken-from.png)
전통적인 애플리케이션 구축 방식에서는 모놀리식(Monolithic)에 중점을 두었으며 애플리케이션에서 구축 가능한 모든 부분이 하나의 애플리케이션에 포함되어 있었습니다.
이 방식의 단점은 애플리케이션이 커질수록 새로운 문제를 해결하고 새로운 기능을 추가하는 것이 어려워진다는 점이었습니다. 
마이크로서비스 기반 애플리케이션 구축 방법은 이러한 문제를 해결하고 개발 및 대응 속도를 가속화할 수 있습니다.
## Micro Service 시대의 도래
![image](https://user-images.githubusercontent.com/23617635/150291582-4b5c16de-3408-4629-9a5d-b09940afb033.png)
마이크로서비스란 소프트웨어를 구축하기 위한 아키텍처이자 하나의 접근 방식으로, 애플리케이션을 상호 독립적인 최소 구성 요소로 분할합니다. 모든 요소를 하나의 애플리케이션에 구축하는 전통적인 모놀리식 접근 방식 대신 마이크로서비스에서는 모든 요소가 독립적이며 연동되어 동일한 태스크를 완수합니다. 이러한 각각의 구성 요소 또는 프로세스가 마이크로서비스입니다. 소프트웨어 개발에 대한 이러한 접근 방식은 세분화, 경량화되어 있으며 다수의 애플리케이션 간에 유사한 프로세스를 공유하는 기능을 중시합니다.

개발자의 목표는 고품질 소프트웨어를 보다 신속하게 제공하는 것입니다. 
마이크로서비스는 이 목표를 실현하기 위한 수단이 되며, 이때 고려해야 할 사항이 있습니다. 
바로 애플리케이션을 마이크로서비스로 분할하는 것으로는 충분하지 않다는 사실입니다. 
애플리케이션을 관리하고 오케스트레이션하며 여기서 생성되고 수정되는 데이터를 처리해야 합니다.
그러므로써 나오게 된것이 Kubernetes 같은 Container Orchestration 이며, Service Mesh인 Istio등이 탄생하게 되었습니다. 

## Service Mesh 구조
Service Mesh Architecture는 MicroService Architecture로의 전환이 가속화 되면서, 내부 서비스간의 Orchestration 뿐 아니라, 서비스 레지스트리, 중앙 로그 수집, 분산 트렌젝션 추적, 메세지 라우팅등 다양한 기능들이 필요하게되었는데,
이러한 구현은 ESB에서 근래에 API GW 등을 사용하는 접근으로 바뀌었지만, 적당한 솔루션이 없고, 중앙 집중화된 솔루션으로 인한 장애와 운영 복잡도에 따라 중앙 집중형이 아닌 Proxy 서버를 각 서비스 앞에 배치 시키는 형태의 접근 방법이 대두되고 있는데, 이를 service mesh라고 하며, 이에 대한 구현체로는 Isitio나 linkerd와 같은 오픈 소스 프로젝트들이 진행되고 있다.

![image](https://miro.medium.com/max/703/0*XnD9vnWjjQSpAe5I.png)


## Service Mesh는 왜필요한가?
마이크로 서비스에서의 수많은 네트워크 환경이나 통신을 처리하기위하여, Control Plane과 각 마이크로 서비스마다 프록시 서버를 배치한 네트워크 인프라 계층을 구축하게 되었습니다.
마이크로 서비스 환경에서 가장 중요한점은 각각의 마이크로 서비스간의 통신이 원활하게 이루어져야 한다는 것입니다.
큰 어려움이 없겠지만 마이크로 서비스가 수십, 수백개가 넘어가고 사용자의 증가로인해 더 늘어난다면
그때는 서비스 내부의 통신 로직을 개발자가 모두 구현할수 없을것입니다.


서비스메쉬에서는 요청이 프록시를 통해 마이크로 서비스간의 라우팅됩니다. 
이렇게 서비스 메쉬를 구성하는 개별 프록시는 마이크로 서비스 내부 실행이아닌 따로 실행이 되므로 Sidecar 라고 부르기도 합니다.
만약 서비스메쉬가 없다면 위에서 설명했듯이 MSA간 통신 로직을 모두 서비스 내부에 구현해야 해서 실제로 서비스 해야하는 비즈니스 로직에 충실하지 못하게되고 마이크로 서비스 간 통신의 장애나 추적을하기가 어려워지기 때문에 사용하는것입니다.

![serviceMesh2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLJ2zW%2FbtqEh8ekYCc%2FVtpbr8Au4YsiqvrCbBRhPk%2Fimg.png)


## 사이드카 패턴
이 패턴은 오토바이에 연결된 사이드카와 유사하므로 사이드카라고 합니다. 패턴에서 사이드카는 상위 애플리케이션에 연결되고 애플리케이션에 대한 지원 기능을 제공합니다. 또한 사이드카는 상위 애플리케이션과 동일한 수명 주기를 공유하므로 상위 애플리케이션과 함께 만들어지고 사용 중지됩니다.
![sideCar](https://www.istioworkshop.io/images/sidecar-oriented-pattern.png)

# ISTIO
Istio는 컨테이너의 연결, 모니터 및 보안을 Kubernetes 클러스터에서 실행하는 구성 가능한 오픈 소스 서비스 메시 계층입니다.

## 모니터링 및 관측성
DevOps 팀과 IT 관리자는 트래픽을 관찰하여 대기 시간, 서비스 시간, 오류(트래픽 백분율로서) 등을 확인하고자 할 수 있습니다. 종종 이들은 대시보드를 보고자 합니다. 대시보드에서는 아마도 특정 노드, 서비스 또는 팟(Pod)으로 "드릴다운"하는 기능을 통해 시간의 흐름에 따라 해당 메트릭 또는 합계 또는 평균의 시각화를 제공합니다. Kubernetes는 이 기능을 기본적으로 제공하지 않습니다.

## 정책
기본적으로 Kubernetes는 모든 팟(Pod)이 다른 모든 팟(Pod)에 트래픽을 전송할 수 있도록 허용합니다. Istio를 사용하여 관리자는 서로 간에 작업할 수 있는 서비스를 제한하는 정책을 작성할 수 있습니다. 따라서 예를 들어 서비스는 진정한 종속 항목인 다른 서비스만 호출할 수 있습니다. 서비스 구동을 유지하는 다른 정책은 속도 제한적이며, 이는 과잉 트래픽이 서비스를 방해하지 못하게 하고 서비스 거부 공격을 차단합니다.

## 라우팅 및 로드 밸런싱
기본적으로, Kubernetes는 라운드 로빈 로드 밸런싱을 제공합니다. 마이크로서비스를 제공하는 여섯 개의 팟(Pod)이 있는 경우, Kubernetes는 오름차순으로 각 팟(Pod)에 요청을 전송하는 로드 밸런서 또는 "서비스"를 제공한 후 다시 시작합니다. 그러나 종종 한 회사가 프로덕션에서 동일한 서비스의 서로 다른 버전을 배치하기도 합니다.

이의 가장 간단한 예는 파란색/녹색 배치일 수 있습니다. 이 경우에 소프트웨어는 프로덕션 사용자를 이에 보내지 않고도 프로덕션에서 완전히 새 버전의 애플리케이션을 구축할 수 있습니다. 새로운 버전을 프로모션한 후에 회사는 장애 발생 시의 빠른 스위치백을 위해 기존 서버를 계속 유지할 수 있습니다.

Istio를 사용하면 이는 구성 파일에서 태깅을 사용하는 것처럼 간단합니다. 또한 관리자는 헤더에 기반하여 규칙를 구축하고 연결할 서비스의 유형을 표시하기 위해 레이블을 사용할 수도 있습니다. 예를 들어 베타 사용자는 최신의 가장 큰 빌드를 사용하여 ‘canary’ 팟(Pod)을 라우팅하는 한편, 일반 사용자는 안정된 프로덕션 빌드로 이동할 수 있습니다.

## 서킷 브레이킹
서비스가 과부하되거나 작동 중지된 경우 시스템 과부하가 지속되는 동안 추가 요청이 실패합니다. Istio가 오류와 지연을 추적하기 때문에, 이는 정책에 의해 설정된 특정 수의 요청 이후 일시정지를 강제 실행하여 서비스의 복구를 허용할 수 있습니다. 작은 텍스트 파일을 작성하고 이를 새 정책으로 사용하도록 Istio에 지시하여 전체 클러스터에서 이 정책을 시행할 수 있습니다.

## 보안
Istio는 인증, 권한 부여 및 감사(AAA)와 함께 기본적으로 ID, 정책 및 암호화를 제공합니다. 기타 대상과 통신하는 관리 중인 모든 팟(Pod)은 암호화된 트래픽을 사용하여 관찰을 방지합니다. 암호화와 결합된 ID 서비스는 인가되지 않은 사용자가 서비스 호출을 위조 또는 "도용"할 수 없도록 보장합니다. AAA는 보안 및 운영 전문가에게 적은 오버헤드로 모니터에 필요한 툴을 제공합니다.

## 관리의 단순화
기존 애플리케이션에서는 Istio에서 제공하는 ID, 정책 및 보안 기능이 여전히 필요합니다. 
여기에는 모든 서비스에 대해 동일한 보안 규칙을 반복하여 재구현하면서 잘못된 추상 레벨에서 작업하고 있는 프로그래머와 관리자가 있습니다. 
Istio를 사용하여 이들은 단일 제어 패널을 통해 클러스터에 대한 올바른 레벨 설정 정책에서 작업할 수 있습니다. 
이와 동시에, 아래에서 설명하는 Istio의 액세스 제어, 대시보드 및 디버깅 툴을 사용하여 사용자는 웹 페이지로 이동하는 대신 명령행에서 플러그인을 손쉽게 추가할 수 있습니다.
![istioArch](https://istio.io/latest/img/service-mesh.svg)

