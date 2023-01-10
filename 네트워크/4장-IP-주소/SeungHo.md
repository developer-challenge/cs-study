영상: [[따라學 IT] 04. 실제로 컴퓨터끼리는 IP주소를 사용해 데이터를 주고받는다](https://youtu.be/s5kIGnaNFvM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# 3계층의 기능

### 3계층에서 하는 일

다른 네트워크 대역 즉, **멀리 떨어진 곳에 존재하는 네트워크**까지 어떻게 데이터를 전달할지 제어하는 일 담당 → LAN과 LAN을 연결

발신에서 착신까지의 패킷의 경로 제어

- 3계층 장비 : 라우터
- 2계층 장비 : 스위치

### 3계층에서 쓰는 주소

**IP 주소 :** WAN에서 통신할 때 사용하는 주소

cmd → ipconfig 명령어

![](https://i.imgur.com/gQ85H9I.png)

### 3계층 프로토콜

ARP 프로토콜

![](https://i.imgur.com/ym0C1GK.png)

IPv4 프로토콜

![](https://i.imgur.com/3PZDXk7.png)

ICMP 프로토콜

![](https://i.imgur.com/SolzK14.png)

# 일반적인 IP 주소

### Classful IP 주소

![](https://i.imgur.com/ZLK0v3h.png)

A클래스 한 네트워크 대역에 부여된 컴퓨터 가용수 : 2^24 약 1600만대

→ 보통 다 사용하지 못함 / 낭비가 심함

### Classless IP 주소

![](https://i.imgur.com/zWfQAFV.png)

**서브넷 마스크**

- Classful한 네트워크 대역을 나눠주는데 사용하는 값 (네트워크 대역 / 호스트 구분)
- 2진수로 표기했을 때 1로 시작, 1과 1사이에는 0이 올 수 없음

### 사설 IP와 공인 IP

![](https://i.imgur.com/DaFVo70.png)

현재 사용하고 있는 IPv4 개념 : 사설 IP와 공인 IP + Classless IP

**공인 IP** : 실제 인터넷 네트워크 통신망과 통신할 때 사용하는 IP 주소 (공인 IP 1개당 2^32개의 사설 IP)

**사설 IP** : 같은 네트워크 대역에서 사용하는 IP 주소

다른 네트워크 대역으로 갈 때 공인 IP하나로 바꾸어 통신

**NAT(Network Address Translation)** : 네트워크 주소 변환 기술 (사설 IP → 공인 IP)

- 사설 IP에서 데이터 보낼 때 NAT 테이블에 어떤 요청인지 기록
- 해당 요청에 대한 응답이 왔을 때 NAT 테이블의 기록을 토대로 전달
  - 요청 기록이 없는 응답은 공유기에서 받고 전달 X
    → 사설 IP 주소로 웹 서버를 구축하면 외부에서 해당 웹 서버에 요청 시 응답을 받지 못함

![](https://i.imgur.com/JabLLan.png)

실제 인터넷 세상에서는 공인 IP로만 통신

→ 외부 네트워크 대역에서는 사설 IP 대역이 보이지 않음

# 특수한 IP 주소

### 0.0.0.0/0 - Wildcard

나머지 모든 IP

### 127.0.0.1

나 자신을 나타내는 주소

### 게이트웨이 주소

일반적으로 공유기 IP

기본 게이트웨이 : 네트웨크 대역 중 사용할 수 있는 IP중 가장 작거나 가장 큰 IP 주소
