영상: [운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

## [1 운영체제 개요](https://core.ewha.ac.kr/publicview/C0101020140307151724641842?vmode=f)

### 운영 체제란?

컴퓨터 하드 웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층

좁은 의미의 운영체제 (커널)

- 운영체제의 핵심 부분으로 메모리에 상주하는 부분
- 대부분 이 의미로 사용

넓은 의미의 운영체제

- 커널 뿐 아니라 각종 주변 시스템 유틸리티를 포함한 개념
- 메모리에 상주하지 않아도 운영체제 범주에 포함

### 운영 체체의 목적

1. 컴퓨터 시스템의 자원을 효율적으로 관리
   - 프로세서, 기억장치, 입출력 장치 등의 효율적 관리 (자원 분배, 효율성)
   - 사용자 및 운영체제 자신의 보호
2. 컴퓨터 시스템을 편리하게 사용할 수 있는 환경 제공
   - 동시 사용자 / 프로그램들이 각각 독자적 컴퓨터에서 수행되는 것 같이 동작
   - 직접 다루기 힘든 하드웨어 부분을 운영체제가 대행

### 운영 체제의 분류

1. 동시 작업 가능 여부
   - 단일 작업 (single tasking)
     - 한번의 하나의 작업만 처리
     - 기능이 하나밖에 없는 경우(특수 목적)을 위한 운영체제로 아직 사용되기도 함 (엘레베이터 등등)
   - 다중 작업 (multi tasking)
     - 동시에 두 개 이상의 작업 처리
     - 최근 운영체제들은 보통 다중 작업
2. 사용자의 수
   = 컴퓨터 한대를 여러 사용자가 접속해서 사용할 수 있는지

- 단일 사용자
- 다중 사용자

3. 처리 방식
   1. 일괄 처리(batch processing)
      - 작업 요청을 바로바로 처리 ❌
      - 일정량 모아서 한번에 처리
      - 현대 운영체제에선 찾아보기 힘듦
   2. 시분할 (time sharing)
      - 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
      - 일괄 처리 시스템에 비해 짧은 응답 시간
      - 사용자 입장에선 interactive한 서비스
      - deadline이 있지 않아 응답 시간이 일정하진 않음
   3. 실시간 (Realtime OS)
      - 정해진 시간안에 어떤 일이 반드시 종료됨이 보장
      - 공장 제어 / 미사일 제어 / 반도체 장비 등
      - 개념 확장
        - Hard realtime system (경성 실시간 시스템)
        - Soft realtime system (연성 실시간 시스템)

### 몇 가지 용어

**컴퓨터에서 여러 작업을 동시에 수행하는 것을 의미하는 단어들**

`multitasking` : CPU에서는 사실상 하나의 작업만 실행되지만 짧은 시간 간격으로 바뀌기 때문에 동시에 실행되는 것처럼 보이는 것.
`multiprogramming` : 메모리에 여러 프로그램이 동시에 올라가 있음을 강조
`time-sharing` : CPU를 좀 더 강조
`multiprocess` : 여러 프로그램이 동시에 실행

- `multiprocessor` : 하나의 컴퓨터에 CPU가 여러개 붙어있음을 의미 -> 고성능 컴퓨팅

### 운영 체제의 예

#### 유닉스

- for 대형 컴퓨터
- 코드의 대부분을 C언어로 작성
- 높은 이식성
- 최소한의 커널 구조
- 복잡한 시스템에 맞게 확장 용이
- 소스 코드 공개
- 프로그램 개발에 용이
- 다양한 버전

#### WINDOWS 계열

- for 개인용 컴퓨터

### 운영 체제의 구조

![](https://i.imgur.com/TzStZDA.png)

**컴퓨터 시스템의 여러 자원**
-> 이러한 자원들을 관리하는게 운영 체제가 할 일 - CPU 스케줄링 - 메모리 관리 - 파일 관리

> 나는 오늘부터 운영체제,,

## [2 시스템 구조 및 프로그램의 실행 Part-1](https://core.ewha.ac.kr/publicview/C0101020140311132925816476?vmode=f)

### 컴퓨터 시스템 구조

![](https://i.imgur.com/jGWHtWY.png)

CPU의 작업 공간 = **메모리**

CPU는 매 시간(clock)마다 메모리에서 기계어를 읽어 실행

I/O devices

Input device와 Output device

disk = 데이터를 읽어 메모리로 읽어들이기도 하고 처리 결과를 저장하기도 함.(input & output)

각각의 디바이스들을 전담하는 CPU가 붙어있으면서 디바이스를 통제 = `device controller`

device controller의 메모리 = `local buffer`

`registers` : 메모리보다 빠르면서 정보를 저장할 수 있는 공간

`mode bit`: 지금 CPU에서 실행되는게 운영체제인지 사용자 프로그램인지 구분

`interrupt line` : 입출력 이벤트 발생시 확인

> CPU는 항상 메모리랑만 일함.

CPU가 실행하는 instruction에 I/O가 필요한 경우,
일단 요청을 보내놓고 다른 프로그램 실행.

### mode bit

사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치

하드웨어적으로 두가지 모드의 operation 제공

![](https://i.imgur.com/Tzb7iW4.png)

`1` : **사용자 모드** : 사용자 프로그램 수행 - 사용자 프로그램을 CPU에 넘기기 전에 mode bit를 1로 셋팅

`0` : **모니터 모드** : OS코드 수행 - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 특권 명령으로 규정 - interrupt나 exception 발생시 하드웨어가 mode bit을 0으로 바꿈

### Timer

특정 프로그램이 CPU를 독점하는 것을 막기 위해 Timer가 존재

- 정해진 시간이 흐른 뒤 운영체제에게 제어권이 넘어가도록 인터럽트를 발생
- 타이머는 매 클럭 틱 때마다 1씩 감소
- 타이머 값이 0이 되면 타이머 인터럽트 발생
- CPU를 특정 프로그램이 독점하는 것으로부터 보호

- time-sharing을 구현하기 위해 이용
  - 만약 무한 루프를 도는 프로그램을 실행하는 경우, 프로그램이 종료되지도 않고 CPU 사용 -> CPU가 Time sharing을 못하게 됨. 이를 방기하기 위함
- 현재 시간을 계산하기 위해서도 사용

### Device Controller

I/O device controller

- 해당 장치를 관리하는 일종의 작은 CPU
- 제어 정보를 위해 control register, status register를 가짐
- local buffer를 가짐 (일종의 data register)

I/O는 실제 device와 local buffer 사이에 일어남
Device controller는 I/O가 끝난 경우 interrupt로 CPU에 그 사실을 알림

- `device driver` (장치 구동기)
  : OS 코드중 각 장치별 처리 루틴 -> software

- `device controller` (장치 제어기)
  : 각 장치를 통제하는 일종의 작은 CPU -> hardware

### [입출력과 인터럽트 (Input-Output and Interrupt)](/컴퓨터-구조/5장-기본-컴퓨터의-구조와-설계-Part2/입출력과-인터럽트.md)

모든 입출력 명령은 특권 명령

#### 사용자 프로그램은 어떻게 I/O를 하는가? -> **시스템 콜**

1. 사용자 프로그램은 운영체제에게 I/O 요청
2. `trap`을 사용해 인터럽트 벡터의 특정 위치로 이
3. 제어권이 인터럽트 벡터의 특정 위치로 이동
4. 올바른 I/O 요청인지 확인 후 I/O 수행
5. I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김. (hw interrupt)

#### 인터럽트

인터럽트 당한 시점의 레지스터와 program counter를 save한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다.

`interrupt` : 하드웨어

`trap` : 소프트웨어 인터럽트

- `exception`: 프로그램이 오류를 범한 경우

- `system call` : 프로그램이 커널 함수를 호출하는 경우

> **시스템 콜을 거는 방법**
> 사용자 프로그램이 interrupt를 걸어서 CPU가 OS로 넘어가도록 함.

인터럽트 관련 용어

- `인터럽트 벡터` : 해당 인터럽트의 처리 루틴 주소를 가짐
- `인터럽트 처리 루틴` (= interrupt service routine) : 해당 인터럽트를 처리하는 커널 함수

-> **현대의 운영체제는 인터럽트에 의해 구동됨.**

### 시스템 콜 (System Call)

사용자 프로그램이 운영체제의 서비스를 받기 위해 **커널 함수**를 호출하는 것

## [2 시스템 구조 및 프로그램의 실행 Part-2](https://core.ewha.ac.kr/publicview/C0101020140314151238067290?vmode=f)

### 동기식 입출력과 비동기식 입출력

#### 동기식 입출력

- I/O 요청 후 입출력 작업이 완료된 후에 제어가 사용자 프로그램에 넘어감
- 구현 방법1
  - I/O가 끝날때까지 CPU를 낭비시킴
  - 매 시점 하나의 I/O만 일어날 수 있음
- 구현 방법2
  - I/O가 완료될 때까지 해당 프로그램에서 CPU를 빼앗음
  - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
  - 다른 프로그램에 CPU를 줌

#### 비동기식 입출력

- I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

> 두 경우 모두 I/O의 완료는 **인터셉트**로 알려줌

![](https://i.imgur.com/m4ALvQL.png)

### DMA (Direct Memory Access)

빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
CPU의 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 block 단위로 직접 전송
바이트 단위가 아니라 block 단위로 인터럽트를 발생시킴

![](https://i.imgur.com/mp1KPBt.png)

- 메모리에 접근 가능한 컨트롤러
- CPU와 동시 접근시 에러가 발생할 수 있어 `memory controller`가 이를 조율
- I/O 장치가 CPU에 너무 잦은 interrupt를 거는 것을 막고,
- local buffer의 작업을 메모리로 옮겨주는 작업을 해줌.

### 서로 다른 입출력 명령어

![](https://i.imgur.com/PagO8GP.png)

1. I/O를 수행하는 special instruction
   1. 메모리로 접근하는 instruction (memory address)
   2. I/O device 별 별개의 instruction을 통해 접근 (device address)
2. Memory Mapped I/O에 의해
   1. Memory address를 확장해서 사용

### 저장장치 계층 구조

![](https://i.imgur.com/miNlh0d.png)

맨 위에 CPU 존재

Registers

Cache memory

Main Memory = DRAM으로 구성 (byte 단위 접근 가능 -> executable)

---

Magnetic Disk

Optical Disk

Magnetic Tape

(hard disk들은 sector 단위로 접근 -> non-executable)

위로 갈 수록 단위당 값이 비싸기 때문에 용량이 적음
아래층에 있는 데이터를 전부 상위로 올리지 못함.

![](https://i.imgur.com/BDXXpvy.png)

> 최근에는 많이 바뀌긴 함

> `Caching` ; 재사용을 목적으로 저장장치가 빠르게 동작하도록 하는 것

### 프로그램의 실행 (메모리 load)

![](https://i.imgur.com/WX25sXn.png)

프로그램은 실행파일 형태로 file system에 저장
이를 실행하는 경우, 메모리에 올라가 프로세스로 변환
물리적인 메모리에 바로 올라가는 것이 아니라 virtual memory를 거치게 됨.

file system -> virtual memory -> physical memory

![](https://i.imgur.com/r2aBQKc.png)

virtual memory에서 독자적인 주소 공간이 만들어짐.
(stack, data, code)

physical 메모리의 커널은 컴퓨터를 킬 때 생성
당장 필요한 프로세스만 virtual -> physical 이동
당장 필요하지 않은 부분은 disk(swap area)에 저장

프로세스가 연속적으로 할당되는 것이 아니라 쪼개져서 이동되고 실행됨.

#### file system vs swap area

file system의 파일은 전원이 꺼져도 남아있지만,
swap area의 파일은 전원이 꺼지면 필요없어지게 됨.

#### address translation

하드웨어의 도움을 받아 이뤄짐

### 커널 주소 공간의 내용

![](https://i.imgur.com/OewxTj5.png)

#### code (커널 코드)

- 시스템콜, 인터럽트 처리 코드
- 자원 관리를 위한 코드
- 편리한 서비스 제공을 위한 코드

> 커널은 interrupt가 들어오면 CPU를 얻게 됨.

#### data

1. 운영체제가 사용하는 여러 자료구조가 저장

   - CPU나 memory나 disk같은 하드웨어 통제
   - 각각를 관리하기 위한 자료구조를 가지고 있음

2. 프로세스 관리
   - 각 프로그램마다 운영체제가 관리하고 있는 자료구조 존재
   - = `PCB(Process control block)`

#### stack

운영체제도 함수 구조로 구성되기 때문에 이를 실행시키기 위한 스택 필요
프로그램마다 커널 스택을 따로 가지고 있음

### 사용자 프로그램이 사용하는 함수

#### 사용자 정의 함수

자신의 프로그램에서 정의한 함수

#### 라이브러리 함수

자신의 프로그램에서 정의하지 않고 가져다 쓴 함수
자신의 프로그램의 실행 파일에 포함되어있는 함수

#### 커널 함수

운영체제 프로그램의 함수
커널 함수의 호출 = `시스템 콜`

사용자 정의 함수, 라이브러리 함수와 달리 **커널 함수는 내 프로그램 안에 들어있는 함수가 아님**
영역 자체가 다르기 때문에 사용자 정의함수에서 커널 함수로 넘어가는 것이 불가능 (virtual -> physical / user-mode -> kernel-mode)

![](https://i.imgur.com/vVMUr5t.png)
