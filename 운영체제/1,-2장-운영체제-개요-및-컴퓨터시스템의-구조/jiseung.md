영상: [운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

# [1 운영체제 개요](https://core.ewha.ac.kr/publicview/C0101020140307151724641842?vmode=f)

## 운영 체제란?

> 컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층

- 좁은 의미 : 커널
  - 운영체제의 핵심 부분. 메모리에 상주
- 넓은 의미
  - 커널 + 각종 주변 시스템 유틸리티를 포함한 개념

## 운영 체체의 목적

- 컴퓨터 시스템의 자원을 효율적으로 관리하는 것
  - 주어진 자원으로 최대한의 성능
  - 사용자 간의 형평성 있는 자원 분배
  - 자원? 프로세스, 파일, 메시지 …
- 사용자의 편리성
  - 각 프로그램이 독자적 컴퓨터에서 수행되는 것 같은 경험 제공
    - 컴퓨터(운영체제)가 내부적으로 여러 프로그램을 잘 돌리고 있는 건데..
    - CPU를 번갈아 할당하는 식으로. 메모리 공간을 적절히 분배하고 있음

## 운영 체제의 분류

### 동시 작업 가능 여부

- 단일 작업
  - 한 번에 하나의 작업 처리
  - MS-DOS 프롬프트
- 다중 작업
  - 동시에 두 개 이상의 작업 처리
  - UNIX, MS Windows …

### 사용자의 수

- 단일 사용자
  - MS-DOS, MS Windows
- 다중 사용자
  - UNIX, NT Server

### 처리 방식

- 일괄 처리 (batch processing)
  - 작업 요청의 일정량을 모아서 한꺼번에 처리
    - interactive X
  - 작업이 완전 종료될 떄까지 기다림
  - 과거 방식
- **시분할 (time sharing)**
  - 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
  - 짧은 응답 시간
  - Deadline X
    - 정확하다는 뜻은 아님. 상황에 따라 다름
  - interactive한 방식
    - 사용자의 행동에 대한 반응이 즉각적
  - 현재 우리가 사용하는 운영체제 방식
- 실시간 (Realtime OS)
  - 짧은 응답 시간
  - Deadline O
    - 정해진 시간 안에 반드시 종료됨이 보장됨
    - 정교한 시스템
  - 확장 (치명적임의 정도에 따라)
    - Hard realtime system
    - Soft realtime system
  - 컴퓨터에서 사용하는 방식은 아님
  - 범용 컴퓨터에서는 아니지만.. 모바일에서는 필요해지고 있음(카메라 등)

## 몇 가지 용어

- Multitasking
  - Mutiprogrammming
    - 메모리관점. 여러 프로그램이 동시에 상주
  - Time sharing
    - CPU관점. 시간을 분할
  - Multiprocess
- Multiprocessor
  - 다중 CPU

## 운영 체제의 예

### UNIX

- 대형 컴퓨터
  - Multitasking
- C언어 : UNIX를 위해 만든 프로그래밍 언어
  - 커널 코드 대부분이 C언어로 작성됨
  - 독립적
  - 높은 이식성 (Portable)
- 최소한의 커널 구조
  - 복잡한 시스템에 맞게 확장 용이
- 소스 코드 공개
  - 프로그램 개발에 용이
- System V, FreeBSD, SunOS, Solaris, …, Linux
  - Linux. 코드 공개, 개인에게도 좋음

### DOS(Disk Operating System)

- 단일 사용자
- 메모리 관리 능력의 한계 (640KB.,. 무시했음)
- 누더기 코드…..

### MS Windows

- 개인
- 풍부한 지원 소프트웨어

## 운영 체제의 구조

### CPU

- CPU 스케줄링
  - 하나뿐인 CPU를 누가 쓸지?
  - FIFO? 선착순이 합리적?
    - CPU는 하나 뿐이어서 block됨
  - 평균적인 대기 시간을 짧게?

### Memory

- 메모리 관리
  - 한정된 메모리를 어떻게 쪼개어 쓸지
    - 1/n은 불가능
    - Working Set
      - 과거를 통한 예측

### Disk

- 파일 관리
  - 디스크에 파일을 어떻게 보관할지
  - 디스크 스케쥴링
    - FIFO? LIFO?
      - 엘레베이터 스케쥴링
      - head가 이동하는 동안 최소한의 이동으로

### I/O divice

- 입출력 관리
  - 각기 다른 입출력장치와 컴퓨터 간에 어떻게 정보를 주고 받게 할지
    - Interrupt

### 그 외

- 프로세스 관리
  - 프로세스의 생성과 삭제
  - 자원 할당 및 반환
  - 프로세스 간 협력
- 보호 시스템
- 네트워킹
- 명령어 해석기

# [2 시스템 구조 및 프로그램의 실행 Part-1](https://core.ewha.ac.kr/publicview/C0101020140311132925816476?vmode=f)

## 컴퓨터 시스템 구조

### CPU

- Memory : CPU의 작업 공간. CPU가 처리할 기계어들이 정의되어 있는 공간
  - 짧은 시간 간격으로(interactive, time sharing) 메모리에 계속 접근하면서 작업 실행
- DMA(Direct Memory Access) controller : CPU가 아니어도 직접 메모리 접근 가능
  - Memory Controller는 동시 접근을 못하도록 제어
  - interrupt가 빈도가 높은 CPU를 방해하지 않도록 오버헤드 방지 역할
- Interrupt line
  - CPU는 Interrupt line을 계속해서 확인
  - I/O Device, timer 등이 interrupt
- registers
  - CPU는 program counter register를 확인
    - 실행할 instruction 주소
    - 다음 instruction 주소
    - 제어문을 만나면 실행할 instuction으로 점프

### I/O Device

> 운영체제가 접근. 프로그램이 직접 접근 X (mode bit 0)

- Disk
  - 하드웨어 장치

## Mode bit

- mode bit
  - 0 : 모니터 모드. OS 코드 수행
    - Interrupt, Exception
  - 1 : 사용자 모드. 사용자 프로그램 수행
    - 사용자 연산
    - 한정적 (보안 문제). 운영체제에 요청 (시스템 콜)
  - 운영체제가 전환
    - 보안을 해칠 수 있는 중료한 명령어는 모니터 모드에서만 수행 가능

## Timer

- timer : 특정 작업이 CPU를 독점하는 것을 방지
  - 시간 할당
  - 시간이 만료되면 운영체제에 제어권이 넘어가도록 interrupt
  - time sharing

## Device Controller

- Controller
  - 입력 요청이 끝났음을 알려줌
- I/O device controller (hardware)
  - 해당 I/O 장치유형을 관리하는 일종의 작은 CPU
  - 제어정보를 위해 control register, status register를 가짐
    - 제어 명령
  - localbuffer를 가짐 (data register)
    - data를 담는 곳
  - I/O는 실제 device와 localbuffer 사이에서 일어난다.
    - 메인 메모리는 CPU만 접근 가능
    - CPU가 localbuffer에 접근
  - I/O가 끝나면 interrupt로 CPU에 끝났음을 알림
- I/O device driver (software)
  - OS 코드 중 각 디바이스를 처리하는 루틴
  - 설치 필요

## [입출력과 인터럽트 (Input-Output and Interrupt)](/컴퓨터-구조/5장-기본-컴퓨터의-구조와-설계-Part2/입출력과-인터럽트.md)

### 시스템 콜 (System Call)

> 모든 입출력 명령은 특권 명령

- **System Call**
  - 사용자 프로그램이 운영체제의 커널 호출
    - 일반적인 함수 호출과는 다름
    - 사용자 프로그램이 OS에 접근할 수 없으므로 의도적으로 interrupt line를 이용해서 요청 (mode bit 0으로 전환됨)
    - ⇒ Trap을 이용해 Interrupt 전달
      - 인터럽트 벡터의 특정 위치로 이동
      - 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
      - 올바른 I/O 요청인지 확인 후 I/O 수행
      - I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김

### 인터럽트(interrupt)

- 일반적인 의미
  - 인터럽트 당한 시점의 레지스터와 program counter를 저장한 후 CPU 제어를 인터럽트 처리 루틴에 넘긴다.
- 넓은 의미
  - Interrupt : 하드웨어(디바이스)가 발생시킨 인터럽트
  - Trap : 소프트웨어 인터럽트. 프로그램이 직접 발생시킨 인터럽트
    - Exception : 프로그램이 오류를 범한 경우 (0으로 나누는 연산, 운영체제 주소 사용 …)
      - 프로그램 종료 등의 대응
    - System call : 프로그램이 커널 함수를 호출
  ⇒ 소프트웨어 인터럽트로 인터럽트를 발생시키고, 완료 후에는 하드웨어 인터럽트로 끝낸다.
- 관련 용어
  - 인터럽트 벡터 : 해당 인터럽트의 처리 루틴 주소
    - 인터럽트 종류 별로 어떤 인터럽트를 실행 중인지
    - 주소와 인터럽트의 쌍
  - 인터럽트 처리 루틴(Interrupt Service routine, Interrupt Handler)
    - 해당 인터럽트를 처리하는 커널 함수 (실제 해야할 일)

# [2 시스템 구조 및 프로그램의 실행 Part-2](https://core.ewha.ac.kr/publicview/C0101020140314151238067290?vmode=f)

## 동기식 입출력과 비동기식 입출력

### 동기식 입출력 (synchronous I/O)

- I/O 요청 후 입출력 작업이 완료된 후에 제어가 사용자 프로그램에 넘어감
- 매 시점 하나의 I/O
  - I/O가 완료될 때까지 CPU 낭비
- CPU를 배정해줘야함

### 비동기식 입출력 (asynchronous I/O)

- I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

> 두 경우 모두 I/O의 완료는 인터럽트로 알려준다.

## DMA (Direct Memory Access)

> CPU에 Interrupt가 너무 많음

- 빠른 입출력 장치(인터럽트 빈번)를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 block 단위로 전송
- 특정 크기가 버퍼에 쌓이면 한번에 CPU에 전달
  - byte 단위가 아니라 block 단위로 인터럽트 발생

## 서로 다른 입출력 명령어

### 일반적인 I/O

- 메모리 접근 instrution, 입출력 디바이스 별 instruction 분리

### Memory Mapped I/O

- 입출력 디바이스에 Memory 주소 할당

## 저장장치 계층 구조

- [12장 메모리 구조](/컴퓨터-구조/12장-메모리-구조.md)

## 프로그램의 실행 (메모리 load)

## Address space

> 모든 프로세스는 독자적인 메모리 공간을 가지고 있음

- stack : 함수를 호출, return 하는 영역
- data : 변수 등 자료구조
- code : 기계어 공간

### Address Translation

- 하드웨어 계층에서 가상→물리 주소 변환

## Virtual memory

> 필요한 부분만 Physical memory에 상주시켜 메모리 낭비 최적화

## File System vs. Swap area

- FIle System : 영구 공간
  - 비휘발적
- Swap area : 임시 공간
  - 메모리 연장 공간

## 커널 주소 공간의 내용

- code
  - 시스템콜, 인터럽트 처리 코드
  - 자원 관리 코드
  - 편리한 서비스 제공을 위한 코드
- data
  - 하드웨어 종류별 관리
  - PCB(Process Control Block) : 프로세스별 관리 자료구조
- stack
  - 프로세스별 커널 스택

## 사용자 프로그램이 사용하는 함수

### 함수(function)

- 사용자 정의 함수
  - 자신의 프로그램에서 정의한 함수
- 라이브러리 함수
  - 가져다 쓴 함수
  - 자신의 프로그램 실행 파일에 포함(code)
- 커널 함수
  - 운영체제 프로그램의 함수
  - 커널 함수의 호출 = 시스템 콜(code)
