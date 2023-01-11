### [IPv4 프로토콜](https://youtu.be/_i8O_o2ozlE?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 다른 네트워크와 데이터를 교환하기 위한 프로토콜
- 하지만 `정확한 전달을 보장하지는 않는다`.(중간에 깨지거나 누락될 수 있음. 이 보정은 4계층에서)
- 멀리 보내는 역할만

#### IPv4 프로토콜 구조

- 기본적으로 20byte라고 생각
- 끝부분 IP Option 부분에 4byte 단위로 추가적으로 붙을 수도 총 10개까지 가능 => 이론적으로 60byte까지 가능

![](https://i.imgur.com/82tJe5T.png)

- Version : 4bit = 0.5byte -> 어차피 IPv4라 4밖에 못씀. IPv6는 애초에 다른 구조
- IHL(Internet Header Length, 헤더 길이) : 0.5byte -> (보통) 20 / 4 -> 5 -> 0101
- TOS(Type of Service) : 1byte 옛날에 쓰던거라 그냥 안씀
- Total Length : 2byte -> payload까지 합친 전체의 길이
- Identification + IP Flags + Fragment Offset : 4byte -> 하나의 세트로 생각
  - Identification : 2byte -> 하나의 정보가 여러 개로 나뉜 각각의 패킷들을 합칠 때 사용하는 ID값
  - IP Flags : 3bit -> 안씀 + Don't fragmentation + More fragmentation
  - Fragment Offset : 13bit -> 원래 데이터를 만들 수 있도록 해주는 시작으로부터 떨어진 정도 정보 
  | 일반적으로 Offset이란 기준으로부터 얼마나 떨어져있다를 의미
- TTL(Time To Live) : 1byte -> 패킷이 살 수 있는 시간
  - 정확히는 시간이라기보다는 숫자를 입력. 장비 넘어갈 때마다 숫자가 1씩 줄어든다.
  - 운영체제마다 TTL 값이 달라서 상대 컴퓨터의 OS를 알 수 있다.(Windows 128, Linux 64)
- Protocol : 1byte -> 상위 Protocol이 뭔지 알려 줌(ICMP 0x01, TCP 0x06, UDP 0x11(17))
- Header Checksum : 2byte -> Header 오류를 확인하는 값. 받는 쪽에서 자신이 계산한 Header와 Checksum 값을 비교. 다르면 오류
- Source Address : 4byte
- Destination Address : 4byte
- IP Option : 0 ~ 40byte


### [ICMP 프로토콜](https://youtu.be/JaBCIUsFE74?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- Internet Control Message Protocol 인터넷 제어 메시지 프로토콜
- 제어 메시지를 주고 받는 프로토콜
- 즉, 통신이 잘 되나 안되나를 확인하기 위한 프로토콜

#### ICMP 프로토콜 구조

- 총 8byte

![](https://i.imgur.com/N84I4UP.png)

- Type : 1byte -> 대분류. 종류가 많음. 기본적으로 0(응답), 3(목적지 도달 불가), 8(요청), 11(요청 시간 만료), 5(원격지 라우팅 테이블 수정, 옛날에나 썼음) 정도 알면 좋음
  - 보통 3은 도달 but 시간 만료, 11은 상대방 문제(방화벽 등)
- Code : 1byte -> 소분류
- Checksum : 2byte

### [IPv4, ICMP프로토콜 실습](https://youtu.be/8ZwTvTuZlVw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- Wireshark 실습
- packet capture, ping, icmp 필터로 확인

### [라우팅 테이블](https://youtu.be/CjnKNIyREHA?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 다른 네트워크를 찾아가는 지도
- 3계층 역할은 최적의 경로를 찾아가는 것. 경로를 저장해두고 있는 것이 라우팅 테이블
- `netstat -r`로 찾을 수 있음. 
- 라우팅 테이블에 없는 정보는 찾아갈 수 없음
- 테이블에 없는 정보는 0.0.0.0 등 설정으로 찾아간다.

#### 다른 네트워크와의 통신 과정

A가 B와 통신하기 위해서는 B의 네트워크 대역이 A의 라우팅 테이블에 있어야 한다. 보통은 네트워크 엔지니어가 설정해준다.
알고 있는 상태를 상정

*ICMP로 통신 확인만 하기 위한 과정*

1. ICMP 요청 프로토콜 작성 : Type 8(요청)
2. IP 프로토콜 작성
3. Ethernet 프로토콜  
   - 이 때의 MAC 주소는 B의 MAC 주소가 아닌, `게이트웨이의 MAC주소`
   - 이 과정에서 다음 MAC 모르면 그 때마다 ARP 통신한다(!)
5. 게이트웨이가 받고 자신의 IP와 비교
6. 다르므로 Ethernet 프로토콜을 다시 작성
7. 이를 반복
8. B에 도달
9. 자신의 IP와 같으므로 ICMP 응답 반대로 작성 : Type 0(응답)

### [라우팅 테이블 확인 실습](https://youtu.be/tVntagSJctc?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 네트워크 대역이 써있음 보통
- 특정 네트워크와 통신하고 싶은데 테이블에 없으면 안된다. 추가를 해줘야함

### [IPv4 조각화 이론](https://youtu.be/_AONcID7Sc8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 보통 네트워크 장비들은 최대 전송 단위(Maximum Transmission Unit)가 1500byte로 설정되어 있다.
- 네트워크 장비들을 통해서 하나의 패킷이 전달될 때, 보내려는 데이터가 1500byte보다 크면 데이터를 잘게 쪼개는 작업 => 조각화

- MTU는 IP 프로토콜까지 포함한다. 그러면 보통 기본적으로 20byte는 빠짐. 순수 payload는 MTU - 20byte 정도. IPv4의 크기까지 계산에 넣어줘야 한다.

> 예를 들어서 보내야하는 데이터 11,980byte, MTU가 3300byte일 경우  
> -> 3280, 3280, 3280, 2140 이런식으로 20byte 빼고 계산해줘야한다.

- 조각화시 뒤에 조각 있으면 MF(More Fragment)에 1, Offset은 byte / 8 적으면 됨.
- 맨 뒤의 조각인 경우 MF에 0 쓰면 됨.
- identification 값은 전부 다 똑같다.

- MTU가 1,500일 경우 - 2,000byte의 data를 전송하기 위해 8byte ICMP, 20byte IPv4가 붙으면 한번에 전송할 수 없다. 그래서 1480, 520으로 나누고 전송. 이후는 그림과 같음.
- 순서가 중요한데, IP까지 붙이고 MTU로 거르고서 Ethernet이 붙는다. 그래서 보통 1500 앞에 Ethernet 14가 붙어서 1514
- ICMP는 MTU 여유가 남는 마지막 패킷에 담긴다.


![](https://i.imgur.com/UCaW7HA.png)


### [IPv4 조각화 실습](https://youtu.be/QKEL9aBgHtg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 보내려는 데이터 크기 : 2379
- MTU : 980

1. 몇개의 패킷?  
  - 960 단위(IP 20 빼고)로 쪼개면 3개
2. 첫번째 패킷의 데이터 크기?  
  - 960 byte
3. 마지막 패킷의 데이터 크기?
  - 459 byte(offset 240)

- 역시 실습 영상 좋다.