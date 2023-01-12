# [TCP 프로토콜](https://youtu.be/cOK_f9_k_O0?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### TCP가 하는 일

전송 제어 프로토콜(Transmission Control Protocol, TCP)

연결된 컴퓨터에서 실행되는 **프로그램 간에 통신을 안정적으로, 순서대로, 에러없이** 교환할 수 있게 함

TCP는 UDP보다 안전하지만 느림

### TCP 프로토콜의 구조

![](https://i.imgur.com/qn5ymsu.png)

Offset : 헤더의 길이 / 4

Reserved : 예약된 필드 (사용하지 않는 필드)

Window : 남아있는 TCP 버퍼 사용공간 (데이터를 얼만큼 더 보내도 되는지 알려주는 필드)

# TCP 플래그

TCP Flags

- U : Urgent Flag - 긴급 비트 (현재 보내고 있는 데이터가 우선순위가 높다)
  - Urgent Pointer : 어디서부터가 긴급 데이터인지 알려주는 위치값
- **A** : Ack - 승인 비트
- P : Push - TCP 버퍼가 일정한 크기만큼 채워져야 패킷을 전송하지만 이것과 상관 없이 데이터 전송
- **R** : Reset - 초기화 비트 (연결 관계 리셋)
- **S** : Syn - 동기화 비트 (상대방과 연결을 시작할 때 무조건 사용 / S 플래그가 보내지고 난 다음부터 둘 사이의 연결이 동기화되기 시작)
- **F** : Fin - 종료 비트 (연결 끊을 때)

# [TCP 3Way Handshake](https://youtu.be/Ah4-MWISel8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 연결 수립 과정

![](https://i.imgur.com/O6hYg3g.png)

**3Way Handshake** : TCP를 이용한 데이터 통신을 할 때 프로세스와 프로세스를 연결하기 위해 **가장 먼저 수행되는 과정**

1. 클라이언트가 서버에게 요청 패킷을 보냄
2. 서버가 클라이언트의 요청을 받아들이는 패킷을 보냄
3. 클라이언트는 이를 최종적으로 수락하는 패킷을 보냄

# [TCP를 이용한 데이터 전송 과정](https://youtu.be/0vBR666GZ5o?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 데이터 송수신 과정

![](https://i.imgur.com/UwmQZCB.png)

TCP를 이용한 데이터 통신을 할 때 단순히 TCP 패킷만을 캡슐화해서 통신하는 것이 아닌 페이로드를 포함한 패킷을 주고 받을 때의 일정한 규칙

1. 보낸 쪽에서 또 보낼 때는 SEQ번호와 ACK번호가 그대로
2. 받는 쪽에서 SEQ번호는 받은 ACK번호가 됨
3. 받는 쪽에서 ACK번호는 받은 SEQ번호 + 데이터의 크기

# [TCP의 연결 상태 변화](https://youtu.be/yY0uQf0BTH8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/HaR9jg8.png)

LISTEN : 포트 번호를 열어놓고 있는 상태 (상대방/클라이언트의 요청을 계속 듣고 있는 상태)

ESTABLISHED : 연결이 수립된 상태 (3Way Handshake 과정이 끝나면 ESTABLISHED 상태)

![](https://i.imgur.com/v5zM4ba.png)

# [TCP 프로토콜 분석 실습](https://youtu.be/WseqBDo-j3Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
