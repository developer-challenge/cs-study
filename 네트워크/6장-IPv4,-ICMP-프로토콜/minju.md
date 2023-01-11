# [IPv4 프로토콜](https://youtu.be/_i8O_o2ozlE?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## IPv4가 하는 일

다른 네트워크의 특정 대상을 찾아 네트워크 상에서 데이터를 교환하기 위한 프로토콜

**데이터 정확성 보장 ❌**
중복된 패킷을 전달하거나 패킷의 순서를 잘못 전달할 가능성도 있다.
악의적으로 이용시 - DoS공격

그럼 데이터의 정확성과 순서 보장은 어디에서? **TCP(4계층)**

## IPv4 프로토콜의 구조

20byte((옵션(4byte) 추가되는 경우 제외))

### version

- 항상 4

### Header Length

- 최소 20byte, 최대 6byte
- 하지만 4bit가 최대 입력 가능한 칸이므로 실제 길이 / 4 로 작성해줌. ``(20byte -> 5)`

### TOS

- 현재는 사용하지 않음 (0으로 채움)

### Total Length

- header + payload의 길이

### Identification + IP Flags + Fragment Offset

- 큰 데이터를 보낼 때 최대 전송길이가 정해져 있으므로 데이터를 잘라서 보내게 됨.
- 이 때 쪼개진 데이터를 알아볼 수 있게 해주는 값

**`Identification`**
: 쪼개진 데이터들의 ID를 동일하게 줌으로써 동일한 데이터임을 알게 함.

**`IP Flags`(3bit)**

- `x`(사용하지 않음)
- `D`(Don't fragmentation) : 보내는 사람이 데이터를 안쪽에서 보내겠다고 명시 -거의 사용 x
- `M`(More fragmentation) : 첫번째 패킷 뒤에 다른 패킷이 더 있다는 것을 알려주는 설정. 최대 전송 단위보다 큰 데이터를 보내는 경우 무조건 1로 세팅. 아니면 0

**`Fragment Offset`**
: 쪼개진 데이터들의 순서를 보장하기 위한 값. 데이터의 시작부분으로 부터 쪼개진 조각이 얼마만큼 떨어져있는지 알려줌.

### Time To Live (TTL)

- 패킷이 살아있을 수 있는 시간
- 패킷의 목적지가 잘못 설정해 무한궤도를 도는 경우, 3계층 장비를 하나씩 넘어갈 때마다 1씩 줄어들도록 해 패킷의 생존 여부 결정
- 이걸 이용해 어떤 운영체제를 사용하는 지 알 수 있음 (운영체제별로 다름)

### Protocol

- 상위 프로토콜을 알려줌
  - ICMP : 01
  - TCP : 06
  - UDP : 17

### Header Checksum

- 이 헤더가 오류가 있는지 없는지 확인하는 값
- 헤더에 있는 값들을 계산해 체크섬 값을 추출
- 받은 쪽에서도 계산을 통해 체크섬의 값과 동일한지 확인

### 출발지 IP (4byte)

### 목적지 IP (4byte)

# [ICMP 프로토콜](https://youtu.be/JaBCIUsFE74?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## ICMP가 하는 일

Internet Control Message Protocol
인터넷 제어 메세지 프로토콜

네트워크 컴퓨터 위에서 돌아가는 운영체제에서 오류 메세지를 전송 받는데 주로 사용

프로토콜 구조의 `type`과 `Code` 필드를 통해 오류메세지를 전송 받음

## ICMP 프로토콜의 구조

![](https://i.imgur.com/sJZOvl4.png)

### Type

- 카테고리
- `0` : Echo Reply (기본)
- `8` : Echo (기본)
- `3` : Destination Unreachable (에러)
- `11` : Time Exceded (에러)
- `5` : Redirect (보안)

0번과 8번은 기본적인 값으로 0번은 **응답**, 8번은 **요청**
3번과 8번은 **상대방과 통신이 안되는 경우** 이유 확인용. 3번은 **목적지까지 가지 못한 경우(가는 경로상의 문제)**, 11번은 목적지까지 도착했으나 **응답을 받지 못한 경우(상대방이 방화벽을 켜놓은 경우)**
5번은 원격지에 있는 상대방의 라우팅 테이블을 ICMP를 이용해 수정할 때 사용. (old)

### Code

- Type에 대한 소분류

# [IPv4, ICMP프로토콜 실습](https://youtu.be/8ZwTvTuZlVw?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

- 앞의 8byte만 ICMP 나머지는 data
- ![](https://i.imgur.com/wRlALhx.png)

# [라우팅 테이블](https://youtu.be/CjnKNIyREHA?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

> 3계층의 궁극적인 목표
> 프로토콜을 이용해 **최적의 경로**로 찾아가는 것

최적의 경로를 저장하는 것 == **라우팅 테이블**

## 내가 보낸 패킷은 어디로 가는가

`netstat -r`을 통해 라우팅 테이블 확인 가능
테이블에 있는 경로만 찾아갈 수 있고, 테이블에 없다면 찾아갈 수 없음

![](https://i.imgur.com/oH6N6fF.png)
모르는 경우를 대비해 0.0.0.0 설정을 해줌. 게이트웨이를 알려주면서 모르면 일단 나가도록 유도(?)

## 다른 네트워크까지의 내 패킷의 이동 과정

A라는 컴퓨터가 B와 통신을 하는 경우

- A의 라우팅 테이블을 확인
  - ![](https://i.imgur.com/YZl2NgQ.png)
  - B의 네트워크 대역이 A의 테이블에 있는지 확인
- 경로가 확인이 되면 프로토콜 작성
  - Eth | IPv4 | ICMP 요청 (8)
  - ...

* 이더넷 프로토콜은 네트워크 대역이 바뀔때마다 새로 작성
* ping 명령어 한번에 4번 왕복
* arp랑 이 과정이 한번에 실행되기도 함.

# [라우팅 테이블 확인 실습](https://youtu.be/tVntagSJctc?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

인터페이스에 내가 통신하고 싶은 대상이 없으면 추가 필요

# [IPv4 조각화 이론](https://youtu.be/_AONcID7Sc8?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 조각화란?

fragmentation

큰 IP 패킷들이 적은 MTU(Maximum Transmission Unit)를 갖는 링크를 통해 전송되려면 여러 개의 작은 패킷으로 쪼개어 전송해야함.

즉, 목적지까지 패킷을 전달하는 과정에 통과하는 각 라우터마다 전송에 적합한 프레임으로 변환이 필요

IPv4에서는 발신지 뿐만 아니라 중간 라우터에서도 IP 조각화 가능

IPv6에서는 IP 단편화가 발신지에서만 가능

**재조립은 항상 최종 수신지에서만 가능**

## 조각화하는 과정

![](https://i.imgur.com/LJit3XH.png)

조각화는 보통 한번만 함.

원래 보내려고 하는 데이터가 11,980byte 일 때, 프로토콜 포함 12,000byte
MTU보다 보내려는 데이터가 크기때문에 조각화 필요

IPv4헤더의 값을 포함해 MTU값이 되어야 함.
MTU - 20 = 보낼 수 있는 데이터 값

MTU는 일반적으로 1500

ICMP는 마지막 패킷에만 붙음

# [IPv4 조각화 실습](https://youtu.be/QKEL9aBgHtg?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

Data : 2379byte / MTU : 980byte

mf : offset : data
1 : 0 : 960 (보낼 수 있는 데이터의 크기)
1 : 120 : 960 (1920)
1 : 240 :459 (2379)

3개의 패킷
