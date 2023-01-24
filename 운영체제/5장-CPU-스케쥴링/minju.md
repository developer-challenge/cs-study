# CPU 스케쥴링

[CPU 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140328151311578473?vmode=f)

[CPU 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

1. CPU에서 intruction을 실행하는 단계 (CPU burst)
2. I/O 작업을 하는 단계 (I/O burst)

-> 모든 프로그램의 수행에서 반복됨.

컴퓨터 시스템에서 돌아가는 프로그램은 일정한 패턴으로 동작하지 않음
CPU burst에 들어온 프로그램 중 골라 CPU를 주는 것 = `CPU 스케쥴링`

### 2가지 이슈

- 여러 프로그램 중 어떤 프로그램에 CPU를 줄 것인가
- 결정된 프로그램에 계속 CPU를 줄 것인가, 중간에 뺏을 것인가.
  - 만약에 뺏지 않고 프로그램이 종료될 때까지 CPU 보장하는 경우, I/O bound jobs들이 오랫동안 기다려야함

### 2가지 스케쥴링 방법

- `nonpreemptive` : 강제로 CPU 강탈 ❌
- `preemptive` : 강제로 CPU 강탈 ⭕️

## 성능 척도

CPU 성능을 측정하는 방법

### 시스템 입장에서의 성능 척도

1. CPU utilization (이용률)
   - 최대한 일을 시켜 효율을 뽑아냄
2. Throughput (처리량)
   - 주어진 시간안에 몇개의 일을 처리했는지

### 프로그램 입장에서의 성능 척도

1. Turnaround time (소요시간, 반환시간)
   - CPU를 사용 대기 시작 ~ 종료 동안의 시간
2. Waiting time (대기 시간)
   - Ready queue에서 기다리는 시간
3. Response time (응답 시간)
   - Ready queue에 들어와서 처음으로 CPU를 얻기까지 걸리는 시간

> Waiting time vs Response time
> preemptive 스케줄링에서 `waiting time`은 빼앗긴 후 다시 CPU를 얻기까지 기다리는 시간 포함.

## CPU 스케줄링 알고리즘

### FCFS (first come first served)

- 먼저 온 순서대로 먼저 사용
- 비선점형 스케줄링 방법 -> 효율적이진 않음
- 앞에 어떤 프로세스가 있는지에 따라 `waiting time`에 영향

> **convoy effect**
> short process behind long process

### SJF (shortest job first)

- CPU 사용 시간 (CPU burst)이 가장 짧은 순서대로
- 두가지 스케쥴링 방법 모두 사용 가능
  - `nonpreemptive` : 일단 CPU를 잡으면 CPU burst가 완료될 때까지 CPU를 선점 당하지 않음
  - `preemptive` : 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가진 새로운 프로세스가 도착하면 CPU를 빼앗음 (SRTF, Shortest Remaining time first)
- SJF의 preemptive한 사용은 Average waiting time을 최소화
  - 어떤 스케쥴링 알고리즘을 사용하더라도 이보다 적은 waiting time이 나올 수 없음
- 단점
  - starvation
  - 프로세스의 CPU 사용시간을 미리 알 수 없음 (과거의 CPU burst time을 이용해 추정은 가능)

#### **starvation**

SJF 알고리즘의 단점 : 사용시간이 긴 프로세스는 계속 기다려야함.

### Priority scheduling

우선순위가 제일 높은 프로세스에게 CPU 제공

- SJF와 같이 두가지 스케쥴링 방법 모두 사용 가능
- 우선순위가 높을 수록 **작은 수**
- SJF도 일종의 priority scheduling
  - starvation 문제 공유

#### aging

우선 순위가 낮은 프로세스의 우선순위를 높여주는 방법

### Round Robin

- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐
- Preemptive 스케쥴링
- Responsive time이 빨라짐
  - 기다리면 적어도 한번은 기회가 옴
  - 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않음
- 대기시간과 CPU 사용시간이 비례
  - q large = FCFS
  - q small = context switch 오버헤드가 커짐
- SJF보다 average waiting time은 길 수 있지만, response time은 짧아짐.
- CPU 사용시간이 모두 동일한 프로그램의 경우, Round robin이 안좋을 수 있음

### Multilevel queue

![](https://i.imgur.com/k0HWIer.png)

- Ready queue를 여러개로 분할
  - foreground (interactive)
  - background (batch , 사람과의 인터랙션이 없는 작업)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - foreground : RR
  - background : FCFS
- 큐에 대한 스케줄링이 필요
  - fixed priority scheduling
  - time slice

### Multilevel Feedback Queue

기존 multilevel의 경우, 우선순위가 변경되지 않는 문제
이를 해결하기 위해 **프로세스가 다른 큐로 이동 가능**한 큐

#### Multilevel Feedback Queue를 정의하는 파라미터들

- Queue의 수
- 각 큐의 scheduling algorithm
- process를 상위 큐로 보내는 기준
- process를 하위 큐로 내쫒는 기준
- 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

### Multiple Process Scheduliing

CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐

#### Homogeneous processor인 경우

- 큐에 한줄로 세워 각 프로세서가 알아서 꺼내가도록
- 반드시 특정 프로세서에서 수행되어야 하는 프로세서가 있는 경우에 문제가 더 복잡

#### Load sharing

- 일부 프로세서에 job이 몰리지 않도로고 부하를 적절히 공유하는 메커니즘 필요
- 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법

#### Symmetric Multiprocessing (SMP)

- 각 프로세서가 각자 알아서 스케줄링 결정

#### Asymmetric multiprocessing

- 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

### Real-Time Scheduling

deadline이 있는 job -> 반드시 deadline을 보장해야함

#### Hard real-time systems

hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링 해야함

#### Soft real-time computing

soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야함. (deadline을 꼭 보장하지 않음)

### Thread Scheduling

#### Local Scheduling

user level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할 지 결정

사용자 프로세스가 **직접** 스레드를 관리해 운영체제가 이를 모름

#### Global Scheduling

Kernel level thread의 경우 운영체제가 스레드의 존재를 알고 있어 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정

## Algorithm Evaluation

### Queueing models

확률 분포로 주어지는 arrival rate와 service rate등을 통해 각종 performance index 값을 계산

### Implementation (구현) & Measurement (성능 측정)

실제 시스템에 알고리즘을 구현해 실제 작업에 대해 성능 측정 비교

Queueing models(이론)<-> Implementation & Measurement (실전)

### Simulation (모의 실험)

알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교

> `trace` : 실제 프로그램을 통해 추출한 input data
