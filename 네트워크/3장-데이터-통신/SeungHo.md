영상: [[따라學 IT] 03. 가까이 있는 컴퓨터끼리는 이렇게 데이터를 주고받는다](https://youtu.be/HkiOygWMARs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# 2계층에서 하는일

### 2계층의 기능

하나의 네트워크 대역 즉, 같은 LAN 대역에 존재하는 여러 장비들 중에서 어떤 장비가 어떤 장비에게 보내는 데이터를 전달 + 오류제어, 흐름제어

### 2계층의 네트워크 크기

하나의 네트워크 대역 LAN에서만 통신할 때 사용

→ 다른 네트워크와 통신할 때는 항상 3계층의 주소와 3계층의 프로토콜을 이용해야 가능

# 2계층에서 사용하는 주소

### 물리적인 주소

**MAC 주소 -** 물리적인 장비가 부여받은 고유한 주소값

16진수로 표시 : 6C-29-95 04-EB-A1

6C-29-95 : OUI / IEEE에서 부여하는 일종의 제조회사 식별 ID

04-EB-A1 : 고유번호 / 제조사에서 부여한 고유번호

# 2계층(Ethernet) 프로토콜

### Ethernet 프로토콜

![](https://imgur.com/1v5T78i.png)

**Destination Address**(6Bytes) + **Source Address**(6Bytes) + Ethernet Type(2Bytes) = 14Bytes

Destination Address : 목적지 MAC 주소

Source Address : 출발지 MAC 주소

Ethernet Type : 상위 프로토콜 타입 (IPv4 : 0x0800, ARP : 0x0806)
