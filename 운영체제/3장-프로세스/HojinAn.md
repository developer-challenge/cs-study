## [프로세스 #1](https://core.ewha.ac.kr/publicview/C0101020140318134023355997?vmode=f)

### 프로세스의 개념

- `실행 중`인 `프로그램`
- 프로세스는 독자적인 주소공간을 갖는다.
- 프로세스의 `문맥(context)`가 중요하다.  
  시작부터 종료까지 시점을 놓고 봤을 때, 프로세스가 어디까지 진행됐는지, 무엇을 했는지 등  
  Program Counter로 파악  
  프로그램 메모리에 무엇을 담고 있나.  
  레지스터가 어디까지 실행했나
- 문맥
  - 하드웨어 문맥: CPU 수행 상태를 나타냄, Program Counter, 각종 register
  - 프로세스 주소 공간: code, data, stack
  - 관련 커널 자료 구조: PCB, Kernel stack(커널 함수는 모든 프로그램들이 공통으로 사용하지만, 프로세스 별로 스택을 따로 쌓는다) => 따라서 프로세스 문맥을 위해서 이들도 파악 필요
    프로세스의 문맥을 알지 못한다면, CPU가 다시 실행시킬 때, 처음부터 실행해야하는 불상사가 발생. 다음 시점부터 instruction 실행할 수 있도록

### 프로세스의 상태 (Process State)

프로세스는 상태를 변경해가면서 수행된다.

- `Running`: CPU를 잡고 instruction 수행 중
- `Ready`: CPU 기다리는 상태(메모리(CPU가 Disk는 접근 못하므로) 등 다른 조건 모두 만족된 상태) -> 보통 이 상태의 프로세스들이 CPU를 번갈아가며 잡는다.
- `Blocked(wait, sleep)`: CPU를 줘도 당장 instruction 수행 불가  
  I/O, Disk 작업 등 오래 걸리는 일을 완료해야 수행 가능한 경우
  달리 말하면, Process가 자신이 요청한 event가 즉시 만족되지 않아 기다리는 상태

보통은 이렇게 3가지 상태. 다른 상태 추가로 이야기하기도

- New: 프로세스 생성 중
- Terminated: 수행(execution) 끝난 상태

![](https://i.imgur.com/QiEiPsY.png)

각 수행들은 Queue로 들어간다는 것을 기억할 것.  
Ready Queue, Resource Queue, Disk I/O Queue...

그런데 사실 이 Queue들은 커널 주소 공간 내 data 영역에 만들어서 관리하고 있는 거임

### Process Control Block(PCB)

- 각 Process를 관리하기 위한 정보
- 구성 요소(구조체로 유지)
  1. OS가 관리상 사용하는 정보: Process 상태(Ready, Running, Blocked), Process ID, Queue에서의 Priority, 스케쥴링 정보 등
  2. CPU 수행 관련 HW 값: PC(Program Counter), Register
  3. 메모리 관련: code, data, stack 위치 정보
  4. 파일 관련: 프로세스가 Open 중인 파일 정보 등

### 문맥 교환 (Context Switch)

- CPU는 매우 빠른 자원
- Process가 CPU를 장악, 빼앗김을 반복
- CPU를 되찾는 시점에는 뺏기던 시점의 문맥을 기억해서, 그 시점부터 실행해줘야 한다.
- 문맥 교환: CPU가 한 Process로부터 다른 Process로 넘어가는 과정
- 인터럽트 등으로 CPU를 넘겨줘야 하는 상황이 오면, register에 저장되어 있던 값을 Process PCB에 저장해둔다. PC, Memory Map 등을 저장.
  -> CPU 빼앗길 때, 이런 정보를 OS가 PCB에 저장  
  -> CPU 얻을 때, 이런 정보를 PCB에서 찾아서 HW에 복원 -> CPU 넘겨 줌

Q. System Call/Interrupt는 반드시 Context Switch을 일으키나?
A. No. Context Switch는 분명 `하나의 프로세스에서 다른 프로세스로` CPU가 넘어가는 과정이다. System Call/Interrupt로는 CPU가 `OS로` 넘어간다. 이것은 Context Switch가 아니다.  
Timer Interrupt, I/O 요청 System Call => 얘네는 Context Switch 일으킬 법하다.
![](https://i.imgur.com/c2N22I9.png)
물론 커널모드로 들어갔다 유저모드로 들어가기 위해서 문맥 저장을 해야한다. 하지만 문맥 교환 없는 그냥 user mode 복귀는 overhead가 적다. ex. 그냥 문맥 교환에서는 cache memory flush가 발생해서 overhead가 크다.

### 프로세스를 스케줄링하기 위한 큐

- Job queue: 현재 시스템 내 모든 Process 집합
- Ready queue: 현재 메모리 내 && CPU 실행 기다리는 Process 집합
- Device queues: I/O 디바이스 처리 기다리는 Process 집합

프로세스를 줄 세운다고는 했지만, 정확히 말해서는 OS가 Process 관리하기 위해 만든 PCB를 줄 세운다고 말하는 것이 맞다. PCB 내 pointer로 각각을 연결

### 스케줄러 (Scheduler)

- 각각의 자원별로 각 하는 일의 시간을 얼만큼 잡는 것
- 종류
  - Long-term scheduler(장기 스케줄러, job scheduler)  
    어떤 Process에 `memory` 줄지의 문제. New로 생성 이후 Memory에 올라가야 Ready 상태가 되는데, 그걸 결정해준다.  
    `degree of multiprogramming`을 제어: 메모리에 여러 프로그램이 동시에 올라가는 개념. 몇개 올라가 있는가. 너무 많은 프로그램이 올라가 있으면 성능 저하. 너무 적어도 성능 저하.
    - 하지만 우리가 보통 사용하는 time sharing system에서는 장기 스케줄러가 없다(?!) => Process 무조건 Ready 상태 -> 그러면 degree of multiprogramming은? => 그래서 중기 스케줄러를 사용
  - Short-term scheduler(단기 스케줄러, CPU scheduler)  
    짧은 시간 단위, ms 단위. 어떤 Process에 `CPU` 줄지를 결정.
  - Medium-term scheduler(중기 스케줄러, Swapper)  
    너무 많은 프로세스가 메모리에서 돌아가면, 일부 프로그램을 메모리에서 쫓아낸다. => `degree of multiprogramming` 제어 => 시스템 성능 향상

과거에는 처음에 몇개 올릴지를 정했는데, 이제는 일단 다 올리고, Swapper가 제어해준다고 생각하면 될 듯
장기는 메모리를 주는 방식으로 제어, 중기는 빼앗는 방식으로 제어. 중기가 더 효율적

중기 스케줄러 때문에 새롭게 생긴 Process 상태: Suspended(stopped)

- Suspended(stopped): CPU 없을 뿐 아니라 외부에서 `정지`시켜놓은 상태. Blocked면 차라리 I/O라도 열심히 일할텐데. 메모리에서 쫓겨나 Disk로 Swap out된 상태. 사람이 일시정지 시킬 수도 있다.  
  외부: 중기 스케줄러 or 사람

`Blocked vs Suspended`

- Blocked: 요청 Event 만족시 Ready => 자신이 요청한 걸 기다리는 상태
- Suspended: 외부 Resume해줘야 Active => 외부에서 정지시켜놓은 상태

> 유의할 점  
> Running, Ready 이렇게 하는 것들은 Process의 상태지 OS의 상태가 아니다. OS가 Process 관리상 상태를 나눠놓은 것. 운영체제 본인의 상태가 아니다.  
> kernel mode에서의 Running도 프로세스가 커널모드에서 Running 중이라는 것이지 운영체제가 Running 중인 것이 아니다.

![](https://i.imgur.com/bERzhYb.png)

## [프로세스 #2](https://core.ewha.ac.kr/publicview/C0101020140321141759959993?vmode=f)

### 질문 답변

#### 동기식 입출력, 비동기식 입출력에 관하여

Process 입장에서 I/O 요청. but 본인이 직접하는 것이 아니므로 OS에게 요청. I/O 요청은 오래 걸린다.  
수행되는동안 I/O 요청한 해당 Process가 기다려야하는 경우 => 동기식 입출력
I/O 요청 후 (I/O 수행되는동안) 해당 Process가 곧바로 CPU 잡고 instruction 실행하는 경우 => 비동기식 입출력

I/O 요청 후 instruction 실행하지 않으면, 입출력 보고서 실행하면 동기식
I/O 요청 후 바로 instruction 실행하면 비동기식

동기식 구현방법

1. I/O 끝날 때까지 CPU 점유하며 낭비 => 매시점 하나의 I/O만 발생
2. 어차피 CPU 사용 못할 거, CPU 빼앗아서 다른 프로그램에 CPU 준다.

### Thread

- Process 내부에 CPU 수행 단위가 여러개 있는 경우 이를 스레드라한다.

프로세스 주소 공간: stack, data, code
OS가 Process 관리 위해 => PCB => 현재 실행 중인 메모리 부분을 PC로 가리킨다.  
동일한 일을 하는 Process를 여러개 만든다면 별도의 주소 공간 여러 개가 생겨서 낭비 => 하나의 메모리 공간에서 프로세스(라기보단 CPU 실행단위)마다 다른 부분을 실행해주도록 하면 된다. 이것이 스레드

프로세스는 하나. CPU가 어느 부분을 실행하고 있는지를 가리키는 PC(Programm Counter)를 여러개 두는 것
따라서 프로세스 하나에 CPU 수행 단위만 여러개 두고 있는 것이 thread다

CPU 실행 즉, instruction 실행을 위해서는 어디 code 실행 중인지 가리키는 PC가 필요하고, CPU 실행하며 register 값을 가리키고 있을 것. 각 스레드마다 register에 어떤 값을 넣고, PC가 코드 어느 부분을 가리키고 있는가를 별도로 유지

스레드 하나가 코드 실행하다가 함수를 호출하면 => 관련 정보(호출, 리턴)를 stack에 쌓는데, 스레드가 여러개면 stack도 별도로 둬야 한다.

즉, 스레드는 하나의 프로세스에서 공유할 수 있는 것(메모리 주소 공간, PCB, 각종 자원 등)은 최대한 공유하고 CPU 수행과 관련된 정보(PC, register, stack)는 별도로 관리한다.

CPU를 수행하는 단위

구성: PC, register set, stack space
공유: code, data, OS resources(나머지 자원, task)

lightweight process라고도 부름

전통적인 process는 heavyweight process라고도 부른다.

## [프로세스 #3](https://core.ewha.ac.kr/publicview/C0101020140321143516139010?vmode=f)

### Single and Multithreaded Processes

![](https://i.imgur.com/BGjAvOe.png)

### Benefits of Threads

- thread 하나가 blocked 상태일 때, 다른 thread가 CPU를 잡고 Running 할 수 있다. => 응답 시간 빨라진다.
- 메모리 자원 낭비 방지. 같은 일을 하는 Process를 여러 개 만들면 메모리 낭비
- 다중 thread가 협력해서 높은 처리율(throughput)을 얻을 수 있다.
- CPU 여러개 달린 컴퓨터에서 병렬성을 높일 수 있다. 서로 다른 CPU에서 계산 후 합쳐줄 수 있다. <= thread마다 CPU 정보를 가지고 있기 때문.

크게 4가지

1. 응답성(Responsiveness)  
   이미지를 불러오는 등. 오래 걸리는 작업이 걸리면 thread는 block된다. 여러 thread를 사용하면, 이미지 불러오는 thread만 분리해서 요청할 수 있다. 다른 thread가 문서라도 먼저 보여줄 수 있다. 일종의 비동기식 입출력
2. 자원 공유(Resource Sharing)
   같을 일을 하는 여러 프로그램, 여러 Process는 낭비. 하나의 Process 내 여러 thread를 만들면 code, data, 자원을 공유할 수 있다. => 효율적 사용 효과
3. 경제성(Economy)
   응답성과 다른 의미로 빠르다는 것. Process 하나를 만드는 것은 Overhead가 꽤 큰 일이다. 반면, Process 내에 thread 하나 추가하는 일은 overhead가 그리 크지 않다. 추가적으로, context switch 발생시, Process에서 Process로의 이동은 Overhead가 크다(CPU 관련 정보 저장, cache memory flushing 등). Process 내 thread 간 context switch는 간단한 일. 동일 주소 공간 사용하고 있기 때문에 대부분의 context는 그대로 사용할 수 있다.

- Solaris(UNIX 일종)에서 생성은 30배, 스위칭은 5배 overhead

4. (CPU 여러 개인 환경에서의 장점) 멀티 프로세서 아키텍쳐 이용
   Process가 하나여도 여러 thread가 각각 다른 CPU에서 병렬적으로 일할 수 있음. => 결과를 더 빨리 얻을 수 있다.

### Implementation of Threads

- 어떤 OS는 Kernel의 도움 => Kernel Threads : OS가 thread가 여러개라는 것을 알고 있음 thread context switch도 kernel이 CPU 스케줄링 해주듯 수행해 줌
- 어떤 OS는 library의 도움 => User Threads : OS는 모른다. 사용자 프로그램이 library의 도움을 받아 여러 thread를 관리. kernel 입장에서는 일반적인 Process로 보인다. 구현 상에서 약간의 제약 사항이 있을 수 있다.
- Realtime-thread
