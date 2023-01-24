## [CPU 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140328151311578473?vmode=f)

모든 프로그램은 CPU instruction 실행 단계와 오래 걸리는 I/O 작업 단계의 반복이라는 경로로 실행된다. 프로그램마다 방식이 조금씩 다를 수는 있다.

I/O bound의 job들은 사람과의 interaction이기 때문에 응답 시간이 너무 길면 사용자 경험 측면에서 좋지 않다.  
Ready Queue에 들어와 있는 Process 중 어떤 것에 CPU를 줄 것인지 결정하는 메커니즘이 CPU 스케쥴링

크게 2가지 이슈

1. 당장 누구에게 줄 것인가의 문제
2. CPU burst가 너무 길 경우 뺏어서 줄 것인가의 문제

공평도 중요하겠지만 어떻게 스케쥴링 하는 것이 더 `효율적`인가 결정.

### Scheduling Algorithms

- FCFS, SJF, SRTF, Priority Scheduling, RR, Multilevel Queue, Multilevel Feedback Queue ...

크게 2가지로 나눠보면

1. nonpreemptive(비선점형): 강제로 빼앗지 않는 방법. 사용 보장.
2. preemptive(선점형): 강제로 뺏는 방법. 현대적인 스케쥴링은 대부분 선점형 사용

어떤게 좋은지 어떻게 평가할 것인가? => `성능 척도`(Scheduling Criteria)

### Scheduling Criteria

다른 말로 Performance Index(= Performance Measure, 성능 척도)

크게 2가지로 분류

1. 시스템 입장: CPU 하나로 최대한 일을 시키면 좋다.
   - CPU utilization(이용률): 전체 시간 중 CPU가 일한 시간(놀지 않고) = 비싼 CPU를 가능한 바쁘게 만들어라
   - Thoughput(처리량): 주어진 시간동안 몇개의 작업을 처리했는가. 가능한 많이 할 수록 좋다.
2. 프로그램(고객) 입장: 얼마나 빨리 처리하느냐
   - Turnaround time(소요시간, 반환시간): CPU를 사용하러 들어와서 쓰고 나갈 때까지 걸린 시간 하나의 `CPU burst 시간`
   - Waiting time(대기 시간): CPU를 기다린 시간. 선점형처럼 하나의 CPU burst 시간동안도 빼앗기고 다시 기다리고를 반복할텐데 Ready Queue에서 기다린 시간을 합친 것.
   - Response time(응답 시간): 응답 시간. Ready Queue 들어와서 처음으로 CPU를 얻기까지 걸린 시간. 최초로 CPU 받기까지의 시간

여기에서 처리량, 시간은 Process 생성부터 종료까지의 총 시간을 말하는 것이 아니라 CPU 사용하러 와서 쓰고 I/O하러 나갈 때까지의 시간 - CPU 관점이라는 것이 중요!

응답 시간 도입 이유?  
time sharing 환경에서는 처음으로 CPU를 얻는 시간이 사용자 응답과 관련되어 매우 중요한 시간 개념(첫번째 음식이 나오는 시간 비유, 최초의 허기 달래는 느낌)

#### FCFS(First-Come First-Served)

비선점형(nonpreemptive).  
먼저 들어왔으면 먼저 사용, 빼앗지 않음. 썩 효율적이지는 않다.  
매우 긴 점유시간을 가진 Process가 들어올 경우, 다른 Process들에게 응답시간이 너무 길어진다.(= 성능 척도면에서 좋지 못하다.)  
앞에 있는 Process들의 시간에 따라서 성능이 너무 달라진다.

- Convoy effect: 긴 Process가 먼저 와서 짧은 Process들이 지나치게 오래 기다리는 현상. 좋은 현상은 아니다.

> Convoy Effect?  
> Convoy는 호송, 호위 등 나쁘지 않은 단어 같은데?  
> 2차 대전 당시 전쟁 물자 나르면서 호위하는 개념. 컴퓨터로 넘어오며 Queue에서 오래 기다리는 현상으로 변형되었다고

사실 CPU Scheduling은 별 거 없다~

#### SJF(Shortest Job First)

CPU burst가 가장 짧은 Process에 먼저 주는 방식. 전체적으로 행복해지는 결과가 나옴. Queue가 전체적으로 짧아진다. 평균 대기 시간이 가장 짧아짐.  
`minimum average wating time`  
2가지 나눠 생각

- Nonpreemptive: 짧은 Process에 이미 줬으면, 더 짧은게 들어와도 CPU 사용권 보장.
- Preemptive: CPU 줬어도, 더 짧은 Process가 도착하면 그 Process에 CPU 준다. => SRTF(Shortest-Remaining-Time-First)

최소 평균 대기 시간을 만들어주는 방식(optimal)은 선점형

CPU 스케쥴링이 언제 일어나는가?

- nonpreemptive: CPU 다 쓰고 `나가는 시점`에 스케쥴링 할지 안할지 결정
- preemptive: 새로운 Process `도착시` 언제든지 스케쥴링 일어난다.

optimal도 있겠다. 이게 제일 좋은 방식인가?  
2가지 문제점.

1. Starvation(기아 현상)  
   SJF는 극단적으로 CPU 사용시간이 짧은 job을 선호. 길면 영원히 CPU를 못받을 수도 있다. Long Process가 starvation 될 수 있다.
2. CPU 사용 시간을 미리 알 수 없다.  
   본인이 얼마나 사용할지조차 모른다.  
   추정할 수는 있다. 사용자 interaction => CPU burst 시간 짧다 / 과학 계산 프로그램 등 => CPU burst 길다. ==> 이런 특징으로 예측. 과거 사용한 기록 토대로. 물론 정확하지 않다.

> 다음 CPU Burst Time 예측  
> exponential averaging 이용. 과거 CPU burst time 이용  
> 실제 사용 시간, 예측 사용 시간. 식 이용.
> Define: (예측 사용 시간)\_n+1 = a(실제 사용시간)\_n + (1-a)(예측 사용 시간)\_n  
> 최근 사용 시간은 가중치 좀 더 높게, 예전일 수록 가중치 낮게 반영해서 결과 얻는다.  
> 정확히 알 수는 없지만 예측치를 통해서 제일 짧은 Process에 CPU 준다.

#### Priority Scheduling

우선 순위 스케쥴링. 우선 순위 제일 높은 Process에 CPU 주겠다~

역시 2가지로 생각

- Nonpreemptive: 더 높은 우선 순위 와도 뺏지 않는다.
- Preemptive: 더 높은 수선 순위 오면 뺏는다.

우선 순위는 다양하게 정의할 수 있다. 보통 정수 값, 작을 수록 우선 순위 높음.

Starvation의 문제점. SJF와 마찬가지로 낮은 우선 순위가 영원히 실행되지 못하는 문제  
효율성 가장 중요 but 지나친 차별은 방지해야 함 => Aging 기법으로 해결

Aging: 시간이 지날 수록 우선 순위 높여주는 방법.

#### Round Robin(RR)

현대적 컴퓨터 시스템에서의 CPU Scheduling은 RR에 기반. timer 동작도 마찬가지고.  
선점형(preemptive) 스케쥴링.

동일한 할당 시간(time quantum)  
CPU 줄 때 동일한 시간을 할당. 끝나면 CPU 빼앗고, 빼앗긴 Process는 Ready Queue 뒤로 들어감. 그 다음 Process 할당, 실행...

가장 좋은 점? 응답 시간이 빨라진다. 줬다 뺏었다 계속하므로. 누구든지 CPU 맛보기 가능. 누가 오래 쓸지 모르는 상황에서 예측 시간 계산할 필요 없이 할 수 있다.  
n개의 Process가 Ready Queue에 있다면 적어도 (n-1)\*q(할당 시간)만큼만 기다리면 차례온다
CPU 길게 쓰는 Process는 waiting time도 길어진다. 대기 시간이 CPU 사용 시간에 비례한다는 특징

극단적 상황 생각

- q가 너무 클 경우 => FCFS
- q가 너무 작을 경우 => RR 철학에서는 이상적 but context switch 빈번 => Overhead 문제 발생

적정 시간: 10~100ms

일반적으로 SJF보다 average turnaround time은 길지만 response time은 더 짧다.

CPU 사용시간이 모두 동일한 Process일 경우, RR로하면 모든 Process가 동일한 시간에 끝나는 경향이 있어서 안좋을 수도 있다. 일반적으로는 짧고 긴 Process가 섞여 있어서 RR 효과적.

## [CPU 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

#### Multilevel Queue

![](https://i.imgur.com/aAPqZaZ.png)

줄마다 우선순위가 있다. 다른 우선순위 Queue로 들어갈 수 없다.

고려 사항

- Process를 어느 줄에 넣을 것인가.
- Starvation 문제

Ready Queue 여러 개로 분할 & 각 Queue는 특성에 맞게 독립적 Scheduling 알고리즘을 갖는다.

- foreground(Interactive job, 우선 순위 높다할 수 있음) - RR
- background(Batch job: no human interaction) - FCFS

어떤 Queue에게 CPU를 줄 것인가. 줄 결정되면 누구에게 줄 것인가 결정

- Fixed priority scheduling: foregroud 다 끝나야 background 하는 식. starvation 문제점
- Time slice: 각 Queue CPU 시간 적절히 할당. ex) 80% foreground in RR, 20% background in FCFS

공정하기 않고 차별적 방식이긴 한데, 우선 순위대로 처리하려는 의도. 신분 극복 못하는 방식 문제점.

#### Multilevel Feedback Queue

낮은 우선 순위여도 승격되기도 하고, 강등되기도 한다.

- 기준: Queue의 개수, Queue별 스케쥴링 알고리즘, 상위 보내는 기준, 하위 내쫓는 기준, 처음 Process가 어떤 Queue로 갈 것인가

보통은 그림과 같이 구현

![](https://i.imgur.com/6mQ5C6Q.png)

- 처음 들어올 때는 우선 순위 가장 높은 Queue에 넣는다 -> RR
- 하위로 갈 수록 할당 시간 점점 길어지다가
- FCFS 방식이 된다.

상위 Queue에서 완료되지 못한다면 강등, 또 완료 못되면 강등,... 결국 FCFS

CPU burst time 짧으면 짧게 받고 바로 빠져나가고, 긴 Process는 하위로 가서 더 긴 time quantum 받고서, 상위 Queue 완료까지 대기하는 방식.  
CPU 사용 시간 짧은 Process에게 우선 순위를 더 많이 주는 Scheduling 알고리즘. RR의 특징을 더 극대화한 느낌. CPU 사용 시간 긴지 짧은지 고려할 필요 없다. 예측도 필요 없고.

--- 여기까지는 CPU가 하나 밖에 없는 스케쥴링 ---

--- CPU 여러개 or 시간 제약 조건 or Thread 여러개 등 특이 케이스 ---

#### Multiple-Processor Scheduling

CPU 여러 개인 경우  
어떤 job은 특정 CPU가 처리해줘야 하는 경우도 있다. -> 할당하고 스케쥴링

- Load sharing: 일부 CPU에 몰리지 않도록 부하 공유 메커니즘. 별개 Queue vs. 공동 Queue.
- Symmetric Multiprocessing(SMP): 모든 CPU 대등 => 각자 알아서 스케쥴링
- Asymmetric Multiprocessing: 하나의 CPU가 전체적 컨트롤 담당

#### Real-Time Scheduling

Deadline이 있는 job. 정해진 시간 안에 반드시 실행되어야만 하는 job. 반드시 deadline 보장 해줘야 한다.

그 때 그 때 스케쥴링해주는 방식이 아니라, 미리 deadline 정해놓고 동작하는 방식.

Offline하게 설정. Periodic한 성격의 job들이 많다. 주기적으로 activate.

- Hard RTS: 반드시 정해진 시간 안에
- Soft RTS: 일반적 Time sharing system에서 다른 Process들과 섞여 실행. 영화 보는 것처럼. deadline 내에 높은 우선 순위를 갖도록

#### Thread Scheduling

하나의 Process 안에 CPU 수행 단위

thread 구현 방식

- Local Scheduling: User level thread => `사용자 Process가 직접` thread 관리, OS는 그 thread 존재 모름. => OS 입장에서 해당 Process에 CPU 줄지 안줄지 결정 -> CPU가 Process로 갔을 때는 어떤 thread에 줄지는 Process 내부에서 결정
- Global Scheduling: Kernel level thread => `OS가 thread 직접` 아는 상황. => Process에 CPU 주듯이 OS가 thread에 직접 CPU 준다.

---

### Algorithm Evaluation

어떤 알고리즘이 좋은지를 평가할 수 있는 방법  
크게 3가지

1. Queueing models
   이론가들이 선호. 확률 분호로 주어지는 `arrival rate`와 `service rate` 통해 performance index 계산
2. Implementation & Measurement
   실제 시스템에 구현해서 돌려보고 성능 측정. 실측. 리눅스 내부 코드 수정
3. Simulation
   실제로 돌리는 것이 아니라 모의 실험.  
   실제 프로그램을 돌려서 얻은 input data를 `trace`라고 부른다. 이를 simulation의 input으로 넣어준다.
