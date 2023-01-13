### [HTTP 프로토콜](https://youtu.be/TwsQX1AnWJU?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 7계층 프로토콜은 엄청 많다. 자신만의 프로토콜을 만들 수도 있다(!). 보통 socket 통신 구현 : 사실 socket은 채팅만을 위한 게 아니다. 데이터 보내는 형식을 만들면, 그게 하나의 프로토콜이 되는 것.
- 가장 대표적인 HTTP
- 서버에 저장되어 있는 HTML, JS, CSS를 클라이언트로 가져오는 기술이 HTTP다. 즉, 웹 표준 데이터를 받아오는 것.
- HTTPS는 SSL/TLS 보안을 추가한 것
- HyperText Transfer Protocol : www에서 쓰이는 핵심 프로토콜
- 과거 문서 전송에 쓰였다면, 오늘 날 거의 모든 웹 애플리케이션에서 사용

#### 특징

- Request/Response 동작에 기반
- 1.0
  - 과거 문서 하나만 받으면 될 정도로 단순한 웹 때 사용
  1. TCP 3 way로 연결 열고
  2. HTTP 요청/응답 한 번
  3. 연결 종료
  - 이런 식으로 매 요청, 응답마다 TCP 열고 닫는 비효율
- 1.1
  - 매번 열고 닫지 않도록 수정

### [HTTP 요청 프로토콜](https://youtu.be/rxaBwwI_JnI?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 16진수로 쓰지 않고 영어, 특수 문자로 작성

#### 구조

- Request Line : 1번 줄 중요
- Headers : 2번 줄부터 공백 전까지. 여러 옵션이 붙는다.
- 공백
- Body

`Request Line`

![](https://i.imgur.com/I71tg60.png)

띄워쓰기 반드시 들어가야 함
요청 타입, URI 너무 중요

- 요청 타입 HTTP 메소드 : GET, POST, DELETE, PUT/PATCH
- HTTP Method 설명 중 GET, POST만 사용해야 한다고 하지만 개발자 입장에서 RESTful API 개발시 PUT, DELETE도 사용하는게 원칙임
- 사실 GET, POST 둘 다 요청과 정보 전달 가능
- GET은 쿼리 스트링으로, POST는 Body에 담아서 정보 전달

### [URL, URI란?](https://youtu.be/2ikhZ_fNP5Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- Uniform Resource Identifier
- 인터넷 상 특정 자원을 나타내는 유일한 주소
- scheme :// host [:port][/path][?query]
- host에는 보통 도메인 주소(DNS 통해서 IP로 변환)
- [:port]는 보통 80, 443 컴퓨터가 알아서 붙여줌
- [/path]는 경로를 나타내는 값
- [?query]는 전달하고자 하는 정보

### [HTTP 요청 프로토콜 작성 실습](https://youtu.be/XbGJYsxed2w?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- HTTP 프로토콜을 직접 작성하는 실습

`GET / HTTP/1.1` 전달하면 응답 프로토콜 확인 가능

### [URI 이해를 위한 실습](https://youtu.be/HBojczyd1Ac?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- Java, Tomcat 실습
- 윈도우 환경 변수 설정. 윈도우 정말..
- 폴더 경로 커스텀
- 톰캣 웹서버 실행
- URI로 접속해서 가져가기 가능
- 서버 홈 디렉토리 설정 따라서 path 달라짐

### [HTTP 응답 프로토콜](https://youtu.be/kuucNF4Zvbs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

#### 구조

- Status Line : 1번 줄 중요
- Headers : 2번 줄부터 공백 전까지. 여러 옵션이 붙는다.
- 공백
- Body : 사용자 요청 데이터

`Status Line`
![](https://i.imgur.com/AaQiovk.png)

상태 코드, 상태 문구는 하나의 쌍이기 떄문에 `상태 코드`만 알고 있으면 된다.

200 OK
403 Forbidden
404 Not Found
500 Internal Server Error : 보통 코드 잘못 짜면
503 Service Unavailable

### [HTTP 헤더 포맷](https://youtu.be/mQTGmxendk8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 일반, (요청/응답), 항목(특별한 경우) 헤더로 구성
- 엄청 많음. 일단 기본적인 것만
- 일반 헤더
  - Content-Length : 메시지 바디 길이
  - Content-Type : 메시지 바디 컨텐츠 종류
- 요청 헤더
  - Cookie
  - Host : 요청 URL 호스트명 상세 표시 HTTP 1.1부터는 필수
  - User-Agent : Client Program 식별 정보 -> 컴퓨터인지, 모바일인지 구분
- 응답 헤더
  - Server : 서버 정보 포함. 감추려면 안보내고
  - Set-Cookie

### [HTTP 프로토콜 분석 실습](https://youtu.be/dhMrKTwNI8U?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- burpsuite(jdk 설치 필요)로 HTTP 통신을 중간에 잡아서 수정해보는 실습
- Proxy-Intercept 기능 이용
- 크롬 기준 - 크롬 Falcon Proxy 확장 프로그램 편하게 껐다 켤 수 있도록
- 웹 통신은 한번에 엄청 많은 통신을 주고 받는다
- 우클릭 막힘 되어 있는 페이지 응답을 잡아서 `rightClickOpen = false` 를 `true`로 변경해서 받을 수도 있다(꿀팁)
