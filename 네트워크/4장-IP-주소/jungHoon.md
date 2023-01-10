영상: [[따라學 IT] 04. 실제로 컴퓨터끼리는 IP주소를 사용해 데이터를 주고받는다](https://youtu.be/s5kIGnaNFvM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 3계층의 기능

- 서로 다른 네트워크(LAN) 대역들을 연결시켜주는 역할 \_ WAN

  - LAN과 LAN을 연결

- 발신에서 착신까지 패킷의 경로를 제어

- WAN에서 통신할 때 사용하는 IP 주소

3계층 프로토콜\_WAN에서 통신할 때 사용하는

- ARP

- IPv4

- ICMP

### 일반적인 IP 주소

[![network_4 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/6d392012404a49188f2a10ec7d7660bd/bd647282-953a-43d0-a8f1-fcb360dc8139.png)](https://app.slid.cc/vdocs/6d392012404a49188f2a10ec7d7660bd?v=88edd075a0d44e3494dfb6c386a4ca24&start=1843.305838)

- Classful IP 주소 \_ 낭비가 심한

[![network_4 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_images/6d392012404a49188f2a10ec7d7660bd/f372300c-5020-4175-b2d9-abf0526e95f4.png)](https://app.slid.cc/vdocs/6d392012404a49188f2a10ec7d7660bd?v=88edd075a0d44e3494dfb6c386a4ca24&start=1851.507023)

- Classless IP 주소 \_ 낭비되지 않도록 아껴쓰는

  - 서브넷 마스크

    - 클래스풀한 네이트워크 대역을 나눠주는데 사용하는 값

    - 2진수로 표기시 1로 시작, 1과 1 사이에는 0이 올 수 없다는 규칙을 가지고 있음

[![network_4 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/6d392012404a49188f2a10ec7d7660bd/a9d2131b-b6f5-4db3-83b3-4468e993f75e.png)](undefined)

사설 IP와 공인 IP

- 공인 IP 1개당 2^32개의 사설IP

  - 외부와 통신할때 공인 IP를 사용

  - 외부 네트워크 대역에서는 사설 IP 대역이 보이지 않음 \_ 사용할려면 포트 포워딩 해야 함

### 특수한 IP 주소

- 0.0.0.0

  - Wildcard

  - 나머지 모든 IP

- 127.0.0.1

  - 자기 자신을 나타내는 주소

- 게이트웨이 주소

  - 외부와 통신하는 문

  - 일반적으로 공유기 IP를 사용

[![network_4 image](https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/6d392012404a49188f2a10ec7d7660bd/fb519898-3b8e-4bf4-a6bf-34a31d958422.png)](undefined)

이 3가지가 기본적으로 구성되어야 인터넷이 된다.

- mac 터미널에서 ip 주소 확인하는 방법 \_ **ipconfig getifaddr en0**
