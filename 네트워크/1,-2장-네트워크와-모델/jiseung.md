전체영상: [네트워크 기초(개정판)](https://www.youtube.com/playlist?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# [네트워크란?](https://youtu.be/Av9UFzl_wis?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

<aside>
🌐 노드들이 데이터를 공유할 수 있게 하는 디지털 전기 **통신망**의 하나
</aside>

- **인터넷** : 문서, 이미지, 영상과 같은 여러가지 데이터를 공유하도록 구성된 세상에서 가장 큰 네트워크
- **www** : 인터넷 중 웹 서비스

## 네트워크의 분류

### 크기에 따른 분류

- **LAN** (Local Area Network)
    - 가까운 지역을 하나로 묶은 네트워크
    - PC방에서 Battle.net으로 스타크래프트 할 때 LAN UDP 사용
- **WAN** (Wide Area Network)
    - 멀리 있는 지역을 하나로 묶은 네트워크
    - 여러개의 LAN을 묶은 것
- MAN (Metropolitan Area Network)
- 기타 (VLAN, CAN, PAN …)

### 연결 형태에 따른 분류

- Star형(성형)
    - 중앙 장비에 모든 노드 연결
    - 중앙 장비가 고장나면 큰일
    - LAN 대역에서 많이 사용
- Mesh형(망형)
    - 여러 노드들이 서로 그물처럼 연결
    - 우회가 가능
- 혼합형
    - 여러 형태를 혼합
    - 여러개의 LAN을 연결 ⇒ WAN
    - 실제 인터넷은 보통 이런 식으로 되어 있다.

## 네트워크의 통신 방식

- 유니 캐스트
    - 특정한 대상과 통신 (1:1)
- 멀티 캐스트
    - 특정 다수와 통신 (1:N)
- 브로드 캐스트
    - 같은 대역의 모두와 통신

## 네트워크 프로토콜

<aside>
🤙 네트워크에서 노드와 노드가 통신할 때 누가 누구에게 어떤 데이터를 어떻게 보내는지에 대한 약속(양식)

</aside>

- Ethernet 프로토콜 (MAC 주소)
    - 가까운 곳과 통신할 때
- ICMP, IPv4, ARP (IP 주소)
    - 멀리 있는 곳과 통신할 때
- TCP, UDP (포트 번호)
    - 여러가지 프로그램으로 통신할 때

> 실제로는 여러 개의 프로토콜을 함께 사용하게 되는데,
이 프로토콜들이 합쳐진 것(캡슐화)을 **패킷**이라고 한다.
>

# [실습1 (traceroute)](https://youtu.be/paJf7JbBWqY?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

<aside>
🤔 구글 서버와 내 컴퓨터가 어떻게 연결되어 있을까?
</aside>

```shell
traceroute 8.8.8.8
```
- 여러 개의 네트워크 대역을 거쳐 구글 서버에 도달
- 보통 15개 안으로 전 세계가 연결되어 있음

### [실습2 (Wireshark)](https://youtu.be/vBrQ3yzerMg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

<aside>
🤔 프로토콜은 어떻게 생겼을까?
인터넷을 통해 주고 받는 모든 내용을 직접 확인해보자!
</aside>

- Ethernet (가까운 곳)
- IPv4 (멀리 떨어져 있음)
- TCP (Chrome - 웹 서버 연결)

# [네트워크 모델](https://youtu.be/y9nlT52SAcg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 네트워크 모델의 종류

### TCP/IP 모델

- 1960년대~
- 현재 인터넷에서 컴퓨터들이 서로 데이터를 주고 받는데 쓰이는 통신 규약(프로토콜)의 모음

### OSI 7계층 모델

- 1984년~
- 네트워크 통신을 체계적으로 다루는 ISO에서 네트워크 표준으로 지정한 모델
- 데이터를 주고 받을 때 데이터 자체의 흐름을 각 구간 별로 나눠 놓은 것

| 7계층 (응용) | HTTP … |
| --- | --- |
| 6계층 (표현) | … |
| 5계층 (세션) | … |
| 4계층 (전송) | TCP, UDP … |
| 3계층 (네트워크) | ICMP, IP, ARP … |
| 2계층 (데이터링크) | 이더넷 … |
| 1계층 (물리) | … |

## 두 모델 비교

### 공통점

- 계층적 네트워크 모델
- 계층 간 역할을 정의

### 차이점

- 계층의 수

| TCP/IP | OSI |
| --- | --- |
| 프로토콜 기반 (기술적) | 역할 기반 (논리적) |
| 데이터 전송 기술 특화 | 통신 전반에 대한 표준 |

## 네트워크를 통해 전달되는 데이터, 패킷

### 패킷이란?

<aside>
🤔 네트워크 상에서 전달되는 데이터를 통칭
네트워크에서 전달하는 데이터의 형식화된 블록
패킷은 제어 정보와 사용자 데이터(페이로드)로 이루어져 있다.

</aside>

| header | (payload) | (footer) |
| --- | --- | --- |

### 패킷을 이용한 통신 과정

- **패킷**
    - 순서가 있는 여러 프로토콜들로 **캡슐화**된 것
    - 상위 계층과 하위 계층이 역전된 형태로는 패킷이 만들어질 수 없음
- **Encapsulation**
    - 여러 프로토콜을 이용해 최종적으로 보낼 패킷을 만드는 과정
    - 상위 프로토콜부터 하나씩 순서대로 캡슐화
        
        
        | Ethernet | IPv4 | TCP | HTTP (header) | Data (payload) |
        | --- | --- | --- | --- | --- |
        
        | Ethernet | IPv4 | TCP (header) | HTTP + Data (payload) |
        | --- | --- | --- | --- |
        
        | Ethernet | IPv4 (header) | TCP + HTTP + Data (payload) |
        | --- | --- | --- |
        
        | Ethernet (header) | IPv4 + TCP + HTTP + Data (payload) |
        | --- | --- |
- **Decapsulation**
    - 패킷을 받았을 때 프로토콜들을 하나씩 확인하면서 데이터를 확인하는 과정
    - 하위 프로토콜부터 하나씩 순서대로 디캡슐화
        
        
        | Ethernet (header) | IPv4 + TCP + HTTP + Data (payload) |
        | --- | --- |
        
        | Ethernet | IPv4 (header) | TCP + HTTP + Data (payload) |
        | --- | --- | --- |
        
        | Ethernet | IPv4 | TCP (header) | HTTP + Data (payload) |
        | --- | --- | --- | --- |
        
        | Ethernet | IPv4 | TCP | HTTP (header) | Data (payload) |
        | --- | --- | --- | --- | --- |

### 계층별 패킷의 이름 PDU

<aside>
📦 어느 계층까지 캡슐화되어 있는 패킷이냐에 따라 부르는 이름이 다르다.

</aside>

- PDU (Protocol Data Unit)
    - (4계층) **Segment** = TCP + Data
    - (3계층) **Packet** = IPv4 + TCP + Data
    
        ARP : 3계층까지 Encapsulation
        자기 자신이 header. payload X <br />
        ICMP : 3계층까지 Encapsulation<br />
        IPv4 : 3계층까지 Encapsulation<br />
        
    - (2계층) **Frame** = Ethernet + IPv4 + TCP + Data

        Ethernet : 2계층까지 Encapsulation.
        footer를 사용하는 프로토콜<br />
        Frame : 2계층까지 Encapsulation
        전체 프로토콜