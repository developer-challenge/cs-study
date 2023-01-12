# [UDP 프로토콜](https://youtu.be/3MkI3FBFzX8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## UDP가 하는 일

UDP(User Datagram Protocol) : 사용자 데이터그램 프로토콜

전송방식이 단순해 서비스의 신뢰성이 낮음
(도착 순서가 바뀌거나, 중복되거나, 누락되는 경우 많음)

오류의 검사와 수정이 필요없는 프로그램에서 수행

## UDP 프로토콜의 구조

![](https://i.imgur.com/gXFgBWI.png)

## UDP 프로토콜을 사용하는 프로그램들

- 도메인을 물으면 IP를 알려주는 DNS서버
- UDP로 파일을 공유하는 tftp 서버
- 라우팅 정보를 공유하는 RIP 프로토콜

> 대표적인 라우팅 설정 방법
>
> 1.  사람이 수동으로 설정하기
> 2.  라우터간 정보 공유를 통해 자동으로 업데이트

# [tftpd로 파일 전송 실습](https://youtu.be/5Woau-EJChw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/r67xNmb.png)
host : 서버의 IP
port : 서버가 사용하고 있는 포트 번호
