# [Chapter 5. CPU 스케쥴링 Part-1](https://core.ewha.ac.kr/publicview/C0101020140328151311578473?vmode=f)

CPU를 어떤 프로세스에게 얼마만큼 할당할 것인지 결정

## Scheduling Criteria

**스케줄링 알고리즘의 성능을 평가하는 방법**으로 Performace index(= Performace measure, 성능 척도)라고도 부른다.
크게 시스템 입장의 성능 척도와 프로그램 입장의 성능 척도로 분류할 수 있다.

### 시스템 입장의 성능 척도

#### CPU utilization(이용률)

- Keep the CPU as busy as possible
- 전체 시간에서 CPU가 일한 시간의 비율

#### Throughput(처리량)

- Number of proccess that complete their execution per time unit
- 주어진 시간 동안 CPU가 프로세스를 처리한 갯수

### 프로그램(프로세스) 입장의 성능 척도

#### Turnaround time(소요시간, 반환시간)

- Amount of time to execute a particular process
- CPU를 사용하러 들어와서 다 쓰고 나갈 때까지의 소요 시간

#### Waiting time(대기 시간)

- Amount of thime a process has been waiting in the ready queue
- 준비 큐에서 CPU를 할당받기 위해 기다리는 시간

#### Response time(응답 시간)

- Amount of time it takes from when a request was submitted util the **first response** is produced. Not output(for time-sharing environment)
- 준비 큐에 들어가서 처음으로 CPU를 얻기까지 걸린시간

## Scheduling Algorithms

### FCFS(First-Come First-Served)

- 먼저 들어온 순서대로 처리하는 방법(비선점형)
- CPU를 오래 사용하는 프로세스가 먼저 도착하여 CPU를 할당 받는 경우, 해당 프로세스가 CPU를 반환하기까지 나머지 프로세스들은 전부 기다려야하므로 효율적이지 않음
  ⇒ **Convoy effect** : 긴 프로세스가 먼저 도착해서 짧은 프로세스들이 지나치게 오래 기다려야 하는 현상
- CPU를 짧게 사용하는 프로세스가 먼저 도착한 경우, 전체 대기 시간이 굉장히 짧아진다.

⇒ 어떤 프로세스가 먼저 CPU를 할당 받느냐에 따라서 대기 시간에 영향을 미친다.

### SJF(Shortest-Job-First)

- CPU burst 시간이 가장 짧은 프로세스를 먼저 처리하는 방법
- 각 프로세스의 다음 CPU burst 시간을 가지고 스케줄링에 활용
- 평균 대기 시간을 가장 짧게 하는 최적의 알고리즘
- Non-Preemptive SJF
  - 일단 CPU를 잡으면 해당 CPU burst가 완료될 때까지 CPU 사용권을 보장
  - CPU 다 쓰고 나가는 시점에 스케줄링
- Preemptive SJF(최적의 알고리즘)
  - 현재 실행중인 프로세스의 남은 CPU burst 시간보다 짧은 CPU burst 시간을 가지는 프로세스가 도착하면 CPU를 빼앗김
    ⇒ SRTF(Shortest-Remaining-Time-First)라고도 부름
  - 새로운 프로세스가 도착하면 언제든지 스케줄링
- 문제점
  - Starvation(기아 현상) : CPU burst 시간이 긴 프로세스는 영원히 CPU를 할당받지 못할 수 있다.
  - CPU 사용시간을 미리 알 수 없다. 단, 과거의 CPU 사용 이력을 통해 예측할 수 있다.

#### Exponential average

- CPU burst 시간 예측 방법으로 과거의 CPU burst 시간을 이용하여 추정
  - 𝑡<sub>n</sub> : n번째의 실제 CPU 사용 시간
  - 𝜏<sub>n + 1</sub> : (n + 1)번째의 CPU 사용을 예측한 시간(𝑡<sub>1</sub> ~ 𝑡<sub>n</sub>까지의 시간은 주어진 상태)
  - 𝛂, 0 ≤ 𝛂 ≤ 1
  - 𝜏<sub>n + 1</sub> = 𝛂𝑡<sub>n</sub> + (1 - 𝛂)𝜏<sub>n</sub>

### Priority Scheduling

- 우선순위가 가장 높은 프로세스에서 CPU를 할당하는 방법
- 우선순위를 나타내는 숫자값이 정수로 부여된다.
  - 우선순위가 높을 수록 값이 작아진다.
- Non-Preemptive Priority Scheduling
  - 더 높은 우선순위의 프로세스가 도착하더라도 실행중인 프로세스의 CPU 사용권을 보장
- Preemptive Priority Scheduling
  - 더 높은 우선순위의 프로세스가 도착하면 CPU를 빼앗김
- SJF는 일종의 Priority Scheduling이다.
  ⇒ 우선순위(Priority) = 다음 CPU burst 시간
- 문제점
  - Starvation(기아 현상) : 우선순위가 낮은 프로세스가 영원히 CPU 할당을 받지 못할 수 있다.
- 해결 방법
  - Aging(노화): 우선순위가 낮은 프로세스의 대기 시간이 길어지면 우선순위를 높여주는 기법

### Round Robin(RR)

- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐
  - 일반적으로 10 - 100 milliseconds
- 할당 시간이 지나면 프로세스는 선점 당하고 준비 큐의 맨 뒤에 가서 다시 줄을 선다.
- 현재 준비 큐에 n 개의 프로세스가 있고 할당 시간이 `q`(time unit)인 경우, 각 프로세스는 최대 `q` 단위로 CPU 시간의 `1 / n`을 얻는다
  ⇒ 어떤 프로세스도 `(n - 1)q` 이상 기다리지 않는다.
- 프로세스의 대기 시간이 CPU 사용 시간에 비례
  - CPU 사용 시간이 긴 프로세스는 그만큼 대기 시간이 길어진다.
- 장점
  - 응답시간이 빨라진다.
  - 어떤 프로세스가 CPU를 오래 사용할 지 모르는 상황에서 예측할 필요가 없다.
    ⇒ CPU 사용 시간이 짧은 프로세스와 긴 프로세스가 섞여 있을 때 사용하기 좋은 스케줄링. CPU 사용 시간이 모두 동일한 경우 비효율적이다.
- Performance
  - `q` large ⇒ FCFS
  - `q` small ⇒ 문맥 교환이 빈번해지면서 context switch 오버헤드가 커진다.
- 일반적으로 SJF보다 평균 소요 시간은 길지만 응답 시간은 짧다.

# [Chapter 5. CPU 스케쥴링 Part-2](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

## Multilevel Queue

![](https://i.imgur.com/8aoRwSH.png)

- 준비 큐를 여러 개로 분할하고 각 큐는 독립적인 스케줄링 알고리즘을 가진다.
  - foreground(interactive) : RR
  - background(batch - no human interaction) : FCFS
- 큐가 여러 개로 분할되어 있으므로 어떤 큐에 CPU를 줄 것인지 큐에 대한 스케줄링이 필요
  - Fixed priority scheduling : 우선순위를 강하게 적용하는 방식
    - 우선순위가 높은 큐가 비어있을 때 우선순위가 낮은 큐에 CPU를 할당하는 방법
    - 기아 현상이 발생할 수 있다.
  - Time slice
    - 각 큐에 CPU 시간을 적절한 비율로 할당
    - e.g. 80% to foreground in RR, 20% to background in FCFS

## Multilevel Feedback Queue

- 멀티 레벨 큐처럼 준비 큐가 여러 개로 분할되어 있으나 프로세스가 다른 큐로 이동이 가능한 방식
- 에이징(aging)을 이와 같은 방식으로 구현할 수 있다.
- 멀티 레벨 피드백 큐 스케줄러를 정의하는 파라미터들
  - 큐의 수
  - 각 큐의 스케줄링 알고리즘
  - 프로세스를 상위 큐로 보내는 기준
  - 프로세스를 하위 큐로 내쫒는 기준
  - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
- 일반적으로 처음 들어오는 프로세스는 우선순위가 높은 큐에 넣고 우선순위가 높은 큐는 라운드 로빈 방식을 이용하여 할당 시간을 짧게 설정한다.
- 하위 큐로 갈수록 라운드 로빈 방식의 할당 시간을 점점 길게 설정하고, 최하위의 큐에는 FCFS 방식을 사용한다.
- 상위 큐에서 할당 시간에 프로세스가 완료되지 않으면 하위 큐로 보내지는 방식
  ⇒ CPU 버스트가 짧은 프로세스에게 우선순위를 높게 주는 방식
- 멀티 레벨 피드백 큐는 CPU 사용시간을 예측할 필요가 없다.

## Multi-Processor Scheduling(다중처리기 스케줄링)

- Multi-Processor System(다중처리기 시스템) : CPU가 여러 개인 시스템
- CPU(프로세서)가 여러 개인 경우의 스케줄링
- Homogeneous processor인 경우
  - 큐에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 복잡해진다.
- Load sharing(Load balancing)
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘
  - 별개의 큐를 두는 방법과 공동 큐를 사용하는 방법이 있다.
- Symmetric Multiprocessing(SMP, 대칭형 다중처리) : 각 프로세서가 알아서 스케줄링 결정
- Asymmetric Multiprocessing(비대칭형 다중처리) : 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따르는 방식

## Real-Time Scheduling(실시간 스케줄링)

- Real-Time System(실시간 시스템) : 각 작업마다 주어진 데드라인이 있어 정해진 데드라인 안에 반드시 작업을 처리해야 한다.
- Hard real-time system(경성 실시간 시스템)
  - 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함
  - 예: 미사일 발사,, 원자로 제어 등
- Soft real-time system(연성 실시간 시스템)
  - 데드라인이 존재하지만 데드라인을 지키지 못했다고 해서 위험한 상황이 발생하지 않는 job을 스케줄링
  - 일반 프로세스에 비해 높은 우선순위를 할당
  - 예: 멀티미디어 스트리밍 시스템 등

## Thread Scheduling

#### Local Scheduling

- User level thread의 경우 사용자 수준의 스레드 라이브러리에 의해 어떤 스레드를 스케줄할 지 결정
- 운영체제는 해당 프로세스에 CPU의 할당 여부를 결정하고, 프로세스에 CPU가 할당되면 프로세스 내부의 스레드에게 CPU할당하는 것은 프로세스 내부에서 결정

> #### User Threads
>
> - 라이브러리 통해 지원을 받고 있는 것
> - 프로세스 안에 스레드가 여러 개인 것을 운영체제는 모르는 상태로, 사용자 프로그램이 스스로 관리하는 것

#### Global Scheduling

- Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 스레드를 스케줄할 지 결정

> #### Kernel Threads
>
> - 운영체제의 커널에 지원을 받고 있는 것
> - 프로세스 안에 스레드가 여러 개인 것을 운영체제가 아는 상태로 하나의 스레드에서 다른 스레드로 CPU가 넘어가는 것을 커널이 CPU 스케줄링 하듯 넘겨줄 수 있다.

## Algorithm Evaluation(스케줄링 알고리즘의 평가)

스케줄링 알고리즘의 성능을 평가하는 방법

#### Queueing models

![](https://i.imgur.com/Qvhuh4C.png)

- 주로 이론가들이 수행하는 방식
- 확률 분포로 주어지는 프로세스들의 도착률(arrive rate)과 CPU의 처리율(service rate) 등을 통해 각종 성능지표(performance index)값을 계산

#### Implementation & Measurement(구현 및 실측)

- 이론가와 정반대인 구현가들이 수행할 수 있는 방식
- 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 비교
- 동일한 프로그램을 원래 커널과 CPU 스케줄러를 수정한 커널에서 수행시켜보고 실행시간을 측정하여 알고리즘의 성능을 평가

#### Simulation(시뮬레잉션, 모의 실험)

- 알고리즘을 모의 프로그램으로 작성 후 \*트레이스를 입력으로 하여 결과 비교
- 실제 시스템에 구현해 수행시켜보는 것이 아니라 가상으로 CPU 스케줄링 프로그램을 작성한 후 프로그램의 CPU 요청을 입력값으로 넣어 어떠한 결과가 나오는지 확인하는 방법

##### \*트레이스(trace) : 실제 시스템에서 추출한 입력값

# [Chapter 6. 프로세스 동기화 Part-1]

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
