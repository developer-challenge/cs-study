# [IPv4 프로토콜](https://youtu.be/_i8O_o2ozlE?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### IPv4가 하는 일

네트워크 상에서 데이터를 교환하기 위한 프로토콜

데이터가 **정확하게 전달될 것을 보장하지 않음**(중복된 패킷 전달, 잘못된 패킷 순서 전달 → 악의적으로 이용되면 Dos 공격)

- 데이터의 정확하고 순차적인 전달은 상위 프로토콜인 TCP에서 보장

### IPv4 프로토콜의 구조

![](https://i.imgur.com/xR3wjNR.png)

Version : 4 (IPv6는 6이 아님, 구조자체가 다름)

IHL(Header Length) : 헤더 길이를 4로 나눈 값

Type of Service(TOS) : 데이터의 형식(현재는 쓰이지 않음, 0으로 비워져있음)

Total Length : 헤더 + 페이로드 전체 길이

Identification : 쪼개진 네트워크 패킷들을 다시 합칠 때 판단할 수 있는 ID값(큰 데이터를 보낼 때 최소전송단위가 있어 잘게 쪼개보냄)

IP Flags : 3비트로 이루어져있음

- x : 사용 안함
- D : Don’t Fragment데이터를 쪼개지 않고 보낸다는 표시(최소전송단위보다 크게 보내면 전송 안됌 → 거의 사용 안함)
- M : More Fragments, 쪼개진 패킷이 추가로 더 있다고 표시

Fragment Offset : 쪼개진 패킷의 순서를 파악하기 위한 표시(시작부터 얼마만큼 떨어져 있는지)

Time To Live(TTL) : 패킷의 유효 시간(3계층 네트워크 장비를 지날 때 마다 1씩 차감 / Window : 128, Linux : 64)

Protocol : 상위 프로토콜 (ICMP : 1, TCP : 6, UDP : 17 - 0x11)

Header Checksum : 오류 체크

# [ICMP 프로토콜](https://youtu.be/JaBCIUsFE74?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### ICMP가 하는 일

Internet Control Message Protocol, 인터넷 제어 메시지 프로토콜

네트워크 컴퓨터 위에서 돌아가는 운영체제에서 **오류 메시지**를 전송 받는 데 주로 쓰임

프로토콜 구조의 Type과 Code를 통해 오류 메시지를 전송 받음

### ICMP 프로토콜의 구조

![](https://i.imgur.com/FV4m6Zw.png)

Type : 카테고리, 대분류

- **0 : Echo Reply** (통신 확인 / 응답)
- **3 : Destination Unreachable** (목적지까지 가지 못함 / 경로상의 문제 - 라우터..)
- **5 : Redirect** - 원격지에 있는 라우팅 테이블을 ICMP로 수정할 때 사용(요즘은 잘 안쓰임)
- **8 : Echo** (통신 확인 / 요청)
- **11 : Time Exceded** (목적지까지 갔지만 응답 X / 상대방의 문제 - 방화벽..)

Code : 소분류

Checksum : 오류 체크

ICMP 프로토콜 - 특정 대상과 내가 통신이 잘 되는지 확인

# [IPv4, ICMP프로토콜 실습](https://youtu.be/8ZwTvTuZlVw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# [라우팅 테이블](https://youtu.be/CjnKNIyREHA?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 라우팅 테이블

3계층의 대표적인 역할은 최적의 경로를 위의 프로토콜들로 찾아가는 것

→ 최적의 경로를 지도처럼 저장해 놓음

패킷을 어디로 보내야 하는지 설정되어 있음

**다른 네트워크 대역을 찾아가는 경로에 대한 지도**

netstat -r 명령어로 라우팅 테이블 확인

![](https://i.imgur.com/PxgGmLn.png)

라우팅 테이블에 적혀있는 네트워크 대역만 찾아갈 수 있음

→ 테이블에 없으면 찾아갈 수 없으므로 0.0.0.0 설정(테이블에 없으면 게이트웨이로 일단 나가라)

### 외부 네트워크 통신 과정

![](https://i.imgur.com/p9OjORe.png)

A컴퓨터 → B컴퓨터 통신

192.168.20.0/24 → 192.168.10.1 설정된 라우팅 테이블

![](https://i.imgur.com/6hgPGt7.png)

ICMP 요청 - Type : 08

![](https://i.imgur.com/ERapHJj.png)

IPv4 프로토콜

![](https://i.imgur.com/efUfTKh.png)

Ethernet 프로토콜의 목적지 MAC 주소는 가까운/같은/LAN 네트워크 범위

aa:aa:aa:aa:aa:aa → cc:cc:cc:cc:cc:cc(라우팅 테이블의 게이트 웨이 MAC 주소)

![](https://i.imgur.com/5beBkEv.png)

라우터에서 목적지 IP주소 확인 후

![](https://i.imgur.com/SUxm0ER.png)

본인의 라우팅 테이블 확인

![](https://i.imgur.com/M14lG9c.png)

이더넷 프로토콜의 목적지 MAC 주소 다시 작성

![](https://i.imgur.com/MnEk1kq.png)

B컴퓨터까지 위의 과정을 거쳐 전송하여 ICMP 요청을 받으면 ICMP 응답을 작성하여 A컴퓨터에게 전송

# [라우팅 테이블 확인 실습](https://youtu.be/tVntagSJctc?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/iPXbjjt.png)

네트워크 대상 : 보통 네트워크 대역, 특정 IP주소가 쓰여져 있기도 함

# [IPv4 조각화 이론](https://youtu.be/_AONcID7Sc8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 조각화란?

![](https://i.imgur.com/941giSA.png)

큰 IP 패킷들이 적은 **MTU(Maximum Transmission Unit)**를 갖는 링크를 통하여 전송되려면 **여러 개의 작은 패킷으로 쪼개어/조각화 되어 전송**되어야 함

- 일반적으로 MTU는 1500Bytes로 설정되어있음

일단 조각화되면, **최종 목적지에 도달할 때까지 재조립되지 않는 것**이 일반적

- IPv4에서는 발신지 뿐만 아니라 중간 라우터에서도 IP 조각화 가능
- IPv6에서는 IP 단편화가 발신지에서만 가능
- 재조립은 항상 최종 수신지에서만 가능

MF : More Fragments 1 → 뒤에 패킷이 더 있음, 0 → 마지막 패킷

Offset : Data / 8

### 큰 데이터를 전송하는 패킷이 조각화하는 과정

![](https://i.imgur.com/uqVc4wv.png)

![](https://i.imgur.com/Y3YL0sb.png)

IPv4가 붙고 MTU → 이더넷 프로토콜 ⇒ 1514Bytes

# [IPv4 조각화 실습](https://youtu.be/QKEL9aBgHtg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

보내려는 데이터 크기 : 2379Bytes

MTU : 980Bytes

![](https://i.imgur.com/SEQa3up.png)

몇 개의 패킷으로 쪼개지는가? ⇒ 3개

첫 번째 패킷의 데이터의 크기는 몇인가? ⇒ 960Bytes

마지막 패킷의 데이터의 크기는 몇인가? ⇒ 459Bytes
