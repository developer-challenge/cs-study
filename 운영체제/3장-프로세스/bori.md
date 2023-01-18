# JDC-230118

# [Chapter 3. 프로세스 Part-1](https://core.ewha.ac.kr/publicview/C0101020140318134023355997?vmode=f)

## 프로세스의 개념

> "Process is a program in execution"
> ⇒ 프로세스란 실행 중인 프로그램을 의미

### 프로세스 문맥(context)

- 프로세스가 현재 어떤 상태에서 수행되고 있는지 정확히 규명하기 위해 필요한 정보
  ⇒ 프로세스의 주소 공간을 비롯해 레지스터에 어떤 값을 가지고 있었는지, 시스템 콜 등을 통해 커널에서 수행한 일의 상태, 그 프로세스에 관해 커널이 관리하고 있는 각종 정보 등을 포함한다.

#### 프로세스 문맥의 분류

- 하드웨어 문맥 : CPU 수행 상태는 나타내는 것
  - Program Counter 값
  - 각종 레지스터에 저장하고 있는 값
- 프로세스의 주소 공간 : code, data, stack
- 프로세스 관련 커널 자료 구조(커널상의 문맥)
  - PCB(Process Control Block)
  - Kernel stack

⇒ 여러 프로세스가 함께 수행되는 시분할 시스템 환경에서는 타이머 인터럽트에 의해 짧은 시간동안 CPU의 제어권을 넘겼다가 획득하는 방식으로 CPU 관리가 이루어진다. 따라서 CPU를 다시 획득하여 다음 인스트럭션을 실행하기 위해서는 프로세스의 문맥을 파악하여 프로세스가 어디까지 실행했는지를 정확한 상태를 알아야할 필요가 있다.

## 프로세스의 상태 (Process State)

![](https://i.imgur.com/xoUMZoq.png)

프로세스는 상태(state)가 변경되며 수행된다.

#### Running(실행)

- 프로세스가 CPU를 보유하고 인스트럭션을 수행 중인 상태

#### Ready(준비)

- 메모리 등 다른 조건을 모두 만족하고 CPU를 기다리고 있는 상태
- 프로세스가 CPU를 보유하면 당장 실행할 수 있지만 CPU를 할당받지 못한 상태

⇒ Ready 상태인 프로세스가 CPU를 잡았다 놨다 하면서 time sharing을 구현

#### Blocked(Wait/Sleep, 봉쇄)

- CPU를 획득해도 당장 인스트럭션을 수행할 수 없는 상태
- 프로세스 자신이 요청한 event(예: I/O)가 즉시 만족되지 않아 이를 기다리는 상태
  예) 디스크에서 파일을 읽어와야 하는 경우 시간이 오래 걸리는 작업을 하고 있으므로 CPU를 획득해도 당장 인스트럭션 수행이 불가능하다.

#### Suspended ⇒ 중기 스케줄러에 내용 작성

#### 그 외의 프로세스 상태

- New(시작)
  - 프로세스가 생성 중인 상태
  - 이미 프로세스가 생성이 되었다면 위의 세 가지 상태 중 하나의 상태
  - 프로세스가 시작되어 그 프로세스를 위한 각종 자료구조는 생성되었지만 아직 메모리 획득을 승인받지 못한 상태
- Terminated(완료)
  - 프로세스가 종료 중인 상태
  - 프로세스의 수행(execution)은 끝났지만 운영체제가 그 프로세스와 관련된 자료 구조를 완전히 정리하지 못한 상태

운영체제의 커널이 본인의 데이터 영역에 자료구조 큐를 만들어 놓고 프로세스의 상태를 바꿔가면서 운영

## Process Control Block(PCB, 프로세스 제어 블록)

- 운영체제가 각 프로세스를 관리하기 위해 프로세스마다 유지하는 정보
- 커널 주소 공간의 data 영역에 저장

#### PCB 구성 요소(구조체로 유지)

![](https://i.imgur.com/eFfE8GL.png)

(1) 운영체제가 관리상 사용하는 정보

- Process state(프로세스 상태), process ID(프로세스 번호)
- Scheduling information(스케줄링 정보), priority(우선순위)
  (2) CPU 수행 관련 하드웨어 값 : 프로세스의 문맥을 표시하기 위한 정보들 - Program counter, registers
  (3) 메모리 관련 - Code, data, stack의 위치 정보
  (4) 파일 관련 - Open file descriptors(프로세스가 오픈한 파일 정보)

## 문맥 교환 (Context Switch)

![](https://i.imgur.com/OnRdR88.png)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행
  - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

#### 시스템 콜이나 인터럽트 발생 시 반드시 문맥 교환이 일어나는 것은 아니다.

- CPU가 사용자 프로세스로부터 운영체제로 넘어가는 것을 문맥 교환이라고 하지 않는다.
  ⇒ 문맥 교환은 하나의 프로세스에서 다른 프로세스로 CPU가 넘어가는 과정을 의미하므로 이와 같은 경우는 하나의 프로세스의 실행 모드만 사용자 모드에서 커널 모드로 변경되는 것 뿐이다.
- 이와 같은 경우에도 CPU 수행 정보 등 프로세스의 문맥 중 일부를 PCB에 저장한다.

![](https://i.imgur.com/5nPXr4W.png)

- 프로세스가 실행되다가 타이머 인터럽트나 I/O 요청 시스템 콜을 인해 CPU가 해당 프로세스에서 운영체제를 거쳐 다른 프로세스로 넘어가는 경우에는 문맥 교환이 일어난다.
- 문맥 교환에는 모드 변경에 비해 훨씬 많은 \*오버헤드가 발생한다.

##### \* 오버헤드(overhead) : 특정 기능을 수행하는데 드는 간접적인 시간, 메모리 등 자원을 말한다. 예를 들어, 프로그램의 실행 흐름 도중에 동떨어진 위치의 코드를 실행시켜야 할 때 추가적으로 시간, 메모리, 자원 등이 사용되는 현상

![](https://i.imgur.com/R97ZLsS.png)

## 프로세스를 스케줄링하기 위한 큐

프로세스들은 각 큐들을 오가며 수행된다.

#### Job queue(작업 큐)

- 현재 시스템 내에 있는 모든 프로세스의 집합
- 시스템 내의 모든 프로세스를 관리하기 위한 큐로, 프로세스의 상태와 무관하게 현재 시스템 내에 있는 모든 프로세스가 작업 큐에 속하게 된다.
  ⇒ 작업 큐가 가장 넓은 개념이고 준비 큐와 장치 큐에 있는 프로세스들은 모두 작업 큐에 속한다.

#### Ready queue(준비 큐)

- 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합(ready)

#### Device queue(장치 큐)

- I/O 디바이스의 처리를 기다리는 프로세스의 집합(blocked)

#### Ready Queue와 다양항 Device Queue

![](https://i.imgur.com/d0Zl112.png)

#### 프로세스 스케줄링 큐의 모습

![](https://i.imgur.com/GmCJp6e.png)

## 스케줄러(Scheduler)

어떤 프로세스에게 자원을 할당할지 결정하는 운영체제 커널의 코드를 지칭

### Short-term scheduler(단기 스케줄러, CPU scheduler)

- 준비 상태의 프로세스 중 어떤 프로세스를 다음번에 실행(running) 상태로 만들 것인지 결정
- 프로세스에 CPU를 주는 문제에 관여
- 충분히 빨라야 한다(millisecond 단위)

### Long-term scheduler(장기 스케줄러, Job scheduler)

- 시작 프로세스 중 어떤 프로세스를 ready queue로 보낼지 결정
  - new 상태의 프로세스 중 메모리를 줄 프로세스를 결정
- 프로세스 메모리를 할당하는 문제에 관여
  - 준비 큐는 CPU만 얻으면 당장 실행될 수 있는 프로세스의 집합으로 CPU에서 실행되기 위해서는 프로세스가 메모리를 보유해야 하므로
- 메모리에 동시에 올라가 있는 프로세스의 수(degree of multiprogramming)를 조절하는 역할
- 현대의 시분할 시스템에는 보통 장기 스케줄러가 없다.
  - 프로세스가 시작 상태가 되면 장기 스케줄러 없이 바로 프로세스에 메모리를 할당해 준비 큐에 넣어준다.

> #### 그럼 지금 우리가 사용하는 시스템이 degree of multiprogramming을 어떻게 조절하나요?
>
> Medium-term scheduler

### Medium-term scheduler(중기 스케줄러, Swapper)

- 여유 공간을 만들기 위해 메모리에 올라와 있는 프로세스 중 일부를 선정해 통째로 메모리에서 디스크(스왑 영역)로 쫒아냄
- 프로세스에게서 메모리를 뺏는 문제에 관여
- Degree of multiprogramming을 제어

### 프로세스 상태 - Suspended

#### Suspended(stopped, 중지)

- 중기 스케줄러의 등장으로 인해 추가된 프로세스 상태
- 외부적인 이유로 프로세스의 수행이 정지된 상태
- 프로세스는 통째로 디스크에 swap out된다.
  예) 사용자가 프로그램을 일시정지 시킨 경우(break key) 시스템이 여러 이유로 프로세스를 잠시 중단시킴(메모리에 너무 많은 프로세스가 올라와 있을 때)

⇒ 메모리를 완전히 잃어버리는 상태를 의미하고 CPU 관점에서 아무일을 할 수 없다. 하지만 I/O 같은 작업은 진행할 수 있다.

> **Blocked** vs **Suspended**
>
> - Blocked: 자신이 요청한 이벤트가 만족되면 ready 상태가 된다.
> - Suspended: 외부에서 재개(resume)해주어야 active 상태로 넘어갈 수 있다.

#### 중지 상태를 포함한 프로세스의 상태 변화도

![](https://i.imgur.com/ke3kgeI.png)

# [Chapter 3. 프로세스 Part-2](https://core.ewha.ac.kr/publicview/C0101020140321141759959993?vmode=f)

## Thread

> "A thread(or lightweight process) is a basic unit of CPU utilization"
> ⇒ 스레드는 CPU 사용의 기본 단위

- 프로세스 내부에 CPU 수행 단위가 여러 개 있는 경우 그것을 Thread라고 부른다.
  ⇒ 하나의 프로세스에 프로그램 카운터(CPU 사용 단위)만 여러 개 두는 것

![](https://i.imgur.com/Lm7SzMO.png)

#### 스레드의 구성

- Program counter
- Register set
- Stack space

⇒ CPU 수행에 관한 정보만 스레드마다 다르게 가지고 있다.

#### 스레드가 동료 스레드와 공유하는 부분(= task)

- Code section
- Data section
- OS resources

⇒ 프로세스 하나에서 공유할 수 있는 것을 최대한 공유한다. (메모리 주소 공간, 프로세스 상태, 프로세스 자원 공유)

> 프로세스를 별도로 두는 것보다 프로세스 안에 스레드를 여러 개 두는 것이 훨씬 가볍기 때문에 프로세스를 스레드로 관리
>
> - 스레드를 lightweight process 라고도 부른다.
> - 전통적인 개념의 heavyweight process는 하나의 스레드를 가지고 있는 task로 볼 수 있다.

![](https://i.imgur.com/B4ONwp1.png)

#### Single and Multithreaded Processes

![](https://i.imgur.com/7ghJPja.png)

# [Chapter 3. 프로세스 Part-3](https://core.ewha.ac.kr/publicview/C0101020140321143516139010?vmode=f)

## Thread의 장점

#### Responsiveness(응답성)

- 사용자 입장에서 응답이 빠르다.
- 다중 스레드로 구성된 웹 브라우저의 경우, 하나의 스레드는 이미지나 추가 데이터를 받기 위해 웹 서버에 요청하면 해당 스레드만 blocked 상태가 된다. 이때 다른 스레드는 이미 불러온 html 텍스트를 화면에 먼저 출력할 수 있다.
  ⇒ 일종의 비동기식 입출력
- 하나의 프로세스(태스크) 안에 스레드가 여러 개일 경우(다중 스레드) 스레드 하나가 blocked 상태일 때 다른 스레드가 CPU를 잡고 running 할 수 있어서 빠른 처리가 가능하다.

#### Resource Sharing(자원 공유)

- 하나의 프로세스만 만들고 그 안에 CPU 수행 단위만 여러 개 둔다면 코드, 데이터, 각종 자원을 스레드들이 공유할 수 있다.
- 동일한 작업을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput), 성능 향상을 얻을 수 있고 자원을 절약할 수 있다.
  ⇒ 동일한 작업을 하는 프로세스가 여러 개 생성될 경우 독자적인 주소 공간을 여러 개 만들게 되므로 메모리를 차지하게 되어 자원을 낭비하게 된다.

#### Economy(경제성)

- 프로세스를 하나 만드는 것은 오버헤드가 크지만 프로세스 하나 안에 스레드를 여러 개 두는 것은 숟가락만 얹는 것이므로 오버헤드가 크지 않다.
- 프로세스 간의 문맥 교환은 오버헤드가 크지만 스레드 간의 CPU switching은 오버헤드가 크지 않다.

#### Utilization of MP Architectures(CPU가 여러 개인 아키텍쳐)

- 각 스레드들이 다른 CPU에서 병렬적으로 실행되면 결과를 빨리 얻을 수 있다.

## Thread 구현 방법

#### Kernel Threads

- 운영체제의 커널에 지원을 받고 있는 것
- 프로세스 안에 스레드가 여러 개인 것을 운영체제가 상태로 하나의 스레드에서 다른 스레드로 CPU가 넘어가는 것을 커널이 CPU 스케줄링 하듯 넘겨줄 수 있다.
  - Windows 95/98/NT
  - Solaris
  - Digital Unix, Mach

#### User Threads

- 라이브러리 통해 지원을 받고 있는 것
- 프로세스 안에 스레드가 여러 개인 것을 운영체제는 모르는 상태로, 사용자 프로그램이 스스로 관리하는 것
  ⇒ 커널은 모르는 상태이므로 구현상의 제약점이 있을 수 있다. - POSIX Pthreads - Mach C-threads - Solaris threads

#### Real-time Threads

- 리얼타임을 지원하는 스레드
