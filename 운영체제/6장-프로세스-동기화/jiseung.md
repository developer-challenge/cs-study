# [프로세스 동기화 #1](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

39분 부터 시작

## 데이터의 접근

| Execution   | Storage   |
| ----------- | --------- |
| CPU         | Memory    |
| 컴퓨터 내부 | 디스크    |
| 프로세스    | 주소 공간 |

## Race Condition

<aside>
💡 여러 프로세스들이 동시에 공유 데이터에 접근하는 상황
데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐

</aside>

- Storage(Memory Address Space)를 공유하는 여러 개의 CPU(Process)
- Multiprocessor
  - 공유 메모리를 사용하는 프로세스들
  - 커널 내부 데이터를 접근하는 루틴 간
    - 커널모드 수행 중 인터럽트로 커널모드 다른 루틴 수행 시

## OS에서의 Race Condition

### Interrupt handler vs. Kernel

- 커널모드 running 중 interrupt 발생 → 인터럽트 처리루틴이 수행
  - 양쪽 다 커널 코드이므로 kernel address space 공유
- 해결책
  - interrupt enable/disable
  - 순서를 정해둠

### Preempt a process while in kernel mode

- Process가 system call을 해서 kernel mode로 수행 중인데 context switch가 일어나는 경우
- 해결책
  - 커널 모드에서 수행 중일 때는 CPU를 preempt하지 않음
  - 커널 모드에서 사용자 모드로 돌아갈 때 preempt
  - time sharing ≠ real time

### Multiprocessor

어떤 CPU가 마지막으로 count를 store 했는가

- Multiprocessor에서 shared memory 내의 kernel data
- 작업 주체가 여러개
- interrupt enable/disable로 해결되지 않음
- 해결책
  - 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법
    - 커널 전체를 하나의 lock
    - 비효율적일 수 있음
  - 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock/unlock을 하는 방법
    - 각각의 데이터마다 lock

## Process Synchronization 문제

> 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.

일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘 필요

>

<aside>
💡 race conditon을 막기 위해서 concurrent process는 동기화(synchronize)되어야 한다.

</aside>

## Example of a Race Condition

## The Critical-Section Problem

- Critical Section(임계 영역) : 공유 데이터에 접근하는 코드
- 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.

## 프로그램적 해결법의 충족조건

- Mutual Exclusion (상호 배제)
  - 프로세스 P가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 critical section에 들어가면 안 된다.
- Progress (진행)
  - 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 들어가게 해줘야 한다.
- Bounded Waiting (유한 대기)
  - 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.
- 가정
  - 모든 프로세스의 수행 속도는 0보다 크다.
  - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

## Initial Attempts to Solve Problem

> 소프트웨어적으로 해결 시도

- 두 개의 프로세스가 있다고 가정
- 프로세스들은 수행의 동기화(synchronize)를 위해 몇몇 변수(Synchronization variable)를 공유할 수 있다.

  ```cpp
  do {
    entry section (lock)
    critical section
    exit section (unlock)
    remainder section
  } while(1);
  ```

- Algorithm 1
  - Synchronization variable `turn`
  ```cpp
  do {
  	while (turn != 0);
  	critical section
  	turn = 1;
  	remainer section
  } while (1);
  ```
  - Mutual Exclusion O
  - Progress X
    - 반드시 교대로만 실행 가능함
    - 균일하지 않게 접근하는 경우 문제가 됨
- Algorithm 2
  - Synchronization variable `flag[n]`
  ```cpp
  do {
  	flag[i] = true;
  	while (flag[j]);
  	critical section
  	flag[i] = false;
  	remainder section
  } while (1);
  ```
  - Mutual Exclusion O
  - Progress X
    - 아무도 critical section에 접근하지 못함
- Algorithm 3 (Peterson’s Algorithm)
  - Combined synchronization variablesf of algorithm 1, 2
  ```cpp
  do {
  	flag[i] = true;
  	turn = j;
  	while (flag[j] && turn == j);
  	critical section
  	flag[i] = false;
  	remainder section
  } while (1);
  ```
  - Mutual Exclusion O
  - Progress O
  - Bounded Waiting
  - 문제점 : Busy Waiting(Spin lock)
    - 계속 CPU와 memory를 사용하면서 wait

## Synchronization Hardware

<aside>
💡 하드웨어적으로 Test & modify를 atomic하게 수행할 수 있도록 지원하는 경우 앞의 문제를 간단히 해결할 수 있다.

</aside>

- Mutual Exclusion with Test & Set
  - Synchronization variable `lock`
  ```cpp
  do {
  	while (Test_and_Set(lock));
  	critical section
  	lock = false;
  	remainder section
  }
  ```

# [프로세스 동기화 #2](https://core.ewha.ac.kr/publicview/C0101020140404151340260748?vmode=f)

## Semaphores

- 일종의 추상 자료형
  - Object, Operation
  - 앞의 방식들을 추상화했다.
- Semaphore S
  - Integer variable
  - 변수. 자원의 개수
  - 아래 두 가지 atomic 연산에 의해서만 접근 가능
    ```cpp
    P (S): while (S<=0) do no-op; S--;
    // If positive, decrement & enter
    // Otherwise, wait until positive (busy-wait)
    V (S): S++;
    ```

## Critical Section of n Processes

- Synchronization variable `semaphore mutex`
  - initially 1 : 1개가 CS에 들어갈 수 있음

```cpp
do {
	P(mutex); // if positive, dec & enter. Otherwise, wait
	critical section
	V(mutex); // Increment semaphore
	remainder section
} while (1);
```

- 여전히 busy-wait(spin-lock) 문제
- Block & Wakeup 방식(sleep-lock) 구현

## Block / Wakeup Implementation

- Semaphore 정의
  ```cpp
  typedef struct
  {
  	int value; // semaphore
  	struct process *L; // process wait queue
  } semaphore;
  ```
- block과 wakeup 가정
  - block : 커널은 block을 호출한 프로세스를 suspend시킴
    이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
  - wakeup(P) : block된 프로세스 P를 wakeup 시킴
    이 프로세스의 PCB를 ready queue로 옮김

### Implementation

- 자원 획득

  ```cpp
  P(S):

  S.value--; // prepare to enter
  if (S.value < 0) // negative case. 자원 여분 없음
  {
    add this process to S.L; // wait queue
    block(); // block 시킴
  }
  ```

- 자원 반납

  ```cpp
  V(S):

  S.value++; // 자원 반납
  if (S.value <= 0) // 0의 경우 포함
  {
    remove a process P from S.L; // List에서 제거
    wakeup(P); // 잠들어 있는 프로세스 깨워줌
  }
  ```

## Busy-wait vs. Block/WakeUp

- Block/WakeUp 오버헤드 vs. Critical section 길이
  - Critical section 길이가 긴 경우 Block/WakeUp이 적당
  - Critical section 길이가 매우 짧은 경우 Block/WakeUp 오버헤드 > Busy-wait 오버헤드
- 일반적으로는 Block/WakeUp is better

## Two Types of Semaphores

- Counting semaphore
  - 도메인이 0 이상인 임의의 정수값
  - 주로 resource counting에 사용
- Binary semaphore (=mutex)
  - 0 또는 1 값
  - 주로 mutual exclusion (lock/unlock)에 사용

## Deadlock and Starvation

- Deadlock : 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
  - 둘 이상의 semaphore을 획득해서 일해야 하는 경우 문제가 될 수 있다.
  - S와 Q가 1로 초기화된 semaphore라고 할 때
  - P0 프로세스가 끝나지 않으므로 P1이 영원히 semaphore S를 얻을 수 없음
  - 자원을 갖는 순서를 동일하게 수정하면 쉽게 해결할 수 있다.
    - 프로그래머가 유의해야함
- Starvation(Indefinite blocking)
  - 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
  - 특정 프로세스들이 자원을 독차지
- Dining-Philosophers Problem
  - Starvation & DeadLock

# [프로세스 동기화 #3](https://core.ewha.ac.kr/publicview/C0101020140408134626290222?vmode=f)

# [프로세스 동기화 #4](https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f)

## Classical Problems of Syncronization

- Bounded-Buffer Problem (Producer-Consumer Problem)
- Readers-Writers Problem
- Dining-Philosophers Problem

## Monitor

## Semaphore의 문제점

- 코딩하기 힘들다.
- 정확성(correctness)의 입증이 어렵다.
- 자발적 협력(voluntary cooperation)이 필요하다.
- 한번의 실수가 모든 시스템에 치명적 영향
- 예시

  ```cpp
  V(mutex)
  Critical Section
  P(mutex)
  // Mutual exclusion 깨짐

  P(mutex)
  Critical Section
  P(mutex)
  // Deadlock
  ```

## Monitor

하나의 프로세스만 실행가능하므로 나머지 프로세스는 대기

> 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct

```cpp
monitor monitor-name {
	shared variable declarations
	procedure body P1 (...) {}
	procedure body P2 (...) {}
	...
	procedure body Pn (...) {}
	{
		initialization code
	}
}
```

- no lock
- 모니터 내에서 한번에 하나의 프로세스만 활동 가능
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요 없음
- 자원의 개수를 세는 것은 동일 ⇒ condition variable
  - 프로세스가 모니터 안에서 기다릴 수 있도록
  - `condition x, y;`
- Condition variable은 wait와 signal 연산에 의해서만 접근 가능
  - `x.wait()`;
    - `x.wait()`를 invoke한 프로세스는 다른 프로세스가 `x.signal()`을 invoke하기 전까지 suspend 된다.
  - `x.signal();`
    - `x.signal()`은 정확하게 하나의 suspend된 프로세스를 resume
    - suspend된 프로세스가 없으면 아무 일도 일어나지 않는다.
- Semaphore와 conversion 용이

### Bounded-buffer Problem

```cpp
monitor bounded_buffer {
	int buffer[N];
	condition full, empty;
	// condition variable은 값을 가지지 않고
	// 자신의 큐에 프로세스를 매달아 sleep 시키거나 큐에서 프로세스를 깨우는 역할만 함

	void produce (int x) {
		if there if no empty buffer
			empty.wait();
		add x to an empty buffer
		full.signal(); // 깨움
	}

	void consume (int *x) {
		if there is no full buffer
			full.wait();
		remove an item from buffer and store it to *x
		empty.signal(); // 깨움
	}
}
```

- semaphore에서는 값 관리와 lock/unlock 로직이 분리되어 있음
- monitor에서는 공유 자원 관리를 해줌

### Dining Philosophers Example

- lock을 사용하지 않는다는 차이점
