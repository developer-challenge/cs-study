## [HTTP 프로토콜](https://youtu.be/TwsQX1AnWJU?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

> HyperText Transfer Protocol (하이퍼텍스트 전송 프로토콜)

www에서 쓰이는 핵심 프로토콜로 문서의 전송을 위해 쓰이며 오늘날 거의 모든 웹 애플리케이션에서 사용되고 있다.

- 음성, 화상 등 여러 종류의 데이터를 MIME로 정의하여 전송 가능

HTTP 특징 : Request/Response (요청/응답) 동작에 기반해 서비스 제공

>

### 웹을 만들기 위해 사용되는 다양한 기술들

- 필수
  - HTTP 프로토콜
    - 웹 표준 데이터를 받아오는 기술
    - HTTPS → SSL/TLS
  - HTML, JS, CSS
    - 웹 표준 : 클라이언트에서 동작
  - ASP, JSP, PHP
    - 서버 페이지 기술 : 서버에서 동작
  - DB
- 선택
  - Python
  - Spring
  - jQuery, Ajax …

### HTTP/1.0

- 연결 수립, 동작, 연결 해제의 단순함이 특징
  - 하나의 URL은 하나의 TCP 연결
  - HTML 문서를 전송 받은 뒤 연결을 끊고 다시 연결하여 데이터를 전송한다.
- 문제점
  - 단순 동작이 반복되어 네트워크 부하 문제 발생

### HTTP/1.1

- HTTP/1.0 문제 보완
- 한번의 3-way handshake

## [HTTP 요청 프로토콜](https://youtu.be/rxaBwwI_JnI?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- HTTP Method 설명 중 GET, POST만 사용해야 한다고 하지만 개발자 입장에서 RESTful API 개발시 PUT, DELETE도 사용하는게 원칙임

### HTTP 요청 프로토콜의 구조

> 요청하는 방식을 정의하고 클라이언트의 정보를 담고 있다.

- Request Line
  - 요청 타입
    - GET : 정보 전송 + 요청
    - HEAD : 페이지 정보
    - POST : 정보 전송 + 요청
    - PUT : 자원 업로드
    - COPY, MOVE, DELETE 등은 보안 상 막을 수 있음
      - 클라이언트가 서버 코드를 수정할 수 없도록
      - 관리자가 설정은 가능
  - 공백
  - URI
  - 공백
  - HTTP 버전
- Headers
  - 옵션(필수, 선택)
- 공백 한 줄
- Body

### GET vs. POST

> 두 방식 다 전송, 응답이 가능

- GET
  - URI에 서버에 전송할 정보를 포함. 사용자가 볼 수 있음
  - 노출되어도 중요하지 않은 데이터
- POST
  - Body에 정보를 포함
  - ID, PW 등 중요한 정보 → HTTPS를 써야함

## [URL, URI란?](https://youtu.be/2ikhZ_fNP5Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### URI(Uniform Resource Identifier)

- 인터넷 상에서 특정 자원(파일)을 나타내는 유일한 주소
- `scheme://host[:port][/path][?query]`
  - **scheme** : ftp, http …
  - **host** : IP 주소(도메인 주소)
  - **port** : 주로 생략
  - **path** : 자원의 원격지 경로
  - **query** : 전달할 데이터

### [HTTP 요청 프로토콜 작성 실습](https://youtu.be/XbGJYsxed2w?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### [URI 이해를 위한 실습](https://youtu.be/HBojczyd1Ac?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## [HTTP 응답 프로토콜](https://youtu.be/kuucNF4Zvbs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- Status Line
  - HTTP 버전
  - 공백
  - 상태 코드
  - 공백
  - 상태 문구 (상태 코드와 1:1)
    - 2xx : 정상
      - 200 : OK
    - 4xx : 클라이언트 오류
      - 403 : Forbidden. Client 권한이 없는 페이지 요청
      - 404 : Not Found. 서버에 없는 페이지 요청
    - 5xx : 서버 오류
      - 500 : Internal Server Error. 서버 설정 오류
      - 503 : 세션 초과
- Headers
- 공백
- Body

## [HTTP 헤더 포맷](https://youtu.be/mQTGmxendk8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### HTTP 헤더 구조

| 헤더 종류      | 설명                                                                                        |
| -------------- | ------------------------------------------------------------------------------------------- |
| Content-Length | 메시지 바디 길이                                                                            |
| Content-Type   | 메시지 바디 컨텐츠 종류(text/html)                                                          |
| Cookie         | 서버로부터 받은 쿠키를 다시 서버에 보내주는 역할                                            |
| Host           | HTTP/1.1에서 필수. 요청된 URL에 나타난 호스트명을 상세히 표시                               |
| User-Agent     | Client Program에 대한 식별 가능 정보(운영체제 정보, 브라우저 버전 정보)를 제공              |
| Server         | 사용하고 있는 웹서버의 소프트웨어에 대한 정보를 포함                                        |
| Set-Cookie     | 쿠키를 생성하고 브라우저에 보낼 때 사용. 해당 쿠키 값을 브라우저가 서버에 다시 보낼 때 사용 |

### [HTTP 프로토콜 분석 실습](https://youtu.be/dhMrKTwNI8U?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
