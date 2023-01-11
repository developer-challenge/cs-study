컴퓨터의 특정 프로그램까지 어떻게 찾아가는가

# [4계층 프로토콜](https://youtu.be/tG0ldt4sBzY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 4계층에서 하는 일

전송 계층은 송신자의 프로세스와 수신자의 프로세스를 연결하는 통신 서비스 제공 - 카톡은 카톡끼리 네이트온은 네이트온끼리

> `프로세스` : 메모리에서 동작중인 프로그램

연결 지향 데이터 스트림 지원, 신뢰성, 흐름 제어, 다중화와 같은 편리한 서비스 제공

## 4계층 프로토콜의 종류

### `TCP`(전송 제어 프로토콜)

: 연결 지향 프로토콜

### `UDP`(사용자 데이터그램 프로토콜)

: 비연결 지향형 프로토콜

# 포트 번호

## 포트번호의 특징

특정 프로세스간 통신을 위해 사용

하나의 포트는 하나의 프로세스만 사용 가능
일반적으로 번호가 정해져있지만 필수는 아님

## Well-known 포트

![](https://i.imgur.com/SIWGrfO.png)

## Registered 포트

![](https://i.imgur.com/Mnb6nkl.png)

## Dynamic 포트

![](https://i.imgur.com/Q3Jl40V.png)

# [포트 연결 확인 실습](https://youtu.be/Jb7tCFp-udM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

```
netstat -a | grep -i "Listen"
```

![](https://i.imgur.com/60XMNBt.png)
