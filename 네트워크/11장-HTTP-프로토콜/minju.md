7계층은 굉장히 많은 프로토콜이 존재

# [HTTP 프로토콜](https://youtu.be/TwsQX1AnWJU?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 웹을 만드는 기술들

HTML / CSS / JS = 웹 프론트
ASP / JSP / PHP = 웹 백

## HTTP 프로토콜의 통신 과정

HTTP/1.0 : 네트워크 부하가 심함
한번의 요청과 응답만 하면 연결 종료

![](https://i.imgur.com/VDbCmxX.png)

HTTP/1.1 에서는 이 문제점을 보완해 여러 요청과 응답이 끝나면 종료하도록 변경

# [HTTP 요청 프로토콜](https://youtu.be/rxaBwwI_JnI?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 프로토콜의 구조

요청하는 방식을 정의하고, 클라이언트의 정보를 담음

![](https://i.imgur.com/NI5YTNP.png)

## 요청 타입

![](https://i.imgur.com/1w0K0iW.png)

- HTTP Method 설명 중 GET, POST만 사용해야 한다고 하지만 개발자 입장에서 RESTful API 개발시 PUT, DELETE도 사용하는게 원칙임

get 방식은 중요한 데이터를 쿼리에 담고
post는 body에 담아서 보냄

## [URL, URI란?](https://youtu.be/2ikhZ_fNP5Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/1Dav4rk.png)

`scheme` : 요청하는 요청 형식 지정
`host` : 도메인 주소 사용.
`post` : 보통 웹 브라우저가 알아서 입력해줌
`path` : 페이지의 경로
`query` : 경로 안의 데이터를 필터링해주는 값

# [URI 이해를 위한 실습](https://youtu.be/HBojczyd1Ac?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/87EN0mx.png)

# [HTTP 응답 프로토콜](https://youtu.be/kuucNF4Zvbs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 프로토콜의 구조

![](https://i.imgur.com/WofI7kZ.png)

`status line` :star:

![](https://i.imgur.com/r8famri.png)

## 상태 코드

![](https://i.imgur.com/5PQB4bL.png)

`400` : 클라이언트 잘못

![](https://i.imgur.com/FmQOedE.png)

`500` : 서버 잘못

![](https://i.imgur.com/dHBDqxi.png)

# [HTTP 헤더 포맷](https://youtu.be/mQTGmxendk8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## HTTP 헤더 구조

## 일반

> 일반적인 정보를 담고 있음
>
> ![](https://i.imgur.com/Rtc2WQE.png)

## 요청 헤더

> 클라이언트 정보를 포함

![](https://i.imgur.com/35PA7T8.png)

## 응답 헤더

> 서버 정보를 포함

![](https://i.imgur.com/9MX1Pvi.png)

# [HTTP 프로토콜 분석 실습](https://youtu.be/dhMrKTwNI8U?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

[burpsuite](https://portswigger.net/burp)사용

: 해킹툴 이라는데..

> 참고 자료
> https://velog.io/@hevton/Mac-OSProxy-%ED%94%84%EB%A1%9D%EC%8B%9C-%ED%88%B4-Burp-Suite-%EC%84%A4%EC%B9%98-%EB%B0%A9%EB%B2%95 >https://nantech.tistory.com/21

`falcon proxy` 크롬 익스텐션을 이용해 proxy 온오프가 쉽게 할 수 있음 혹은 크롬에서 수동으로 설정 가능
