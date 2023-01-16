# [NAT란?](https://youtu.be/Qle5cfCcuEY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### NAT란?

NAT(Network Address Translation)

**IP 패킷의 TCP/UDP 포트 숫자와 소스 및 목적지의 IP 주소 등을 재기록**하면서 라우터를 통해 네트워크 트래픽을 주고 받는 기술

→ 패킷에 변화가 생기기 때문에 IP나 TCP/UDP의 체크섬도 다시 계산되어 재기록

- 대개 사설 네트워크에 속한 여러 개의 호스트가 하나의 공인 IP 주소를 사용하여 인터넷에 접속하기 위함
- 꼭 사설 IP를 공인 IP로 변환 하는 데에만 사용하는 기술은 아님

# 포트 포워딩

### 포트포워딩이란?

포트 포워딩 또는 포트 매핑(port mapping)은 패킷이 라우터나 방화벽과 같은 네트워크 장비를 가로지르는 동안 **특정 IP 주소와 포트 번호의 통신 요청을 특정 다른 IP와 포트 번호로 넘겨주는** 네트워크 주소 변환(NAT)의 응용

- 게이트웨이(외부망)의 반대쪽에 위치한 사설네트워크에 상주하는 호스트에 대한 서비스를 생성하기 위해 흔히 사용

![](https://i.imgur.com/fTeuGpc.png)

![](https://i.imgur.com/M1nq6eK.png)

![](https://i.imgur.com/Z8xaHae.png)

# [포트 포워딩 설정 실습](https://youtu.be/EvYI14QdM6A?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
