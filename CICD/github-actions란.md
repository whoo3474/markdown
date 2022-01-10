>출처:
>https://docs.github.com/en/actions
>https://velog.io/@ggong/Github-Action%EC%97%90-%EB%8C%80%ED%95%9C-%EC%86%8C%EA%B0%9C%EC%99%80-%EC%82%AC%EC%9A%A9%EB%B2%95
>https://meetup.toast.com/posts/286
>https://www.sunny-son.space/AWS/Github%20Action%20CICD/


# github-actions 이란?

## GitOps란?
GitOps는 인프라 구성을 위해 Git을 버전 제어 시스템으로 사용하는 코드형 인프라(IaC)가 진화한 것이라고 생각하면 됩니다. IaC는 원하는 시스템 상태를 정의하고 시스템의 실제 상태를 추적하여 인프라 관리에 대한 선언적 접근 방식을 따르는 경우가 많습니다.

IaC로 관리되기때문에, 시간의 흐름에따른 버전관리가 가능하다.
그렇기에 모든 버전에따른 롤백이 가능하다.
빌드 테스트, 배포등을 순차적으로 진행이 가능하다.
여러 운영체제(windows, linux, macos) 및 런타임 버전(nodejs, java ...등)에서 동시 테스트가 가능하다.

## CI/CD란?
CI는 Continuous Integration(지속적 통합), CD는 Continuous Delivery(지속적 전달)의 줄임말이다.

- CI : 테스트, 빌드, Dockerizing, 저장소에 전달하는 것까지 프로덕션 환경으로 서비스를 배포할 수 있도록 준비하는 프로세스
- CD : 저장소로 전달된 프로덕션 서비스를 실제 사용자들에게 배포하는 프로세스

작업한 소스 코드를 빌드하고, 저장소에 전달 후 배포까지 하는 과정을 통상적으로 CI/CD라고 부른다.

---------------------------------------------

# github-actions 스팩
github actions의 컴퓨터 스펙은 이하와 같다.
Github-Hosted Runner는 마소에서 지원해주는 Azure의 가상 머신 위에서 돌아갑니다. 하지만 가상 머신의 사양은 그리 좋진 않습니다.

>2-core CPU
7 GB of RAM memory
14 GB of SSD disk space


Github Actions는 Runner 위에서 실행이 되고, Runner는 가상 머신 위에서 실행이 됩니다. Github에서 두 종류의 Runner를 제공합니다. Github-hosted Runner, Self-Hosted Runner(사용자가 직접 호스팅하는 환경)가 있습니다.

---------------------------------------------

# github-action 가격
>https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions

공개 저장소 경우에는 무료이며, 비공개 저장소는 계정에 부여한 무료 사용량 이후에 과금이 됩니다. Workflow는 저장소마다 최대 20개까지 등록이 가능하며 Workflow의 Job이라는 단계마다 최대 6시간 동안 실행이 가능하며, 초과하면 자동으로 중지됩니다.
 
## 스토리지, 분당 
|상품	|스토리지 크기|	한달당 할당된 분|
|------|---------|--------------|
|GitHub Free|	500 MB|	2,000|
|GitHub Pro|	1 GB|	3,000|
|GitHub Free for organizations|	500 MB|	2,000|
|GitHub Team|	2 GB|	3,000|
|GitHub Enterprise Cloud|	50 GB|	50,000|

## 분당 승수
분당 승수란?
리눅스는 1분당 1분
맥 OS는 1분당 10분
윈도우는 1분당 2분
을 차감하겠다는것이다.
PC방 유료게임할때 시간이 더 빨리 줄어드는 느낌
|운영 체제 | 분당 승수|
|--------|--------|
|Linux	|1|
|macOS	|10|
|Windows	|2|

## 1분당 가격
|운영 체제|	분당 요금(USD)|
|-------|-------------|
|Linux|	$0.008|
|macOS|	$0.08|
|windows|	$0.016|


---------------------------------------------

# github-action 용어
![image](https://user-images.githubusercontent.com/23617635/142808858-27020cdc-1f2d-49bc-a7e1-d6462276cd99.png)

## Workflow
자동화된 전체 프로세스. 하나 이상의 Job으로 구성되고, Event에 의해 예약되거나 트리거될 수 있는 자동화된 절차를 말한다.
Workflow 파일은 YAML으로 작성되고, Github Repository의 .github/workflows 폴더 아래에 저장된다. Github에게 YAML 파일로 정의한 자동화 동작을 전달하면, Github Actions는 해당 파일을 기반으로 그대로 실행시킨다.

## Event(on)
Workflow를 트리거(실행)하는 특정 활동이나 규칙이다.
예를 들어, 누군가가 커밋을 리포지토리에 푸시하거나 풀 요청이 생성 될 때 GitHub에서 활동이 시작될 수 있다.


## Job(jobs:{job})
워크플로우의 기본 단위라고 보면 되며 다시 더 작은 단위인 스텝(step)으로 이루어져 있다.
다른 Job에 의존 관계를 가질 수도 있고, 독립적으로 병렬로 실행될 수도 있다.
기본적으로 워크 플로우는 여러 작업을 병렬적으로 실행하며 순차적으로 실행하도록 설정할 수도 있다.
 예를 들어 빌드와 테스트 코드의 수행인 두 작업을 순차적으로 실행할 수도 있으며 이 경우에는 빌드 작업이 실패하면 테스트 작업은 실행되지 않는다.

## Steps(jobs:{job}:steps:{step})
Job 안에서 순차적으로 실행되는 프로세스 단위이다.
 step에서 명령을 내리거나, action을 실행할 수 있다.
작업에서 커맨드를 실행하는 독립적인 단위이다. 한 작업(job)의 각 스텝들은 동일한 러너에서 실행되므로 해당 작업의 액션들은 서로 데이터를 공유한다.

## Action(jobs:{job}:steps:{step}:{uses})
job을 구성하기 위한 step들의 조합으로 구성된 독립적인 명령이다. workflow의 가장 작은 빌드 단위이다. workflow에서 action을 사용하기 위해서는 action이 step을 포함해야 한다.
action을 구성하기 위해서 레포지토리와 상호작용하는 커스텀 코드를 만들 수도 있다. 
사용자가 직접 커스터마이징하거나, 마켓플레이스에 있는 action을 가져다 사용할 수도 있다.


## Runner(jobs:{job}:steps:runs-on)
Gitbub Action Runner 어플리케이션이 설치된 머신으로, Workflow가 실행될 인스턴스
