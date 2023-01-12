## [TCP 프로토콜](https://youtu.be/cOK_f9_k_O0?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### TCP가 하는 일

> 전송 제어 프로토콜 (Transmission Control Protocol, TCP)은 인터넷에 연결된 컴퓨터에서 실행되는 프로그램 간에 통신을 안정적으로, 순서대로, 에러 없이 교환할 수 있게 한다.

TCP의 안정성을 필요로 하지 않는 애플리케이션의 경우 일반적으로 TCP 대신 비접속형 사용자 데이터그램 프로토콜(User Datagram Protocol)을 사용한다.

TCP는 UDP보다 안전하지만 느리다.

>

### TCP 프로토콜의 구조

> 안전한 연결을 지향 (20~60byte)

- Sequece Number(4byte)
- Acknowledgement Number(4byte)
- Offset
  - 헤더의 길이
- Reserved
  - 예약되어 사용되지 않는 필드
- **TCP Flags**
- Window
  - 보낼 데이터 양을 알려주는 공간(TCP 버퍼 공간)
- Checksum
- Urgent Pointer
- TCP Options
  - 4byte씩 10개까지 가능

## TCP 플래그

> TCP 플래그의 종류 (1, 0)

- C
- E
- U : Urgent flag. 긴급 비트. 우선 순위가 높은 데이터를 포함. Urgent Pointer는 어디부터가 긴급한지 알려주는 포인터
- A : Ack. 승인 플래그
- P : Push. 밀어넣기 비트. TCP 버퍼가 일정 크기 쌓여야 추가 전송이 가능한데, 상관없이 밀어넣음
- R : Reset. 문제 발생 시 연결 상태 초기화
- S : Sync. 동기화 비트. 연결을 시작할 때 무조건 사용하는 플래그. S가 보내지고 난 후부터 서로의 상태 동기화가 시작됨
- F : Fin. 종료 비트. 연결 끊을 때

## [TCP 3Way Handshake](https://youtu.be/Ah4-MWISel8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 연결 수립 과정

> TCP를 이용한 데이터 통신을 할 때 프로세스와 프로세스를 연결하기 위해 가장 먼저 수행되는 과정

- 3 way handshake

  1. 클라이언트가 서버에게 요청 패킷을 보낸다.

  1. TCP Encapsulation (Eth, IPv4, TCP)

     - 플래그 SYN

  1. 서버가 클라이언트의 요청을 받아들이는 패킷을 보낸다.

  1. Decapsulation
     - 플래그 확인
  1. Encapsulation

     - 플래그 SYN+ACK

  1. 클라이언트는 이를 최종적으로 수락하는 패킷을 보낸다.

  1. Decapsulation
     - 플래그 확인
  1. Encapsulation
     - 플래그 ACK

- 세션 하이재킹
  - 클라이언트가 아닌 누군가가 값을 계산해서 서버와 통신

## [TCP를 이용한 데이터 전송 과정](https://youtu.be/0vBR666GZ5o?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 데이터 송수신 과정

> TCP를 이용한 데이터 통신을 할 때 단순히 TCP 패킷만을 캡슐화해서 통신하는 것이 아닌 페이로드를 포함한 패킷을 주고 받을 때의 일정한 규칙

1. 보낸 쪽에서 또 보낼 때는 SEQ번호와 ACK번호 유지
2. 받는 쪽에서 SEQ 번호는 받은 ACK번호
3. 받는 쪽에서 ACK 번호는 받은 SEQ 번호 + 데이터의 크기

## [TCP의 연결 상태 변화](https://youtu.be/yY0uQf0BTH8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- LISTEN
  - 포트 번호를 열고 있는 상태
    - 포트 번호를 서버에서 사용 중
    - 클라이언트를 듣고 있는 상태
- ESTABLISHED
  - 연결 수립 상태
- 연결 과정
  - 클라이언트
    - active open
    - SYN_SENT
  - 서버
    - passive open
    - LISTENING
    - SYN_RECEIVED
  - 클라이언트, 서버
    - ESTABLISHED

## [TCP 프로토콜 분석 실습](https://youtu.be/WseqBDo-j3Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- TCP 3 way handshake 이후 연결 시작
