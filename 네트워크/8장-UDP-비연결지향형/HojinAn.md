### [UDP 프로토콜](https://youtu.be/3MkI3FBFzX8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 사용자 데이터그램 프로토콜(User/Universal Datagram Protocol, UDP)
- 연결된 상태에서 데이터 주고 받는 것이 아니기 때문에 신뢰성은 낮다.

#### UDP 프로토콜 구조

- 총 8byte
- Source Port : 2byte (0 ~ 65,535)
- Destination Port : 2byte
- Length : 2byte (UDP 헤더 포함 뒤의 Payload까지의 길이)
- Checksum : 2byte (프로토콜 손상 확인)

#### UDP 프로토콜을 사용하는 프로그램

- DNS 서버 : Domain 물으면 IP 알려줌
- tftp 서버 : 파일 공유
- RIP 프로토콜 : 라우팅 정보 공유. 라우터들끼리 서로 최신의 라우팅 테이블을 공유하도록 해준다.

### [tftpd로 파일 전송 실습](https://youtu.be/5Woau-EJChw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 윈도우 안에 VM 띄워서 실습
- 서버의 Host, 포트 번호, 파일 경로, 이름을 클라이언트 컴퓨터에서 입력해서 다운 시도하면 다운 됨.
- 파일 전송을 UDP로 하면 좋을까?
  - 유실돼서 중간 정보 날라가면 열받으니까, 용량 큰 파일들은 보통 TCP
- 동영상 스트리밍은 UDP로 함 보통
- 운영 체제 없는 컴퓨터처럼 정상 작동하지 않는 컴퓨터에 OS Image 전달할 때 사용하기도
- 결국 상황에 맞게 하자
- VM 띄워서 통신할 때 LAN으로 하는 게 아니라 VMnet8 등으로 함 : 얘 NAT다

> VMNet8 – NAT 라고 부르며 Vmware 가 가상의 게이트웨이역할을 해주어서 이 가상의 게이트웨이를 통해서 외부 네트워크와 연결이 되는 것입니다.
