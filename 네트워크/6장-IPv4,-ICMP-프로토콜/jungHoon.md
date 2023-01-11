[→ Open in Slid](https://app.slid.cc/docs/4eb9f87f54bd439a91c986161612d817)


---

### IPv4 프로토콜

[![network_6 멀리 있는 통신 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/4eb9f87f54bd439a91c986161612d817/af21bee7-e6bf-4c28-9c3d-d6e0be7a3a72.png)](https://app.slid.cc/vdocs/4eb9f87f54bd439a91c986161612d817?v=21820ac78c4b4c59bf5e619f24fcb6c0&start=561.0815031754761)

- 네트워크상의 데이터를 교환하기 위한 프로토콜 \_ 20바이트(최대 60바이트)

  - 데이터가 정확하게 전달되는 것을 보장하지는 않는다.

  - 중복된 패킷을 전달하거나 패킷의 순서를 잘못 전달한 가능성이 있음

  - 데이터의 정확, 순차적 전달은 그보다 상위 프로토콜인 TCP에서 보장

### ICMP 프로토콜

[![network_6 멀리 있는 통신 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/4eb9f87f54bd439a91c986161612d817/42fe8347-b998-4586-b814-b55babe3a488.png)](https://app.slid.cc/vdocs/4eb9f87f54bd439a91c986161612d817?v=f3f2882c8fcc45a6bf754f5740122db3&start=42.99795171961975)

- 인터넷상에서 제어 메세지를 주고 받는 프로토콜

- 상대방과 통신이 되는지 확인하기 위한 프로토콜

  - 네트워크 컴퓨터 위에서 돌아가는 운영체제에서 오류 메세지를 전송 받는 데 사용

- 메세지 타입 (대표적인 것만)

  - 8 : 요청

  - 0 : 응답

  - 5 : 리다이렉트

  - 3 : 보통 목적지까지 도착을 못함\_경로의 문제

  - 11 : 목적지까지 갔는데 응답을 못함\_상대방의 문제(방화벽)


‏‏‎ ‎

### IPv4, ICMP프로토콜 실습

- 

### 라우팅 테이블

- 최적의 경로를 지도처럼 저장해 놓는 것

  - 어디로 보내야 하는지 설정되어 있음

- 라우팅 테이블에 적혀있는 IP로만 찾아갈 수 있음

  - 그래서 기본 값은 하나 있음 \_ 모르면 우선 도착하도록

  - 0.0.0.0


‏‏‎ ‎


다른 네트워크와 통신하는 과정

[![network_6 멀리 있는 통신 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/4eb9f87f54bd439a91c986161612d817/946dc6d6-77bb-44b7-b8c0-19f85c05cb5d.png)](https://app.slid.cc/vdocs/4eb9f87f54bd439a91c986161612d817?v=07a4547811004b3db87e327a43f3be1a&start=178.52746597997285)

- A의 라우팅 테이블에 B의 네트워크 대역 주소가 있어야 통신 가능

- A요청 - 8

- B응답 - 0


‏‏‎ ‎

### IPv4 조각화 이론

[![network_6 멀리 있는 통신 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/4eb9f87f54bd439a91c986161612d817/8c20dbf5-531d-4477-bf5d-cd5fb0d6a883.png)](https://app.slid.cc/vdocs/4eb9f87f54bd439a91c986161612d817?v=c53b60c4640e4f56ba0bc4733956b40c&start=75.16373536382675)

[![network_6 멀리 있는 통신 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/4eb9f87f54bd439a91c986161612d817/8da10338-dcea-4062-95c7-72a574f079e4.png)](https://app.slid.cc/vdocs/4eb9f87f54bd439a91c986161612d817?v=c53b60c4640e4f56ba0bc4733956b40c&start=703.596958143528)

- 큰 IP패킷들이 적은 MTU(Maximum Transmission Unit)를 갖는 링크를 통하여 전송되려면 여러 개의 작은 패킷으로 조각화 되어 전송되야 한다.

  - 조각화가되면, 최종 목적지에 도달할 때까지 재조립되지 않는 것이 일반적

  - IPv4는 발신지 & 중간 라우터에서도 IP 조각화 가능

  - IPv6는 발신지에서만 가능

  - 재조립은 수신지에서만


‏‏‎ ‎
