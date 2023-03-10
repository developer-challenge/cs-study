### [네트워크란?](https://youtu.be/Av9UFzl_wis?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- **노드들이 데이터를 공유**할 수 있게 하는, 디지털 전기통신망중 하나이다.

  - 노드 : 네트워크에 속한 컴퓨터 또는 통신 장비

- 네트워크 > 인터넷

  - 인터넷 : 문서, 그림 영상과 같은 데이터를 공유하도록 구성된 가장 큰 네트워크

- 네트워크의 분류

  - 크기에 따른 분류

    - LAN ( Local Area Netwrok )

      - 근거리 통신망

    - WAN ( Wide Area Network )

      - 원거리 통신망

      - 가까운 지역끼리 묶인 LAN과 LAN을 다시 하나로 묶은 것

  - 연결 형태에 따른 분류

[![network 1~2 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/22fcfe465d3e4b49b29c29d573bc7c9f/85a6bbf7-2e02-439d-b7f9-1cb0ffd0e185.png)](undefined)

    - Star 형

      - 중앙 장비에 모든 노드가 연결된 형태

      - 공유기가 고장난다면 모든 연결기기가 먹통

      - LAN대역을 구축할때 많이 이용

    - Mesh 형

      - 여러 노드들이 서로 그물처럼 연결된 형태

- 네트워크 통신 방식

  - 유니 캐스트 - 1:1

  - 멀티 캐스트 - 1:N

  - 브로드 캐스트 - 모든 대상과 통신

## 네트워크 프로토콜

- 프로토콜

  - 네트워크에서 노드와 노드가 통신할 때 주고 받는 양식

- 패킷 : 여러 프로토콜들로 캡슐화됨

### [](https://github.com/junior-developer-challenge/cs-study/blob/main/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/1%2C-2%EC%9E%A5-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%99%80-%EB%AA%A8%EB%8D%B8/1%2C-2%EC%9E%A5-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%99%80-%EB%AA%A8%EB%8D%B8.md#%EC%8B%A4%EC%8A%B51-tracert)[실습1 (tracert)](https://youtu.be/paJf7JbBWqY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 구글 DNS 서버와 통신 by cmd

  - 여러 네트워크 대역을 거쳐 구글 서버에 도달 (보통 15개 안으로 전세계 연결 - WAN)

  - \`traceroute 8.8.8.8\` 입력 (for MAC)

- \*홉 : 지나간 네트워크 대역

### [](https://github.com/junior-developer-challenge/cs-study/blob/main/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/1%2C-2%EC%9E%A5-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%99%80-%EB%AA%A8%EB%8D%B8/1%2C-2%EC%9E%A5-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%99%80-%EB%AA%A8%EB%8D%B8.md#%EC%8B%A4%EC%8A%B52-wireshark)[실습2 (Wireshark)](https://youtu.be/vBrQ3yzerMg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 프로토콜 직접 확인해보기

  - Ethernet - 가까운 곳

  - IPv4 - 멀리 떨어져 있음

  - TCP - 크롬 & 웹 서버 연결

‏‏‎ ‎

### [](https://github.com/junior-developer-challenge/cs-study/blob/main/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/1%2C-2%EC%9E%A5-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%99%80-%EB%AA%A8%EB%8D%B8/1%2C-2%EC%9E%A5-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EC%99%80-%EB%AA%A8%EB%8D%B8.md#%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EB%AA%A8%EB%8D%B8)[네트워크 모델](https://youtu.be/y9nlT52SAcg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

[![network 1~2 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/22fcfe465d3e4b49b29c29d573bc7c9f/c60755de-5055-4ea3-b9ec-bb0563dd4aed.png)](undefined)

- TCP/IP 4계층 모델\_프로토콜기반 (좌측)

  - 1960년대 개발

- OSI 7계층 모델\_역할기반(우측)

[![network 1~2 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/22fcfe465d3e4b49b29c29d573bc7c9f/0fa25d7c-2742-4d12-bb8c-e72eeaafcca6.png)](undefined)

- 1984년 통신 전체를 체계적으로 ISO에서 표준으로 지정한 모델

‏‏‎ ‎

- 패킷

  - 네트워크를 통해 전달되는 데이터

  - 현재 인터넷에서 컴퓨터들이 서로 정보를 주고받는데 쓰이는 통신 규약(프로토콜)의 모음

  - 형식화된 블록단위

  - 제어 정보, 사용자 데이터로 구성

  - 사용자 데이터는 페이로드라 함

  - "헤더 - 페이로드 - 푸터" 로 구성

- 인캡슐레이션 구조 (높은 계층에서 낮은 계층으로)

[![network 1~2 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_markup_images/22fcfe465d3e4b49b29c29d573bc7c9f/6c7aebbd-b481-47eb-a965-021dfbb19103.png)](undefined)

- 디캡슐레이션 구조 (낮은 계층에서 높은 계층으로)

[![network 1~2 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_markup_images/22fcfe465d3e4b49b29c29d573bc7c9f/32ff9fcb-4938-42e1-ba92-4d3e5c98d249.png)](undefined)

- 계층별 패킷의 이름 = PDU

  - 5~7계층의 PDU = Data

  - 4계층의 PDU = 세그먼트

  - 3계층의 PDU = 패킷

  - 2계층의 PDU = 프레임 \_ "2계층까지 인캡슐레이션된 상태"

  - 1계층의 PDU = Bit
