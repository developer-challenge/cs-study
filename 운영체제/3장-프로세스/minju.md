## [프로세스 #1](https://core.ewha.ac.kr/publicview/C0101020140318134023355997?vmode=f)

### 프로세스의 개념

a program in execution
= 실행 중인 프로그램

#### 프로세스의 문맥(context)

> 과거의 프로그램이 실행되어 영향을 미친 현재 상태

- CPU 수행 상태를 나타내는 하드웨어 문맥
  - 현재 시점에 register가 어떤 값을 가지고 있었는가
  - Program Counter
  - 각종 register
- 프로세스의 주소 공간
  - 현재 시점의 주소공간
  - code, data, stack
- 프로세스 관련 커널 자료 구조
  - 운영체제의 역할 중 하나인 프로세스를 관리하는 역할을 수행하기 위해 데이터 영역에 자료구조를 가짐
  - PCB(Process Control Block)
  - Kernel stack

현재 문맥을 알고있지 않으면 다음 CPU를 잡았을 때 어느 부분에서 중단되었는지 알 수 없음

### 프로세스의 상태 (Process State)

프로세스는 상태가 변경되며 수행된다.

#### Running

: CPU를 잡고 instruction을 수행중인 상태

#### Ready

: CPU를 기다리는 상태 (메모리 등 다른 조건을 모두 만족하고)

#### Blocked(wait, sleep)

- CPU를 주어도 당장 instruction을 수행할 수 없는 상태
- Process 자신이 요청한 event가 즉시 만족되지 않아 기다리는 상태
- 디스크에서 file을 읽어와야하는 경우

#### New

프로세스가 생성중인 상태

#### Terminated

수행이 끝난 상태

![](https://i.imgur.com/v43MFwM.png)

CPU에서 Running중인 프로세스가 Disk에서 데이터를 읽어와야하는 경우, Blocked로 상태가 바뀌며 Disk I/O queue에 줄을 섬

![](https://i.imgur.com/tMnPpaY.png)

#### Suspended (stopped)

외부적인 이유로 프로세스의 수행이 정지된 상태

= 중기 스케줄러에 의해 CPU에서 쫒겨나게 된 경우

프로세스는 통째로 디스크에 swap out 된다.

ex) 사용자가 프로그램을 일시 정지시킨 경우 (break key) - 종료된 것은 아님
시스템이 여러 이유로 프로세스를 잠시 중단 시킴
(메모리에 너무 많은 프로세스가 올라와 있을 때)

> #### `Blocked` vs `Suspended`
>
> `Blocked` : 자신이 요청한 event가 만족되면 Ready
> `Suspended` : 외부에서 resume해 주어야 Active

### Process Control Block(PCB)

운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

![](https://i.imgur.com/tEF18AW.png)

#### 구성요소(구조체로 유지)

1. OS가 관리상 사용하는 정보
   - Process state, Process ID
   - scheduling information, priority
2. CPU 수행 관련 하드웨어 값
   - Program counter, registers
3. 메모리 관련
   - code, data, stack 의 위치 정보
4. 파일 관련
   - Open file descriptions..

### 문맥 교환 (Context Switch)

CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
**사용자 프로세스a -> 커널 -> 사용자 프로세스b** 인 경우에만 문맥교환이라고 볼 수 있음

사용자a -> 커널(운영체제) -> 사용자a는 같은 사용자 프로세스 중간에 interrupt가 발생한 것이므로 문맥교환이라고 볼 수 없음.

CPU가 다른 프로세스에게 넘어갈 때, 운영체제는 다음을 수행

    - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
    - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

System call이나 interrupt 발생시 반드시 context switch가 일어나는 것은 아님

![](https://i.imgur.com/FBDjGtN.png)

1의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 save 해야하지만, 문맥 교환을 하는 2의 경우 그 부담이 훨씬 큼 (`cache memory flush`)

### 프로세스를 스케줄링하기 위한 큐

![](https://i.imgur.com/egtV2yM.png)

ready queue에 있던 프로세스가 시간이 되면 CPU로 이동

인터럽트 발생 시에는 ready queue로 돌아가지 않고, 다른 처리가 됨.

### 스케줄러 (Scheduler)

#### Long-term scheduler (장기 스케줄러 / job scheduler)

- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
- 프로세스에 memory(및 각종 자원)을 주는 문제
- degree of Multiprogramming을 제어
- time sharing system에는 보통 장기 스케줄러가 없음 (무조건 ready)
- 프로그램이 시작될 때 메모리를 줄지말지 결정 (현재 시스템에선 일단 줌)

#### short-term scheduler (단기 스케줄러 / CPU scheduler)

- 어떤 프로세스를 다음번에 running 시킬지 결정
- 프로세스에 CPU를 주는 문제
- 충분히 빨라야 함 (ms 단위)

#### Medium-term scheduler (중기 스케줄러 or Swapper)

- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫒아냄
- 프로세스에게서 Memory를 뺏는 문제
- degree of multiprogramming을 제어
- 메모리에 너무 많은 프로그램이 동시에 올라가 있으면 메모리에서 통째로 쫒아냄

> 메모리에 프로그램이 너무 적거나 많아도 성능이 안좋아짐.

![](https://i.imgur.com/CrM5o1m.png)

> `inactive`: 외부적인 이유로 프로세스가 정지된 상태, 외부에서 active 로 변경되도록 해주어야 active될 수 있음

## [프로세스 #2](https://core.ewha.ac.kr/publicview/C0101020140321141759959993?vmode=f)

#### Q&A

1. **동기식 입출력과 비동기식 입출력의 차이**
   사용자 프로세스의 입출력 요청은 운영체제를 통해 가능
   프로세스가 입출력 요청이 끝날 때까지 기다려야하면 **동기식 입출력**
   입출력이 진행되는 동안에 intruction을 실행하면 **비동기식 입출력**

2. **동기식 입출력을 구현하는 방법**
   동기식 입출력은 I/O가 완료될 때까지 작업을 하지 못함. 이 때 **CPU를 가지고 있는가**에 따라 구현 방식이 두가지로 나뉨.

### Thread

CPU 실행 단위가 여러개 있는 경우

a thread (or lightweight process) is a basic unit of CPU utilization

#### thread의 구성

- program counter
- register set
- stack space

#### Thread가 동료 Thread와 공유하는 부분 (=task)

- code section
- data section
- OS resources

전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

같은 일을 하는 프로세스를 여려개 띄우고 싶은 경우 메모리 효율을 위해 메모리 공간을 하나만 띄우고 다른 부분의 코드를 실행할 수 있게 해주는 것 = `스레드`

프로세스 하나에 Program Counter만 여러개 두는 것.

#### 장점

**빠른 응답성** : 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(Running)되어 빠른 처리를 할 수 있다.

**메모리 절약** : 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(Throughput)과 성능 향상을 얻을 수 있다.

((CPU가 여러개 달린 컴퓨터에서)) 스레드를 사용하면 병렬성을 높일 수 있다.

스레드는 CPU와 관련된 정보만 각자 가지고 나머지는 다른 스레드와 서로 공유

## [프로세스 #3](https://core.ewha.ac.kr/publicview/C0101020140321143516139010?vmode=f)

### Single and Multithreaded Processes

![](https://i.imgur.com/4irRra6.png)

### Benefits of Threads

#### 1. 응답성

사용자 입장에서 빠름
웹 브라우저가 스레드를 여러개 갖게되면, 하나의 스레드가 멀리있는 서버에서 파일을 들고오는 동안(blocked) 다른 스레드가 실행됨.

#### 2. 자원 공유

하나의 프로세스를 만들고 그 안에 여러 스레드 생성
스레드는 binary code, data, 프로세스 리소스를 공유할 수 있음

#### 3. 경제성

프로세스를 생성 및 문맥교환하는 것보다 스레드를 생성 및 문맥교환 하는것이 오버헤드가 적음
대부분의 문맥교환을 그대로 사용 가능.

#### 4. (CPU가 여러개 있는 환경) Utilization of MP Architectures

각각의 스레드가 병렬적으로 동작 가능

### Implementation of Threads

어떤 스레드는 커널의 지원을 받고(`kernel threads`) 어떤 스레드는 라이브러리의 지원을 받음 (`user threads`).

> 프로세스 안에 스레드가 여러개 있다는 사실을 운영체제는 모름
