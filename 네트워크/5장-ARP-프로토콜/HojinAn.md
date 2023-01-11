### [ARP 프로토콜](https://youtu.be/LDsp-Xb168E?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

Address Resolution Protocol

- 외부 망 IP 주소, 내부 망 MAC 주소
- IP로 통신할 때 IP를 가지고 상대 컴퓨터의 MAC 주소를 찾게 되는데, 이때 ARP 프로토콜을 이용한다.
- 컴퓨터가 계속 이용하고 있는 프로토콜

#### ARP 프로토콜 구조

총 24byte
앞은 거의 고정 값

- Harward Type(2계층 프로토콜 타입, 보통 Ethernet 0x0001) : 2byte
- Protocol Type(보통 IP 0x0800) : 2byte
- Hardware Address Length(06 :: 6byte니까) : 2byte
- Protocol Address Length(04 :: 4byte니까) : 2byte
  변하는 값은 거의 이하만
- Op(eration)code(2가지만 있음. 요청 0x0001, 응답 0x0002) : 2byte
- Source Harware Address(보통 MAC) : 6byte
- Source Protocol Address(보통 IP) : 4byte
- Destination Hardware Address : 6byte
- Destination Protocol Address : 4byte
  ![](https://i.imgur.com/5fkiOHa.png)

### ARP 프로토콜의 통신 과정

1. Opcode 0001(요청), 출발지 MAC, IP, 목적지 IP 채우고 목적지 MAC 비우고(00:00:00:00:00:00) 출발 => ARP 요청 패킷
2. 해당 ARP 패킷에 Ethernet 헤더 붙임. 이 때 Ethernet 헤더 목적지 MAC은 브로드 캐스트(FF:FF:FF:FF:FF:FF) => 이동
3. 2계층 장비(스위치 등)가 2계층 프로토콜까지만 확인해서 브로드캐스트로 뿌려줌
4. 받은 모든 장비 Decapsulation -> 3계층까지 확인.
5. 목적지 IP와 자신 IP가 일치하지 않으면 패킷 폐기
6. 일치할 경우 응답 ARP 패킷 생성
7. Opcode 0002(응답), 출발지에 자신의 MAC, IP 넣고, 목적지에 요청 ARP의 출발지 정보 넣는다. => ARP 응답 패킷
8. 목적지 주소 알고 있으므로 Ethernet 헤더 끝에 0806만 넣어서 거꾸로 전달
9. 알아온 MAC 주소를 ARP 캐시 테이블에 등록

### ARP 테이블

- 나와 통신했던 컴퓨터들의 주소는 ARP 테이블에 남는다.

### [ARP 프로토콜 실습](https://youtu.be/-M_S50Ga384?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- cmd `arp -a` 입력
- ARP 프로토콜은 3계층인데 같은 네트워크 대역에서만 쓰인다.
  - 이유? ARP 과정에 브로드 캐스트가 있는데, 3계층 장비(라우터 등)는 다른 네트워크에 브로드캐스트하지 않는다.
- Who has IP address?
- 역시 실습 영상 신기방기 도움 됨~
- 보통 Frame 60byte 맞추기 위해 footer에 패딩으로 0으로 채워두는데, wireshark에 가끔 패딩 붙기 전에 잡히기도 해서 없는 걸로 나올 수도 있다.
