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

## Classical Problems of Syncronization

- Bounded-Buffer Problem(Producer-Consumer Problem)
- Readers-Writers Problem
- Dining-Philosophers Problem

### Bounded-Buffer Problem(Producer-Consumer Problem)

![](https://i.imgur.com/yypPdu3.png)

- \*버퍼의 크기가 유한한 환경에서 여러 개의 생산자(Producer) 프로세스와 소비자(Consumer) 프로세스의 문제

##### \*버퍼(buffer) : 데이터를 한 곳에서 다른 한 곳으로 전송하는 동안 일시적으로 그 데이터를 보관하는 메모리의 영역

- 생산자 프로세스

  - 비어있는(empty) 버퍼 확인(없으면 기다림)
  - 공유 데이터에 lock을 건다.
  - 비어있는 버퍼에 데이터를 입력 및 버퍼 조작
  - lock을 푼다.
    ⇒ Full buffer 하나 증가
    ⇒ 비어있는 버퍼가 생산자 프로세스의 자원

- 소비자 프로세스
  - 채워진(full) 버퍼 확인(없으면 기다림)
  - 공유 데이터에 lock을 건다.
  - 채워진 버퍼에서 데이터를 꺼내고 버퍼 조작
  - lock을 푼다.
    ⇒ Empty buffer 하나 증가
    ⇒ 채워진 버퍼가 소비자 프로세스의 자원

#### 공유 데이터

- 버퍼 자체 및 버퍼 조작 변수(empty/full 버퍼의 시작 위치)

#### 발생할 수 있는 동기화 문제점

- 여러 프로세스가 하나의 공유 데이터(버퍼)에 접근하는 경우 발생
  - 둘 이상의 생산자 프로세스가 비어있는 버퍼에 동시에 데이터를 만들어 넣으려고 할 때 또는 둘 이상의 소비자 프로세스가 채워진 버퍼에 동시에 접근하여 데이터를 꺼내려고 할 때 문제가 발생할 수 있다.
  - 공유 데이터(버퍼)에 lock/unlock을 해야하는 문제
- 버퍼의 크기가 유한하여 생산자나 소비자 프로세스의 자원이 없는 경우 발생
  - 공유 버퍼가 전부 채워진 상태에서 새로운 생산자 프로세스가 도착한 경우, 생산자 입장에서 사용할 수 있는 자원이 없는 상태가 되어 자원의 여분이 생길 때까지 기다리게 된다.

#### Synchronization variables

- 상호 배제 : 둘 이상의 프로세스가 동시에 공유 버퍼에 접근하는 것을 막기 위해 공유 버퍼 전체에 lock을 걸어 하나의 프로세스가 배타적으로 접근하게 해야한다.
  ⇒ Binary semaphore 필요
- 자원의 개수 : 생산자나 소비자 프로세스가 사용할 수 있는 자원의 수를 표시(남아있는 full/empty buffer의 수 표시)
  ⇒ Integer semaphore 필요

#### 예제 코드

![](https://i.imgur.com/XA2uILw.png)

- `mutex = 1;` : 공유 버퍼에 접근 프로세스를 1개만 허용하는 세마포어
- **Producer**
  - 아이템 `x`를 생성
  - `P(empty)` : empty buffer가 있는지 확인. 값이 0이면 대기
  - `P(mutex)` : P연산을 통해 empty buffer가 있다면 mutex를 0으로 변경하며 lock을 건다.
  - 아이템 `x`를 버퍼에 추가
  - `V(mutex)` : 버퍼에 대한 조작이 끝나고 V연산을 통해 mutex를 1로 변경하며 lock을 푼다.
  - `V(full)` : full buffer(consumer의 자원)의 수를 1 증가시킨다.
- **Consumer**
  - `P(full)` : full buffer가 있는지 확인. 없으면 대기
  - `P(mutex)` : P연산을 통해 full buffer가 있다면 mutex를 0으로 변경하며 lock을 건다.
  - 아이템 `x`를 버퍼에서 꺼내간다.
  - `V(mutex)` : 버퍼에 대한 조작이 끝나고 V연산을 통해 mutex를 1로 변경하며 lock을 푼다.
  - `V(empty)` : empty buffer(producer의 자원)의 수를 1 증가시킨다.

### Readers-Writers Problem

- 여러 개의 읽는 프로세스와 쓰는 프로세스가 존재하는 환경에서 공유 데이터인 DB에 접근할 때 발생하는 문제
- 한 프로세스가 DB에 write 중일 때 다른 프로세스가 접근하면 안된다.
- 여러 프로세스가 동시에 read 작업을 해도 동기화에 문제가 되지 않는다.

#### 해결 방법

- Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기 중인 Reader들은 DB에 접근할 수 있게 해준다.
- Writer는 대기 중인 Reader가 하나도 없을 때 DB에 접근이 허용된다.
- Writer가 DB에 접근 중이면 Reader들은 접근이 금지된다.
- Writer가 DB에서 빠져나가야만 Reader들의 접근이 허용된다.

#### 공유 데이터

- DB 자체
- readcount : 현재 DB에 접근 중인 reader의 수. 모든 읽는 프로세스가 변경할 수 있는 값이므로 공유 데이터이다.

#### Synchronization variables

- mutex : 공유 변수 readcount의 상호 배제를 보장하기 위해 사용하는 바이너리 세마포어 변수
- db : Reader와 Writer가 공유 DB 자체를 올바르게 접근할 수 있도록 하는 변수

#### 예제 코드

![](https://i.imgur.com/RLDCokT.png)

- `DB` : 공유 데이터
- `db` : `DB`에 대한 lock에 해당하는 세마포어 변수
- **Writer**
  - `P(db)` : P연산을 통해 `DB`에 lock을 건다.
  - DB에 write 작업을 수행
  - `V(db)` : V연산을 통해 `DB`에 lock을 푼다.
- **Reader**
  - `P(mutex)` : readcount를 상호 배제하기 위해 P연산을 통해 mutex를 0으로 변경하며 readcount에 lock을 건다.
    ⇒ lock을 걸어 writer의 접근은 막고 reader의 접근은 막지 않는다.
  - `readcount++` : readcount를 1 증가 시킨다.
  - `if (readcount == 1) P(db)` : `readcount == 1` 이라면 최초로 데이터를 읽으러 임계 구역에 진입한 것이므로 DB에 lock을 걸고, readcount가 1보다 클 경우 최초의 reader가 이미 lock이 걸어놓은 상태를 의미한다.
  - `V(mutex)` : 공유 변수를 변경하는 작업이 끝나면 readcount에 대한 lock을 풀어준다.
  - DB를 읽는다.
  - `if (readcount == 0) V(db)` : `readcount == 0` 이라면 다른 프로세스들은 다 읽고 나가고 마지막으로 읽고 나가는 프로세스만 임계 구역에 남아있는 상태이므로 DB에 걸려있는 lock을 풀어준다.
- Starvation : Writer가 DB에 접근하려면 read 중인 프로세스가 없어야 한다. Reader가 먼저 도착하여 DB에 접근할 상태일 때 Writer가 늦게 도착하여 대기 중이라면, read 작업을 무한히 수행할 경우 Writer는 영원히 임계 구역에 접근하지 못하는 상황이 발생할 수 있다.

### Dining-Philosophers Problem

![](https://i.imgur.com/kXiLFNK.png)

다섯명의 철학자가 원탁에 앉아 생각을 하거나 식사를 반복한다. 철학자들마다 생각하는 주기와 식사하는 주기가 다르다. 배가 고파지면 왼쪽과 오른쪽에 있는 젓가락을 집어들어 식사를 하고, 식사를 마치면 젓가락을 놓고 생각을 한다. 젓가락(공유 자원)은 각 철학자들의 양쪽에 한 개씩만 놓여있고, 식사를 하기 위해서는 두 개의 젓가락이 필요하다.

1. 한 철학자가 생각을 하다가 배가 고파져 식사를 하기 위해 왼쪽 젓가락을 집어든다. 다른 철학자가 이미 왼쪽 젓가락을 사용하고 있다면 내려놓을 때까지 생각하며 대기한다.
2. 왼쪽을 들었으면 오른쪽 젓가락을 집어든다. 다른 철학자가 사용하고 있다면 1번과 마찬가지로 들 수 있을 때까지 생각하며 대기한다.
3. 두 젓가락을 모두 들었다면 일정 시간동안 식사를 한다.
4. 식사를 마쳤으면 오른쪽 젓가락을 내려놓고, 그 다음 왼쪽 젓가락을 내려놓는다.
5. 다시 생각하다가 배고프면 1번으로 돌아간다.

#### 문제점

- Deadlock의 가능성 : 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어든 경우 오른쪽 젓가락은 영원히 잡을 수 없다. 한 번 집어든 젓가락은 식사를 마치지 않는다면 놓지 않는다.

#### 해결 방안

1. 철학자가 식사를 할 때만 테이블에 앉을 수 있게하여, 테이블에 동시에 앉을 수 있는 철학자는 최대 4명까지 제한
2. 젓가락을 두 개 모두 잡을 수 있을 때에만 젓가락을 잡을 수 있게 권한을 준다.
3. 비대칭 : 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 잡을 수 있도록 한다.

#### 해결 방안 2의 예제 코드

```java
enum {thinking, hungry, eating} state[5]; // 철학자들의 상태를 나타내는 공유 변수
semaphore self[5] = 0; // 젓가락을 두 개 모두 들 수 있는 권한을 나타내는 세마포어, 0이면 권한이 없음
semaphore mutex = 1; // 철학자의 상태를 상호 배제하기 위한 변수
```

- 일반적으로 세마포어는 자원의 갯수를 나타내며 초기값이 얼마냐에 따라 일을 하는 것인데 여기서는 젓가락을 모두 들 수 있는 권한을 0으로 설정하고, 조건에 따라 값을 변경한다.
- **Philosopher i**

```java
do {
  pickup(i);
  eat();
  putdown(i);
  think();
} while(1);

void pickup(int i) {
  P(mutex);
  state[i] = hungry;
  test(i);
  V(mutex);
  P(self[i]);
}

void putdown(int i) {
  P(mutex);
  state[i] = thinking;
  test((i + 4) % 5); // 왼쪽 철학자의 상태를 확인
  test((i + 1) % 5); // 오른쪽 철학자의 상태를 확인
  V(mutex);
}

// 철학자 i 가 젓가락 두 개를 모두 잡을 수 있는 상태인지 확인하는 함수
void test(int i) {
  // 왼쪽 철학자가 식사 중이 아니고, 오른쪽 철학자도 식사 중이 아니고, 철학자 i가 배고픈 상태인 경우
  if (state[(i + 4) % 5] != eating && state[i] == hungry && state[(i + 1) % 5] != eating) {
    // 철학자 i의 상태를 eating으로 변경
    state[i] = eating;
    // 초기에 젓가락을 잡을 수 있는 권한을 0으로 설정하여
    // test 함수를 통해 조건을 충족하면 철학자 i의 권한이 1로 변경된다.
    V(self[i]);
  }
}
```

#### 세마포어의 문제점

- 코딩하기 힘들다.
- 정확성(correctness)의 입증이 어렵다.
- 자발적 협력(voluntary cooperation)이 필요하다.
- 한 번의 실수가 모든 시스템에 치명적 영향을 미친다.
- 예시
  - V연산을 먼저한 경우 : 상호 배제가 깨진다.
  ```java
  V(mutex)
  Critical Section
  P(mutex)
  ```
  - P연산을 하고 또 P연산을 한 경우 : Deadlock
  ```java
  P(mutex)
  Critical Section
  P(mutex)
  ```

## Monitor

- 동시 수행 중인 프로세스 사이에서 ADT(abstract data type, 추상 자료형)의 안전한 공유를 보장하기 위한 high-level synchronization construct
- 공유 데이터에 접근하기 위해서는 모니터 내부의 프로시져를 통해서만 접근할 수 있도록 한다.
- 세마포어는 프로그래머가 직접 코딩하여 구현하고, lock을 통해 동기화 문제를 해결하지만 모니터는 내부에서 동기화를 관리해주기 때문에 lock 없이 동기화 문제를 해결할 수 있다.

```java
monitor monitor-name {
  // 공유 변수 선언
  shared variable declarations

  // 공유 변수에 접근하기 위한 프로시저를 모니터 내부 함수로 구현
  procedure body P1(...) {
    ...
  }
  procedure body P2(...) {
    ...
  }
  procedure body P3(...) {
    ...
  }
  {
    initialization code // 초기화 코드
  }
}
```

![](https://i.imgur.com/pchGGA9.png)

- 모니터 내에서는 한 번에 하나의 프로세스만 활동 가능하고, 모니터에 접근하지 못한 프로세스는 entry queue에서 대기(상호 배제)
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없다.
- 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 **condition vaiable**을 사용한다.
  ```java
  condition x, y;
  ```
- Conition variable은 wait와 signal 연산에 의해서만 접근 가능
  - `x.wait();`
    - 자원이 있다면 바로 접근할 수 있지만 자원이 없으면 wait 함수를 통해 프로세스를 줄을 세워 기다리게 한다.
    - x.wait()을 호출(invoke)한 프로세스는 다른 프로세스가 x.signal()을 호출하기 전까지 suspend 된다.
  - `x.signal();` : x.signal()은 정확하게 하나의 suspend 된 프로세스를 resume 한다. Suspend 된 프로세스가 없으면 아무 일도 일어나지 않는다.

### Monitor Example: Bounded-Buffer Problem

```java
monitor bounded_buffer {
  // 공유 버퍼가 모니터 내부에 정의
  int buffer[N];
  condition full, empty;
  /* condition variable은 값을 가지지 않고 자신의 큐에 프로세스를 매달아 sleep 시키거나 큐에서 프로세스를 깨우는 역할말 함 */

  // 생산
  void produre (int x) {
    if there is no empty buffer
      empty.wait(); // empty buffer를 기다림
    add x to an empty buffer // 공유 데이터를 넣거나 빼는 작업 앞뒤에 lock을 하는 과정이 없다.
    full.signal();
  }

  // 소비
  void consume (int *x) {
    if there is no full buffer
      full.wait(); // full buffer를 기다림
    remove an item from buffer and store it to *x
    empty.signal();
  }
}
```

# [Chapter 6. 프로세스 동기화 Part-4](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)

### Monitor Example: Dining-Philosophers Problem

```java
Each Philosopher:
{
  pickup(i); // Enter monitor, 젓가락을 잡는 코드는 모니터 내부에 존재
  eat();
  putdown(i); // Enter monitor
  thick();
} while(1)

monitor dining_philospher {
  enum {thinking, hungry, eating} state[5];
  condition self[5];

  void pickup (int i) {
    state[i] = hungry; // 철학자 i는 hungry 상태
    test(i); // 철학자 i가 젓가락을 잡을 수 있는지 확인
    if (state[i] != eating) // test 함수를 통해 조건을 만족하지 못해 철학자 i의 상태가 식사 중이 아니라면
      self[i].wait(); // 젓가락을 잡을 수 있는 상태가 아니므로 대기
  }

  void putdown (int i) {
    state[i] = thinking; // 식사를 마치면 철학자 i는 thinking 상태로 변경
    // 인접한 철학자들의 상태를 확인
    test((i + 4) % 5); // 왼쪽 철학자 테스트
    test((i + 1) % 5); // 오른쪽 철학자 테스트
  }

  void test (int i) {
    // 왼쪽 철학자가 식사 중이 아니고, 철학자 i는 배고픈 상태이고, 오른쪽 철학자도 식사 중이 아니라면
    if ((state[(i + 4) % 5] != eating) && (state[i] == hungry) && (state[(i + 1) % 5] != eating) {
      state[i] = eating; // 철학자 i을 식사하는 상태로 변경
      self[i].signal(); // 혹시 철학자 i가 잠들어 있다면 깨워준다.
    }
  }

  void init () {
    for (int i = 0; i < 5; i++)
      state[i] = thinking;
  }
}
```
