# [프로세스 #1](https://core.ewha.ac.kr/publicview/C0101020140318134023355997?vmode=f)

## 프로세스의 개념

> Process is a program in execution

## 프로세스의 문맥(context)

> 각 프로세스는 프로세스의 주소공간(stack, data, code)을 갖는다.
> 프로세스의 현재 진행 상태(stack, data, code 위치)에 대한 것을 프로세스의 문맥이라고 한다.
> CPU에는 단 하나의 프로세스만 상주하므로 Time Sharing(Multi Tasking)을 위해 프로세스의 문맥을 기억하는 것이 필수적이다.

- CPU 수행 상태를 나타내는 하드웨어 문맥 (CPU)
  - Program Counter
  - 각종 register
- 프로세스의 주소 공간 (메모리)
  - code, data, stack
- 프로세스 관련 커널 자료 구조 (운영체제)
  - PCB (Process Control Block)
  - Kernel stack
    - 커널마다 스택을 별도로 두고 있음

## 프로세스의 상태 (Process State)

> 프로세스는 상태(state)가 변경되며 수행된다.
> 여기서 상태는 운영체제의 상태가 아닌 프로세스의 상태를 말한다는 것에 유의하자

- Running (User mode, Monitor mode)
  - CPU가 실행중인 프로세스
- Ready
  - 메모리 등 조건을 만족하고 CPU를 기다리는 상태
  - Time Sharing ⇒ Ready queue
- Blocked (wait, sleep)
  - I/O, file read 등의 이벤트로 당장 instruction을 수행할 수 없는 상태
  - 자신이 요청한 event가 만족되면 Ready
- Suspended (stopped)
  - 외부적인 이유로 프로세스의 수행이 정지된 상태
  - ex. 중기 스케줄링으로 프로세스가 통째로 디스크에서 swap out
    - 외부(중기 스케줄러)에서 resume 해 주어야 Active
  - ex. 사용자가 일시 정지 (break key)
    - 사용자가 재개
- New
  - 프로세스가 생성 중인 상태
- Terminated

  - 수행이 끝난 상태. 종료 상태

- Ready : Ready queue
- Blocked : Resource queue, Disk I/O queue, keyboard I/O queue

## Process Control Block(PCB)

> 운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보

1. OS가 관리 상 사용하는 정보
2. CPU 수행 관련 하드웨어 값
3. 메모리 관련
4. 파일 관련

## 문맥 교환 (Context Switch)

> CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
> System Call이나 Interupt가 발생했을 때 반드시 context switch가 일어나는 것은 아님

- CPU를 내어주는 프로세스 상태를 그 프로세스의 PCB에 저장
- CPU를 새롭게 얻는 프로세스의 상태를 그 프로세스의 PCB에서 읽어옴

(1) ISR, system call이 발생할 때 단순히 User mode와 kernel mode를 전환함

이 경우 context를 저장할 필요는 있지만 문맥 교환은 발생하지 않음

(2) Timer interrupt, I/O 요청 system call의 경우 프로세스가 완전히 전환되어 문맥 교환이 발생함

이 경우에는 cache memory flush 등의 이유로 (1)보다 오버헤드 부담이 큼

## 프로세스를 스케줄링하기 위한 큐

- Job queue
  - 현재 시스템 내에 있는 모든 프로세스 집합 (Ready queue + Device queue)
- Ready queue
  - Ready queue와 다양한 Device queue
    프로세스들은 각 큐들을 오가며 수행된다.

  - 현재 메모리 내에 있으면서 CPU를 잡아 실행되기를 기다리는 프로세스 집합
- Device queue
  - I/O device 처리를 기다리는 프로세스 집합

## 스케줄러 (Scheduler)

### Long-term scheduler (장기 스케줄러, job scheduler)

- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지
- 프로세스에 memory를 주는 문제
- degree of Multiprogramming : 메모리에 몇 개 프로그램 (CPU가 놀지 않게 적당히)
- time sharing system에는 장기 스케줄링이 없는 것이 보통
  - 바로 ready queue

### Short-term scheduler (단기 스케줄러, CPU scheduler)

- ms 단위
- 다음 번에 어떤 프로세스를 running 할 지
- 프로세스에 CPU를 주는 문제

### Medium-term scheduler (중기 스케줄러, Swapper)

- 메모리에 너무 많은 프로세스가 동시에 올라가지 않도록
- 프로세스에서 memory를 뺏는 문제
- degree of Multiprogramming
-

# [프로세스 #2](https://core.ewha.ac.kr/publicview/C0101020140321141759959993?vmode=f)

## 질문 답변 > [1, 2장 운영체제 개요 및 컴퓨터시스템의 구조](/운영체제/1,-2장-운영체제-개요-및-컴퓨터시스템의-구조.md)

## Thread

> A thread is a basic unit of CPU utilization
> 하나의 프로세스 주소공간으로 여러 작업을 수행

- 구성
  - Program counter
  - Register set
  - Stack space
- 공유하는 것
  - Code section
  - Data section
  - OS resources

## 프로세스 #3

## Single and Multithreaded Processes

<aside>
💡 전통적인 개념의 heavyweight process는 하나의 thread(lightweight process)를 가지고 있는 task

</aside>

- code, data 등의 자원은 공유
- CPU 수행과 관련된 register set, stack은 스레드마다 별도로 갖게 된다.

## Benefits of Threads

> 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다. 동일한 일을 수행하는 다중 스레드가 협력해 높은 처리율, 성능 향상을 기대할 수 있다.

- Responsiveness
  - ex. multi-threaded Web
    - 웹 브라우저가 여러개의 스레드를 사용해서 읽어오는 대로 출력
    - 동기적 입출력 vs. 비동기적 입출력
- Resource Sharing
  - 하나의 프로세스로 여러개의 수행 단위
  - 코드, 각종 자원을 공유. 자원을 효율적으로 사용
- Ecomony
  - Process에 비해 Thread creating & CPU switching 오버헤드가 작다.
  - 같은 일을 한다면 Process X Thread O
- Utilization of MP(Multi Processor) Architectures
  - CPU가 여러개인 경우의 장점. 병렬성
  - 각각의 스레드가 서로 다른 CPU에서 수행됨

## Implementation of Threads

- Kernel Threads
  - 운영체제(커널)에서 지원
  - 운영체제에서 스레드가 여러개임을 알고 스케줄링
- User Threads
  - 사용자 수준에서 라이브러리를 통해 지원
- Real-time Threads
  - Real time 지원
