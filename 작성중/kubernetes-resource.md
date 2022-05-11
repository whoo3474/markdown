
# Node의 QoS

쿠버네티스의 노드에는 각종 노드의 이상 상태 정보를 의미하는 Conditions라는 값이 존재합니다.



쿠버네티스에서는 Pod의 Limit와 Request에 따라서 QoS 클래스를 지정합니다.

QoS클래스의 종류는 BestEffort, Burstable, Guaranteed 3가지가 존재합니다.

https://no-easy-dev.tistory.com/entry/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-Pod-%EC%9E%90%EC%9B%90%EA%B4%80%EB%A6%AC-QoS?category=949859


# request, limits - best practices
https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-resource-requests-and-limits

구글 문서에 따르면 데이타 베이스등 아주 무거운 애플리케이션이 아니면, 일반적인 경우에는 CPU request를 100m 이하로 사용하기를 권장한다. 

또한 세밀하게 클러스터를 운영하기 어려운 경우에는 request와 limit의 사이즈를 같게 하는 것을 권장한다. limit이 request보다 클 경우 overcommitted 상태가 발생할 수 있는데, 이때 CPU가 throttle down 되면, 실제 필요한 CPU양 보다 작은 CPU양으로 줄어들기 때문에 성능저하가 발생할 수 있다.  

> 보안. bett practices
> https://bcho.tistory.com/1290
