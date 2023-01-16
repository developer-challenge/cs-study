영상: [운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

## [1 운영체제 개요](https://core.ewha.ac.kr/publicview/C0101020140307151724641842?vmode=f)

### 운영 체제란?

- 컴퓨터 하드웨어 바로 위 설치 => 사용자, 모든 SW와 하드웨어 연결하는 `소프트웨어 계층`
- 좁은 의미로는 `커널`(부팅 이후 메모리 상주), 넓은 의미로는 주변 시스템 유틸리티까지 포함이라고도 부름
  - 좁은 의미, 넓은 의미로 나눠 접근하는거 좋다
- 사용자 바로 아랫단 설치.

### 운영 체체의 목적

- 컴퓨터 시스템의 `자원을 효율적으로 관리`.
  - 효율적 관리 : 형평성 분배, 최대한 성능
  - 사용자, OS 보호
- 컴퓨터 시스템을 편리하게 사용할 수 있는 `환경 제공`
  - 동시 사용자, 독자 수행같은 환상 제공

### 운영 체제의 분류

- 동시 작업 가능 여부
  - 단일 작업(single tasking) ex. MS-DOS, 특수 목적 장비들(엘리베이터 등)
  - 다중 작업(multi tasking) ex. UNIX, MS Windows. 현대 대부분의 OS
- 사용자의 수
  - 단일 사용자(single user) ex. MS-DOS, MS Windows
  - 다중 사용자(multi user) ex. UNIX, NT server
- 처리 방식
  - 일괄 처리(batch processing): 모아서 한꺼번에 처리
  - 시분할(time sharing): 대부분의 컴퓨터. 여러 작업 수행 but 일정 시간 단위로 번갈아가면서 사용. 일괄 처리에 비해 짧은 응답 시간 => 사용자 입장 interactive
  - 실시간(Realtime OS): 정해진 시간 내 반드시 종료 보장. 특수 목적. ex. 원자로/공장 제어. Hard RTOS, Soft RTOS

### 몇 가지 용어

컴퓨터 여러 작성 동시 수행에 관한 용어들

- Multitasking: 좀 더 일반적인 용어. 그리고 사실 CPU에서는 매 순간 한 가지 일만 수행하고 있다. 동시 실행처럼 보이는 것.
- Multiprogramming: 메모리에 여러 프로그램이 올라가 있음. 메모리 강조.
- Time sharing: CPU의 시간 분할 나눠 씀. CPU 강조
- Multiprocess: process는 실행 중인 프로그램.

> Multiprocessor: 여러 CPU(processor)가 붙어 있음을 의미

운영 체제에서는 보통 하나의 CPU 전제

### 운영 체제의 예

유닉스(UNIX)

- C 언어(어셈블리 언어로 운영 체제 만들기 어려워 쉽게 하려고 만든 언어): 기계어와 가까우면서 사람과 가까운 언어
- 소스 코드 직접 공개
- 높은 이식성
- 핵심 기능만 커널에 넣고 사용
- 초기 대형 컴퓨터 용도. Linux 이후로 PC 많이 활용.

DOS

- 작은 메모리(주 기억 장치: 640KB)

MS Windows

- 불안정성(많이 해소 됨)

많은 형태로 발전

### 운영 체제의 구조

```
CPU <-> memory <-> Disk
               <-> I/O device
```

CPU 스케줄링: 누구한테 CPU를 줄 것인가
메모리 관리: 한정된 메모리를 어떻게 쪼개어 사용할 것인가
파일 관리: 디스크에 파일을 어떻게 보관할 것인가
입출력 관리: 각기 다른 입출력장치와 컴퓨터 간 정보를 어떻게 주고 받게 할 것인가(보통 인터럽트 기반)

프로세스관리, 보호 시스템, 네트워킹, 명령어 해석기...

## [2 시스템 구조 및 프로그램의 실행 Part-1](https://core.ewha.ac.kr/publicview/C0101020140311132925816476?vmode=f)

하드웨어가 어떻게 동작하는가

### 컴퓨터 시스템 구조

```
     컴퓨터       |  Devices
CPU <-> Memory <-> Disk
               <-> I/O device
```

- 각 Device를 컨트롤하는 Device Controller가 각각 있다. 또한 Device도 각자의 작업 공간이 필요한데, 이를 local buffer라고 부른다.
- CPU와 I/O Device는 처리 속도가 엄청나게 차이 난다. Disk만 해도 100만배가 느리다. 그래서 Disk가 CPU와 직접 연결해 작업하는 것이 아니라 Controller가 존재하는 것
- 사실 CPU는 매 클럭마다 Memory에서 instruction을 읽어서 실행하는 것 뿐이다.

- CPU

  - registers: Memory보다 빠른 작은 저장 공간
  - mode bit: CPU에서 실행 중인 것이 OS인지, 사용자인지 구분
  - interrupt line: I/O 등의 정보 받아오기 위해 존재. CPU는 항상 Memory와만 일한다.

- 열심히 일하던 CPU가 I/O 작업이 필요할 경우 직접하는 것이 아니라 Device controller에게 받아오도록 시킨다(요청). 그렇게 요청한 작업은 Device local buffer에 있다가 CPU가 Memory가 달라고 하면 전달.  
  중요한 것은 CPU는 계속 instruction따라 자기 일만 하고 있는 것.
- instruction따라 작업하다가 Interrupt Line 확인(Timer, I/O), 반복
- CPU 제어권을 받은 OS는 다음 프로그램에게 Timer를 세팅하고 다시 넘겨준다. 이런 과정을 또 반복
- 기본적으로 사용자 프로그램은 I/O 장치에 직접 접근할 수 없다. 모든 접근은 OS를 통해서만 가능하게 막아놨다. 보안 등의 이유.  
  프로그램에서 I/O 입출력 등이 필요하면 스스로 CPU 제어권을 OS에 넘겨주고, OS가 해당 작업을 I/O Controller에게 다시 일을 시킨다. 프로그램은 I/O 작업이 완료되어야 다음 작업을 할 수 있으므로, 요청한 I/O 작업이 끝나고서 controller가 interrupt가 발생시키면, CPU 제어권이 다시 OS로 넘어가고, OS가 interrupt 이유를 살피고, 요청했던 프로그램을 찾아서 해당 프로그램의 메모리 값에다가 copy해준다. 그러고서 할당 된 시간이 남은 프로그램에게 다시 돌려준다.

### Mode bit

- 0: 모니터 모드(Kernel Mode) => OS가 CPU에서 수행 중 => 모든 일이 가능하게 세팅
- 1: 사용자 모드 => 사용자 프로그램이 CPU에서 수행 중 => 제한된 instruction만 가능하게 세팅
- interrupt 발생 시 Mode bit은 자동적으로 0으로 바뀐다.

### Timer

- 특정 프로그램이 CPU 독점하는 것을 막는다. OS가 사용자 프로그램에 Timer를 설정하면서 CPU 제어권을 넘겨준다.
- Timer 값이 0이 되면 Timer interrupt 발생 => OS로 CPU 제어권 빼앗김
- CPU Time Sharing

### Device Controller

- I/O 장치를 전담하는 일종의 작은 CPU. 얘도 Hardware
- control register, status register => 제어 정보
  - 제어정보: CPU가 controller에 일 시키기 위한 정보
- local buffer: 디바이스가 사용하는 정보가 담긴 공간
- ex. 데이터를 Disk 저장하고 싶으면 해당 Data는 local buffer에 넣고 CPU가 제어 정보 통해 저장하라는 명령을 controller에게 전달.
- Memory Controller도 존재 => 원칙적으로 CPU만 접근할 수 있게 해줌
- CPU는 Memory 접근도 가능, local buffer도 접근 가능 / Device별 작은 CPU는 자신의 local buffer만 접근 가능
- CPU에 interrupt가 너무 많이 발생 => 효율적이지 못한 동작 => DMA Controller로 해결
  - DMA Controller(Direct Memory Access)  
    CPU도 Memory 접근, DMA도 Memory 접근 => 동시 접근시 문제 발생 => Memory Controller가 제어  
    I/O가 너무 자주 interrupt를 걸기 때문에, CPU 대신에 local buffer에 있는 데이터를 Memory에 직접 복사해준다. 다 끝나면, CPU에 interrupt 한번만 걸어서 Memory에 데이터 있음을 알려줘서 interrupt 발생 줄임

> device driver: device별 interface에 맞게 접근할 수 있도록 해주는 모듈

결국 CPU가 하는 일?
=> 실행해야 할 instruction memory 주소를 program counter(PC)라는 register에 있는 다음 instruction 주소를 찾아 가서 또 실행  
I/O 관련 instruction의 경우 device driver로 I/O 접근  
메뉴얼대로만 일하는 격

### [입출력과 인터럽트 (Input-Output and Interrupt)](/컴퓨터-구조/5장-기본-컴퓨터의-구조와-설계-Part2/입출력과-인터럽트.md)

#### 입출력 수행

- 모든 입출력 명령은 `특권 명령`
- How?
  - OS에 부탁: `시스템 콜(system call)` 일반 함수와 다름

#### 인터럽트

- Hardware 일꾼들이 CPU와 정보 교신하기 위해 발생 가능 => 일반적인 의미의 Interrupt
- 사용자 프로그램이 직접 처리 못하고 OS에게 대신 처리해달라고 요청해야 하는 상황에서도 Software적으로도 발생 가능: Software interrupt = `트랩(Trap)`

종류

1. Timer 인터럽트: CPU 독점 방지
2. I/O 인터럽트: 입력 완료 알림
3. Trap

`현재의 운영체제는 인터럽트에 의해 구동 됨`

관련 용어

- 인터럽트 벡터
  - 각 인터럽트가 들어왔을 경우 어디 있는 함수를 실행해야하는지 볼 수 있는 주소
- 인터럽트 처리 루틴(=Interrupt Service Routine)
  - OS에 정의 되어있는 실제로 처리해야 하는 인터럽트 정보에 관한 실제 코드

### 시스템 콜 (System Call)

사용자 프로그램이 OS의 서비스를 받기위해 Kernel 함수를 호출하는 것

- 일반 함수는 결국 메모리 내 스택 등에서 메모리 주소 찾아서 사용하면 되는데, 시스템 콜은 단순 메모리 참조가 아니다.
- 사용자 프로그램에서는 mode bit이 1이기 때문에 메모리 안에서만 함수호출이 가능하다. 바로 OS 메모리로 jump할 수가 없다. => 프로그램이 직접 Interrupt Line을 세팅하는 instruction을 실행한다. Timer, I/O Interrupt가 아니라 소프트웨어가 interrupt를 거는 것(Trap). => CPU는 interrupt가 발생했기 때문에 mode bit 0으로 전환하고 CPU 제어권 OS로 넘어가는 커널모드 => OS가 I/O Controller에게 요청 보낼 수 있다.
- 결국, 시스템 콜 호출 시 일반 함수 호출과 다르게 사용자 프로그램이 직접 인터럽트를 걸어서 즉, Trap을 걸어서 CPU가 OS에게 넘어가도록 한다. 단, OS는 항상 올바른 요청인지 확인. I/O 완료시는 Hardware 인터럽트 발생
- 다르게 또 보자면, I/O를 하기 위해서는 2가지 종류의 interrupt가 발생한다. 사용자 프로그램 => Trap, I/O => Hardware Interrupt

## [2 시스템 구조 및 프로그램의 실행 Part-2](https://core.ewha.ac.kr/publicview/C0101020140314151238067290?vmode=f)

### 동기식 입출력과 비동기식 입출력

-

### DMA (Direct Memory Access)

-

### 서로 다른 입출력 명령어

-

### 저장장치 계층 구조

- [12장 메모리 구조](/컴퓨터-구조/12장-메모리-구조.md)

### 프로그램의 실행 (메모리 load)

-

### 커널 주소 공간의 내용

-

### 사용자 프로그램이 사용하는 함수

-
