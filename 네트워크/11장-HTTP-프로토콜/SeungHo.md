# [HTTP 프로토콜](https://youtu.be/TwsQX1AnWJU?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 웹을 만드는 기술들

HTML / Javascript / CSS : 서버에 저장되어 클라이언트 컴퓨터에서 동작하는 코드

**HTTP** : HTML,Javascript,CSS를 웹 서버에게 요청하고 받아오는 프로토콜

### HTTP 프로토콜의 특징

HyperText Transfer Protocol

www에서 쓰이는 핵심 프로토콜로 문서의 전송을 위해 쓰임

오늘날 거의 모든 웹 애플리케이션에서 사용되고 있음 → 음성, 화상 등 여러 종류의 데이터를 MIME로 정의하여 전송 가능

- Request / Response (요청/응답) 동작에 기반하여 서비스 제공

**HTTP 1.0**

![](https://i.imgur.com/aKybLeJ.png)

“연결 수립, 동작, 연결 해제”의 단순함이 특징 → 하나의 URL은 하나의 TCP 연결

HTML 문서를 전송받은 뒤 연결을 끊고 다시 연결하여 데이터 전송

- 단순 동작 (연결 수립, 동작, 연결 해제)이 반복되어 통신 부하 문제 발생

**HTTP 1.1**

![](https://i.imgur.com/Jn77Hxa.png)

HTTP 1.0과 호환 가능

**Multiple Request 처리가 가능하여 Client의 Request가 많을 경우 연속적인 응답 제공** → Pipeline 방식의 Request / Response 진행

HTTP 1.0과는 달리 Server가 갖는 하나의 IP Address와 다수의 Web Site 연결 가능

- 빠른 속도와 Internet Protocol 설계에 최적화될 수 있도록 Cache 사용
- Data를 압축해서 전달이 가능하도록 하여 전달하는 Data 양이 감소

# [HTTP 요청 프로토콜](https://youtu.be/rxaBwwI_JnI?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### HTTP 요청 프로토콜의 구조

![](https://i.imgur.com/OI1WCBc.png)

요청하는 방식을 정의하고 클라이언트의 정보를 담고 있음

**Request Line**

![](https://i.imgur.com/0QANNCs.png)

![](https://i.imgur.com/cFNnJPw.png)

GET : 데이터를 서버로 보낼 때 URI에 포함시켜서 보냄

POST : 데이터를 body에 포함시켜서 보냄

# [URL, URI란?](https://youtu.be/2ikhZ_fNP5Y?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### URI

![](https://i.imgur.com/y3Q1ktc.png)

Uniform Resource Identifier

인터넷 상에서 특정 자원(파일)을 나타내는 유일한 주소

![](https://i.imgur.com/79iKoAn.png)

# [HTTP 요청 프로토콜 작성 실습](https://youtu.be/XbGJYsxed2w?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# [URI 이해를 위한 실습](https://youtu.be/HBojczyd1Ac?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# [HTTP 응답 프로토콜](https://youtu.be/kuucNF4Zvbs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### HTTP 응답 프로토콜의 구조

![](https://i.imgur.com/xj9mC5K.png)

사용자가 볼 웹 페이지를 담고 있음

**Status Line**

![](https://i.imgur.com/Iats6H0.png)

![](https://i.imgur.com/I7LWNF4.png)

# [HTTP 헤더 포맷](https://youtu.be/mQTGmxendk8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 일반 헤더

Content-Length : 메시지 바디 길이를 나타낼 때 쓰임

Content-Type : 메시지 바디에 들어있는 콘텐츠 종류 (Ex: HTML 문서는 text/html)

### 요청 헤더

Cookie : 서버로부터 받은 쿠키를 다시 서버에게 보내주는 역할

Host : 요청된 URL에 나타난 호스트명을 상세하게 표시 (HTTP 1.1은 필수)

User-Agent : Client Program에 대한 식별 가능 정보를 제공 (PC / 모바일)

### 응답 헤더

Server : 사용하고 있는 웹서버의 소프트웨어에 대한 정보를 포함

Set-Cookie : 쿠키를 생성하고 브라우저에 보낼 때 사용, 해당 쿠키 값을 브라우저가 서버에게 보낼 때 사용

# [HTTP 프로토콜 분석 실습](https://youtu.be/dhMrKTwNI8U?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
