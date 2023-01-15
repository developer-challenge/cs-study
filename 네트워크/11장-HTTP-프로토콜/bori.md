# 11장 HTTP 프로토콜

## HTTP 프로토콜(HyperText Transfer Protocol)

### 웹을 만들기 위해 사용되는 다양한 기술들

- HTTP(HTTPS ⇒ SSL/TLS)
- HTML : 웹 페이지를 채울 내용
- JavaScript : 웹 페이지에 들어갈 기능
- CSS : 웹 페이지를 예쁘게 꾸밀 디자인
- ASP/ASP.NET
- JSP
- PHP
- DB

---

- Python
- Spring
- JQuery
- Ajax

클라이언트 사이드에서 동작하는 코드(Client Side Script) : HTML, JavaScript, CSS

- Client Side Script : 서버에 저장되어 있고 이것을 다운로드 하여 클라이언트에서 실행된다. 이것을 받아오는 프로토콜이 HTTP이다.

웹 서버 페이지를 만드는 기술들 : ASP/ASP.NET, JSP, PHP

### HTTP 프로토콜의 특징

- www에서 쓰이는 핵심 프로토콜로 문서의 전송을 위해 쓰이며, 오날날 거의 모든 웹 애플리케이션에서 사용되고 있다.
  ⇒ 음성, 화상 등 여러 종류의 데이터를 MIME로 정의하여 전송 가능
- Request/Response(요청/응답) 동작에 기반하여 서비스

#### HTTP 1.0

- "연결 수립, 동작, 연결 해제"의 단순함이 특징
  ⇒ 하나의 URL은 하나의 TCP 연결
- HTML 문서를 전송 받은 뒤 연결을 끊고 다시 연결하여 데이터를 전송
- 문제점 : 단순 동작(연결 수립, 동작, 연결해제)이 반복되어 통신 부하 문제 발생

![](https://i.imgur.com/Jw7TUye.png)

#### HTTP 1.1

- HTTP 1.0과 호환 가능, 1.0의 문제점 보완
- Multiple Request 처리가 가능하여 클라이언트의 요청이 많을 경우 연속적인 응답 제공
  ⇒ Pipeline 방식의 Request/Response 진행
- HTTP 1.0과 달리 Server가 갖는 하나의 IP Address와 다수의 Web Site 연결 가능
- 빠른 속도와 Internet Protocol 설계에 최적화될 수 있도록 Cache 사용
- Data를 압축해서 전달이 가능하도록 하여 전달하는 Data 양이 감소

![](https://i.imgur.com/Phrz40s.png)

### HTTP 프로토콜의 통신 과정

## HTTP 요청 프로토콜

- 요청하는 방식을 정의하고 클라이언트의 정보를 담고 있다.

### HTTP 요청 프로토콜의 구조

![](https://i.imgur.com/xPczci6.png)

#### Request Line

`[요청 타입][공백][URI][공백][HTTP 버전]`

### 요청 타입(HTTP 메소드 요청 방식)

- GET : Client가 Server로부터 문서를 읽어오려 할 때 사용
- POST : Client가 Server에게 어떤 정보를 전송할 때 사용
- GET과 POST는 모두 데이터를 보내면서 요청할 수 있다.
- 차이점 : 클라이언트가 특정 페이지를 요청하면서 서버에 보내는 데이터를
  - GET : 중요하지 않은 데이터를 URI에 포함시켜서 보냄
  - POST : 중요한 데이터를 Body에 숨겨서 보냄

### URI(Uniform Resource Identifier)

- 인터넷 상에서 특정 자원(파일)을 나타내는 유일한 주소
- `scheme://host[:port][/path][?query]`
  - `scheme` : 요청 형식 지정
  - `host` : IP주소
  - `[:port]` : 포트번호. 생략해도 80 또는 443번을 웹 브라우저가 자동으로 붙여줌
  - `[/path]` : 원격지의 경로
  - `[?query]` : 전달하는 데이터

## HTTP 응답 프로토콜

- 사용자가 볼 웹 페이지를 담고 있다.

### HTTP 응답 프로토콜의 구조

![](https://i.imgur.com/ihxgYvA.png)

#### Status Line

`[HTTP 버전][공백][상태 코드][공백][상태 문구]`

### 상태 코드

- 100 ~ 199 : 단순한 정보
- 200 ~ 299 : 클라이언트 요청 성공 ⇒ 정상적인 통신 완료
- 300 ~ 399 : 클러이언트의 요청이 수행되지 않아 다른 URL로 재지정
- 400 ~ 499 : 클라이언트 요청이 불완전하여 다른 정보가 필요 ⇒ 클라이언트의 잘못으로 발생
- 500 ~ 599 : 서버의 오류를 만나거나 클라이언트의 요청 수행 불가 ⇒ 서버의 잘못으로 발생

#### `200` : 성공적인 통신

| 상태 코드 종류 | 상태 문구 | 설명                   |
| -------------- | --------- | ---------------------- |
| 200            | OK        | 클라이언트의 요청 성공 |

#### `400` : 클라이언트의 실수, 잘못 오류

| 상태 코드 종류 | 상태 문구 | 설명                                          |
| -------------- | --------- | --------------------------------------------- |
| 403            | Forbidden | 클라이언트가 권한이 없는 페이지를 요청했을 때 |
| 404            | Not Found | 클라이언트가 서버에 없는 페이지를 요청했을 때 |

#### `500` : 서버의 실수, 잘못 오류

| 상태 코드 종류 | 상태 문구             | 설명                                    |
| -------------- | --------------------- | --------------------------------------- |
| 500            | Internal Server Error | 서버의 일부가 멈췄거나 설정 오류가 발생 |
| 503            | Service Unavailable   | 최데 Session 수를 초과했을 때           |

## HTTP 헤더 포맷

### HTTP 헤더 구조

![](https://i.imgur.com/NT7iPpd.png)

### 일반

- 일반적인 정보
- `Content-Length` : 메시지 바디 길이를 나타낼 때 사용
- `Content-Type` : 메시지 바디에 들어있는 컨텐츠 종류 (예: HTML 문서는 text/html)

### 요청 헤더

- 클라이언트 정보
- `Cookie` : 서버로부터 받은 쿠키를 다시 서버에게 보내주는 역할
- `Host` : 요청된 URL에 나타난 호스트명을 상세하게 표시(HTTP 1.1은 필수)
- `User-Agent` : Client Program에 대한 식별 가능 정보 제공

### 응답 헤더

- 서버 정보
- `Server` : 사용하고 있는 웹 서버의 소프트웨어에 대한 정보를 포함
- `Set-Cookie` : 쿠키를 생성하고 브라우저에 보낼 때 사용. 해당 쿠키 값을 브라우저가 서버에게 다시 보낼 때 사용
