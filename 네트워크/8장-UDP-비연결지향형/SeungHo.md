# [UDP 프로토콜](https://youtu.be/3MkI3FBFzX8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### UDP가 하는 일

사용자 데이터그램 프로토콜(User Datagram Protocol, UDP)

**전송 방식이 매우 단순하여 서비스의 신뢰성이 낮고**, 데이터그램 도착 순서가 바뀌거나, 중복되거나, 심지어는 통보 없이 누락시키기도 함

일반적으로 **오류의 검사와 수정이 필요 없는** 프로그램에서 수행할 것으로 가정

### UDP 프로토콜의 구조

![](https://i.imgur.com/kKEOYDT.png)

안전한 연결을 지향하지 않음

Length : Header + 페이로드 길이

### UDP 프로토콜을 사용하는 대표적인 프로그램들

![](https://i.imgur.com/SMRcRxC.png)

DNS 서버 : 도메인을 물으면 IP를 알려주는 서버

![](https://i.imgur.com/CXB9k1r.png)

tftp 서버 : UDP로 파일을 공유하는 서버

![](https://i.imgur.com/XKdNjFi.png)

RIP 프로토콜 : 라우팅 정보를 공유

# [tftpd로 파일 전송 실습](https://youtu.be/5Woau-EJChw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
