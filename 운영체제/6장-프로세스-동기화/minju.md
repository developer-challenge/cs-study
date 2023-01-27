# [프로세스 동기화 #1](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

39분 부터 시작

## 데이터의 접근

![](https://i.imgur.com/R1VeLtm.png)

데이터를 읽어가는 것 자체의 순서는 중요하지 않음
하지만 읽어간 데이터를 수정해 그 결과를 데이터에 반영하게 된다면
누가 먼저 데이터를 가져갔는지 순서가 중요해짐

## Race Condition

![](https://i.imgur.com/RDbLAo6.png)

2개 이상의 프로세스가 공유 자원을 병행적으로 읽거나 쓰는 상황

유저 레벨에서는 문제가 생기지 않지만 커널의 데이터는 공유 데이터이므로 문제 발생

## OS에서의 Race Condition

### 1. kernel 수행 중 인터럽트 발생시

![](https://i.imgur.com/S2Smhhe.png)

결과적으로 1 증가만 반영됨. (Interrupt handler 반영되지 않음)
이 문제를 해결하기 위해 작업이 끝날 때까지 interrupt 처리가 들어와도 수행시키지 않음

### 2. process가 system call을 하여 kernel mode로 수행중인데 context switch가 일어나는 경우

![](https://i.imgur.com/uCiXIiH.png)

두 프로세스의 address space 간에는 data sharing이 없음
그러나 system call을 하는 동안에는 kernel address space의 data를 access하게 됨 (share)
**이 작업 중간에 CPU를 preempt 해가면 `race condition` 발생**

![](https://i.imgur.com/nJpvL2a.png)
-> PB는 반영되지 않음

해결책
: 커널 모드에서 수행 중일 때는 CPU를 preempt 하지 않음
커널 모드에서 사용자 모드로 돌아갈 때 preempt

### 3. Multiprocessor에서 shared memory 내의 kernel data

![](https://i.imgur.com/DbGTU2O.png)

어떤 CPU가 마지막으로 count를 store 했는가? -> `Race condition`
multiprocessor의 경우 Interrupt enable/disable로 해결 되지 않음 (앞선 해결방법 적용 불가능)

#### 방법

1. 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법
2. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 `Lock/unlock`을 하는 방법 ✅

> 사용자 프로세스 P1 수행중 timer interrupt가 발생해서 context switch가 일어나 P2가 CPU를 잡으면?

## Process Synchronization 문제

- Example of a Race Condition

### The Critical-Section Problem

n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재

Problem

- 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.
- ![](https://i.imgur.com/cDsJ84R.png)

## 프로그램적 해결법의 충족조건

### Initial Attempts to Solve Problem

두 개의 프로세스가 있다고 가정시, 프로세스의 일반적인 구조

![](https://i.imgur.com/mMegP7O.png)

프로세스들은 수행의 동기화(synchronize)를 위해 몇몇 변수를 공유할 수 있다
= `synchronization variable`

# [프로세스 동기화 #2](https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f)

## Semaphores

> 추상 자료형 : 오브젝트와 오퍼레이션으로 구성된 자료형

Semaphore S
integer variable
아래의 두가지 **atomic 연산**에 의해서만 접근 가능

![](https://i.imgur.com/W8NZMXx.png)

프로그램은 직접 코드를 작성하는게 아니라 추상적으로

## Critical Section of n Processes

busy-wait : 효율적이지 못함 (= spin lock)
block & wakeup 방식의 구현 (= sleep lock)

## Block / Wakeup Implementation

![](https://i.imgur.com/w3NSf8D.png)

block 과 wakeup을 다음과 같이 가정

`Block` : 커널은 block을 호출한 프로세스를 suspend 시킴. 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
`wakeup(P)` : block된 프로세스 P를 wakeup 시킴. 이 프로세스의 PCB를 ready queue로 옮김

![](https://i.imgur.com/Bgpq2Av.png)

block & wakeup 방식은 단순히 기다리는게 아니라 잠든게 있다면 깨워주는 형식

### Busy-wait vs block/wakeup

block/wakeup overhead vs Critical section 길이

- critical section의 길이가 긴 경우 block/wakeupdl 적당
- critical section의 길이가 매우 짧은 경우 block/wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
- 일반적으로는 block/wakeup 방식이 더 좋음

## Two Types of Semaphores

### Counting semaphore

- 도메인이 0 이상인 임의의 정수값
- 주로 resource counting에 사용

### Binary semaphore

- 0 또는 1값만 가질 수 있는 semaphore
- 주로 mutual exclusion (lock/unlock)에 사용

## Deadlock and Starvation

### Deadlock

둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상

![](https://i.imgur.com/7T0I24y.png)

> 해결 방법 : 자원을 획득 하는 순서를 정해두기

### Starvation

`indefinite blocking` 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 파져나갈 수 없는 현상

# [프로세스 동기화 #3](https://core.ewha.ac.kr/publicview/C0101020140408134626290222?vmode=f)

# [프로세스 동기화 #4](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)

## Classical Problems of Syncronization

### Bounded-Buffer Problem (Producer-Consumer Problem)

버퍼의 크기가 유한한 상태

![](https://i.imgur.com/2dQ4eQl.png)

#### 생산자 프로세스

비어있는 버퍼 = 사용 가능한 자원

💣 생산자 두개가 동시에 도착해 데이터를 만드는 경우 -> Lock을 걸어서 한 생산자만 접근 가능하도록
💣 버퍼가 다 찬 상태에서 또 다른 생산자가 도착한 경우

#### 소비자 프로세스

채워진 버퍼 = 사용 가능한 자원

💣 두 소비자가 동시에 같은 자원을 가져가는 경우 -> Lock을 걸어 한 소비자만 데이터를 가져가도록

#### 공유 자원

buffer 자체 및 buffer 조작 변수 (empty / full buffer의 시작 위치)

#### synchronization variables

mutual exclusion (lock 해제): binary semaphore 필요
자원의 갯수 : integer semaphore 필요

![](https://i.imgur.com/Z3DfwOM.png)

### Readers-Writers Problem

한 프로세스가 DB에 write중일 때 다른 프로세스가 접근하면 안됨
read는 동시에 여럿이 가능

#### 해결 방법

- writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB에 접근하게 해준다.
- Writer는 대기중인 Reader가 하나도 없을 때 DB접근이 허용된다.
- 일단 Writer가 DB에 접근 중이면 Reader들의 접근이 금지된다.
- Writer가 DB에서 빠져 나가야만 Reader의 접근이 허용된다.

#### 공유 자원

DB 자체
Readcount : 현재 DB에 접근 중인 Reader의 수

![](https://i.imgur.com/RMUtERx.png)

#### synchronization variables

mutex : 공유 변수 readcount를 접근하는 코드 (critical section)의 mutual exclusion 보장을 위해 사용
db : reader와 writer가 공유 DB자체를 올바르게 접근하게 하는 역할

큐에 우선순위를 둬서 reader에 접근 가능하도록 구현

### Dining-Philosophers Problem

![](https://i.imgur.com/t4oOTDn.png)

![](https://i.imgur.com/qw7sk0A.png)

젓가락을 내려 놓는 순간, 주변의 상태를 살핌

### Monitor

#### Semaphore의 문제점

![](https://i.imgur.com/21mGNR4.png)

- 코딩하기 힘들다
- 정확성의 입증이 어렵다
- 자발적 협력이 필요하다
- 한번의 실수가 모든 시스템에 치명적 영향을 미친다.

=> 모니터를 이용해 위의 문제 해결

동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct

모니터의 processor를 통해서만 공유 데이터에 접근 가능
모니터 내부의 processor는 자체적으로 하나만 실행 가능하도록 함
= Lock이 필요 없음.

프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 condition variable 사용
condition variable은 wait과 signal 연산에 의해서만 접근 가능

`wait()`

- x.wait()을 invoke한 프로세스는 다른 프로세스가 x.signal을 invoke 하기 전까지 suspend된다.

`signal()`

- x.signal()은 정확하게 하나의 suspend된 프로세스를 resume한다.
- suspend된 프로세스가 없으면 아무일도 일어나지 않는다.

![](https://i.imgur.com/XEPPr8Z.png)

process synchronization (프로세스 동기화) = concurrency control (병행 제어)

#### bounded-buffer problem

condition variable은 값을 가지지 않고 자신의 큐에 프로세스를 매달아서 sleep 시키거나 큐에서 프로세스를 깨우는 역할만 함.

![](https://i.imgur.com/gf62OAe.png)

공유 버퍼에 대해 락을 풀거나 거는 코드가 필요하지 않음

`monitor`는 동시접근을 자체적으로 막음
`semaphore`에서는 자원을 획득하기 위해 프로그래머가 값을 이용해 막음
