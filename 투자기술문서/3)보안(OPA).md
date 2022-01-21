# OPA?
OPA(Open Policy Agent)는 정책을 결정하는 부분과 수행하는 영역을 분리하기위해 시작되었고, 전체 시스템에 걸쳐 통합된 상황에 따른 정책 시행을 가능하게 하는 오픈 소스 범용 정책 엔진이 되었다. OPA는 현재 CNCF의 졸업 프로젝트(graduated project)로 클라우드 네이티브 정책의 사실상 표준으로 자리잡고 있다. 실제로 넷플릭스, 핀터레스트, 골드만삭스 등에서 쿠버네티스 및 마이크로서비스에 대한 정책엔진으로 사용되고 있으며 아틀라시안 제품군이나 Chef에도 포함되어있다.


## 동작방식
![OPA](https://d33wubrfki0l68.cloudfront.net/b394f524e15a67457b85fdfeed02ff3f2764eb9e/6aeb1/docs/v0.28.0/images/opa-service.svg)

OPA는 정책 및 데이터에 대한 질의를 평가하여 정책의 결정을 돌려준다. 여기에서 질의(Query)와 결정(Decision)은 json으로 표현하면 어떤 것이든 포함할 수 있으며 이에따라 매우 큰 확장성을 갖는다.

정책은 전용 언어(Rego)를 사용하여 기술하는데 도메인에 제한 받지 않는 (거의) 모든 종류의 정책의 기술이 가능하다.

질의의 예는 다음과 같다.

- 어떤 사용자가 어떤 리소스에 액세스할 수 있는가?
- 어떤 서브넷에 외부통신이 허용되는가?
- 어떤 클러스터로 워크로드를 배포해야 할 것인가?
- 어떤 레지스트리로부터 바이너리를 다운로드할 수 있는가?
- 어떤 OS 기능이 컨테이너와 함께 실행되는가?
- 어떤 시간에 시스템에 접근할 수 있는가?
- 결정도 단순 '예/아니오' 뿐 아니라 임의의 구조화된 데이터를 출력으로 사용할 수 있다.

데이터는 Json 형태로 저장되며 OPA 서버는 정책(Policy)을 기반으로 판단하는데 질의와 함께 이 데이터를 활용하여 결정을 만든다.

# 가상 시나리오
가상 환경을 정의하고 이 환경에서 OPA가 사용되는 시나리오를 통해 OPA를 더 구체적으로 이해해 보자.

 

## 가상환경 정의 및 입력데이터 생성
본 단락에서는 입력데이터를 만드는 구체적인 예를 보여준다. 다음 그림과 같은 시스템이 있다고 가정하자.

0 개 이상의 프로토콜을 받는 서버들이 존재 (X,... Y, Z..)
공용이나 사설망에 서버들을 연결하는 네트워크 (public, .... private)
네트워크에 서버를 연결시키는 포트 (A, ... X, Y,.... Z)

![example](https://d33wubrfki0l68.cloudfront.net/ccaa16878b16f07b8f03403662cb483decb11389/680b1/docs/v0.28.0/images/system.svg)


![example2](https://devocean.sk.com/CKFinderJava/userfiles/images/opa3(2).png)

위 시스템은 다음과 같이 json으로 표현할 수 있고 이후 설명에서 입력데이터로 사용할 것이다.
```json
{
    "servers": [
        {"id": "app", "protocols": ["https", "ssh"], "ports": ["p1", "p2", "p3"]},
        {"id": "db", "protocols": ["mysql"], "ports": ["p3"]},
        {"id": "cache", "protocols": ["memcache"], "ports": ["p3"]},
        {"id": "ci", "protocols": ["http"], "ports": ["p1", "p2"]},
        {"id": "busybox", "protocols": ["telnet"], "ports": ["p1"]}
    ],
    "networks": [
        {"id": "net1", "public": false},
        {"id": "net2", "public": false},
        {"id": "net3", "public": true},
        {"id": "net4", "public": true}
    ],
    "ports": [
        {"id": "p1", "network": "net1"},
        {"id": "p2", "network": "net3"},
        {"id": "p3", "network": "net2"}
    ]
}            
```

## 요구사항 및 이를 위한 정책생성
여기에서 아래와 같은 요구사항이 있고 이에따라 정책을 적용해야한다고 가정하자.

>1. 인터넷에서 접속가능한 서버들은 http를 노출해서는 안됨
>2. 모든 서버들은 telnet을 노출해서는 안됨

```rego
package example

default allow = false                               # 기본적으로 허용되지 않는다.

allow = true {                                      # 위반이 하나도 없다면 허용한다.
    count(violation) == 0
}

violation[server.id] {                              # 서버는 다음의 경우에 위반
    some server                                     # 모든 서버를 대상으로
    public_server[server]                           # public_server 함수가 를 통해 설정되어 있거나
    server.protocols[_] == "http"                   # http 프로토콜을 포함하고 있다면
}

violation[server.id] {                              # 서버는 다음의 경우에 위반 (앞의 룰과 or조건)
    server := input.servers[_]                      # input에 있는 모든 서버들에 대해
    server.protocols[_] == "telnet"                 # telnet 프로토콜을 포함하고 있다면
}

public_server[server] {                             # 서버가 public에 연결되었는지 체크
    some i, j
    server := input.servers[_]                      # input에 있는 모든 서버를 대상으로
    server.ports[_] == input.ports[i].id            # 모든 포트들의 id를 기준으로
    input.ports[i].network == input.networks[j].id  # 그 포트의 네트워크를 찾아서 
    input.networks[j].public                        # 그 네트워크가 public 이면
}
```

정책의 OUTPUT으로는 허용되지않고, busybox와 ci가 위반을 하였다고 나온다.
```json
{
    "allow": false,
    "public_server": [
        {
            "id": "app",
            "ports": [
                "p1",
                "p2",
                "p3"
            ],
            "protocols": [
                "https",
                "ssh"
            ]
        },
        {
            "id": "ci",
            "ports": [
                "p1",
                "p2"
            ],
            "protocols": [
                "http"
            ]
        }
    ],
    "violation": [
        "busybox",
        "ci"
    ]
}
```