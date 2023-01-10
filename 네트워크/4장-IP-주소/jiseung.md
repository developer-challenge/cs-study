영상: [[따라學 IT] 04. 실제로 컴퓨터끼리는 IP주소를 사용해 데이터를 주고받는다](https://youtu.be/s5kIGnaNFvM?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

## 3계층의 기능

### 3계층에서 하는 일

> 서로 다른 네트워크 대역을 연결
> 멀리 떨어진 곳에 존재하는 네트워크까지 어떻게 데이터를 전달할 지 제어
> 발신에서 착신까지의 패킷 경로를 제어

### 3계층에서 쓰는 주소

- LAN 주소에서 쓰이는 MAC 주소
- WAN에서 사용될 IP 주소
- 서브넷 마스크
- 게이트웨이

### 3계층 프로토콜

- ARP 프로토콜 : IP주소를 이용해 MAC주소를 가져올 때 사용
- IPv4 프로토콜 : WAN에서 통신할 때 사용
- ICMP : 서로가 통신 되는지 확인할 때 사용

## 일반적인 IP 주소

### Classful IP 주소

- 초창기 IP 주소
- 클래스 별로 IP 주소의 범위를 나눠서 사용
  - 4개의 필드를 `.`으로 구분
  - 10진수. (0.0.0.0 ~ 255.255.255.255)
- 낭비가 심함
  - 첫번째 필드는 네트워크 대역을 구분
  - 남는 IP 주소 낭비 → 부족

### Classless IP 주소

- 낭비되지 않도록 필드를 딱 맞춰쓰지 말자
  - 구간을 자유롭게 사용
  - 그래도 부족 IPv6로 바로 넘어가기 어려움
- 서브넷 마스크
  - IPv4와 함께 사용
  - 네트워크 대역을 나눠주는데 사용하는 값
  - 2진수로 표기했을 때 1로 시작. 1과 1 사이에는 0이 올 수 없다.
- Classless IP + 사설 IP & 공인 IP
  - 사설 IP : 인터넷 사용 시 사용하는 IP
    - 공인 IP로 Internet과 통신
    - 요청에 대한 응답은 가능. 그 외의 통신은 불가능
    - 내 IP 주소 ≠ ifconfig IPv4 주소
  - 공인 IP : 같은 네트워크가 사용하는 IP (공유기)
    - 공인 IP 1개당 2^32 사설 IP
    - Internet에서 들어온 데이터를 적절한 사설 IP로 전달
    - NAT : Network Address Translation
      - [IP](https://ko.wikipedia.org/wiki/IP) [패킷](https://ko.wikipedia.org/wiki/%ED%8C%A8%ED%82%B7)의 [TCP](https://ko.wikipedia.org/wiki/%EC%A0%84%EC%86%A1_%EC%A0%9C%EC%96%B4_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C)/[UDP](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9A%A9%EC%9E%90_%EB%8D%B0%EC%9D%B4%ED%84%B0%EA%B7%B8%EB%9E%A8_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C) 포트 숫자와 소스 및 목적지의 [IP 주소](https://ko.wikipedia.org/wiki/IP_%EC%A3%BC%EC%86%8C) 등을 재기록하면서 [라우터](https://ko.wikipedia.org/wiki/%EB%9D%BC%EC%9A%B0%ED%84%B0)
        를 통해 [네트워크 트래픽](https://ko.wikipedia.org/wiki/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC_%ED%8A%B8%EB%9E%98%ED%94%BD)을 주고 받는 기술
      - [사설 네트워크](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%84%A4_%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)에 속한 여러 개의 호스트가 하나의 공인 IP 주소를 사용하여 [인터넷](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%EB%84%B7)
        에 접속하기 위함
      - 외부 네트워크 대역에서는 사설 IP 대역을 볼 수 없다.
    - 서버?
      - 공인 IP를 사용하거나, 공유기에 설정 필요
      - 포트 포워딩 필요

## 특수한 IP 주소

- 0.0.0.0 : Wildcard
  - 나머지 모든 IP
  - 일반적이지 않음
- 127.0.0.1 : 자기 자신
  - 자기 자신과 통신할 때
- 게이트웨이 주소 : 192.168.0.1
  - WAN IP IP IP IP
  - 사용 가능한 IP 중에 가장 낮은 것
  - 게이트웨이를 통해 외부 Internet과 통신
  - Internet 통신 : IP 주소, 서브넷 마스크, 기본 게이트웨이 설정
    - 추가적으로 DNS 서버를 설정해야 도메인 사용 가능

## 내 PC의 IP주소

```bash
ifconfig | grep "inet"
curl ipecho.net/plain
```
