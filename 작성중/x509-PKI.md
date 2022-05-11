>https://gruuuuu.github.io/security/what-is-x509/

https://coffeewhale.com/kubernetes/authentication/x509/2020/05/02/auth01/

https://coffeewhale.com/kubernetes/authentication/http-auth/2020/05/03/auth02/

https://coffeewhale.com/kubernetes/authentication/oidc/2020/05/04/auth03/

한마디로 x509란 ITU-T가 만든 PKI(Public Key Infrastructure, 공개키기반구조)의 표준입니다.

# PKI란?
PKI-공개키기반구조(Public Key Infrastructure)는 기본적으로 인터넷과 같이 안전이 보장되지 않은 공개된 망 사용자들이 한쌍의 공개키와 개인키를 사용함으로써, 안전하게 데이터를 교환할 수 있게 합니다.


앞서 말씀드린대로 공개키는 말그대로 공개되어있기 때문에 전자서명의 경우 해당 공개키가 과연 내가 원하는 공개키가 맞는지 의심할 수가 있습니다.
신뢰할 수 있는 인증기관(CA:Certificate Authority)가 등장하게 됩니다.

CA(Certificate Authority)Permalink
공인 인증 기관은 인증 정책을 수립하고, 인증서발급과 관리역할을 하며 다른 CA와 상호 인증을 제공합니다.

다시 풀어서 설명하면, CA에서는 개인키와 공개키 쌍을 만들고, 개인키를 자신이 만들었다고 인증서를 발행하게 됩니다.

즉, Private key를 보증하기 위해 쌍이되는 Public Key, 그리고 인증기관이 정식으로 발행했다는 것을 알리기 위해 공인 인증 기관의 전자 서명이 인증서에 들어있습니다.

그런데 이 때 인증서를 작성하는 인증기관마다 상이하면 인증서의 내용을 이해하는데 문제가 있을 것입니다.
이러한 인증서들의 표준이 되는 규칙이 있어야 하는데, 현재 가장 널리 사용되고 있는 PKI의 표준은 X509입니다.