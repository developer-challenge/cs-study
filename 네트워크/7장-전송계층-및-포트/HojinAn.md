### [4계층 프로토콜](https://youtu.be/tG0ldt4sBzY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 앞서 살펴본 바에 따르면,  
  - LAN 내에서 가까운 장비들끼리 2계층 통신  
  - WAN처럼 다른 네트워크 통신을 위해 3계층  
  - 물론 특정 컴퓨터 찾기 위해서는 다시 2계층 통신
- 원하는 컴퓨터까지는 도달했다.  
  - 이제 상대방 컴퓨터에서 통신하고자 하는 특정 프로그램과 내 프로그램이 데이터를 주고 받는 과정, 쉽게 말해 `Client - Server`가 어떻게 data를 주고 받는가.  
  이를 담당하는 것이 4계층 프로토콜.  

- 송신자의 Process와 수신자의 Process를 연결하는 통신 서비스 : 전송 계층(Transport Layer)

    > Process?  
    > Memory에서 동작 중인 Program

- 종류
  - TCP : 연결 지향형. data를 보내고 물어본다. 과정이 복잡.
  - UDP : 비연결 지향형. 일단 data를 던진다. 과정이 간단.

### 포트 번호

- 4계층에서 사용하는 주소라고 생각하자
- 특정 Process가 사용. 컴퓨터가 하나 사용 이런게 아니라
- Process 하나가 Port 번호 하나 사용할 수 있다. `Process/Port = 1`
- 하지만 상대방 컴퓨터의 여러 Process(Port 번호)는 자신의 Port 하나와 연결할 수 있다.
- 0 ~ 65535까지 범위.
- 종류
  - Well-known : 잘 알려진 포트 번호. 이게 중요. HTTP(80), HTTPS(443), DNS(53) 등. 0 ~ 1,024
  - Registered : 조금 유명. 보통 유명 DB 서버. 오라클 DB 서버(1521), MySQL(3306), MS 원격 데스크탑(3389) 1,025 ~ 49,151
  - Dynamic : 일반 사용자 사용. 이 중 아무거나 사용. 49,152 ~ 54,435. 15,000개 정도.
- 결국 인터넷을 쓴다면 -> Port, IP, MAC 3개를 다 써야 겨우 통신이 되는 것

- 프로그램의 연결 정보 : `netsstat -ano`로 활성 연결 테이블 확인

### [포트 연결 확인 실습](https://youtu.be/Jb7tCFp-udM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 크롬으로 네이버 접속 후 활성 연결 테이블 바로 확인
- 443 포트(https)로 되어 있는 프로그램들의 PID 확인
- 작업 관리자에서 일치하는 PID 확인한다면?
- 크롬이 사용하고 있음을 알 수 있다.

### [특정 포트 사용 실습](https://youtu.be/Qqmwm3rFihk?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- WebShare 프로그램으로 실습
- 기본적으로 80번 설정되어 있을 것. 이를 바꾼다면, 그냥 80번 포트로 들어올 때 접속이 안된다.
- www.naver.com으로 접속한다고 할 때 사실은 뒤에 `.:443`이 숨겨져 있는 것이다. 보안 웹 서버는 기본적으로 443 포트 사용하고 있지만 생략되는 것