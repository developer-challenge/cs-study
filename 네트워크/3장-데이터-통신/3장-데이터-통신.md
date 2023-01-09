영상: [[따라學 IT] 03. 가까이 있는 컴퓨터끼리는 이렇게 데이터를 주고받는다](https://youtu.be/HkiOygWMARs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 2계층에서 하는 일

- 2계층의 기능

  - 하나의 네트워크 대역 즉, 같은 네트워크 상 존재하는 여러 장비들 중 어떤 장비가 어떤 장비에게 보내는 데이터를 전달

  - 오류제어, 흐름제어

- 2계층의 크기

  - 2계층은 하나의 네트워크 대역 LAN에서만 통신할 때 사용

  - 다른 네트워크와 통신하기 위해서는 항상 3계층이 필요

    - 3계층의 주소, 프로토콜을 이용

- 2계층에서 사용하는 주소

  - MAC 주소

[![network 3 _ 2계층에서 하는 일 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/1d4a45af6eb54a71a90bb9b3ea511ce6/dd8c1fdf-21c1-48e9-a974-6d88c2a62703.png)](undefined)

    - LAN에서 통신할 때 사용

    - 물리적 주소 (16진수로 구성)\_6Byte

- 2계층의 프로토콜

[![network 3 _ 2계층에서 하는 일 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_markup_images/1d4a45af6eb54a71a90bb9b3ea511ce6/daaf40f9-d585-4681-bbaf-8bcf4e36effb.png)](https://app.slid.cc/vdocs/1d4a45af6eb54a71a90bb9b3ea511ce6?v=8b01f012ff204c0e9849a327286a8f3c&start=718.5693221716614)

- 총 14바이트

  - 목적지 주소 : 데이터를 전달받을 상대방 시스템의 MAC 주소 6바이트

  - 출발지 주소 : 데이터를 전달하는 시스템의 MAC 주소 6바이트

  - 상위 프로토콜 타입 : 2바이트

- Ethernet Type = Protocol Type

  - 상위 프로토콜이 뭔지 먼저 알려주는 것

  - 상위 프로토콜 타입으로 올 수 있는 것

    - 2바이트, IPv4, ARP
