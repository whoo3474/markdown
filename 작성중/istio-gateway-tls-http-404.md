> https://istio.io/v1.7/docs/ops/common-problems/network-issues/#404-errors-occur-when-multiple-gateways-configured-with-same-tls-certificate




각 VirtualService별로 내가 원하는 서브 도메인을 여러개 만들어서, 각각의 gateway를 따로 설정하여 TLS 인증서를 적용시켜 주었더니, 하나의 도메인만 접속이 가능하고, 나머지는 접속이 불가능한 문제가 생기었다.
처음에는 각 서비스별 (harbor, argocd, grafana, kibana ..등)의 yaml파일에 환경설정이나 Service설정을 잘못하여 작동을 안하는줄 알았으나, 혹시나 싶어서 하단의 명령어를 작동 시켜보니, 뚜렷한 문제가 나왔다.
```shell
istioctl analyze

Warning [IST0138] (Gateway metric-monitoring/metric-gateway) Duplicate certificate in multiple gateways [metric-monitoring/metric-gateway argocd/argocd-dev-gateway] may cause 404s if clients re-use HTTP2 connections.
```



동일한 TLS 인증서를 사용하여 둘 이상의 게이트웨이를 구성하면 HTTP/2 연결 재사용을 활용하는 브라우저(즉, 대부분의 브라우저)가 이미 다른 호스트에 대한 연결이 설정된 후 두 번째 호스트에 액세스할 때 404의 오류를 생성한다고 한다.


해결 하고자 한다면, 내가 가진 도메인의 모든 경로를 받을수 있는 하나의 hosts를 gateway로 단 하나만 설정해주고

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: dev-gateway
  namespace: default
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - hosts:
      - "*.dev.xyz"
    port:
      number: 443
      name: https-dev
      protocol: HTTPS
    tls:
      credentialName: dev-tls
      mode: SIMPLE
  - hosts:
      - "*.dev.xyz"
    port:
      name: http-dev
      number: 80
      protocol: HTTP
    tls:
      httpsRedirect: true 
```


각각의 여러 VirtualService에서 단 하나의 gateway를 연결시켜, 내가 원하는 서브 도메인을 입력하여 사용하면 된다.

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: dev-vs
  namespace: default
spec:
  hosts:
    - "eks.dev.xyz"
  gateways:
    - default/dev-gateway
  http:
  - name: "eks-routes"
    route:
      - destination:
          host: development
          port:
            number: 443
```