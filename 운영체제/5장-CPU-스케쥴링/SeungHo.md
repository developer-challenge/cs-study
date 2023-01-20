# 5장 CPU 스케줄링

## [CPU 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140328151311578473?vmode=f)

### CPU 스케줄링 성능 척도

**시스템 입장에서의 성능 척도**

**CPU utilization (이용률) - 전체 시간 중에서 CPU가 놀지 않고 일한 시간의 비율**

- keep the CPU as busy as possible

**Throughput (처리량) - 주어진 시간 동안에 몇 개의 일을 처리했는지**

- #of processes that complete their execution per time unit

---

**프로그램 입장에서의 성능 척도**

**Turnaround time (소요시간, 변환시간) - CPU를 쓰려고 기다리는 시간 + 다쓰고 나갈 때 까지의 시간**

- amount of time to execute a particular process

**Waiting time (대기 시간) - Ready queue에서 CPU를 사용하려고 기다리는 시간**

- amount of time a process has been waiting in the ready queue

**Response time (응답 시간) - CPU를 사용하려고 들어와서 처음으로 CPU를 얻기까지 걸리는 시간**

- amount of time it takes from when a request was submitted until the first response is produced, not output (for time-sharing environment)

### Scheduling Algorithms

### FCFS (First-Come First-Served)

먼저 온 고객을 먼저 서비스 해주는 알고리즘

프로세스 도착 순서 : P1 → P2 → P3

![](https://i.imgur.com/KoYWwyT.png)

Average waiting time : (0 + 24 + 27)/3 = 17

프로세스 도착 순서 : P2 → P3 → P1

![](https://i.imgur.com/DujeGDC.png)

Average waiting time : (0 + 3 + 6)/3 = 3

**Convoy Effect** : short process behind long process

### SJF (Shortest-Job-First)

- 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용
- CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄
- Two schemes

  ![](https://i.imgur.com/1DJARxO.png)

  - **Nonpreemptive**
    - 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점(preemption) 당하지 않음
    Average waiting time = (0 + 6 + 3 + 7)/4 = 4

  ***

  ![](https://i.imgur.com/eIPJtVE.png)

  - **Preemptive**
    - 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
    - 이 방법을 Shortest-Remaining-Time-First (SRTF) 이라고도 부름
    Average waiting time = (9 + 1 + 0 + 2)/4 = 3

- SJF is optimal
  - 주어진 프로세스들에 대해 minimum average waiting time을 보장(Preemptive)

**문제점**

- **Starvation 문제 -** low priority processes may never execute

  높은 우선순위를 가진 작업이 계속해서 추가될 경우 낮은 우선순위의 작업은 실행될 수가 없음

- **다음 CPU Burst Time의 예측**
  - 다음번 CPU burst time을 어떻게 알 수 있는가 (input data, branch, user …)
  - 추정(estimate)만이 가능
  - 과거의 CPU burst time을 이용해서 추정(exponential averaging)

### Priority Scheduling

- A **priority number** (integer) is associated with each process
- **highest priority**를 가진 프로세스에게 CPU 할당
  (smallest integer - highest priority)
  - Preemptive
  - nonpreempitve
- SJF는 일종의 priority scheduling
  - priority = predicted next CPU burst time
- Problem
  - **Starvation(기아 현상)** : low priority processes may never execute
- Solution
  - **Aging(노화)** : as time progresses increase the priority of the process

### Round Robin (RR)

- 각 프로세스는 동일한 크기의 할당 시간(**time quantum**)을 가짐 (일반적으로 10-100 milliseconds)
- 할당 시간이 지나면 프로세스는 선점(preempted)당하고 ready queue의 제일 뒤에 가서 다시 줄을 섬
- n 개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻음
  ⇒ **어떤 프로세스도 (n-1)q time unit 이상 기다리지 않음**
- Performance
  - q(할당 시간) large ⇒ FCFS
  - q(할당 시간) small ⇒ context switch 오버헤드가 커짐
- **Response time (응답시간)이 빨라짐**
- CPU를 길게 쓰는 프로세스는 그에 비례하여 기다리는 시간이 길어짐

**RR with Time Quantum = 20**

![](https://i.imgur.com/dptZoNf.png)

동일한 시간의 프로세스가 여러개일 경우 할당 시간이 작으면 마지막 순간에 모든 프로세스가 동시에 완료되는 현상이 발생할 수 있음

일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧음

## [CPU 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

### Multilevel Queue

![](https://i.imgur.com/z4UdT5u.png)

- Ready queue를 여러 개로 분할
  - **foreground** (interactive)
  - **background** (batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - **foreground** - RR
  - **background** - FCFS
- 큐에 대한 스케줄링이 필요
  - **Fixed priority scheduling**
    - serve all from foreground then from background
    - Possibility of starvation
  - **Time slice**
    - 각 큐에 CPU time을 적절한 비율로 할당
    - Eg.. 80% to foreground in RR, 20% to background in FCFS

### Multilevel Feedback Queue

![](https://i.imgur.com/qCm8DVc.png)

보통 처음 들어오는 프로세스는 우선순위가 가장 높은 큐(Round Robin에서 Time quantum을 가장 짧게 줌 → 밑에 큐에 갈 수록 quantum 값 길게주고 제일 아래의 큐는 FCFS방식)에 넣고

Scheduling

1. new job이 queue Q0로 들어감
2. CPU를 잡아서 할당 시간 8 milliseconds 동안 수행됨
3. 8 milliseconds 동안 다 끝내지 못했으면 queue Q1으로 내려감
4. Q1에 줄서서 기다렸다가 CPU를 잡아서 16ms 동안 수행됨
5. 16ms에 끝내지 못한 경우 queue Q2로 쫓겨남

- 프로세스가 다른 큐로 이동 가능
- 에이징(aging)을 이와 같은 방식으로 구현할 수 있음
- Multilevel-feedback-queue scheduler를 정의하는 파라미터들
  - **Queue의 수**
  - **각 큐의 scheduling algorithm**
  - **Process를 상위 큐로 보내는 기준**
  - **Process를 하위 큐로 내쫓는 기준**
  - **프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준**

### Multiple-Processor Scheduling

- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- **Homogeneous processor인 경우**
  - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있음
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
- **Load sharing**
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
  - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- **Symmetric Multiprocessing (SMP)**
  - 각 프로세서가 각자 알아서 스케줄링 결정
- **Asymmetric multiprocessing**
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

### Real-Time Scheduling

**********\*\***********Hard real-time systems**********\*\***********

- Hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함

**\*\***Soft real-time computing**\*\***

- Soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

### Thread Scheduling

****************\*\*****************Local Scheduling****************\*\*****************

- User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정

****************\*\*****************Global Scheduling****************\*\*****************

- Kerner level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정

### Algorithm Evaluation - 알고리즘 평가 방법

![](https://i.imgur.com/OzYTk6Z.png)

**Queueing models**

- 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

**Implementation (구현) & Measurement (성능 측정)**

- 실제 시스템에 알고리즘을 구현하여 실제 작업 (workload)에 대해서 성능을 측정 비교

**Simulation (모의 실험)**

- 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
