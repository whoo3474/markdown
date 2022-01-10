>https://ikcoo.tistory.com/101



## pause?
POD 안의 컨테이너 들에게 namespace 상속을 위해 있는 container
> 한 ip가 공유되는 이유

-------------------------------------------------

## kubernetes 네트워크?
는 Default 가 docker0 이다. (브릿지 네트워크)

서로 노드의 네트워크가 겹칠수가 있음.
방지하기위해 CNI (container network interface)를 사용하여, 서로 다른 Subnet을 할당해준다.
ex - flanner, 

------------------------------

# 배포시 사용하는 네트워크

## port 정리

NodePort 
 - 외부에서 접속하기 위해 사용하는 포트

port
 - Cluster 내부에서 사용할 Service 객체의 포트

targetPort
 - Service객체로 전달된 요청을 Pod(deployment)로 전달할때 사용하는 포트

​
전체 서비스 흐름으로 보면 NodePort --> Port --> targetPort 



## 예시 

- 아래 Service 리소스에서 정의한 port의 흐름을 그림과 같이 표현 가능함. 

```shell
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  ports:
  - nodePort: 31001
    port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: helloworld
  type: NodePort
```

![image](https://user-images.githubusercontent.com/23617635/143827470-aa8fb1e3-f7ba-435f-ae6d-cb4302e42007.png)


​>https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=freepsw&logNo=221910012471
