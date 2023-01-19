# [프로세스 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140321144554159683?vmode=f)

## 프로세스 생성 (Process Creation)

- 부모 프로세스가 자식 프로세스 생성 (복제X공유)
  - 프로세스의 트리(계층 구조) 생성
  - Copy-On-Write(COW)
    - 일단 부모 자원을 공유하고 있다가 write가 발생하고 나면 copy
    - 필요한 부분만 copy한다.
- 프로세스는 자원(CPU, memory)을 필요로 함
  - 운영체제로부터 받는다.
  - 부모와 공유할 수도 있다.
- 자원 공유 모델
  1. 부모와 자식이 모든 자원을 공유
  2. 일부 공유
  3. 전혀 공유하지 않음 (원칙. 별개의 프로세스)
- 수행 (Execution) 모델
  1. 부모와 자식이 공존하며 수행
  2. 자식이 종료(terminate)될 때까지 부모가 기다림(wait)
- 주소 공간 (Address space)
  - 자식이 부모 공간을 그대로 복사 (binary, OS data)
  - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스 예
  > 프로세스의 생성은 운영체제를 통해서만 가능하다. (시스템 콜)
  - `fork()` 시스템 콜이 새로운 프로세스를 생성
    1. 부모를 그대로 복사 (OS data except PID + binary)
    2. 주소 공간 할당
  - `fork()` 다음에 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램을 메모리에 올림
    - `exec()` : 필요에 따라 새로운 프로세스로 바뀔 수 있음

## 프로세스 종료 (Process Termination)

- `exit()` : 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려주는 시스템 콜 (명시적 or 컴파일러 do)
  - 자식이 부모에게 output data를 보냄 (via `wait` 시스템 콜)
    - 자식이 먼저 종료
  - 프로세스의 각종 자원들이 운영체제에게 반납됨
- `abort()` : 부모 프로세스가 자식의 수행을 강제 종료시킴. `kill`
  - 자식이 할당 자원의 한계치를 넘어섬
  - 자식에게 할당된 태스크가 더이상 필요하지 않음
  - 부모가 종료(exit)하는 경우
    - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더이상 수행되는 것을 허용하지 않는다.
    - 단계적 종료

# [프로세스 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140325134428879622?vmode=f)

## 프로세스와 관련한 시스템콜

### `fork()` 시스템 콜

> A process is created by the `fork()` system call
> creates a new address space that is a duplicate of the caller

- `fork()`를 실행하면 자식 프로세스는 부모의 문맥(Program counter)을 그대로 복사해와서 코드를 이어서 실행한다.
- 결과값(반환값)을 통해 부모와 자식 프로세스를 구분한다.
  - 부모 : 양수(자식 프로세스의 PID)
  - 자식 : 0

### `exec()` 시스템 콜

> A process can execute a different propram by the `exec()` system call.
> replaces(overlay) the memory image of the caller with a new program

- execlp : exec() 시스템 콜
- execlp(프로그램이름, 프로그램이름, “인자1”, “인자2”, …, (char \*) 0);

- 자식 프로그램에 완전히 새로운 프로세스를 덮어 씌워서 실행
- 꼭 `fork()`와 함께 사용하지 않아도 가능
  - 이 경우 `exec()` 이후의 코드는 실행이 불가능

### `wait()` 시스템 콜

> 자식 프로세스의 종료를 기다리는 시스템 콜
> sleep until child is done

- 프로세스 A가 `wait()` 시스템 콜을 호출하면
  - 커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다. (block 상태)
  - Child process가 종료되면 커널은 프로세스 A를 깨운다. (ready 상태)
- 예시 : Linux command line
  - program 실행
  - 쉘 프롬프트에서 자식 프로세스의 종료를 기다림

### `exit()` 시스템 콜

> 프로세스의 종료
> frees all the resources, notify parent

- 자발적 종료
  - 실행시킬 마지막 statement 수행 후 `exit()` 시스템 콜을 통해 종료
  - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
- 비자발적 종료
  - 부모 프로세스가 자식 프로세스를 강제 종료시킴
    - 자식 프로세스가 한계치를 넘어서는 자원 요청
    - 자식에게 할당된 태스크가 더 이상 필요하지 않음
  - 외부 : 사용자가 kill, break 등의 command 실행, 혹은 ctrl + c 등의 사용자 액션
  - 부모가 종료하는 경우
    - 부모 프로세스가 종료되기 전에 자식들이 먼저 종료됨
    - 계층적/단계적 종료

## 프로세스 간 협력

- 독립적 프로세스(Independent process)
  - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스 (Cooperating process)
  - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음

## Message Passing

- Message system
  - 프로세스 사이에 공유 변수 (shared variables)를 일체 사용하지 않고 통신하는 시스템
- Direct Communication
  - 통신하려는 프로세스의 이름을 명시적으로 표시
- Indirect Communication
  - mailbox 또는 port를 통해 메시지를 간접 전달

## Interprocess communication

- 프로세스 간 협력 메커니즘 (IPC, Interprocess Communication)
  - 메세지를 전달하는 방법
    - message passing: 커널을 통해 메시지 전달
  - 주소 공간을 공유하는 방법
    - shared memory: 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘
    - thread : thread들은 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들 간에 주소 공간을 공유해 협력 가능

## CPU and I/O Bursts in Program Execution

- CPU Burst
  - CPU가 실행하는 기계어
  - load store, add store, read from file… (running)
- I/O Burst
  - wait for I/O
  - 오래 걸리는 작업

## CPU-burst Time의 분포

> 여러 종류의 job(process)들에 대해 적절하게 시스템 자원을 분배하기 위해 CPU 스케줄링이 필요하다.

I/O bound job의 빈도가 높으면 CPU가 오래 기다려야 한다.
interactive job이 오래 걸리지 않도록 적절한 response를 제공해야 한다.

- 누구에게 우선할 것인가
- 언제까지 줄 것인가

## 프로세스의 특성 분류

- I/O-bound process
  - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
  - many short CPU bursts
- CPU-bound process
  - 계산 위주의 job
  - few very long CPU bursts

## CPU Scheduler & Dispatcher

### CPU Scheduler

> Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.

### Dispatcher

> CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.
> 이 과정을 context switch(문맥 교환)라고 한다.

### CPU 스케줄링이 필요한 경우

1. Running → Blocked (I/O를 요청하는 시스템 콜)
2. Running → Ready (할당 시간 만료로 timer interrupt)
3. Blocked → Ready (I/O 완료 후 interrupt)
   1. 우선순위에 따라 I/O가 완료되고 나서 바로 실행할 수 없는 경우
4. Terminate

- 1, 4 : nonpreemptive. 자진 CPU 반납
- 2, 3 : preemptive. 강제 CPU 반납
