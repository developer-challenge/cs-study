### [TCP 프로토콜](https://youtu.be/cOK_f9_k_O0?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- UDP와 달리 기능이 아주 많다.
- 연결 지향. 안정적으로, 순서대로, 에러 없이 전달 할 수 있게 해줌.
- 통신에서 보통 TCP, IP, Ethernet 프로토콜을 많이 사용하게 된다. 페이로드 따라 데이터 달라질 것
- 이론상 UDP보다 느리긴 한데, 체감할 정도일지는..?

#### TCP 프로토콜 구조

- 20byte 기본 IP처럼 TCP Option 따라 4byte 단위로 60byte까지 가능.
- Source Port : 2byte
- Destination Port : 2byte
- Sequence Number : 4byte
- Acknowledgement Number : 4byte
- Offset : 0.5byte -> Header 길이
- Reserved : 0.5byte 사용하지 않음
- [TCP Flags](#TCP 플래그) : 1byte 중요
- Window : 2byte -> TCP 연결 후 얼마나 data를 더 보낼지 물어볼 때 사용. 내 사용공간 얼마나 남았나 알려주는 부분.
- Checksum : 2byte
- Urgent Pointer : 2byte
- TCP Options : 0 ~ 40byte

![](https://i.imgur.com/r1yc2C2.png)

### TCP 플래그

- C E U A P R S F 중에서 U A P R S F만 알면 된다(거의 다 아닌가..?)
- TCP는 연결하면서 계속 연결상태를 물어보는데, 그 연결상태를 나타내는 값
- TCP의 주요기능이 Flags로 나뉜다.

- 종류
  - U(Urgent) : 우선 순위가 높은 data가 포함되어 있음. Urgent Pointer랑 세트
  - A(Ack) : 물어본 것에 대한 응답, 승인
  - P(Push) : TCP Buffer가 일정 쌓여야 data를 전달하는데, 이것과 상관없이 data를 밀어넣을 때 사용.
  - R(Reset) : 연결 상태 초기화
  - S(Sync) : 얘가 제일 중요. 상대방과 연결 시작할 때 무조건 사용하는 Flag. 얘가 보내지고서 연결이 동기화되는 것
  - F(Fin) : 종료 Flag

### [TCP 3Way Handshake](https://youtu.be/Ah4-MWISel8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- TCP에서 제일 중요한 것은 `둘 사이 연결을 수립하는 것`
- 대부분의 통신이 TCP.
- 총 3가지 과정으로 연결 수립

1. Client ----(요청)----> Server
2. Client <-(수락, 요청)-- Server
3. Client ----(수락)----> Server

이 3가지 과정을 3Way Handshake라고 부른다.

자세히 살펴보자

1. Client에서 TCP Encapsulation -> IP Encapsulation -> Ethernet Encapsulation 해서 패킷 생성

- TCP : 출발지 Port 아무거나, 목적지 Port 보통 80(0x0050), Seq No 설정, Flag(SYN 0x02) 등등

2. Server에서 요청을 받고 Decapsulation해서 자신 정보 확인
3. TCP Port 정보는 거꾸로, Ack는 요청 Seq + 1, Seq는 임의로, Flag는 SYN, ACK같이 세팅(0x12)
4. 다시 Encapsulation해서 응답
5. Client도 Decapsulation해서 응답 확인, 연결 요청 확인
6. Ack = Seq + 1 하고, Seq는 받은 Ack, 연결 승인 Flag(ACK, 0x10) 담아서 응답
7. 다시 Server에 전송

Seq, Ack는 보안에 중요

> Session 하이재킹  
> Seq, Ack 계산해서 Client 연결을 가로채는 것

### [TCP를 이용한 데이터 전송 과정](https://youtu.be/0vBR666GZ5o?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 연결 수립 후 data 주고 받을 때, 3Way Handshake 그대로 이어감.
- 일정한 규칙

  1. 보낸 쪽이 또 보낼 때는 Seq, Ack가 그대로
  2. 받는 쪽 Seq는 받은 Ack
  3. 받는 쪽 Ack는 받은 Seq + data 크기

- 구체적 예시는 영상 보기 : 너무 복잡
- 연결 끊을 때는 시작할 때처럼 무조건 뭔가 하는게 있지는 않다.

### [TCP의 연결 상태 변화](https://youtu.be/yY0uQf0BTH8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 굉장히 복잡

![](https://i.imgur.com/Mb6oo8G.png)

- 실선: Client 상태 변화, 점선: Server 상태 변화

* 종류
  - CLOSED, LISTEN, ESTABLISHED, SYN_RCVD, SYN_SENT...
  - 중요한 건 `LISTEN`, `ESTABLISHED`
* LISTEN : 상대의 요청을 항상 듣고 있는 상태
* ESTABLISHED : 연결이 수립된 상태 (3Way Handshake 이후 상태)

다른 것들도 살펴보면

- CLOSED에서 open 될 때, Client는 능동적(active open, 송신 SYN), Server는 수동적(passive open)
- Server는 SYN 받으면 SYN_RCVD 상태가 됨

3 way handshake로 보면

0. Server:LISTENING
1. Client -> active open, SYN => Client:SYN_SENT
2. SYN => Server:SYN_RCVD
3. Server -> SYN, ACK Server:SYN_RCVD 그대로
4. SYN, ACK => Client:ESTABLISHED
5. Client -> ACK => Server:ESTABLISHED

### [TCP 프로토콜 분석 실습](https://youtu.be/WseqBDo-j3Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- TCP 워낙 많이해서 아무거나 골라서 TCP Stream으로 내역 보기
