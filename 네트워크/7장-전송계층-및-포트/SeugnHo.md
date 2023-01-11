# [4계층 프로토콜](https://youtu.be/tG0ldt4sBzY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 4계층에서 하는 일

송신자의 **프로세스와** 수신자의 **프로세스를 연결하는 통신 서비스** 제공

연결지향 데이터 스트림 지원, 신뢰성, 흐름 제어, 다중화와 같은 편리한 서비스 제공

전송 제어 프로토콜 - **TCP**

데이터그램 프로토콜 - **UDP**

### 4계층 프로토콜의 종류

![](https://i.imgur.com/GVye88M.png)

**TCP 프로토콜** : 안전한 연결 지향

![](https://i.imgur.com/iO9NALG.png)

**UDP 프로토콜** : 비연결 지향

# 포트 번호

### 포트 번호의 특징

**특정 프로세스와 특정 프로세스가 통신**을 하기 위해 사용

**하나의 포트는 하나의 프로세스만** 사용 가능 / 하나의 프로세스가 여러 개의 포트를 사용하는 것은 가능

- 포트 번호는 일반적으로 정해져 있지만 무조건 지켜야 하는 것은 아님
  ex) 일반적으로 웹 서비스는 80번 포트를 사용하지만 웹 서비스가 항상 80번 포트를 사용해야만 하는 것은 아님

### Well-Known 포트

세계적으로 유명한 포트 번호

![](https://i.imgur.com/Z4J6acW.png)

### Registered 포트

공신력 있는 곳이 등록해놓은 포트 번호

![](https://i.imgur.com/DUiHsN3.png)

### Dynamic 포트

일반 사용자들이 사용하는 포트 번호

보통 상대방 프로그램에 연결할 때 사용하는 포트 번호

ex) 상대방 웹 서버 80번 포트에 49153번 포트로 연결 요청

![](https://i.imgur.com/TO6Tc42.png)

### 나와 현재 연결되어 있는 컴퓨터들

netstat -ano 명령어 : 현재 포트 활성 여부를 나타내는 활성 연결 테이블

![](https://i.imgur.com/Pl2baK3.png)

PID : 내 컴퓨터에서 실행되는 프로그램

# [포트 연결 확인 실습](https://youtu.be/Jb7tCFp-udM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/EjLZLRM.png)

네이버 접속 시

내 컴퓨터(192.168.0.100:6153) → 네이버(104.74.192.17:443)

PID(4636) : 내 컴퓨터에서 실행되는 chrome 브라우저 / pid는 고정 값 x

# [특정 포트 사용 실습](https://youtu.be/Qqmwm3rFihk?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
