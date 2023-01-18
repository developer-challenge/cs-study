## [프로세스 #1](https://core.ewha.ac.kr/publicview/C0101020140318134023355997?vmode=f)

### 프로세스의 개념

“Process is **a program in execution**” - **실행중인 프로그램**

프로세스의 문맥(context)

- CPU 수행 상태를 나타내는 하드웨어 문맥
  - Program Counter
  - 각종 register
- 프로세스의 주소 공간
  - code, data, stack
- 프로세스 관련 커널 자료 구조
  - PCB (Process Control Block)
  - Kerner stack

### 프로세스의 상태 (Process State)

![](https://i.imgur.com/IUbOvme.png)

프로세스는 상태(state)가 변경되며 수행됨

- **Running**
  - CPU를 잡고 instruction을 수행중인 상태
- **Ready**
  - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고)
- **Blocked (wait, sleep)**
  - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
  - Process 자신이 요청한 event(예:I/O)가 즉시 만족되지 않아 이를 기다리는 상태
    ex) 디스크에서 file을 읽어와야 하는 경우
- **Suspended (stopped)**
  - 외부적인 이유로 프로세스의 수행이 정지된 상태
  - 프로세스는 통째로 디스크에 swap out 됨
    ex) 사용자가 프로그램을 일시 정지시킨 경우 (break key),
    시스템이 여러 이유로 프로세스를 잠시 중단시킴(메모리에 너무 많은 프로세스가 올라와 있을 때)

**Blocked** : 자신이 요청한 event가 만족되면 Ready

**Suspended** : 외부에서 resume해 주어야 Active

New : 프로세스가 생성중인 상태

Terminated : 수행(execution)이 끝난 상태

**프로세스 상태도**

![](https://i.imgur.com/jb2Jrrn.png)

**\*\***\*\***\*\***Suspended 상태 추가**\*\***\*\***\*\***

![](https://i.imgur.com/GKfF2eB.png)

Running(monitor mode) → 프로세스가 System call을 해서 운영체제 code가 실행 중일 때 사용자 프로세스가 커널모드에서 Running하고 있다고 표현 (운영체제가 Running하고 있다고 표현 X)

### Process Control Block(PCB)

운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

![](https://i.imgur.com/gBg5qI5.png)

구성요소(구조체로 유지)

(1) OS가 관리상 사용하는 정보

- Process state, Process ID
- scheduling information, priority

(2) CPU 수행 관련 하드웨어 값

- Program counter, registers

(3) 메모리 관련

- code, data, stack의 위치 정보

(4) 파일 관련

- Open file descriptors…

### 문맥 교환 (Context Switch)

![](https://i.imgur.com/zxaem6N.png)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행
  - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

![](https://i.imgur.com/1nHgm26.png)

System call이나 Interrupt 발생 시 반드시 context switch가 일어나는 것은 아님

→ 사용자 프로세스로 부터 운영체제로 넘어감 ⇒ context switch가 아님

→ System call이나 Interrupt 발생 후 운영체제가 무언가를 처리한 후 발생하기 이전의 프로세스에 다시 CPU가 넘어감 ⇒ context switch가 아님

System call이나 Interrupt가 발생한 이후에 운영체제가 CPU를 다른 프로세스에 넘겨주는 것은 context switch

(1)의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 save해야 하지만 문맥교환을 하는 (2)의 경우 그 부담이 훨씬 큼(eg. cache memory flush ⇒ context switch가 일어나면 cache 내용을 지워야함)

### 프로세스를 스케줄링하기 위한 큐

![](https://i.imgur.com/AlxwPiw.png)

**\*\*\*\***\*\***\*\*\*\***Job queue**\*\*\*\***\*\***\*\*\*\***

- 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready queue, Device queue 모두 Job queue에 속함

Ready queue

- 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합

Device queues

- I/O device의 처리를 기다리는 프로세스의 집합

프로세스들은 각 큐들을 오가며 수행됨

![](https://i.imgur.com/j3zojBw.png)

프로그램이 시작이 되면 ready queue에 줄을 서게 됨

→ 본인 차례가 되면 CPU를 얻게 됨

→ I/O처리 or 할당된 시간 만료 or 자식 프로세스 or 인터럽트 일 경우 ready queue에 줄을 서게 됨

- 인터럽트의 경우 다시 ready queue 상태로 가는 것은 아님

### 스케줄러 (Scheduler)

**Long-term scheduler (장기 스케줄러 or job scheduler)**

- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
- 프로세스에 memory(및 각종 자원)을 주는 문제
- degree of Multiprogramming(메모리에 올라가 있는 프로세스의 수)을 제어
- **time sharing system에는 보통 장기 스케줄러가 없음(무조건 ready)**

**Short-term scheduler (단기 스케줄러 or CPU scheduler)**

- 어떤 프로세스를 다음번에 running 시킬지 결정
- 프로세스에 CPU를 주는 문제
- 충분히 빨라야 함(millisecond 단위)

**Medium-Term scheduler (중기 스케줄러 or Swapper)**

- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄 → **Suspended 상태**
- 프로세스에게서 memory를 뺏는 문제
- degree of Multiprogramming을 제어

## [프로세스 #2](https://core.ewha.ac.kr/publicview/C0101020140321141759959993?vmode=f)

### Thread

![](https://i.imgur.com/wjRYaw4.png)

“A **thread** (or **lightweight process**) is a basic unit of CPU utilization”

**프로세스 내부의 CPU 수행 단위가 여러 개 있는 경우**

![](https://i.imgur.com/nfKJ3Rk.png)

CPU가 code의 어느 부분만 실행하고 있는지 program counter만 여러개 두는, 즉 프로세스 하나에 CPU 수행 단위만 여러개 두고 있는 것

thread 하나가 code 어느 부분을 실행하다가 함수 부분을 호출하면 stack에 쌓이게 됨 → stack도 별도로 구분해야함

thread는 프로세스 하나에서 공유할 수 있는 것은 최대한 공유

- 메모리 주소
- 프로세스 상태
- 프로세스가 사용하는 각종 자원

**Thread의 구성** → 각 thread 별로 갖고 있는 것

- program counter
- register set
- stack space

Thread가 동료 thread와 **공유하는 부분** (task ⇒ thread가 여러개일 때 task는 하나)

- code section
- data section
- OS resources

전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있음

**\*\*\*\***장점**\*\*\*\***

- 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있음
- 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있음
  → 동일한 작업을 별도의 프로세스로 만들면 메모리, 자원 낭비(독자적인 주소 공간)
- 스레드를 사용하면 병렬성을 높일 수 있음(CPU가 여러 개 달린 컴퓨터에서만 얻을 수 있는 장점)

## 프로세스 #3

### Single and Multithreaded Processes

![](https://i.imgur.com/bRfU8lN.png)

### Benefits of Threads

**응답성 (Responsiveness)** - 사용자 입장에서 빠름

eg) multi-threaded Web - if one thread is blocked (network) another thread continues (eg display)

**자원 공유 (Resource Sharing)** - 자원을 효율적

- n threads can share binary code, data, resource of the process

**경제성 (Economy)** - 프로세스를 하나 만드는 것의 오버헤드는 쓰레드 하나를 만드는 것 보다 훨씬 큼

- creating & CPU switching thread (rather than a process)
- Solaris의 경우 위 두 가지 overhead가 각각 30배, 5배

**Utilization of MP(multi-processor) Architectures** - CPU가 여러 개 있는 환경에서 얻을 수 있는 장점

- each thread may be running in **parallel** on a different processor

### Implementation of Threads

Some are supoorted by **kerner ⇒ Kernel Threads**

Thread가 여러개 있다는 사실을 운영체제 커널이 알고있음 → 하나의 thread에서 다른 thread로 CPU가 넘어가는 것도 커널이 CPU 스케줄링하듯이 넘겨줌

- Windows 95/98/NT
- Solaris
- Digital UNIX, Mach

Others are supported by **library ⇒ User Threads**

프로세스안에 thread가 여러 개 있다는 사실을 운영체제는 모름 → 유저 프로그램이 라이브러리의 지원을 받아 스스로 여러개의 thread를 관리

- POSIX Pthreads
- Mach C-threads
- Solaris threads

Some are real-time threads
