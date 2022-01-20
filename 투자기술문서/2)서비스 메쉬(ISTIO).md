
# Service Mesh 개요
MSA로 전환이 되면서, 내부 서비스간의 Orchestration 뿐 아니라, 

서비스 레지스트리, 중앙 로그 수집, 분산 트렌젝션 추적, 메세지 라우팅등 다양한 기능들이 필요하게되었는데,

이러한 구현은 ESB에서 근래에 API GW 등을 사용하는 접근으로 바뀌었지만, 적당한 솔루션이 없고, 중앙 집중화된 솔루션으로 인한 장애와 운영 복잡도에 따라 중앙 집중형이 아닌 Proxy 서버를 각 서비스 앞에 배치 시키는 형태의 접근 방법이 대두되고 있는데, 이를 service mesh라고 하며,

이에 대한 실 구현체로는 Isitio나 linkerd 와 같은 오픈 소스 프로젝트들이 진행되고 있다.



# Micro Service 시대의 도래

모놀리식 구조

마이크로 서비스 구조


# Service Mesh는 왜필요한가?

![serviceMesh2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLJ2zW%2FbtqEh8ekYCc%2FVtpbr8Au4YsiqvrCbBRhPk%2Fimg.png)

# ServiceMesh 발전
![serviceMesh](https://www.istioworkshop.io/images/microservices-evolution.png)

# ISTIO 구조
![istioArch](https://istio.io/latest/img/service-mesh.svg)

# 사이드카 패턴
어플리케이션 컨테이너와 독립적으로 동작하는 별도의 컨테이너를 붙이는 패턴
![sideCar](https://www.istioworkshop.io/images/sidecar-oriented-pattern.png)
