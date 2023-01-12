# [TCP 프로토콜](https://youtu.be/cOK_f9_k_O0?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## TCP가 하는 일

전송 제어 프로토콜은 인터넷에 연결된 컴퓨터에서 실행되는 프로그램간에 통신을 안정적으로, 순서대로, 에러없이 교환할 수 있게 해줌.

TCP의 안정성을 필요로 하지 않는 애플리케이션의 경우 일반적으로 TCP 대신 UDP 사용

UDP보다 안전하지만 느림 🐢

## TCP 프로토콜의 구조

![](https://i.imgur.com/itOyY4D.png)

### Offset

헤더의 길이 / 4

### Reserved

예약된 필드, 사용하지 않음

### Window

남아있는 TCP 버퍼공간
얼마만큼 데이터를 더 보내야하는지 알려주는 필드

# TCP 플래그

## TCP 플래그의 종류

C E **U A P R S F**

플래그 : TCP가 통신을 하면서 상대방과 컨택할 때 나타내는 값. 어떤 플래그를 설정하냐에 따라 무슨 내용을 컨택하는 지 달라짐.

## 각 플래그의 기능

- U(Urgent) : 우선순위가 높은 데이터가 포함
- A(Acknowledgement) : 승인 비트, 연결 여부에 대한 응답
- P(Push) : 쌓인 TCP 버퍼에 상관없이 데이터를 보내겠다.
- R(Reset) : 연결 초기화 비트
- S(Synk) : 연결 시작시 동기화 비트 (처음)
- F(Fin) : 연결 종료 비트

# [TCP 3Way Handshake](https://youtu.be/Ah4-MWISel8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

TCP 통신 과정 중 가장 중요한 것 : **연결 수립 과정**

- TCP를 이용한 데이터 통신을 할 때 프로세스간 연결을 위해 가장 먼저 수행되는 과정 1. 클라이언트가 서버에게 요청 패킷 전송 2. 서버가 클라이언트의 요청을 수락하는 패킷 전송 3. 클라이언트는 이를 최종적으로 수락 4.
  == `3way handshake`

![](https://i.imgur.com/Z3yhuPn.png)

Ack 번호는 받은 Synk 번호 + 1

# [TCP를 이용한 데이터 전송 과정](https://youtu.be/0vBR666GZ5o?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

TCP를 이용한 데이터 통신을 할 때 단순히 TCP 패킷만을 캡슐화해서 통신하는 것이 아니라 페이로드를 포함한 패킷을 주고 받을 때의 일정한 규칙

1. 보낸 쪽에서 또 보낼 때는 SEQ번호와 ACK 번호가 그대로다
2. 받는 쪽에서 SEQ번호는 받은 ACK 번호가 된다.
3. 받는 쪽에서 ACK번호는 받은 SEQ번호 + 데이터의 크기

![](https://i.imgur.com/xh3Ooea.png)

앞서 클라이언트가 보낸 데이터의 값이 100byte였기 때문에 Ack의 번호가 전달받은 Seq값에서 100 증가

# [TCP의 연결 상태 변화](https://youtu.be/yY0uQf0BTH8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

```
netstat
```

![](https://i.imgur.com/CM2KZ6q.png)

**LISTEN ⭐️ :**

서버의 데몬이 떠서 접속 요청을 기다리는 상태

**SYN-SENT :**

로컬의 클라이언트 어플리케이션이 원격 호스트에 연결을 요청한 상태

**SYN_RECEIVED :**

서버가 원격 클라이언트로부터 접속 요구를 받아 클라이언트에게 응답을 하였지만 아직 클라이언트에게 확인 메시지는 받지 않은 상태

**ESTABLISHED ⭐️:**

3 way-handshaking 이 완료된 후 서로 연결된 상태

**FIN-WAIT1, CLOSE-WAIT, FIN-WAIT2 :**

서버에서 연결을 종료하기 위해 클라이언트에게 종결을 요청하고 회신을 받아 종료하는 과정의 상태

**TIME-WAIT :**

연결은 종료되었지만 분실되었을지 모를 느린 세그먼트를 위해 당분간 소켓을 열어두고 있는 상태

**CLOSING :**

흔하지 않지만 주로 확인 메시지가 전송도중 분실된 상태

**CLOSED :**

완전히 종료
