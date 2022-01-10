> 출처 :https://velog.io/@seunghyeon/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B5%AC%EC%84%B1%EB%8F%84

>https://speakerdeck.com/devinjeon/kubernetes-neteuweokeu-ihaehagi-1-keonteineo-neteuweokeubuteo-cniggaji

```
Flowchart Orientation
Possible FlowChart orientations are:

TB - top to bottom
TD - top-down/ same as top to bottom
BT - bottom to top
RL - right to left
LR - left to right
```


# Docker

## network가 어떻게되어있나?


## 멀티 컨테이너 구조에서는 docker-compose를 써야하는 이유?

## docker-compose와 kubernetes 네트워크는 뭐가 다른건가?


<div class="mermaid">
flowchart TD
    subgraph host
        subgraph container
            container_eht0[eht0: 172.17.0.2]
        end
        container_eht0---veth
        veth---docker0
        docker0---router{router}
        router---host_eht0[eht0: 172.17.0.2]
    end

    style host fill:#ffffff
    style container fill:#ffffff
</div>

> 사진을 그대로 쓰는것보다는 직접 그리는걸 습관화 해볼까 해서 사용해봅니다.

![image](https://user-images.githubusercontent.com/23617635/144003219-1812c87e-994d-43aa-9f92-2d50d20d8396.png)