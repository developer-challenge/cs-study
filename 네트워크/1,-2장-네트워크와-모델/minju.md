전체영상: [네트워크 기초(개정판)](https://www.youtube.com/playlist?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# [네트워크란?](https://youtu.be/Av9UFzl_wis?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 네트워크란 무엇인가?

### 네트워크란?

노드들이 데이터를 공유할 수 있게 하는 디지털 전기 통신망의 하나
= **분산되어 있는 컴퓨터를 통신망으로 연결한 것**

네트워크에서 여러 장치들은 노드 간 연결을 사용해 서로 데이터 교환

> `노드` : 네트워크에 속한 컴퓨터 또는 통신 장비

### 인터넷이란?

문서, 그림 영상과 같은 여러가지 데이터를 공유하도록 구성된 세상에서 가장 큰 네트워크

네트워크 안에 인터넷이 포함

> `www !== 인터넷`
> www는 인터넷을 통해 **웹**과 관련된 데이터를 공유하는 것

## 네트워크의 분류

### 크기에 따른 분류

- `Local Area Network (LAN)` : 가까운 지역을 하나로 묶은 네트워크
  - 연결 가능한 컴퓨터 찾기 쉬운 방법 = 어떤 장비에 연결이 되어있는지 확인
- `Wide Area Network (WAN)` : 멀리 있는 지역을 한데 묶은 네트워크
  - 가까운 지역끼리 묶인 LAN과 LAN을 다시 하나로 묶은 것
- `Metropolitan Area Network (MAN)`
- `VLAN, CAN, PAN etc`

### 연결 형태에 따른 분류

- `Star형` : 중앙 장비에 모든 노드가 연결
  - 일반적인 가정집에서는 공유기를 통해 기기들이 연결
  - 공유기가 고장? 모든 기기에 인터넷 연결 불가
  - 주로 LAN 대역을 만들 때 star형 사용
- `Mesh형` : 여러 노드들이 서로 그물처럼 연결
  - 어느 한 부분이 고장나도 일부분은 통신 가능
  - WAN 대역에서 주로 사용
- `혼합형` : 실제 인터넷은 여러 형태를 혼합한 형태
- `Tree형` : 계층 구조로 연결
- `링형, 버스형 등등`

## 네트워크의 통신 방식

### 네트워크에서 데이터를 어떻게 주고 받는가?

> 대상들이 같은 네트워크에 있을 때,

- `유니 캐스트` : 특정 대상이랑만 1:1로 통신
- `멀티 캐스트` : 특정 다수와 1:N으로 통신
- `브로드 캐스트` : 네트워크에 있는 모든 대상과 통신

## 네트워크 프로토콜

대상을 어떻게 찾을 것인가 -> 프로토콜 이용

### 프로토콜이란?

일종의 약속, 양식

네크워크에서 노드와 노드가 통신할 때, 어떤 노드가 어떤 노드에게 어떤 데이터를 어떻게 보내는지 작성하기 위한 양식

각 프로토콜들도 해당 프로토콜만의 양식

### 여러가지 프로토콜

- **가까운 곳과 연락할 때** : Ethernet 프로토콜 `(MAC 주소)`
- **멀리 있는 곳과 연락할 때** : ICMP, IPv4, ARP `(IP주소)`
- **여러가지 프로그램으로 연락할 때** : TCP, UDP `(포트 번호)`
  - 패킷을 이용해 데이터 통신
  - 패킷 : 여러 프로토콜로 캡슐화

# [실습1 (tracert)](https://youtu.be/paJf7JbBWqY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

구글 DNS 서버와 통신

- `traceroute 8.8.8.8` 입력 (using MAC)
- ![](https://i.imgur.com/CFRdx1i.png)
- 통신을 위해 거쳐간 네트워크 대역
- 1번 네트워크 대역 = 공유기

> 홉 : 거쳐간 네트워크 대역

# [실습2 (Wireshark)](https://youtu.be/vBrQ3yzerMg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

![](https://i.imgur.com/MkGU6MA.png)

![](https://i.imgur.com/jzfLgdu.png)

이더넷 - IPv4 - TCP가 캡슐화되어 사용되는 것을 확인

# [네트워크 모델](https://youtu.be/y9nlT52SAcg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 네트워크 모델의 종류

### TCP/IP

| 계층  | TCP / IP 모델       |
| ----- | ------------------- |
| 1계층 | 네트워크 인터페이스 |
| 2계층 | 네트워크            |
| 3계층 | 전송                |
| 4계층 | 응용                |

### OSI 7계층 모델

| 계층  | OSI 7계층 모델 |
| ----- | -------------- |
| 1계층 | 물리           |
| 2계층 | 데이터 링크    |
| 3계층 | 네트워크       |
| 4계층 | 전송           |
| 5계층 | 세션           |
| 6계층 | 표현           |
| 7계층 | 응용           |

![](https://i.imgur.com/3f9EoCM.png)

> 체크된 프로토콜은 구조까지 파악해야함.

## 두 모델 비교

### 공통점

- 계층적 네트워크 모델
- 계층간 역할 정의

### 차이점

- 계층 수
- `OSI` : 역할 기반 / `TCP/IP` : 프로토콜 기반
- `OSI` : 통신 전반에 대한 표준 (이론)
- `TCP/IP` : 데이터 전송 기술 특화 (실전)

## 네트워크를 통해 전달되는 데이터, 패킷

### 패킷이란?

네트워크 상에서 전달되는 데이터를 통칭하는 말
네트워크에서 전달하는 데이터의 형식화된 블록

**제어 정보**와 **사용자 데이터(페이로드)** 로 이뤄짐

패킷간에 순서가 있음.
| **헤더** | **페이로드** | 풋터(옵션) |

### 패킷을 이용한 통신 과정

패킷의 형태는 계속 바뀜 (캡슐화)
| 순서 | 헤더 | 페이로드 |
|-----|-----|------|
|1|HTTP | 데이터|
|2| TCP | HTTP + 데이터 |
|3| IPv4 | TCP + HTTP + 데이터 |
|4| Ethernet | IPv4 + TCP + HTTP + 데이터 |

상위 계층에서 하위 계층으로 내려가며 프로토콜 추가
= 상위 프로토콜 앞에 하위 프로토콜이 올 수 없음

받을 때는 반대로 캡슐을 까보게 됨.

### 계층별 패킷의 이름 PDU

`PDU` : Protocol Data Unit

| 계층  | 패킷     |
| ----- | -------- |
| 4계층 | 세그먼트 |
| 3계층 | 패킷     |
| 2계층 | 프레임   |

ex) `arp` 검색시
![](https://i.imgur.com/81OKCfB.png)

![](https://i.imgur.com/ZJy2wcH.png)
3-3-2 같은 경우도 가능하나 같은 계층이라도 순서는 정해져 있음. ICMP -> IP
