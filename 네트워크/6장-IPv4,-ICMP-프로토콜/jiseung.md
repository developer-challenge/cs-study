## [IPv4 프로토콜](https://youtu.be/_i8O_o2ozlE?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### IPv4가 하는 일

> 네트워크 상에서 데이터를 교환하기 위한 프로토콜

데이터가 정확하게 전달될 것을 보장하지 않는다. (다른 계층(4, TCP)에서 보강)
중복된 패킷을 전달하거나 패킷의 순서를 잘못 전달할 가능성(DoS)

>

### IPv4 프로토콜의 구조

> 다른 네트워크의 특정 대상을 찾는다.

- Version
  - 4bit
  - IP 프로토콜의 버전 (4, 6)
  - 4가 온다. 6은 구조가 다름
- IHL (Header Length)
  - 4bit
  - 헤더의 길이(20byte ~60byte)
- Type of Service(TOS)
  - 현재는 사용하지 않음
  - 0 0
- Total Length
  - 전체 길이
- Identification
  - 패킷이 하나의 데이터인지 알기 위한 ID
- IP Flags
  - x D M (3bit)
  - D : Don’t Fragment
    - 데이터를 안쪽에서 보냄을 명시
    - 조각화를 원하지 않음 → 실질적으로 전송 불가
  - M : More Fragment
    - 패킷이 더 있음 (1)
- Fragment Offset
  - 13bit
  - 조각화를 복구할 때 순서를 지켜야 함
  - 시작점으로부터 얼마나 떨어져 있는지 제공
- Time To Live (TTL)
  - 패킷이 살아 있는 시간 (지정한 숫자가 1씩 줄어듦)
  - 네트워크 장비들끼리 DoS 공격을 하듯 돌고 도는 경우를 방지
- Protocol
  - 상위 프로토콜 정보
  - ICMP(0 1), TCP(0 6), UDP(1 1)
- Header Checksum
  - 헤더에 오류가 있는지 없는지 확인
- Source Address
- Destination Address
- IP Option(20byte ~60byte)

![Request](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/395db3ca-eafc-4e60-a2ff-3edb7aee4c9c/Untitled.png)

Request

![Reply](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7f4acf14-3771-4200-9dd9-6383d9f7fbed/Untitled.png)

Reply

## [ICMP 프로토콜](https://youtu.be/JaBCIUsFE74?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### ICMP가 하는 일

> ICMP (Internet Control Message Protocol, 인터넷 제어 메시지 프로토콜)

네트워크 컴퓨터 위에서 돌아가는 운영체제에서 오류 메시지를 전송 받는 데 주로 쓰인다.

프로토콜 구조의 Type과 Code를 통해 오류 메시지를 전송 받는다.

>

### ICMP 프로토콜의 구조

> 특정 대상과 내가 통신이 잘되는지 확인

- Type
  - 대분류
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e43d080e-1386-4e07-bc07-5ed8bdfabc27/Untitled.png)
    - 0 : 응답
    - 8 : 요청
    - 3 : 목적지 도달 불가 (라우터 등 경로 설정 오류)
    - 11 : 요청 시간 만료 (상대방에게 방화벽이 있는 경우)
    - 5 : 원격지에서 상대의 라우팅 테이블 수정. 보안
- Code
  - 소분류

## [IPv4, ICMP프로토콜 실습](https://youtu.be/8ZwTvTuZlVw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 다른 네트워크와 통신 과정

1. 요청 프로토콜 작성
   1. ICMP
      1. type : 08 (요청)
      2. code : 00
   2. IPv4
      1. 버전4, header길이 …
      2. 출발지 IP, 도착지 IP..
   3. Ethernet
      1. 목적지 MAC 주소
      2. 가까운 곳에서 갈 수 있는 곳까지 작성
2. 요청 패킷 전송
   1. 다른 네트워크 대역으로 이동
   2. 확인 가능한 계층까지 디캡슐화하며 패킷 확인
   3. 다시 캡슐화
      1. 가까운 곳에서 갈 수 있는 곳의 MAC주소로 Ethernet 프로토콜 내용 변경
      2. Ethernet 프로토콜은 네트워크 대역이 바뀔 때마다 새로 작성
3. 패킷 도착
   1. ICMP 요청 확인
4. 응답 프로토콜 작성

   1. ICMP
      1. type : 00 (응답)

   …

5. 응답 패킷 전송

> 모든 과정에서 MAC주소를 모르는 경우 중간 중간 계속해서 ARP 통신이 있다.

## [라우팅 테이블](https://youtu.be/CjnKNIyREHA?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

> 최적의 경로를 지도처럼 저장

### 내가 보낸 패킷은 어디로 가는가

> 어디로 보내야 하는지 설정
> 설정되지 않은 대역으로 찾아갈 수 없음

## [라우팅 테이블 확인 실습](https://youtu.be/tVntagSJctc?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

```bash
netstat -r
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/40dcecde-0648-42c1-9e16-dfbee8127a46/Untitled.png)

- 네트워크 대역, 서브넷 마스크 확인
- 멀리 있는, 모르는 네트워크 대역은 게이트웨이를 통해 확인

## [IPv4 조각화 이론](https://youtu.be/_AONcID7Sc8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 조각화란?

- MTU(Maximum Transmission Unit)
  - 데이터의 최대 전송 단위 (1500byte)
  - header + payload

> 큰 IP 패킷들이 작은 MTU를 갖는 링크를 통해 전송되려면 여러 개의 작은 패킷으로 조각화되어 전송돼야 한다.

목적지까지 패킷을 전달하는 과정에 통과하는 각 라우터마다 전송에 적합한 프레임으로 변환이 필요하다.

일단 조각화되면, 최종 목적지에 도달할 때까지 재조립되지 않는 것이 일반적이다.

IPv4에서는 발신지 뿐만 아니라 중간 라우터에서도 IP 조각화가 가능하다.
IPv6에서는 IP 조각화가 발신지에서만 가능하며 재조립은 항상 최종 수신지에서만 가능하다.

>

### 조각화하는 과정

> 큰 데이터를 보낼 때 패킷을 조각화

- ID, MF(More Fragment), Offset
- 동일한 ID의 IP 프로토콜로 각각 Encapsulation된다.
- ICMP는 마지막 패킷에만 존재
- offset : 앞 data크기 / 8

## [IPv4 조각화 실습](https://youtu.be/QKEL9aBgHtg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 보내는 데이터 : 2379
- MTU : 980
- 조각화
  - 960/960/459로 나누고
  - 각각 IP 프로토콜로 Encapsulation
  - 20+960(id, mf 1, offset 0)/20+960(id, mf 1, offset 120)/20+459(id, mf 0, offset 240)+ICMP

```bash
ping ip주소 -l 데이터크기
```
