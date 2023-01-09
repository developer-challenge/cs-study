영상: [[따라學IT] 03. 가까이 있는 컴퓨터끼리는 이렇게 데이터를 주고받는다](https://youtu.be/HkiOygWMARs?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi)

### 2계층에서 하는일

- 하나의 LAN에서 통신을 어떻게 하는가를 정의
- 같은 LAN 대역 내 여러 노드들 중에서 어떤 장비에서 어떤 장비로 데이터를 전달하는가
- Keyword : 오류 제어, 흐름 제어
- 하나의 네트워크에서만 통신할 때 사용 ⇒ `다른 네트워크간 통신`은 항상 `3계층`이 도와야 한다.

### 2계층에서 사용하는 주소

MAC 주소

- 실제 물리 장비가 부여받은 주소, 물리적 주소라고도 부름
- 16진수로 씀. 2개씩(- 또는 : 으로 구분) 총 12개의 16진수
- 앞 6자리 - OUI: IEEE 부여 식별 ID / 뒤 6자리 - 고유번호: 제조사 부여 고유번호
- 전세계 모든 장비가 다 다름
- ex. AA:AA:BB:AA:EE:AA

### 2계층(Ethernet) 프로토콜

- Preamble(헤더로 간주되지 않음, 8byte) → 그 다음 14바이트가 헤더 부분
- 헤더(14byte)
    - 6byte(16진수 2개당 1byte) Destination Address → 목적지 MAC 주소
    - 6byte Source Address → 출발지 MAC 주소
    - 2byte Type 프로토콜 타입 → 상위 계층 Header 프로토콜이 뭔지 알려준다. 다른 프로토콜도 Header 끝에 IP(0x0800), ARP(0x0806)
- Payload(Data)

![Untitled](./assets-HojinAn/Ethernet%20Header.png)

- 내 MAC 주소 확인하기
    
    `ipconfig /all`
    
- 영상 실습 부분 좋음!