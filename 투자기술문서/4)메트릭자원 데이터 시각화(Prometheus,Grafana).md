# Prometheus
Prometheus 는 원래 SoundCloud 에서 구축된 오픈 소스 시스템 모니터링 및 경고 툴킷 이다. 2012년 시작된 이래 많은 회사와 조직이 Prometheus를 채택했으며 이 프로젝트에는 매우 활발한 개발자 및 사용자 커뮤니티 가 있다.
이제 독립 실행형 오픈 소스 프로젝트이며 어떤 회사와도 독립적으로 유지 관리된다. 
이를 강조하고 프로젝트의 거버넌스 구조를 명확히 하기 위해 Prometheus는 Kubernetes 에 이어 두 번째 호스팅 프로젝트로 2016년 Cloud Native Computing Foundation 에 합류하였다.

Prometheus는 메트릭을 시계열 데이터로 수집하고 저장한다. 
즉, 메트릭 정보는 레이블이라는 선택적 키-값 쌍과 함께 기록된 타임스탬프와 함께 저장된다.


![prometheus](https://prometheus.io/assets/architecture.png)

Prometheus는 단기 작업에 대해 직접 또는 중간 푸시 게이트웨이를 통해 계측 작업에서 메트릭을 수집한다. 
스크랩한 모든 샘플을 로컬에 저장하고 이 데이터에 대한 규칙을 실행하여 기존 데이터에서 새 시계열을 집계 및 기록하거나 경고를 생성한다. 
Grafana 또는 기타 API 소비자를 사용하여 수집된 데이터를 시각화할 수 있다.

## Prometheus 특징
- 메트릭 이름 및 키/값 쌍으로 식별되는 시계열 데이터가 있는 다차원 데이터 모델
- 이 차원을 활용 하는 유연한 쿼리 언어 인 PromQL
- 분산 스토리지에 의존하지 않음 단일 서버 노드는 자율적입니다.
- 시계열 수집은 HTTP를 통한 풀 모델을 통해 발생합니다.
- 푸시 시계열 은 중개 게이트웨이를 통해 지원됩니다.
- 대상은 서비스 검색 또는 정적 구성을 통해 검색됩니다.
- 여러 모드의 그래프 및 대시보드 지원

# Grafana
**Prometheus**도 자체 UI를 제공하고 있지만, 기능이 너무 빈약하고, 불편하기때문에 통상 Production 환경에서는 **Grafana**와 함께 연동하여 사용하는것이 일반적이다.
저장된 위치에 상관없이 메트릭 을 쿼리, 시각화, 경고를 하며,
특히 매트릭 데이터를 시각화하는데 가장 최적화된 대시보드를 제공해주는 오픈소스 툴킷이다.
특히 다양한 DB를 연결하여 DB의 데이터를 가져와 손쉽게 시각화할 수 있다.

![grafana](https://grafana.com/products/assets/grafana/Grafana8_Kubernetes.jpg)

## Grafana 이점
### Plugins
데이터 소스 플러그인은 API를 통해 기존 데이터 소스에 연결되며 데이터를 마이그레이션하거나 수집할 필요 없이 실시간으로 데이터를 렌더링한다.
![image](https://user-images.githubusercontent.com/23617635/150489026-a58a2a01-1f37-4d37-aef9-fc37816dfea3.png)

### Alerts
Grafana Alerting기능을 사용하면 하나의 간단한 UI 내에서 모든 경고를 생성, 관리 및 음소거할 수 있으므로 모든 경고를 쉽게 통합하고 중앙 집중화할 수 있다.
![image2](https://grafana.com/products/assets/grafana/alert-screenshot.png)

### Transformations
Transformations을 사용하면 서로 다른 쿼리 및 데이터 소스에  이름을 바꾸고, 요약하고, 결합하고, 계산을 수행할 수 있다.
![image3](https://user-images.githubusercontent.com/23617635/150489528-be6700ce-ddb5-4bcd-8226-63a03867d9dd.png)

### Annotations
서로 다른 데이터 소스의 다양한 이벤트 그래프에 주석을 달수있다. 이벤트 위로 마우스를 가져가면 전체 이벤트 메타데이터 및 태그가 표시된다.
![image4](https://user-images.githubusercontent.com/23617635/150489689-6c43477f-2a5b-4f8a-befa-c2c2cb54a90d.png)

### Panel editor
Panel editor를 사용하면 모든 시각화에서 데이터 옵션을 설정하기 위한 일관된 UI를 통해 모든 패널을 쉽게 구성, 사용자 지정 및 탐색할 수 있다.
![image5](https://user-images.githubusercontent.com/23617635/150490097-c984c904-0509-4365-8243-d04d731f1d0d.png)