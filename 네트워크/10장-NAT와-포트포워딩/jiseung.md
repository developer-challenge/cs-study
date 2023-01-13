## [NAT란?](https://youtu.be/Qle5cfCcuEY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

> NAT(Network Address Translation)은 IP패킷의 TCP/UDP 포트 숫자와 소스 및 목적지의 IP 주소 등을 재기록하면서 라우터를 통해 네트워크 트래픽을 주고 받는 기술

패킷에 변화가 생기므로 IP나 TCP/UDP 체크섬(checksum)도 다시 계산되어 재기록

NAT를 이용하는 이유는 대개 사설 네트워크에 속한 여러 개의 호스트가 하나의 공인 IP 주소를 사용해 인터넷에 접속하기 위함. 꼭 사설 IP를 공인 IP로 변환하는 데에만 사용하는 기술은 아님

### 사설 IP와 공인 IP

- 외부에서는 공유기가 통신하는 것처럼 보임

## 포트 포워딩

> 포트 포워딩 또는 포트 매핑(port mapping)은 패킷이 라우터나 방화벽과 같은 네트워크 장비를 가로지르는 동안 특정 IP 주소와 포트 번호의 통신 요청을 특정 다른 IP와 포트 번호로 넘겨주는 네트워크 주소 변환(NAT)의 응용

이 기법은 게이트웨이(외부망)의 반대쪽에 위치한 사설네트워크에 상주하는 호스트에 대한 서비스를 생성하기 위해 흔히 사용된다.

>

- 특정 IP의 특정 포트로 온 요청을 포트 포워딩
  - 목적지 IP를 공인 IP로 전송
  - 목적지 공인 IP에 포트 포워딩 설정
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/953e0288-1b28-4e3a-a1f1-030eae636237/Untitled.png)

## [포트 포워딩 설정 실습](https://youtu.be/EvYI14QdM6A?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 외부 포트는 접근할 수 있게 설정
- 내부 포트는 커스텀화 가능하되 열려 있어야 함
