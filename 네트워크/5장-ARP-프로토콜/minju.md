# [ARP 프로토콜](https://youtu.be/LDsp-Xb168E?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## ARP가 하는 일

같은 네트워크 대역에서 통신을 하기 위해 필요한 MAC 주소를 IP 주소를 이용해 알아오는 프로토콜

같은 네트워크 대역에서 통신을 하더라도 데이터를 보내기 위해선 7계층부터 캡슐화를 통해 데이터를 보내기 때문에 IP 주소와 MAC 주소 모두 필요
이 때, IP 주소는 알고 MAC 주소는 모르더라도 ARP를 통해 통신이 가능하다.

- ARP 스푸핑

## ARP 프로토콜의 구조

![](https://i.imgur.com/qOyIA6s.png)

총 28byte

Source Hardware Address = 송신지의 MAC 주소 (6byte)
Source Protocol Address = IPv4 주소 (4byte)
Destination Hardware Address = 수신지의 MAC 주소 (6byte)

- 이더넷 프로토콜만 목적지가 먼저오고 다른 프로토콜들은 송신지가 먼저 옴.

Hardware type (2byte) : 주로 이더넷 프로토콜(0001)
Protocol type (2byte) : 주로 IPv4 (0800)
Hardware Address Length : MAC 주소의 길이 = 6byte(06)
Protocol Address Length : IPv4 주소의 길이 = 4byte(04)
Opcode : 어떻게 동작하는지 나타내는 코드

- 1 : 요청
- 2 : 응답

# ARP 프로토콜의 통신 과정

## IP 주소로 MAC 주소를 알아오는 과정

1. A 컴퓨터가 ARP 요청 (캡슐화)
   - 이때 목적지 MAC 주소를 모르기 때문에 ARP 요청의 DMAC에 `00:00:00:00:00:00` 으로 작성해서 보냄.
   - 이더넷 프로토콜에는 `FF:FF:FF:FF:FF:FF`로 작성해서 보냄
2. 요청을 받은 스위치는 이더넷 프로토콜까지만 디캡슐링
   - 전부다 1로 채우는 경우 **브로드캐스트**가 됨
     -> 같은 네트워크 대역에 있는 모두에게 보냄
3. 요청을 받은 기기에서 ARP 요청 디캡슐링
   - IP 주소를 비교 후, 본인의 IP주소와 일치하지 않으면 패킷을 버림
4. 본인의 IP 주소인 경우, 응답 패킷 생성 (0002)
5. 응답 패킷 캡슐화 후 전송 ~ 이하 위의 과정 같음 ~
6. 응답을 받은 A 컴퓨터는 알게된 MAC주소와 IP주소를 ARP 캐시 테이블에 저장

# ARP 테이블

## 나와 통신했던 컴퓨터들

```
arp -a
```

일정 시간이 지나면 없어짐

# [ARP 프로토콜 실습](https://youtu.be/-M_S50Ga384?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## ARP 테이블 확인해보기

![](https://i.imgur.com/2UwqPV1.png)

## ARP 프로토콜 분석하기

info에 물음표가 달린 경우 요청 패킷

![](https://i.imgur.com/prH0b7f.png)

![](https://i.imgur.com/FVVrCXT.png)

프레임의 최소 단위(60byte)를 맞추기 위해 뒤에 무의미한 숫자를 붙이기도 함.

- 최대 1514byte
- 요청의 경우, 패딩이 안붙는 것 처럼 보이기도 함 (42byte)
