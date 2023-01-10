영상: [[따라學IT] 03. 가까이 있는 컴퓨터끼리는 이렇게 데이터를 주고받는다](https://youtu.be/HkiOygWMARs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

# 2계층에서 하는일

### 2계층의 기능

<aside>
🤲 하나의 네트워크 대역 즉, 같은 네트워크 상에 존재하는 여러 장비들 중에서 
어떤 장비가 어떤 장비에게 데이터를 전달 (추가적으로 오류제어, 흐름 제어 수행)
</aside>

### 2계층의 네트워크 크기

<aside>
🤌 하나의 네트워크 대역 LAN에서 통신할 때만 사용
다른 네트워크와 통신할 때는 3계층이 필요
3계층의 주소와 3계층의 프로토콜을 이용해야 다른 네트워크와 통신 가능
</aside>

## 2계층에서 사용하는 주소

### 물리적인 주소

- MAC 주소
    - LAN에서 통신할 때 사용
    - 12개의 16진수(6 byte). 2개씩 `-` 혹은 `:` 로 구분
        - OUI : 앞 6자리. IEEE에서 부여하는 일종의 제조회사 식별 ID
        - 고유번호 : 뒤 6자리. 제조사에서 부여한 고유번호

# 2계층(Ethernet) 프로토콜

### Ethernet 프로토콜

- Destination Address
    - MAC 주소(6 byte)
- Source Address
    - MAC 주소(6 byte)
- Ethernet Type
    - 프로토콜 타입 (2 byte)
    - Encapsuled Data(상위 프로토콜)에 대한 정보
        - IPv4 (0800), ARP (0806)

## 실습

### PC MAC 주소

```shell
ifconfig -a
ifconfig | grep ether
```

- `ether`이 MAC주소 (물리적 주소)

### Ethernet 프로토콜

- Destination Address : b4 a9 4f c3 d2 6f
- Source Address : 3c 06 30 20 17
- Ethernet Type : 08 00