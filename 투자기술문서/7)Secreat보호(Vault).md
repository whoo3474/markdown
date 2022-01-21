# Vault
Vault는 identity-based secrets 및 암호화 관리 시스템이다. 
Secret은 API 암호화 키, 암호 또는 인증서와 같이 액세스를 엄격하게 제어하고자 하는 모든 것이다. 
Vault는 인증 및 인증 방법으로 게이트된 암호화 서비스를 제공한다. 
Vault의 UI, CLI 또는 HTTP API를 사용하여 Secret 및 기타 중요한 데이터에 대한 액세스를 안전하게 저장 및 관리하고, 엄격하게 제어(제한)하며, 감사할 수 있다.

현대 시스템은 데이터베이스 인증 정보, 외부 서비스를 위한 API 키, 서비스 지향 아키텍처 통신을 위한 인증 정보 등 수많은 Secret에 대한 액세스를 요구한다. 
누가 어떤 Secret에 접근하고 있는지 이해하는 것은 이미 매우 어렵고 플랫폼마다 다르다. 
key rolling, 보안 스토리지 및 세부 감사 로그에 추가하는 것은 맞춤형 솔루션 없이는 거의 불가능하다. 
이러한 상황에 Vault가 들어가는 것이다.

# 주요 기능
- Secure Secret Storage: 임의 키/값 Secret은 Vault에 저장할 수 있다. Vault는 persistent storage에 쓰기 전에 이러한 암호를 암호화하므로 raw storage에 액세스할 수 있는 것만으로는 Secret에 액세스할 수 없다. Vault는 disk, Consul 등에 쓸 수 있다.

- Dynamic Secrets: Vault는 AWS 또는 SQL 데이터베이스와 같은 일부 시스템에 대해 필요에 따라 Secret을 생성할 수 있다. 예를 들어 애플리케이션이 S3 버킷에 액세스해야 하는 경우 Vault에 인증 정보를 요청하면 Vault는 요청 시 유효한 사용 권한이 있는 AWS 키페어를 생성하게 된다. 이러한 Dynamic Secret을 만든 후, Vault는 lease 기간이 끝나면 자동으로 해당 Secret을 해지할 것이다.

- Data Encryption: Vault는 데이터를 저장하지 않고도 데이터를 암호화하고 해독할 수 있다. 이를 통해 보안 팀은 암호화 매개변수를 정의하고 개발자는 자체 암호화 방법을 설계할 필요 없이 SQL과 같은 위치에 암호화된 데이터를 저장할 수 있다.

Leasing and Renewal: Vault의 모든 Secret에는 lease가 연결되어 있다. lease 기간이 끝나면 Vault는 자동으로 그 Secret을 파기할 것이다. 고객은 내장된 Renewal API를 통해 lease를 Renewal할 수 있다.

Revocation: Vault는 secret revocation를 위한 지원을 built-in하고 있다. Vault는 하나의 Secret뿐 아니라, 특정 사용자가 읽은 모든 secret, 또는 특정 유형의 모든 secret을 취소할 수 있다. key rolling은 물론 침입 시 시스템을 잠그는 데 도움이 된다.

# 구조
Vault에는 외부 시스템과 통합할 수 있는 Secret 엔진 및 인증 방법 이라고 하는 다양한 연결 가능한 구성 요소가 함께 제공 된다. 
이러한 구성 요소의 목적은 동적 인프라(예: 데이터베이스 자격 증명, 암호, API 키)에서 Secret을 관리하고 보호된다.
![vault](https://learn.hashicorp.com/img/vault/vault-triangle.png)


# 사용 사례
## General Secret Storage
최소한 Vault는 모든 Secret을 저장하는 데 사용될 수 있다. 
예를 들어 Vault는 중요한 환경 변수, 데이터베이스 자격 증명, API 키 등을 저장하는 환상적인 방법이 될 수 있다.

파일, 구성 관리, 데이터베이스 등에 일반 텍스트가 될 수 있는 현재 저장 방법과 비교하였을때, vault read 또는 API 사용하여 query 하는 것이 훨씬 더 안전할 것이다. 
이렇게 하면 이러한 비밀의 일반 텍스트 버전은 물론 Vault 감사 로그의 레코드 액세스를 보호할 수 있습니다

## Employee Credential Storage
이는 "일반 Secret 저장소"와 중복되지만 Vault는 직원들이 웹 서비스에 액세스하기 위해 공유하는 자격 증명을 저장하는 좋은 메커니즘이다. 
감사 로그 메커니즘은 직원이 접근한 Secret과 직원이 퇴사할 때 키를 되돌리는것이 더 쉬우며 어떤 키가 되돌려져 있고 되돌려지지 않았는지를 이해하는 데 도움이 된다.

## API Key Generation for Scripts
Vault의 "dynamic secrets" 기능은 스크립트에 이상적이며, AWS 액세스 키는 스크립트 기간 동안 생성되었다가 취소될 수 있다. 
키페어는 스크립트가 실행되기 전이나 후에 존재하지 않으며, 키의 생성이 완전히 기록된다.

이는 AWS IAM 같은 것을 사용하는 것에 비해 개선된 것이지만 여전히 다양한 장소에서 효과적으로 제한된 액세스 토큰을 하드코딩하는 것이다.

## Data Encryption
Vault는 Secret을 저장할 수 있을 뿐만 아니라 다른 곳에 저장된 데이터를 암호화/암호 해독하는 데도 사용할 수 있다. 
이것의 주요 용도는 애플리케이션이 데이터를 주 데이터 저장소에 저장하면서 데이터를 암호화할 수 있도록 하는 것이다.

이를 통해 개발자가 데이터를 적절하게 암호화하는 방법에 대해 걱정할 필요가 없다는 장점이 있다. 
암호화의 책임은 Vault와 보안 팀이 관리하며, 개발자는 필요에 따라 데이터를 암호화/암호화하기만 하면 된다.