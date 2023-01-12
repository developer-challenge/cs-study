## [UDP 프로토콜](https://youtu.be/3MkI3FBFzX8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### UDP가 하는 일

> 사용자 데이터그램 프로토콜(User Datagram Protocol, UDP)은 유니버셜 데이터그램 프로토콜(Universal Datagram Protocol)이라고도 한다.

UDP의 전송 방식은 너무 단순해서 서비스의 신뢰성이 낮고 데이터그램 도착 순서가 바뀌거나 중복되거나 누락되기도 한다.

UDP는 일반적으로 오류의 검사와 수정이 필요 없는 프로그램에서 수행할 것으로 가정한다.

>

### UDP 프로토콜의 구조

> 안전한 연결을 지향하지 않는 UDP 프로토콜

- Source Port
- Destination Port
- Length : payload까지 포함한 길이
- Checksum

## UDP 프로토콜을 사용하는 프로그램

### UDP 프로토콜을 사용하는 대표적인 프로그램들

- DNS 서버 : 도메인을 물으면 IP를 알려준다.
- tftp 서버 : UDP로 파일을 공유
- RIP 프로토콜 : 라우팅 정보 공유

## [tftpd로 파일 전송 실습](https://youtu.be/5Woau-EJChw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### tftpd를 사용한 데이터 공유

tftpd : DARPA Trivial File Transfer Protoco를 지원하는 서버

### UDP 패킷 캡쳐 및 분석

- TFTP : 7계층 프로토콜
  - 24byte 데이터를 Encapsulation
- UDP (8byte)
  - 비연결지향 : 데이터만 전송
