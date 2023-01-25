## [프로세스 동기화 #1](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

39분 부터 시작

### 데이터의 접근

- 컴퓨터 시스템 어디서든지 데이터 접근은 이런 과정으로 이루어진다.

1. 데이터가 저장되어 있는 위치가 있다.
2. 저장되어 있는 데이터를 읽어온다.
3. 연산을 수행
4. 연산된 결과를 원래 위치에 저장

S-box(Storage-Box): 데이터 저장 위치(추상적 개념)
E-box(Execution-Box): 연산이 이루어지는 위치(추상적 개념)

![](https://i.imgur.com/gnpq6MI.png)

여러 프로세스 중에서 누가 먼저 데이터를 읽어와서 연산 후 저장하느냐가 Synchronization 문제

### Race Condition

경쟁 상태.
`여러 Process들`이 `동시`에 `공유 data`를 접근하는 상황  
연산 결과가 마지막 data 다룬 Process에 의해 달라짐.

- 하나의 S-Box를 여러 E-Box가 공유한다고 할 때 발생하는 문제
  ![](https://i.imgur.com/h8DW893.png)
- 더하는 연산, 빼는 연산이 이루어지는데, 이전 계산을 기억하지 못해서 원래 의도한 값과 다른 값이 저장되는 경우 발생
- 한 주체가 읽어가는동안 다른 주체가 읽어가면 문제가 발생. 조율할 필요가 있다.
- 각 E-Box가 사용하는 S-Box가 달라서 문제가 발생하지 않을 것 같지만 실제로
  1. `Multiprocessor system`인 경우 문제가 발생할 수 있다.
  2. 공유 메모리를 사용하는 경우 역시

### OS에서의 Race Condition

- OS와 관련되어 생기는 문제가 더 중요.

ex) 보통 Process는 자신의 메모리만 접근하기 때문에 문제가 발생할 일이 별로 없지만, OS에 요청해야 하는 부분은 시스템 콜을 통해 커널 코드를 실행한다. 이 경우 커널 데이터에 접근하는데, 다시 CPU 제어권이 빼앗기게 되는 경우 Race condition 발생 가능. 인터럽트 역시 같은 문제 발생 가능.

1. kernel 수행 중 interrupt 발생시  
   load 일어난 이후 값이 바뀌고 다시 회수하면 load 때의 data를 그대로 사용하기 때문에 의도한 값과 달라지는 문제가 발생한다.  
   => 해결책: 보통 load -> execution -> store 사이에 interrupt가 들어와도 무시하고 다 처리 후 interrupt 수행하는 방식으로 해결. 결국 순서를 정해주면 된다.
2. Process가 System call을 해 kernel 모드 수행 중인데 context switch 일어나는 경우  
   1번과 비슷한 상황. kernel 모드에서 수행 중 interrupt로 넘겨주면 비슷한 문제 발생  
   => 해결책: kernel 모드 수행 중일 경우 CPU를 preempt하지 않는다. 사용자 모드로 돌아갈 때 preempt
3. Multiprocessor에서 shared memory 내 kernel data  
   1, 2의 방법으로는 해결되지 않는다. 즉, interrupt를 막는다고 해결되는 문제가 아니다. CPU 간 읽고 있는 것이기 때문에 interrupt와 무관.
   => 해결책: data에 접근할 때 lock을 걸어야 한다. 수정 전 lock을 걸어서 누구도 접근할 수 없도록 해놓고, 저장(store)가 끝나야 lock을 풀어 다른 CPU가 접근하도록 허용.  
   하나의 OS를 여러 CPU가 접근하기 때문에 발생하는 문제. 따라서 1. 한 번에 하나의 CPU만 커널에 접근하도록(kernel 전체를 하나의 lock, 비효율적) 하거나 2. 각각의 data 별로 lock을 걸고 푸는 방식으로 해결.

### Process Synchronization 문제

- 공유 data에 동시 접근(concurrent access)하려는 것에서 data 불일치 문제 발생 가능
- 일관성(inconsistency) 유지를 위해 Process간 실행 순서 정하는 메커니즘 필요
- race condition을 막기 위해 동시 접근 Process간 동기화가 되어야 한다.

- Example of a Race Condition

![](https://i.imgur.com/cMOjvUG.png)

context switch가 일어난다고 무조건 경쟁 상태 문제가 발생하는 것은 아니고 그림과 같이 Shared Data가 있을 경우. Process들은 자신만의 주소 공간이 있다.

- The Critical-Section Problem(임계구역)
  - Critical-Section: 공유 data를 접근하는 `code`  
    ![](https://i.imgur.com/VAoiSH9.png)
  - Critical-Section을 이용. 한 Process가 Critical Section에 들어가 있으면(= 공유 data를 접근하는 code를 실행 중이면) 다른 Process에 CPU가 넘어가더라도(context switch) 해당 Process의 Critical Section(= 공유 data에 접근하는 code)에는 들어가지 못하게 하는 것

### 프로그램적 해결법의 충족조건

Mutual Exclusion, Progress, Bounded Waiting 등

![](https://i.imgur.com/UjMKVOh.png)

- Initial Attempts to Solve Problem  
  ![](https://i.imgur.com/n3tJiRP.png)
  Critical Section이거나 아니거나로 나눠서 해결. 공유 data 접근하는 code 앞뒤로 code를 추가해서 다른 코드가 접근하지 못하게 해준다.

## [프로세스 동기화 #2](https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f)

> 추상 자료형?  
> Object + Operation으로 구성  
> ex) 정수 숫자 추상 자료형: 정수 숫자 + 연산자(+, -, /, \*...)  
> 논리적 정의. 어떻게 구현되는가는 별개의 자료형.

### Semaphores

- 추상 자료형

Semaphore `S`

Object(integer variable) + Operation(P, V)

왜 Semaphore를 사용?

1. lock을 걸고, lock을 풀기 위해 사용.
2. 공유 자원을 획득하고 반납하는 것을 Semaphore가 처리.

- P(S): Semaphore 변수 값(공유 data)을 획득하는 과정. -> P 연산 후 S--로 1 감소. S<=0인 경우 while문 돌면서 접근 불가, 기다림.
- V(S): 다 사용 후 반납하는 과정. S++

Semaphore가 갖는 정수 변수는 `자원의 개수`. S = 5: 자원이 5개.

lock을 걸고 푸는 과정은 S=1인 경우 생각.

atomic 연산.

busy-wait 문제(=spin lock)는 발생

### Critical Section of n Processes

![](https://i.imgur.com/EYBxbKY.png)

- n이 1일 경우, critical section 앞뒤로 P(mutex), V(mutex) 위치. 자연스럽게 임계영역 문제 해결. P, V 구현은 시스템별로 알아서
- busy-wait는 효율적이지 못함. Block&Wakeup 방식 구현도 가능(=sleep lock)

> Block & Wakeup  
> 계속 while문 돌고 있는 것이 아니라 Process 자체를 Blocked 시켜서 CPU 아예 반납, 잠들어있다가 반납하면 깨워서 Ready Queue로 넣어준다.(Process 상태 이야기)

### Block / Wakeup Implementation

![](https://i.imgur.com/qJjRLXF.png)
Semaphore 변수 값 + 잠들어 있는 Process들을 연결하기 위한 Queue `L`

- Semaphore를 획득할 수 없으면 -> Process block 시킴.
- Semephore 반납하면 -> Blocked Process 중 하나를 Ready로wakeup
- 획득 못한 Process들은 L에다가 PCB를 계속 연결해놓는 것

![](https://i.imgur.com/tfuhTRw.png)
Semaphore를 기다리는 Queue

- 구현
  ![](https://i.imgur.com/UP9N85H.png)
  P => 자원 획득, S.value < 0으로 여분이 없다면 block()
  V => 자원 반납, S.value <= 0으로 자는 Process 있다면 wakeup(P from S.L). (P에서 일단 S.value를 빼고 잠들었기 때문에 S.value <= 0일 때이다.)

#### Which is better?

Busy-wait vs. Block/Wakeup

보통은 Block/Wakeup이 좋을 수 있다. 하지만 Overhead가 발생(Process의 상태 바꾸는 작업).

따라서 Critical Section 길이가 매우 짧은 경우 -> busy-wait overhead가 Block/Wakeup overhead보다 효율적일 수 있음.
Critical Section이 긴 경우 -> 오래 기다려야 하므로 Block/Wakeup이 적당

### Two Types of Semaphores

1. Counting semaphore
   - 0, 1만이 아니라 0 이상의 임의의 정수 값
   - resource counting에 사용
2. Binary semaphore(=mutex)
   - 0 or 1만 가질 수 있는 semaphore
   - 주로 mutex에 사용

> 엄밀히는 binary semaphore와 mutex가 다른 것으로 알고 있음.  
> [참고 1](https://www.geeksforgeeks.org/difference-between-binary-semaphore-and-mutex/)  
> [참고 2](https://blog.seongjun.kr/26-difference-between-binary-semaphore-and-mutex/)

### Deadlock and Starvation

Semaphore는 원치 않는 문제 발생 가능.

- Deadlock  
  예시  
  Semaphore S, Semaphore Q를 동시에 모두 획득해야 일을 할 수 있을 경우 S와 Q가 1로 초기화 되어있다. Process 1이 S 갖고 있고, Process 2가 Q 갖고 있을 경우. 각자 서로의 것을 원하고만 있다. 각자 충족을 못해서 일을 못해서 영원히 대기하는 상태  
  코드 내부적으로는 문제가 없어보인다. 하지만 실제로는 문제 발생 가능. 예시의 경우는 획득 순서가 일정하도록(S 다음 Q 획득과 같이) 해서 해결 가능.

- Starvation
  특정 Process가 자원을 얻지 못하고 무한히 기다리는 경우. deadlock도 starvation으로 볼 수도 있겠지만, 특정 Process끼리만 공유하면서 특정 Process만 배제하는 경우를 이야기.

Dining-Philosophers Problem

## [프로세스 동기화 #3](https://core.ewha.ac.kr/publicview/C0101020140408134626290222?vmode=f)

### Classical Problems of Syncronization

- Bounded-Buffer Problem (Producer-Consumer Problem)

  - Buffer의 크기가 유한한 환경에서, 생산자-소비자 문제
  - 2가지의 Process가 있다. 생산자(Producer), 소비자(Consumer) Process. 각각 하나씩만 있는게 아니고 생산자 여러개, 소비자 여러개
    - 생산자: 공유 buffer에 data를 만들어서 집어 넣는 역할
    - 소비자: 공유 buffer에서 data를 꺼내가는 역할
  - Synchronization 관련 문제?
    - 생산자가 동시에 도착해서 비어 있는 buffer에 둘 다 data를 집어 넣는 문제 => 공유 buffer에 lock 걸어서 다른 Process 접근 막음으로 해결
    - 소비자 동시 도착 문제 => 역시 lock으로 해결
    - 각각 다 pointer를 기억해서 진행
    - 공유 buffer가 다 찼는데 생산자 접근 경우 -> 소비자가 접근해서 사용해야 접근 가능. 그 전까지 대기
    - 소비자 입장 역시 마찬가지
  - Semaphore가 2가지로 필요
    1. 생산자, 소비자가 접근 -> 공유 buffer 하나당 사용시. 배타적으로 접근하도록 :: mutex -> lock을 걸고 푸는 용도
    2. buffer가 가득 차거나 비었을 때, 생산자, 소비자가 buffer를 사용 불가, 가능 판단하도록 :: counting semaphore -> 자원 개수 counting 용도
  - Producer 순서
    1. Empty buffer 존재? 없으면 대기
    2. 공유 data(buffer 전체)에 lock => 다른 Process 접근 막기
    3. Empty buffer에 데이터 입력 -> buffer 조작(empty buffer pointer 위치)
    4. lock 풀기
    5. 내용 개수(Full buffer) 1 증가
  - Consumer 순서(= Producer 반대)
    1. Full buffer 존재? 없으면 대기
    2. 공유 data(buffer 전체)에 lock => 다른 Process 접근 막기
    3. Full buffer에서 데이터 꺼냄 -> buffer 조작(full buffer pointer 위치)
    4. lock 풀기
    5. 빈 내용 개수(Empty buffer) 1 증가

  ![](https://i.imgur.com/wtrHgZC.png)

  공유 data: buffer(자체 및 조작 변수)  
  Synchronization 변수: mutual exclusion, resourcecount

- Readers-Writers Problem

  - 읽는 Process, 쓰는 Process 2가지가 여러개씩
  - Write는 동시에 여럿이 하면 안되지만, Read는 동시에 여럿이 해도 문제가 되지 않는다.
  - Write하는동안은 lock(db), Read도 lock(mutex)은 걸되 같이 읽을 수 있도록
  - readcount를 통해서 Read 관리

  ![](https://i.imgur.com/yW1UGXj.png)
  Write는 단순.  
  Read에서는 최초의 Reader라면(readcount++ 이후 1이면) P(db)로 Wirter를 Block하고, 그 이후로 들어오는 Reader들은 db를 읽기만 하면 된다. 여기에서 mutex는 readcount 개수를 세기 위한 semaphore다. -> 나가면서 V(mutex)로 readcount--해주고서 readcount=0이라면 마지막 Reader이므로 V(db)로 lock 해제

  공유 data: DB 자체, readcount(db 접근 중인 reader 개수)  
  Synchronization: mutex, db

  문제점: Write보다 늦게 들어온 Read가 먼저 DB 접근 가능하므로 Write에서 Starvation 발생 가능 => 우선 순위 Queue로 해결할 수 있음  
  Read를 동시에 할 수 있다는 것이 중요

- Dining-Philosophers Problem  
  ![](https://i.imgur.com/ZQBcRcS.png)
  - 생각하는 일, 밥 먹는 일 2가지
  - 젓가락이 한개씩 있으므로 양쪽 젓가락을 잡아야 식사 가능
  - Deadlock의 가능성: 모두가 왼쪽 젓가락을 잡으면 모두가 영원히 식사를 못할 수 있다.
    1. 밥을 먹을 때만 식탁에 앉는다. 4명만 동시에 앉을 수 있도록
    2. 젓가락 양쪽을 모두 집을 수 있을 때만 앉을 수 있도록
    3. 비대칭 - 짝수(홀수) 철학자는 왼쪽(오른쪽)부터 집도록
  - Semaphore에서의 문제를 간단히 해결할 수 있는 Monitor 코드. Monitor에서의 코드를 기계적으로 Semaphore 형식으로 바꾼 것이기 때문에 읽기에 이상할 수 있음
    ![](https://i.imgur.com/K1738Jp.png)  
    self[i]는 젓가락 2개를 다 잡을 수 있다(1) 없다(0)
  - 예시 설명 영상 필요하면 다시 보기 45분부터

Semaphore는 P, V 연산을 통해 프로그래머로 하여금 코딩을 비교적 쉽게 해주긴 했지만, 문제가 생겼을 때 버그 잡기가 쉽지 않다. -> 한 번의 실수가 전 시스템에 치명적 영향.

- V -> Critical Section -> P: Mutual Exclusion 깨짐  
  P -> Critical Section -> P: Deadlock

이런 문제 해결을 위해 추상 데이터 타입을 제공하는 것이 `monitor`. high-level synchronization construct  
lock을 프로그래머가 직접 걸 필요가 없다. monitor가 알아서 동시 접근 못하도록 관리.  
monitor 내부적으로 procedure를 통해서 공유 자원 접근.

semaphore에서의 resource counting은 여전히 필요. -> 역시 monitor의 condition variable로 관리.  
공유 buffer가 monitor 내부. 즉, monitor가 알아서 관리.

## [프로세스 동기화 #4](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)

Process Synchronization(프로세스 동기화) a.k.a Concurrency Control(병행 제어)

프로그래머 관점에서 이를 관리위해 semaphore 사용(P, V 연산)

3가지 고전 문제

- 생산자, 소비자 문제
- Writer, Reader 문제
- 식사하는 철학자 문제

-> Semephore가 어떻게 해결하는가?

### Monitor

- 프로그래밍 언어 차원에서 공유 자원에 접근하는 문제를 자동으로 해결해 줌.
- 프로그래머 부담을 매우 경감
- monitor 내부에 공유 data, 접근 code 존재. 프로그래머가 공유 data에 접근하고자 한다면 내부 접근 코드만을 이용해서 접근 가능.
- 애초에 active한 Process가 하나만 monitor 내부 코드를 실행할 수 있게 monitor가 제어해주는 방식
- active한 Process가 있다면 새로 들어오는 Process는 외부 Entry Queue에서 대기.(active가 다 사용하고 나가던지, block 되어 잠들던지)
- lock을 걸고 풀 필요가 없다. monitor가 알아서. 훨씬 프로그래머가 편함

![](https://i.imgur.com/oAhiXl6.png)

이렇게 객체 지향적인 자료 구조로 정의하기도

- condition variable
  semaphore처럼 개수를 세거나 값을 가지는 변수는 아니다.  
  Process를 잠들게하고, 줄 세우기 위한 변수.
  - 연산 2가지가 이루어짐
    1. wait(): 특정 Process를 잠들게 하겠다.(-> suspend)
    2. signal(): 해당 Process를 깨우겠다.(suspend 없으면 nothing happened)
  -
- 생산자 소비자 문제를 monitor로 해결

![](https://i.imgur.com/d4LgtPf.png)

monitor에서는 직접 lock 걸고 풀 일이 없다.  
full, empty 각각은 내용 들어있는 buffer, 비어있는 buffer를 기다리며 잠들게하는 역할.  
produce: 빈 buffer가 없다면 대기, 빈 buffer 생기면 empty에 추가, full 깨우는 signal 호출  
consume: 내용 buffer가 없다면 대기, 내용 buffer 생기면 empty 내용 비우면서 가져오고, empty 깨우는 signal 호출

lock 거는 코드 없고, count 해주는 코드는 wait, signal로 처리

semaphore는 값을 가지고 연산해서 관리 vs monitor는 값이 없이 체크해서 줄세워 관리

wait, signal은 값을 바꾸는 함수가 아니라, 그냥 재우고 깨우라는 함수이기 때문에 signal에서 잠든게 없다면 `아무것도 일어나지 않는다`. 값을 바꾸지도 않으므로

사실 둘은 목적부터가 다르다.  
monitor: 동시 접근 막는 것을 monitor 차원에서 지원  
semaphore: 자원 획득을 위해 프로그래머가 알아서 P, V 해주는 것

코드는 비슷해진다.

- Dining-Philosopher Problem 해결

![](https://i.imgur.com/1bCc3Ua.png)
