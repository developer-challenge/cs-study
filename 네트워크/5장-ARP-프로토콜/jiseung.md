## [ARP 프로토콜](https://youtu.be/LDsp-Xb168E?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### ARP가 하는 일

> ARP 프로토콜은 같은 네트워크 대역에서 통신을 하기 위해 필요한 MAC주소를 IP주소를 이용해 알아오는 프로토콜

- 같은 네트워크 대역에서 통신을 하더라도 데이터를 보내기 위해 7계층부터 캡슐화를 통해 데이터를 보내므로 IP주소와 MAC주소가 모두 필요하다.
  - 이때 IP주소만 알더라도 ARP를 통해 통신이 가능하다.
- ARP 스푸핑
  - 테이블을 속이는 네트워크 공격

### ARP 프로토콜의 구조

- Hardware type
  - 2계층 프로토콜 타입(이더넷, 0 0 0 1)
- Protocol type
  - 프로토콜 타입 (IPv4, 0 8 0 0)
- Hardware Address Length
  - MAC주소(6byte, 0 6)
- Protocol Address Length
  - IP 주소(4byte, 0 4)
- Opcode
  - Operation code (0 0 0 X)
  - 요청 - 응답 상태를 나타내는 값
    - 1 : 요청
    - 2 : 응답
- Source Hardware Address
  - 출발지 MAC 주소
- Source Protocol Address
  - 출발지 IP 주소
- Destination Hardware Address
  - 도착지 MAC 주소 (모르는 경우 0으로 표현)
  - Ethernet에서는 모르는 경우 F로 표현
- Destination Protocal Address
  - 도착지 IP 주소

## ARP 프로토콜의 통신 과정

### IP주소로 MAC주소를 알아오는 과정

- 2계층 스위치가 Ethernet(ARP의 헤더)을 확인하며 같은 네트워크 대역의 모든 장비 확인
- 목적지 IP가 다르면 패킷 drop
- 확인된 목적지가 ARP 응답을 준다.
  - Opcode 2
  - 출발지 MAC주소에 응답을 준다.
- ARP 캐시 테이블에 확인된 MAC주소 등록

## ARP 테이블

### 나와 통신했던 컴퓨터들

> 통신 했던 컴퓨터들의 주소는 ARP 테이블에 남는다.

- 시간이 지나면 없어짐
- 만료시 ARP 통신
- 수동으로 영구 등록 가능

## [ARP 프로토콜 실습](https://youtu.be/-M_S50Ga384?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### ARP 테이블 확인

```bash
arp -a
```

- 3계층 ARP
  - 같은 네트워크 대역에서만 사용
    - MAC주소를 몰라서 대역 안에서만 브로드캐스트

### ARP 프로토콜 분석

Frame 최소단위 60byte. 최대 1514byte. padding 18byte
