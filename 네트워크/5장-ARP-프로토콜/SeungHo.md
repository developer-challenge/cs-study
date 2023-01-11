# [ARP 프로토콜](https://youtu.be/LDsp-Xb168E?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### ARP가 하는 일

같은 네트워크 대역에서 통신을 하기 위해 필요한 **MAC주소를 IP주소를 이용해서 알아오는** 프로토콜

### ARP 프로토콜의 구조

![](https://i.imgur.com/9q6BqAx.png)

Hardware type : 2계층에서 사용하는 프로토콜 (Ethernet : 0x0001)

Protocol type : Protocol Address type (IPv4 : 0x0800)

Hardware Address Length : MAC 주소 길이 (0x06)

Protocol Address Length : IPv4 주소 길이 (0x04)

Opcode : 요청인지 응답인지 나타내는 코드

**Source Hardware Address** : 출발지 MAC 주소 (6Bytes)

**Source Protocol Address** : 출발지 IPv4 주소 (4Bytes)

**Destination Hardware Address** : 목적지 MAC 주소 (6Bytes)

**Destination Protocol Address** : 목적지 IPv4 주소 (4Bytes)

# ARP 프로토콜의 통신 과정

A컴퓨터 → C컴퓨터 통신

![](https://i.imgur.com/D0nkjBp.png)

ARP 프로토콜의 목적지 MAC 주소를 모르기 때문에 0으로 세팅

![](https://i.imgur.com/SQsbw2a.png)

이더넷 프로토콜의 목적지 MAC 주소를 브로드캐스트로 세팅

![](https://i.imgur.com/4WMAgce.png)

2계층 장비 스위치에서 이더넷 프로토콜까지만 확인하고 목적지 MAC 주소에 전달

![](https://i.imgur.com/wRVDHN8.png)

목적지 IP 주소를 확인하여 자신에게 온 요청 구분

![](https://i.imgur.com/h88BoDk.png)

ARP 프로토콜에 본인의 MAC 주소와 Opcode를 2(응답)로 세팅하여 응답

![](https://i.imgur.com/L5PRWoh.png)

이더넷 프로토콜에 목적지 MAC주소 세팅

- 3계층 장비는 브로드캐스트가 오면 외부 네트워크로 보내지 않음

# ARP 테이블

### 나와 통신했던 컴퓨터들

통신했던 컴퓨터들의 주소는 ARP 테이블에 남음

![](https://i.imgur.com/I1DnMg0.png)

# [ARP 프로토콜 실습](https://youtu.be/-M_S50Ga384?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)
