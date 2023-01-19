## [프로세스 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140321144554159683?vmode=f)

### 프로세스 생성 (Process Creation)

부모 프로세스(Parent process)가 자식 프로세스(children process) 생성

Copy-on-write (COW) : write(자식 프로세스가 새로운 내용을 만들 때 메모리에 올라가 있는 잘게 쪼개진 code data stack부분 카피/이전에는 부모 프로세스 공유)가 발생 했을 때 copy

- 부모 프로세스가 자식 프로세스를 복제하여 만듦
- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요로 함
  - 운영체제로부터 받음
  - 부모와 공유
- 자원의 공유
  - 부모와 자식이 모든 자원을 공유하는 모델
  - 일부를 공유하는 모델
  - 전혀 공유하지 않는 모델
- 수행(Execution)
  - 부모와 자식은 공존하며 수행되는 모델
  - 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델
- 주소 공간 (Address space)
  - 자식은 부모의 공간을 복사함(binary and OS data)
  - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스 예
  - fork () 시스템 콜이 새로운 프로세스를 생성
    - 부모를 그대로 복사 (OS data except PID + binary)
    - 주소 공간 할당
  - fork 다음에 이어지는 exec () 시스템 콜을 통해 새로운 프로그램을 메모리에 올림

### 프로세스 종료 (Process Termination)

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (**exit**)
  - 자식이 부모에게 output data를 보냄 (via **wait**)
  - 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴 (**abort**)
  - 자식이 할당 자원의 한계치를 넘어섬
  - 자식에게 할당된 태스크가 더 이상 필요하지 않음
  - 부모가 종료(exit)하는 경우
    - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않음
    - 단계적인 종료

## [프로세스 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140325134428879622?vmode=f)

### 프로세스와 관련한 시스템콜

**fork() - create a child (copy)**

![](https://i.imgur.com/JRrsIfY.png)

![](https://i.imgur.com/a6eTbfh.png)

부모 프로세스에서 fork()가 실행되면 새로운 자식 프로세스가 생성

⇒ 부모 프로세스의 Context/Program Counter를 복제하기 때문에 자식 프로세스는 fork() 다음 시점부터 실행

자식 프로세스는 fork한 결과값으로 0을 받고 부모 프로세스는 자식 프로세스의 pid를 받음

**exec() - overlay new image**

어떠한 프로그램을 완전히 새로운 프로세스로 태어나게 하는 역할

![](https://i.imgur.com/lZzuMnv.png)

execlp : 이전에 실행되었던 것은 잊고 새로운 프로그램으로 완전히 덮어씌움(프로그램 이름을 쌍따움표 안에 두 번 넣어주고 프로그램에 전달할 argument, null pointer를 넣어줌) → main함수의 시작부분부터 실행

![](https://i.imgur.com/j8H5dKl.png)

실행 결과 : 1 출력 → 3 출력 → 종료

**wait() - sleep until child is done**

![](https://i.imgur.com/yoMmdYC.png)

프로세스 A가 wait() 시스템 콜을 호출하면

- 커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다 (block 상태)
- Child process가 종료되면 커널은 프로세스 A를 깨운다 (ready 상태)

**exit() - frees all the resources, notify parent**

프로세스의 종료

- 자발적 종료
  - 마지막 statement 수행 후 exit() 시스템 콜을 통해
  - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
- 비자발적 종료
  - 부모 프로세스가 자식 프로세스를 강제 종료시킴
    - 자식 프로세스가 한계치를 넘어서는 자원 요청
    - 자식에게 할당된 태스크가 더 이상 필요하지 않음
  - 키보드로 kill, break 등을 친 경우
  - 부모가 종료하는 경우
    - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨

### 프로세스 간 협력

**독립적 프로세스(Independent process)**

- 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함

**협력 프로세스(Cooperating process)**

- 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음

**프로세스 간 협력 메커니즘(IPC : Interprocess Communication)**

- 메시지를 전달하는 방법
  - message passing : 커널을 통해 메시지 전달
- 주소 공간을 공유하는 방법
  - shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
  - thread : thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능

### Message Passing

**Message system**

- 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템

**Direct Communication**

![](https://i.imgur.com/PBk4y2x.png)

- 통신하려는 프로세스의 이름을 명시적으로 표시

**Indirect Communication**

![](https://i.imgur.com/cq3b5ES.png)

- mailbox(또는 port)를 통해 메시지를 간접 전달

### Interprocess communication

![](https://i.imgur.com/aIgH9IX.png)

shared memory를 쓰겠다는 system call을 kernel에게 요청을 한 뒤 사용자 프로세스끼리 메모리 공간 공유

### CPU and I/O Bursts in Program Execution

![](https://i.imgur.com/B3S0bsL.png)

load store, add store… : CPU에서 인스트럭션을 실행하는 기계어

**CPU burst** : CPU만 연속적으로 사용하면서 인스트럭션을 실행

**I/O burst** : I/O만 실행

CPU burst와 I/O burst가 번갈아가면서 실행

### CPU-burst Time의 분포

![](https://i.imgur.com/heCqEbW.png)

여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요

- interactive job에게 적절한 response 제공 요망
- CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

### 프로세스의 특성 분류

**I/O bound process**

- CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
- (many short CPU bursts)

**CPU bound process**

- 계산 위주의 job
- (few very long CPU bursts)

### CPU Scheduler & Dispatcher

**CPU Scheduler - 운영체제 안에서 CPU 스케줄링하는 코드**

- Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름

**Dispatcher**

- CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘김 ⇒ 방금 전에 CPU에서 돌아가던 프로그램의 context를 저장, 새로 선택된 프로세스의 context를 확인
- 이 과정을 context switch(문맥 교환)라고 함

CPU 스케줄링이 필요한 경우의 프로세스 상태 변화

1. Running → Blocked (예 : I/O 요청하는 시스템 콜)
2. Running → Ready (예 : 할당시간만료로 timer interrupt)
3. Blocked → Ready (예 : I/O 완료후 인터럽트)
4. Terminate

1, 4에서의 스케줄링은 **nonpreemptive** (=강제로 빼앗지 않고 자진 반납)

All other scheduling is **preemptive** (=강제로 빼앗음)
