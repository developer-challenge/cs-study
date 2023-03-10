영상: [[따라學 IT] 04. 실제로 컴퓨터끼리는 IP주소를 사용해 데이터를 주고받는다](https://youtu.be/s5kIGnaNFvM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

MAC 주소에 비해 IP 주소는 꽤 복잡함

### 3계층의 기능

- LAN과 LAN을 연결시켜준 것이 WAN
- 대표적 장비: 라우터
- 다른 네트워크끼리 통신을 할거면 결국 라우터가 필요하다.
- 결국 다른 네트워크 컴퓨터와 통신하기 위해서는 MAC + IP 주소 다 필요한 것
- WAN 통신을 위해서는 사실 IP 주소뿐 아니라 서브넷 마스크, 게이트웨이까지 필요하다. DNS까지 필요
- 종류 : ARP, IPv4, ICMP 프로토콜

### 일반적인 IP 주소

- IP도 8byte로 필드를 구분하긴 하는데, MAC과 달리 10진수로 표현

#### Classful IP

- 낭비가 심하다

![](https://i.imgur.com/ITbl0kV.png)

- A 클래스의 경우 네트워크 128개 밖에 못쓰고 그 안에 너무 많은 호스트 주소가 부여됨.  
  => 낭비
- 알면 좋지만, 별로 중요하지 않다.
- 클래스 별 숫자가 요상한데 나누는 기준(원리)은 이하 그림과 같다.

![](https://i.imgur.com/W1iVAiJ.png)

- 결국 IP 주소가 부족해짐

#### Classless IP

- 굳이 `.`으로 구분하지 말자  
  -> 서브넷 마스크 : 네트워크 대역을 어디서 구분할지 지정한다.

> 서브넷 마스크
> ex. 255.255.255.192 -> 11111111.11111111.11111111.11000000
> 1까지가 네트워크 대역 구분, 0부터가 호스트
> 이 예제에서는 64대의 컴퓨터가 들어갈 수 있는 것

- 서브네팅, 슈퍼네팅
- 하지만, Classless IP만으로도 IP가 부족한 지경에 이름
- IPv6(1칸 당 4byte 대신 16byte)를 지원하지 않는 장비도 너무 많음

#### 사설 IP와 공인 IP

- 현재 사용 중인 IPv4 : Classless + 사설/공인
- 공인 IP : 실제 네트워크 망끼리 사용하는 IP 주소
- 사설 IP : 같은 네트워크 대역에서 사용하는 IP 주소
- 즉, 같은 망 내에서는 사설 IP로 서로 통신하고, 외부 망과 통신할 때 공인 IP로 변환돼서 통신.
- 그래서 같은 망 내 장비들을 외부에서 봤을 때는 IP 주소가 구분되지 않는다.
- 외부에서 받아온 데이터는 공유기에서 해당 장비로 보내준다.(NAT)

> NAT
> IP 주소 변환해주는 기술
> NAT 테이블에 나갈 때 기록, 들어올 때 기록 확인
> 테이블에 기록이 없는데 공유기에 들어온다면? 그냥 공유기까지만 들어오고 끝 -> 그래서 서버는 사설 IP를 잘 안쓴다.

- 외부에서 통신할 때는 사설 IP는 보이지 않는다. 공인 IP로만 통신하는 것처럼 보인다.
- 사설 IP는 나갔다가 들어오는 것만 외부에서 접근 가능(NAT). 그런데 포트 포워딩 설정을 통해 외부에서 내부로 들어올 수는 있다.
- 계속 IPv6로 넘어가려고 하고 있다.

### 특수한 IP 주소

- 0.0.0.0 : Wildcard
  - 나머지 모든 IP
- 127.0.0.1 : 자기 자신을 나타내는 주소 => 네트워크 안 될 때 먼저 확인하면 좋다
- 게이트웨이 주소 : 일반적으로 공유기 IP. 일반적으로 해당 네트워크 망에서 가장 크거나 작은 주소.
  - 인터넷이 되기 위해서는 기본적으로 IP, Subnet, Gateway는 설정되어야 한다
