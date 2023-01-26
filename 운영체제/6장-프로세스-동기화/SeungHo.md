# 6장 프로세스 동기화

## [프로세스 동기화 #1](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

### 데이터의 접근

![](https://i.imgur.com/oxHLnbm.png)

### Race Condition

![](https://i.imgur.com/8uYW1aa.png)

S-box를 공유하는 E-box가 여럿 있는 경우 **Race Condition**의 가능성이 있음

Memory - CPU : Multiprocessor system

Address Space - Process : 공유메모리를 사용하는 프로세스들, 커널 내부 데이터를 접근하는 루틴들 간(예 : 커널모드 수행 중 인터럽트로 커널모드 다른 루틴 수행시)

### OS에서의 Race Condition

**OS에서 race condition은 언제 발생하는가?**

1. kernel 수행 중 인터럽트 발생 시
2. Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernel data

****\*\*****interrupt handler v.s. kernel****\*\*****

![](https://i.imgur.com/nUnqBqX.png)

커널모드 running 중 interrupt가 발생하여 인터럽트 처리루틴이 수행

→ 양쪽 다 커널 코드(interrupt handler도 kernel에 있는 코드)이므로 kernel address space 공유

1. Kernel에서 load하여 데이터를 불러옴
2. Interrupt handler로 인해 Count— 실행
3. 다시 Count—되기 전 load한 데이터에 Count++
4. Count++한 뒤의 결과값은 Count—가 반영되지 않음

**********************************\*\*\*\***********************************Preempt a process running in kernel?**********************************\*\*\*\***********************************

![](https://i.imgur.com/pam6Hgs.png)

- 두 프로세스의 address space간에는 data sharing이 없음
- 그러나 system call을 하는 동안에는 kernel address space의 data를 access하게 됨 (share)
- 이 작업 중간에 CPU를 preempt 해가면 race condition 발생

![](https://i.imgur.com/vAweOl9.png)

PA가 System call하여 Kernel모드 실행 중 할당 시간이 끝나서 PB에게 CPU가 넘어갔을 때 → 다시 CPU를 되돌려 받았을 때 PB의 Count++이 반영되지 않음

**해결책**

- 커널 모드에서 수행 중일 때는 CPU를 preempt하지 않음
- 커널 모드에서 사용자 모드로 돌아갈 때 preempt

************\*\*\*\*************Multiprocessor************\*\*\*\*************

![](https://i.imgur.com/fyMFhKc.png)

어떤 CPU가 마지막으로 count를 store했는가? → **race condition**

multiprocessor의 경우 interrupt enable/disable로 해결되지 않음 (작업 주체(CPU)가 여럿이 있기 때문에 데이터 접근 가능)

******\*\*******해결책******\*\*******

1. 한 번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법 - Kernel 전체에 lock 하는 방법
2. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 **lock/unlock**을 하는 방법

### Process Synchronization 문제

- 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있음
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process)간의 실행 순서(orderly excution)를 정해주는 메커니즘 필요
- **Race condition**
  - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
  - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- race condition을 막기 위해서는 concurrent process는 동기화(synchronize)되어야 함

**Example of a Race Condition**

![](https://i.imgur.com/dS67FMb.png)

X = X + 1이라는 고급 언어의 한 줄을 실행하기 위해 load, inc, store 여러 인스트럭션 실행

사용자 프로세스 P1 수행 중 timer interrupt가 발생해서 context switch가 일어나서 P2가 CPU를 잡으면 문제 발생하지 않음 → 특별히 공유 데이터를 사용하고 있거나 Kernel에 있는 데이터를 사용 중일 때 문제 발생

**The Critical-Section Problem**

![](https://i.imgur.com/tfepJjc.png)

- n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 **critical section**이 존재
- Problem
  - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 **critical section**에 들어갈 수 없어야 함

### 프로그램적 해결법의 충족조건

**Mutual Exclusion**

- 프로세스 Pi가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안됨

**Progress**

- 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 함

**Bounded Waiting**

- 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 함

가정

- 모든 프로세스의 수행 속도는 0보다 큼
- 프로세스들 간의 상대적인 수행 속도는 가정하지 않음

**Initial Attempts to Solve Problem**

- 두 개의 프로세스가 있다고 가정 P0, P1
- 프로세스들의 일반적인 구조
  ```yaml
  do{
  entry section
  **critical section**
  exit section
  **remainder section**
  }while(1);
  ```
- 프로세스들은 수행의 동기화(synchronize)를 위해 몇몇 변수를 공유할 수 있음 → synchronization variable

## [프로세스 동기화 #2](https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f)

### Semaphores

- 앞의 방식들을 추상화시킴 - **추상 자료형** : 실제 컴퓨터에서 어떻게 구현되는지와는 별개로 논리적으로 정의
- 사용하는 이유
  - lock/unlock 기능도 간단하게 semaphores를 이용해 프로그래머한테 제공
  - 공유자원을 획득하고 반납하는 것을 semaphores가 처리
- Semaphore S

  - integer variable - 세마포어 변수가 정수값을 가질 수 있음(**자원의 개수**)
    ex) 변수가 5일 경우 P 연산을 5번 해서 다섯이서 동시에 가져갈 수 있음
    lock을 걸고 푸는 과정은 변수가 1
  - 아래의 두 가지 atomic 연산에 의해서만 접근 가능

  ![](https://i.imgur.com/8pfC8ZS.png)

  P 연산 : semaphore 변수값을 획득하는 과정

  V 연산 : 다 사용하고 나서 반납하는 과정

### Critical Section of n Processes

![](https://i.imgur.com/cz6Ogoc.png)

busy-wait 구현 방식(누군가가 critical section에 들어가 있으면 P연산을 하면서 While문을 돌고 계속 본인의 CPU 시간을 씀)는 효율적이지 못함 (=spin lock)

Block & Wakeup 방식은 While문을 돌지 않고 Block 시킴

### Block / Wakeup Implementation

Semaphore를 다음과 같이 정의

![](https://i.imgur.com/w9TG3I4.png)

Block & Wakeup 방식의 구현 (=sleep lock)

- block과 wakeup을 다음과 같이 가정
  - **block** : 커널은 block을 호출한 프로세스를 suspend 시킴, 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
  - **wakeup(P)** : block된 프로세스 P를 wakeup시킴, 이 프로세스의 PCB를 ready queue로 옮김
  ![](https://i.imgur.com/5zstEMt.png)

**block/wakeup version of P() & V()**

Semaphore 연산이 다음과 같이 정의됨

![](https://i.imgur.com/Bmlm0m0.png)

P(S) : 자원을 획득하는 과정 / 자원에 여분이 있다면 바로 획득, 없다면 block

V(S) : 자원을 다 사용한 뒤 반납 / 혹시 자원을 기다리면서 잠들어있는 프로세스가 있다면 깨워줌

**어떤게 더 나은가?**

Busy-wait v.s. Block/wakeup

**Block/wakeup overhead v.s. Critical section 길이**

- Critical section의 길이가 긴 경우 Block/Wakeup이 적당
- Critical section의 길이가 매우 짧은 경우 Block/Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
- **일반적으로는 Block/wakeup 방식이 더 좋음**

### Two Types of Semaphores

**Counting semaphore**

- 도메인이 0 이상인 임의의 정수값
- 주로 resource counting에 사용

**Binary semaphore (=mutex)**

- 0 또는 1 값만 가질 수 있는 semaphore
- 주로 mutual exclusion (lock/unlock)에 사용

### Deadlock and Starvation

**Deadlock**

- 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상

S와 Q가 1로 초기화된 semaphore라 할 때

![](https://i.imgur.com/8YAnzLv.png)

1. P0가 P(S)를 획득
2. P1이 CPU를 뺏어와 P(Q)를 획득
3. P(S)는 P0가 갖고 있기 때문에 영원히 기다려야 함 (P0가 P(S)를 반납하는 시점은 P(Q)를 획득하고 다 사용한 뒤 반납)

**해결** : P1에서 P(Q) → P(S)를 P(S) → P(Q)로 변경 / P1이 P(S)를 갖기 전에 기다림 → 둘 다 하나씩 갖고 있으면서 영원히 내놓지 않는 문제는 발생하지 않음

![](https://i.imgur.com/uVuoptO.png)

********\*\*\*\*********Starvation********\*\*\*\*********

특정한 프로세스가 영원히 자원을 얻지 못하고 기다려야하는 상황

여기서의 starvation은 특정 프로세스들만 자원을 자기들끼리 공유하면서 다른 프로세스들은 영원히 자기 차례가 오지 못하도록 하는 것

- indefinite blocking. 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상

## [프로세스 동기화 #3](https://core.ewha.ac.kr/publicview/C0101020140408134626290222?vmode=f)

### Bounded-Buffer Problem (Producer-Consumer Problem)

![](https://i.imgur.com/AQkGdSC.png)

**생산자(Producer)**

- 데이터를 만들어서 집어넣는 역할 - 비어있는 버퍼를 채움
- 비어있는 버퍼에 두 생산자가 동시에 데이터를 만들어서 넣으면 문제가 생김 → lock/unlock

**소비자(Consumer)**

- 데이터를 꺼내감 - 채워진 버퍼를 소비
- 두 소비자가 동시에 데이터를 꺼내가면 문제가 생김 → lock/unlock

**세마포어를 이용해 해야 할 업무**

- 둘이 동시에 공유 버퍼를 접근하는 것을 막기 위해 공유 버퍼 전체에 lock을 걸어서 나 혼자 베타적으로 접근하게 하고 끝났으면 unlock
- 버퍼가 가득 차거나 비었을 때 가용자원의 개수를 셈 (Counting Semaphore)

************************\*\*\*\*************************세마포어를 이용한 pseudo code************************\*\*\*\*************************

**Synchronization variables**

semaphore full = 0 (내용이 들어있는 버퍼를 세기 위한 변수)

empty = n (비어있는 버퍼를 세기 위한 변수)

mutex = 1 (공유 버퍼를 하나의 프로세스만 접근할 수 있도록 lock을 걸기 위한 변수)

![](https://i.imgur.com/Uj3iHxQ.png)

**********\*\***********Shared data**********\*\***********

- buffer 자체 및 buffer 조작 변수 (empty/full buffer의 시작 위치)

**\*\***Synchronization variables**\*\***

- mutual exclusion → Need binary semaphore (shared data의 mutual exclusion을 위해)
- resource count → Need integer semaphore (남은 full/empty buffer의 수 표시)

### Readers-Writers Problem

읽는 프로세스, 쓰는 프로세스 두 종류가 있음

- 한 process가 DB(공유데이터)에 write 중일 때 다른 process가 접근하면 안됨
- read는 동시에 여럿이 해도 됨
- solution
  - Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB에 접근하게 해줌
  - Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용됨
  - 일단 Writer가 DB에 접근 중이면 Reader들은 접근이 금지됨
  - Writer가 DB에서 빠져나가야만 Reader의 접근이 허용됨

Shared data

- **DB 자체**
- **readcount** (현재 DB에 접근 중인 Reader의 수)

Synchronization variables

- **mutex** (공유 변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용)
- **db** (Reader와 writer가 공유 DB 자체를 올바르게 접근하게 하는 역할)

**Pseudo code**

Shared data

- int readcount = 0
- DB 자체

Synchronization variables

- semaphore mutex = 1
- db = 1

![](https://i.imgur.com/4uiMIWW.png)

**Starvation** : Reader가 계속 요청되면 Writer는 Reader가 없어질 때 까지 계속 대기

→ 어느 정도의 Reader가 지나가면 Reader의 접근을 막고 현재 프로세스가 다 종료되면 Writer 접근 허용

### Dining-Philosophers Problem

![](https://i.imgur.com/pujt5FO.png)

think → eat → think…

5명의 철학자가 왼쪽과 오른쪽 젓가락을 들고 → 밥을 먹음 → 젓가락을 놓고 → 생각

**문제점**

- Deadlock 가능성이 있음
- 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어버린 경우

********\*\*\*\*********해결 방안********\*\*\*\*********

- 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 함
- 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 함
- 비대칭 - 한 쪽 젓가락을 먼저 집은 사람이 모든 젓가락을 집을 수 있음
  - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 잡도록 함

![](https://i.imgur.com/Rc2p6fJ.png)

state : 1~5번 철학자 각각의 상태

semaphore self : 1~5번 철학자 각각이 젓가락을 모두 잡을 수 있는지 권한 여부(0 : 권한 X / 1 : 권한 O)

semaphore mutex : 공유 변수에 대한 동시접근을 막기 위한 lock을 나타내는 변수

## [프로세스 동기화 #4](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)

### Monitor

프로그래밍 언어 차원에서 동시 접근과 관련된(공유데이터에 접근하는) 문제를 자동으로 해결함으로써 프로그래머의 부담을 줄여줌 /

동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 **high-level synchronization construct**

![](https://i.imgur.com/MYpLst4.png)

모니터 내부에 공유 변수에 대한 선언을 해놓고 공유 데이터에 접근하기 위한 프로시저들은 모니터 내부 함수로 구현을 해놓음

**********************\*\***********************Semaphore의 문제점**********************\*\***********************

- 코딩하기 힘듦
- 정확성(correctness)의 입증이 어려움
- 자발적 협력(voluntary cooperation)이 필요
- 한번의 실수가 모든 시스템에 치명적 영향

**Ex)**

![](https://i.imgur.com/lcycD2E.png)

---

![](https://i.imgur.com/aG99Ub9.png)

모니터 안에 공유데이터(shared data)와 공유데이터를 접근하는 프로시저(operations)를 정의해놓고 프로시저를 통해서만 공유데이터에 접근 가능

모니터 내부에 있는 프로시저는 동시에 여러개가 실행이 되지 않도록 통제하는 권한을 줌 → lock을 걸 필요가 없음 (semaphore는 동시접근을 막기 위해 항상 P연산으로 lock을 걸고 일을 하고 unlock : 프로그래머가 이 작업을 함)

entry queue : 나머지 프로세스는 줄 서서 기다림

- 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요없음
- 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 **condition variable** 사용 - condition x, y;
  - **condition variable** : 어떤 조건을 만족하지 않아 오래 기다려야 할 때 프로세스를 잠들게 하고 줄세우기 위한 변수
- Condition variable은 **wait**와 **signal** 연산에 의해서만 접근 가능
  **x.wait();**
  어떤 프로세스를 잠들게 하겠다 → x condition variable에 줄서있음(x라는 조건을 만족하지 못해서 줄서있음)
  x.wait()을 invoke한 프로세스는 다른 프로세스가 x.signal()을 invoke하기 전까지 suspend됨
  **x.signal();**
  누군가가 x를 다 쓰고 빠져나가서 혹시 x라는 condition variable조건을 기다리면서 잠들어 있는 프로세스가 있으면 깨워줌
  x.signal()은 정확하게 하나의 suspend된 프로세스를 resume함
  Suspend된 프로세스가 없으면 아무 일도 일어나지 않음

**Bounded-Buffer Problem → monitor code**

![](https://i.imgur.com/z2r3jWE.png)

공유 버퍼가 모니터 안에 정의되어 있음

→ 생산하거나 소비하는 작업을 하기 위해서는 모니터 안의 내부 코드 실행

→ 생산자(produce)든 소비자(consume)든 하나의 프로세스가 모니터안에서 활성화

→ lock을 걸지 않아도 생산자가 접근하는 동안에 또다른 생산자나 소비자가 접근하는 문제는 고려할 필요가 없음

**Dining Philosophers Example**

![](https://i.imgur.com/ef0tUxR.png)

**pickup**

state[i] : 공유 데이터

철학자가 젓가락을 잡을 수 있는지 test

- 젓가락을 모두 잡을 수 있으면 state = eating
- self[i].signal() → 철학자 i가 혹시 잠들어있으면 깨워줌

test 조건을 만족하면 eat() 실행 / 만족하지 않으면 self[i].wait → 잠들게 됨

******\*\*******putdown******\*\*******

밥을 다 먹었으면 철학자의 상태는 thinking 변경

인접 철학자가 본인때문에 밥을 못 먹고 잠들어있으면 깨워줌 → 왼쪽 철학자 test, 오른쪽 철학자 test
