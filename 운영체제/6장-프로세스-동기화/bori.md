# JDC-230125

# [Chapter 6. 프로세스 동기화 Part-1](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

## 데이터의 접근

![](https://i.imgur.com/ZiMEcD4.png)

예시)
| 구분 | E-box | S-box |
| -------- | -------- | -------- |
| 1 | CPU | Memory |
| 2 | 컴퓨터 내부 | 디스크 |
| 3 | 프로세스 | 그 프로세스의 주소 공간 |

##### Storage-box(S-box), Execution-box(E-box)는 강의에서 임의로 붙인 이름이므로 유의하기

1. 데이터가 저장되어 있는 공간에서 데이터를 읽어옴
2. 연산할 데이터를 연산하는 공간으로 가져와서 연산을 수행
3. 연산 결과를 다시 데이터가 저장되어 있는 공간으로 이동

프로세스(데이터) 동기화가 필요한 이유?

- 데이터를 읽기만 하는 것은 어떤 프로세스가 읽든 어떤 순서로 읽든 문제가 되지 않는다.
- 데이터를 연산하고 수정한 결과를 저장(반영)하는 방식에서는 어떤 프로세스가 먼저 읽어갔느냐에 따라 결과가 달라진다.
  ⇒ 이 때 Synchronization 문제가 발생할 수 있다.

## Race Condition(경쟁 상태)

- 여러 주체(프로세스)가 하나의 데이터에 동시에 접근하려고 할 때 Race Condition이라고 부른다.
- Race Condition이 발생하는 환경
  - 멀티 프로세서 시스템
  - 공유 메모리를 사용하는 프로세스들
  - 커널 내부 데이터를 접근하는 루틴들 간(예: 커널모드 수행 중 인터럽트로 커널모드 다른 루틴 수행)

## 운영체제에서 Race Condition이 발생하는 상황

### 1. 커널 수행 중 인터럽트 발생 시

![](https://i.imgur.com/iPweMmx.png)

- 커널 모드 실행 중 인터럽트가 발생하여 인터럽트 처리루틴이 실행되는 경우
  - 양 쪽 다 커널 코드이므로 커널 주소 공간을 공유
- 해결 방법 : 커널 모드 실행 중 중요한 값에 접근하는 동안에는 인터럽트가 들어와도 해당 작업이 끝날 때까지 인터럽트 처리가 되지 않도록 한다.

### 2. 프로세스가 시스템 콜을 하여 커널 모드로 수행 중인데 문맥 교환이 일어나는 경우

![](https://i.imgur.com/QB3fwyo.png)

![](https://i.imgur.com/ueDihUZ.png)

- 어떤 프로세스가 실행할 때 본인의 코드만 실행하는 것이 아니라 시스템 콜을 통해 운영체제에게 서비스를 요청하는 경우가 발생한다.
  - 두 프로세스의 주소 공간 간에는 공유된 데이터가 없다.
  - 하지만 시스템 콜을 하는 동안에는 커널 주소 공간의 데이터에 접근(공유)할 수 있게 된다.
  - 이 작업 중간에 CPU를 점유(preempt)해가면서 Race Condition 발생
- 해결 방법 : 커널 모드에서 수행 중일 때는 CPU를 점유하지 않고, 커널 모드에서 사용자 모드로 돌아갈 때 CPU를 점유하여 해결
  ⇒ 시분할 시스템은 실시간 시스템이 아니므로 데드라인이 없어 이와 같은 문제는 쉽게 해결할 수 있다.

### 3. 멀티 프로세스 환경에서 공유된 메모리 내의 커널 데이터

![](https://i.imgur.com/ft47HlC.png)

- 멀티 프로세스 환경에서 여러 개의 CPU가 동시에 커널 내부의 공유 데이터에 접근할 경우 발생
  - 멀티 프로세서의 경우 인터럽트를 enable/disable 하는 것으로 해결할 수 없다.

#### 해결방법

1. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터를 lock/unlock 하는 방법
2. 한 번에 하나의 CPU만 커널에 접근할 수 있도록 하는 방법

- 커널 전체를 하나의 lock으로 막아 다른 CPU가 접근하지 못하도록 하고 커널을 빠져나올 때 unlock 하는 방법
- CPU를 여러 개 사용해도 커널에 접근할 수 있는 CPU는 하나이므로 비효율적이다.

## Process Synchronization 문제

- 공유 데이터의 동시 접근(concurrent access)은 데이터의 불일치(inconsistency) 문제를 발생시킬 수 있다.
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘이 필요하다.

#### Race condition

- 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- 이를 막기 위해 병행 프로세스(concurrent process)는 동기화 되어야 한다.

## The Critical-Section Problem

![](https://i.imgur.com/EDkfmya.png)

- n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재
- Critical-Section(임계구역) : 어떤 프로세스가 공유 자원에 접근하고 있는 동안 그 프로세스는 임계구역에 있다고 함. 공유 데이터를 접근하는 코드 자체를 critical section 이라고 한다.
- 하나의 프로세스가 임계구역에 있을 때 다른 모든 프로세스는 임계구역에 들어갈 수 없어야 한다.

# [Chapter 6. 프로세스 동기화 Part-2](https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f)

## Initial Attempts to Solve Problem

- 두 개의 프로세스가 있다고 가정 : P<sub>0</sub>, P<sub>1</sub>
- 프로세스들의 일반적인 구조
  ```clike
  do {
    entry section
    critical section
    exit section
    remainder section
  } while (1);
  ```
  - `critical section` : 임계 구역, 공유 데이터에 접근하는 코드
  - `remainder section` : 나머지 구역, 임계 구역 외의 나머지 코드 부분들을 총칭
  - `entry section` : 입장 구역, 임계 구역에 진입을 요청하는 코드
    ⇒ 여러 프로세스가 동시에 접근하는 것을 막기 위해 **lock**을 건다.
  - `exit section` : 퇴장 구역, 임계 구역 이후 임계 구역을 빠져나왔음을 알리는 구역
    ⇒ **unlock**하여 다른 프로세스가 임계 구역에 진입할 수 있도록 한다.
- 프로세스들은 수행의 동기화를 위해 몇몇 변수를 공유할 수 있다.
  ⇒ Synchronization variable(공유 변수)

## 임계 구역 문제를 해결하기 위한 조건

### Mutual Exclusion(상호 배제)

- 하나의 프로세스가 임계 구역을 수행 중이면 다른 프로세스들은 임계 구역에 진입할 수 없다.

### Progress(진행)

- 어떠한 프로세스도 임계 구역에 있지 않은 상태에서 임계 구역에 들어가고자 하는 프로세스가 있다면 임계 구역에 들어가게 해주어야 한다.

### Bounded Waiting(유한 대기, 한정 대기)

- 프로세스가 임계 구역에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 임계 구역에 들어가는 횟수에 한계가 있어야 한다.
- 특정 프로세스가 임계 구역에 들어가지 못하고 대기 시간이 길어지면서 기아 현상이 발생하는 것을 방지하기 위함

> 가정
>
> - 모든 프로세스의 수행 속도는 0보다 크다.
> - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

## 임계 구역 문제 해결을 위한 알고리즘

### Algorithm 1

- Synchronization variable
  - `turn` : 임계 구역에 진입할 수 있는 프로세스의 차례를 나타내는 변수
  - `turn == i` 일 경우, P<sub>i</sub>가 임계 구역에 진입할 수 있다.
  ```clike
  int turn;
  initially turn = 0;
  ```
- Process P<sub>0</sub>
  ```clike
  do {
    while (turn != 0);
    critial section
    turn = 1;
    remainder section
  } while (1);
  ```
  - `turn`이 0이 아닐 경우, `while`문을 돌면서 자신의 차례를 기다린다.
  - `turn`이 0이 되면 임계 구역에 들어갔다가 나온 후 `turn`을 1로 변경하여 P<sub>1</sub>에게 차례를 넘겨준다.
- 문제점
  - 임계 구역에는 교대로 진입할 수 있는 코드로 반드시 한 번씩 교대로 들어가야 한다.(swap-turn)
    ⇒ 상대방이 `turn`을 바꿔주어야 임계 구역에 진입할 수 있다.
  - P<sub>0</sub>는 임계 구역에 빈번히 진입하길 원하는 상태이고, P<sub>1</sub>은 한 번만 진입하고 더이상 임계 구역에 진입하지 않는 경우 P<sub>0</sub>도 영원히 임계 구역에 들어갈 수 없는 상황이 되어버린다.

⇒ Mutual Exclusion은 만족하지만 Progress 조건은 만족하지 못하는 알고리즘 = **과잉양보**

### Algorithm 2

- Synchronization variable
  - flag : flag는 프로세스가 임계 구역에 들어가고자 하는 의사표시로 각 프로세스가 자신의 flag를 가진다.
  - `flag[i] == true`일 경우, P<sub>i</sub>가 임계 구역에 진입할 준비가 되어 있음을 의미
  ```clike
  boolean flag[2];
  initially flag[모두] = false;
  ```
- Process P<sub>i</sub>
  ```clike
  do {
    flag[i] = true;
    while (flag[j]);
    critial section
    flag[i] = flase;
    remainder section
  } while (1);
  ```
  - `flag[i] == true;` : P<sub>i</sub>가 임계 구역에 진입하기를 원한다는 의사 표시
  - `while (flag[j]);` : P<sub>j</sub>가 임계 구역에 있는지 확인. 만약 임계 구역에 있는 경우 빠져나올 때까지 P<sub>i</sub>는 대기
  - `flag[i] == flase;` : P<sub>i</sub>가 임계 구역에서 나왔음을 표시하여 다른 프로세스가 진입할 수 있도록 한다.
- 문제점
  - P<sub>i</sub>가 실행 중 flag를 `true`로 변경한 상태에서 문맥 교환이 발생하여 CPU가 P<sub>j</sub>에게 넘어간 상황일 때, 이 때 P<sub>j</sub>도 flag를 `true`로 변경한다면 두 프로세스 모두 flag만 든 상태이고 임계 구역에는 진입하지 못한 상태이다.
  - P<sub>j</sub>가 while문에 들어가보니 P<sub>i</sub>의 flag가 true인 상태이므로 임계 구역에 진입하지 못하고 대기하게 된다.
  - P<sub>j</sub>는 대기를 하다가 CPU가 다시 P<sub>i</sub>에게 넘어가고 P<sub>i</sub> 또한 while문에서 P<sub>j</sub>의 flag가 true인 것을 확인하고 임계 구역에 들어가지 못하고 대기하게 된다.
  - 결국 flag를 false로 변경하는 것은 임계 구역에 들어갔다가 나올 때 변경하는 것인데 어떤 프로세스도 임계 구역에 진입하지 못하므로 두 프로세스 모두 flag만 `true`로 표시하고 **어떤 프로세스도 임계 구역에 진입하지 못하는 경우가 발생한다.**

⇒ Mutual Exclusion은 만족하지만 Progress 조건은 만족하지 못하는 알고리즘

## Peterson's Algorithm

- Synchronization variable
  - 위의 두 알고리즘에서 사용했던 `flag`, `trun`을 같이 사용
- Process P<sub>i</sub>
  ```clike
  do {
    flag[i] = true;
    turn = j;
    while (flag[j] && turn == j);
    critial section
    flag[i] = flase;
    remainder section
  } while (1);
  ```
  - `flag[i] == true;` : P<sub>i</sub>가 임계 구역에 들어가고자 하는 의사 표시를 함
  - `turn = j;` : `turn`을 P<sub>j</sub>로 변경한다.
  - `while (flag[j] && turn == j);` : P<sub>j</sub>가 임계 구역에 들어가고자 하는 의사 표시를 하고, P<sub>j</sub>의 차례인 경우 P<sub>i</sub>는 대기한다. 그 외의 경우에는 임계 구역에 진입할 수 있다.
  - 이 후 임계 구역에서 나왔음을 표시한다.

⇒ 임계 구역 문제 해결 조건 3가지를 모두 만족

- 문제점
  - Busy Waiting(Spin lock) : 대기할 때 할당된 CPU와 메모리를 사용하면서 기다리므로 자원을 낭비하게 된다.

고급언어가 여러 개의 CPU 인스트럭션으로 구성되고 인스트럭션 하나하나가 실행되는 도중에 CPU를 빼앗길 수 있기 때문에 소프트웨어적으로 복잡한 코드가 완성된 것이다.

## Synchronization Hardware

- 하드웨어적으로 Test & Modify를 atomic하게 수행할 수 있도록 지원하는 경우 앞의 문제는 간단히 해결할 수 있다.
  ⇒ **데이터를 읽는 것과 쓰는 것을 하나의 인스트럭션으로 처리할 수 없기 때문에 임계 구역 문제가 발생하는 것**이다. 만약 데이터를 읽는 것과 쓰는 것을 하나의 인스트럭션으로 수행할 수 있다면, 하나의 인스트럭션을 수행하는 동안에는 CPU를 빼앗기지 않으므로 간단하게 문제 해결이 가능하다
  - `Test_and_Set` 인스트럭션 : 원래의 값을 읽어내고, 그 값을 1로 설정하는 과정을 하나의 인스트럭션으로 처리
    ![](https://i.imgur.com/Pd5spyy.png)

### Mutual Exclusion with Test & Set

- Synchronization variable
  ```clike
  boolean lock;
  initially lock = false;
  ```
- Process P<sub>i</sub>
  ```clike
  do {
    while (Test_and_Set(lock));
    critial section
    lock = flase;
    remainder section
  } while (1);
  ```
  - `lock = 0`이면 `Test_and_Set`을 통해 lock을 읽고, 그 값을 1로 바꾸며 임계 구역에 들어가게 된다.
  - `lock = 1`이면, while문을 돌면서 기다린다.

## Semaphores

일종의 추상 자료형으로 앞의 방식들을 추상화 시킴

> [추상 자료형](https://ko.wikipedia.org/wiki/%EC%B6%94%EC%83%81_%EC%9E%90%EB%A3%8C%ED%98%95)
> 추상적 자료형(abstract data type, ADT)은 컴퓨터 과학에서 자료들과 그 자료들에 대한 연산들을 명기한 것이다. 추상적 자료형은 구현 방법을 명시하고 있지 않다는 점에서 자료 구조와 다르다.
> 예) 복소수, 리스트, 스택, 큐, 맵, 우선순위 큐, 집합 등

#### 세마포어 변수(S)

- 정수형 변수(integer variable)로 자원의 갯수를 나타낸다.
- P연산과 V연산으로 이루어짐 ⇒ 이 두 가지 atomic 연산에 의해서만 세마포어 변수에 접근 가능
  - **P연산** : 세마포어 변수 값(공유 데이터)을 획득하는 과정 = lock을 거는 과정
    자원(S)이 없으면 while문을 돌면서 기다리고, 자원이 있으면 하나를 가져가는 것
  ```clike
  P(S):
  while (S ≤ 0) do wait;
  S--;
  ```
  - **V연산** : 공유 데이터를 사용한 후 반납하는 과정 = lock을 푸는 과정
  ```clike
  V(S):
  S++;
  ```
- 상호 배제의 원리를 보장하는 알고리즘
- 임계구역에 대하여 각각의 프로세스들의 접근을 제어하고 프로세스 사이의 동기를 유지

## Critical Section of n Processes

- Synchronization variable
  ```clike
  semaphore mutex;
  initial mutex = 1; // 1개의 프로세스만 임계 구역에 들어갈 수 있다.
  ```
- Process P<sub>i</sub>
  ```clike
  do {
    P(mutex);
    critial section
    V(mutex);
    remainder section
  } while (1);
  ```
  - 임계 구역에 들어할 때 P연산 수행
  - 임계 구역에서 빠져나올 때 V연산 수행
- 이 방법도 특정 프로세스가 임계 구역에 들어가 있으면 P연산에서 while문을 돌면서 할당된 CPU를 사용하게 되는 Busy-wait(Spin lock)를 하게 된다.

## Block / Wakeup Implementation

#### Semaphore를 다음과 같이 정의

- `int value` : 실제 세마포어 변수 값
- `struct process *L` : 세마포어 때문에 잠들어 있는 프로세스들을 연결하기 위한 큐(process wait queue)

```clike
typedef struct
{
  int value;
  struct process *L;
} semaphore;
```

#### Block과 Wackup을 다음과 같이 가정

- Bock
  - 커널은 block을 호출한 프로세스를 suspend 시킴
  - 이 프로세스의 PCB를 세마포어에 대한 wait 큐에 넣음
- Wakeup(P)
  - block된 프로세스 P를 wakeup 시킴
  - 이 프로세스의 PCB를 ready 큐로 옮김

![](https://i.imgur.com/dYcHghh.png)

#### Semaphore 연산을 다음과 같이 정의

- **P연산**
  - 세마포어의 획득하기 위해 먼저 `S.value`의 값을 1 감소시킨다.
  - `S.value`의 값이 음수인 경우(자원이 없다면), 해당 프로세스는 S.L에 넣어 block 상태가 된다.
  ```clike
  P(S):
  S.value--; /* prepare to enter */
  if(S.value < 0)
  {
    add this process to S.L;
    block();
  }
  ```
- **V연산**
  - 자원을 쓰고 있던 프로세스가 자원을 쓰고 나면 `S.value`를 1 증가시킨다.
  - 자원을 내놓았는데 `S.value` 값이 0 이하인 경우, P연산을 할 때 `S.value`값을 먼저 감소시킨 프로세스가 자원을 기다리고 있다는 의미이므로 잠들어 있는 프로세스를 깨워준다.
  - `S.value` 값이 양수라면, 프로세스가 P연산을 할 때 자원을 획득하여 이미 사용하고 있다는 것이므로 기다리고 있는 프로세스가 없다는 것을 의미한다.
  ```clike
  V(S):
  S.value++;
  if(S.value <= 0)
  {
    remove a process P from S.L;
    wakeup(P);
  }
  ```
- `S.value`의 의미
  - 자원의 개수를 의미하는 것이 아니다.
  - `S.value` 값이 양수인지 음수인지에 따라 프로세스의 상황(깨워야하는 프로세스가 있는지 없는지)을 나타낸다.

## Busy-wait vs Block/wakeup

- Block/wakeup이 더 효율적이다.
  ⇒ 불필요한 CPU 낭비가 없어 CPU의 의미있는 이용률이 높아진다.

#### Block/wakeup overhead

- 프로세스의 상태를 바꾸는 것이므로 Block/wakeup도 오버헤드가 존재한다.

#### Block/wakeup overhead vs Critical section 길이

- 일반적으로는 Block/wakeup 방식이 더 효과적이다.
- Critical section 길이가 긴 경우, Block/wakeup이 적당
- Critical section 길이가 짧은 경우, Block/wakeup의 오버헤드가 Busy-wait의 오버헤드보다 커질 수 있다.

## Two Types of Semaphores

#### Binary semaphore(= mutex)

- 자원의 개수가 하나인 경우, 한 개의 공유 자원을 상호 배제(mutual exclusion)함
- 0 또는 1 값만 가질 수 있는 세마포어

#### Counting semaphore

- 자원이 여러 개여서 여분의 자원을 가지고 있는 경우, 여러 개의 공유 자원을 상호 배제함
- 도메인이 0 이상인 임의의 정수값
- 주로 resource counting에 사용

## Deadlock and Starvation

### Deadlock(교착상태)

- 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 이벤트를 무한히 기다리는 현상
  ⇒ 두 개 이상의 프로세스가 자원을 점유한 상태에서 서로 다른 프로세스가 점유하고 있는 자원을 요구하며, 서로의 작업을 끝나기만을 기다리며 둘 다 영원히 끝나지 않는 상황
  ![](https://i.imgur.com/hgeXzy2.png)
- 해결 방법 : 자원을 획득하는 순서를 동일하게 맞춰준다.
  ![](https://i.imgur.com/IzB1cxF.png)

### Starvation

- 특정 프로세스가 CPU를 할당받지 못하고 영원히 기다리게 되는 현상
- Indefinite blocking : 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상

# [Chapter 6. 프로세스 동기화 Part-3](https://core.ewha.ac.kr/publicview/C0101020140408134626290222?vmode=f)

# [Chapter 6. 프로세스 동기화 Part-4](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)

## Classical Problems of Syncronization

- Bounded-Buffer Problem (Producer-Consumer Problem)
- Readers-Writers Problem
- Dining-Philosophers Problem

## Monitor

-
