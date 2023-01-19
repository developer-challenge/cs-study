## [프로세스 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140321144554159683?vmode=f)

### 프로세스 생성 (Process Creation)

부모 프로세스가 자식 프로세스 생성

생성 방식 = **복제** `(Copy on write)`
프로세스의 문맥(code, data, stack/ CPU 문맥 등)을 모두 복사

프로세스의 트리(계층 구조) 형성

#### 프로세스는 **자원**을 필요로 함

- 운영체제로부터 자원을 제공받음
- 부모와 자원을 공유

#### 자원의 공유

가능하다면 공유를 하는 것이 효율적이지만 독립적인 자원이 원칙적

- 부모와 자식이 모든 자원을 공유하는 모델
- 일부를 공유하는 모델
- 전혀 공유하지 않는 모델

#### 수행

- 부모와 자식은 공존하며 수행되는 모델
- 자식이 종료될 때까지 부모가 기다리는 모델

#### 주소 공간

- 자식은 부모의 공간을 복사함 (binary and OS data)
- 자식은 그 공간에 새로운 프로그램을 올림

#### 유닉스의 예

fork() 시스템 콜이 새로운 프로세스 생성

- 부모를 그대로 복사 (OS data except PID + binary)
- 주소 공간 할당

fork 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림

### 프로세스 종료 (Process Termination)

항상 자식이 부모보다 먼저 종료되어야 함.

프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (exit)

- 자식이 부모에게 output data를 보냄 (via wait)
- 프로세스의 각종 자원들이 운영체제에게 반납됨

부모 프로세스가 자식의 수행을 종료시킴 (abort)

- 자식이 할당 자원의 한계치를 넘어서는 경우
- 자식에게 할당된 태스크가 더 이상 필요하지 않음
- 부모가 종료(exit)하는 경우
  - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다
  - 단계적인 종료

## [프로세스 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140325134428879622?vmode=f)

### 프로세스와 관련한 시스템콜

#### fork()

호출자를 복제한 새 주소 공간을 생성

![](https://i.imgur.com/s2W9KTU.png)

왼쪽 그림에서 fork()를 만나면 오른쪽의 자식 프로세스 생성
이 때 자식 프로세스는 fork()문 이후 내용부터 실행

> why? 부모와 program counter를 공유하기 때문에 부모와 문맥을 공유

이 때 `return value`를 이용해 해당 프로세스가 복제본인지 아닌지 구분 가능

#### exec()

자식 프로세스를 새로운 프로세스로 만들어 줌

![](https://i.imgur.com/AB6BN9y.png)

execlp가 이전 부모 프로세스의 정보를 잊게하고 새로운 정보를 덮어씌움

#### wait()

프로세스 A가 wait 시스템 콜을 하면

- 커널은 child가 종료될 때까지 프로세스 a를 sleep 시킨다. (block)
- 자식 프로세스가 종료되면 커널은 프로세스 a를 깨운다. (ready)

#### exit()

프로세스의 종료

- 자발적 종료
  - 마지막 statement 수행 후 exit() 시스템 콜을 통해
  - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
- 비자발적 종료
  - 부모 프로세스가 자식 프로세스를 강제로 종료시킴
    - 자식 프로세스가 한계치를 넘어서는 자원 요청
    - 자식에게 할당된 태스크가 더이상 필요하지 않음
  - 키보드로 kill, break 등을 친 경우
  - 부모가 종료하는 경우
    - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨

### 프로세스 간 협력

#### 독립적 프로세스

프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함.

#### 협력 프로세스

프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음

#### 프로세스간 협력 메커니즘 (IPC)

1. **메세지를 전달하는 방법 (message passing)**
   : 커널을 통해 메세지 전달

2. **주소 공간을 공유하는 방법 (sharing memory)**
   : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음

thread : thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들간에는 주소 공간을 공유하므로 협력이 가능

### Message Passing

프로세스 사이에 공유 변수를 일체 사용하지 않고 통신하는 시스템

![](https://i.imgur.com/jHX5j7Q.png)

#### Message passing 방법

1. Direct communication
   통신하려는 프로세스의 이름을 명시적으로 표시

2. Indirect Communication
   mailbox(또는 port)를 통해 메세지를 간접 전달

### Interprocess communication

![](https://i.imgur.com/bsQkTz8.png)

물리적인 메모리에 프로세스를 맵핑할 때, 공유되도록 해 서로 다른 프로세스간 같은 메모리 공간 사용

### CPU and I/O Bursts in Program Execution

![](https://i.imgur.com/U0SPjUi.png)

주로 사람과의 interaction이 많은 경우 I/O burst의 횟수가 늘어남

### CPU-burst Time의 분포

![](https://i.imgur.com/Bz0XRSI.png)

여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케쥴링이 필요 - interactive job에게 적절한 response 제공 요망 - CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

### 프로세스의 특성 분류

I/O bound job : CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
(many short CPU bursts)

CPU bound job : 계산 위주의 job
(few very long CPU burst)

### CPU Scheduler & Dispatcher

#### CPU Scheduler

Ready 상태의 프로세스 중 CPU를 줄 프로세스를 고르는 코드

#### Dispatcher

CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘기는 코드
이 과정을 **문맥 교환**이라고 한다.

CPU 스케쥴링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우

1. `Running` -> `Blocked` (I/O 요청 시스템 콜)
2. `Running` -> `Ready` (할당 시간 만료로 timer interrupt)
3. `Blocked` -> `Ready` (I/O 완료 후 인터셉트)
4. `Terminate`

1,4 에서의 스케쥴링 = nonpreemptive (강제로 빼앗지 않고 자진 반납)
다른 스케쥴링 = preemptive (강탈)
