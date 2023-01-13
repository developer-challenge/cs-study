# [NAT란?](https://youtu.be/Qle5cfCcuEY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

NAT(Network Address Translation)은 **IP 패킷의 TCP/UDP 포트 숫자와 소스 및 목적지의 IP 주소 등을 재기록**하면서 **라우터**를 통해 네트워크 트래픽을 주고 받는 기술

패킷에 변화가 생기기 때문에 IP나 TCP/UDP의 체크섬도 다시 계산되어 재기록해야 함.

NAT을 이용하는 이유
: 대개 사설 네트워크에 속한 여러 개의 호스트가 하나의 공인 IP주소를 사용해 인터넷에 접속하기 위함 (사설 IP -> 공인 IP)
: 반드시 이 이유만 있는 것은 아님.

그렇다면 다른 이유??
= 특정 IP의 특정 포트를 향하고 있던 패킷을 다튼 IP의 다른 포트로 향하도록 수정해주는 기술

# 포트 포워딩

패킷이 라우터나 방화벽과 같은 네트워크 장비를 가로지르는 동안 특정 IP주소와 포트번호의 통신 요청을 특정 다른 IP의 포트번호로 넘겨주는 NAT의 응용
-> 게이트웨이의 반대쪽에 위치한 사설네트워크에 상주하는 호스트에 대한 서비스를 생성하기 위해 흔히 사용

일반적으로 포트포워딩 설정은 공유기에서 함.

# [포트 포워딩 설정 실습](https://youtu.be/EvYI14QdM6A?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- ![](https://i.imgur.com/4MoTXVO.png)

외부에서 내 기기로 전송하는 경우, 외부포트에 작성한 포트 번호로 접속해야함

![](https://i.imgur.com/eXWoQBh.png)

> 외부에서 192.168.10.100으로 접속
