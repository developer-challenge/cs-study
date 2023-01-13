### [NAT란?](https://youtu.be/Qle5cfCcuEY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- NAT(Network Address Translation)
- 사설 IP, 공인 IP
- IP 패킷 TCP/UDP 포트 숫자와 출발지, 목적지 IP 주소를 재기록
- 네트워크 대역 넘어갈 때마다 Ethernet 새로 기록했던 것처럼 NAT는 3, 4계층까지 다시 만들면서 자신이 만들었다고 NAT 테이블에 기록해두는 것
- IP 출발지 주소를 바꾸는 등으로 쉽게 변경
- IP 주소의 특정 Port 번호로 가는 패킷을 다른 IP의 다른 Port로 바꿔주는 것
- 원래 가려던 곳이 있으면 다른 곳으로 바꿔주는 것 => 이 덕분에 사설 IP, 공인 IP를 바꿀 수 있는 것 => 따라서 단순히 NAT를 사설, 공인 IP 변경으로만 알면 안된다. NAT 기술 덕분에 사설, 공인 IP 변환이 가능한 것
- NAT로 IP, Port가 변경 되기 때문에 결국 다른 네트워크 내에서 각자 사설 IP를 이용하는 장비가 공인 IP로 통신이 가능.
- 공유기 안 NAT 테이블에 사설 IP가 나간 기록이 있고 그 요청에 대한 응답이 있어야지만 전달이 가능하다. 나간 기록 모르면 전달 못받는다는 문제점 => 포트 포워딩

### 포트 포워딩

- 특정 IP, Port 번호의 통신 요청을 다른 곳으로 넘겨주는 NAT 응용 기술
- 보통 서버가 사설 네트워크 대역에 있을 때, 네트워크 대역 설정하는 3계층 장비(라우터, 공유기 등)에서 제공하는 기능

1. 보이지 않는 사설 IP에 요청을 보낼 때, 일단 IP 프로토콜 설정 시 목적지 IP를 `공인 IP`로 한다. 대신 특정 Port로 보낸다.
2. 요청을 받은 공유기가 특정 Port 요청을 내부 특정 IP, 특정 Port 번호로 전송 : 설정하기 나름
3. 보이지 않았던 장비가 요청 전달 받음

### [포트 포워딩 설정 실습](https://youtu.be/EvYI14QdM6A?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- NAT/라우터 관리 항목에서 설정
- 내부 IP주소, 외부 포트, 내부 포트 써줌
- VMWare도 NAT로 설정되어 있기 때문에 변경할 수 있다.
- `ipconfig`로 내부 IP 확인
- NAT Settings에서 설정 가능
