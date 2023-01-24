# [CPU 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140328151311578473?vmode=f)

# CPU 스케줄링의 필요성

## CPU and I/O Bursts in Program Execution

- CPU Burst
  - CPU가 실행하는 기계어
  - load store, add store, read from file… (running)
- I/O Burst
  - wait for I/O
  - 오래 걸리는 작업
- …

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

# CPU scheduler & Dispatcher

## CPU Scheduler

> Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.

## Dispatcher

> CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.
> 이 과정을 context switch(문맥 교환)라고 한다.

## CPU 스케줄링이 필요한 경우

1. Running → Blocked (I/O를 요청하는 시스템 콜)
2. Running → Ready (할당 시간 만료로 timer interrupt)
3. Blocked → Ready (I/O 완료 후 interrupt)
   1. 우선순위에 따라 I/O가 완료되고 나서 바로 실행할 수 없는 경우
4. Terminate

- 1, 4 : nonpreemptive(비선점형). 자진 CPU 반납
- 2, 3 : preemptive(선점형). 강제 CPU 반납

# [CPU 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

## Scheduling Criteria

<aside>
💡 좋은 CPU 스케줄링 알고리즘이란?

</aside>

### Performance Index (Performance Measure, 성능 척도)

- CPU utillization (이용률)
  - CPU 이용률
  - 전체 시간 중 CPU가 일한 시간
  - keep the CPU as busy as possible
- Throughput (처리량)
  - 주어진 시간동안 완료한 개수
  - of processes that complete their execution per time unit
- Turnaround time (소요시간, 반환시간)
  - 처음부터 끝까지
  - amount of time to execute a particular process
- Waiting time (대기시간)
  - 순수 대기 시간
  - 중간 대기 시간 포함
- Response time (응답시간)
  - ready queue ~ first cpu time
  - time sharing 시스템에서 중요

## FCFS (First-Come First-Served)

- 프로세스의 도착 순서대로 스케줄 순서를 처리
- 비선점형 스케줄링
- 효율적이지 않음
- CPU를 오래 쓰는 프로그램에 대해 Interactive한 Job들이 수행되지 못함
  - Convoy effect(호의 효과 ,,)

## SJF (Shortest Job First)

- 각 프로세스와 다음번 CPU burst time으로 스케줄링
- CPU burst time이 가장 짧은 프로세스 먼저 스케줄
- 비선점형
  - 기다림
- 선점형
  - 남은 burst time보다 더 짧은 CPU burst time 프로세스가 도착하면 선점
  - SRTF(Shortest-Remaining-Time-First)
- SJF is optimal
  - minimum average waiting time

### 문제점

- Starvation
  - CPU 시간이 긴 프로세스는 계속해서 기다려야 함
- 다음 CPU Burst time 예측 불가한 경우
  - I/O는 짧고 연산은 길 것이라고 추정은 할 수 있음
  - 통계를 통한 예측
  - 최근 데이터에 대한 가중치를 더 높게
  - 과거 데이터 기반 소요 시간 예측

## Priority Scheduling

- 우선 순위가 높은 프로세스 먼저
  - 우선 순위 정수
  - smallest integer : highest priority
- SJF도 일종의 Priority Scheduling이다.
- 선점형
- 비선점형

### 문제점

- Starvation
  - 우선 순위가 낮은 프로세스가 실행되지 못할 가능성

### 해결방안

- Aging
  - as time progresses increase the priority of the process

## Round Robin (RR)

- 선점형
  - 할당 시간이 지나면 프로세스는 선점당하고 ready queue 맨 뒤로 감
- 각 프로세스는 동일한 크기의 할당 시간 (time quantum)을 가짐
  - 일반적으로 10-100 ms
  - CPU 시간의 1/n
- 일반적으로 SJF보다 average turnaround time은 길지만 response time이 더 짧음

### 장점

- 응답 시간이 빨라짐
- 예측이 필요 없음
- 대기 시간이 CPU 사용 시간과 비례하게 됨
- time quantum이 적당해야함
  - 할당 시간이 크면 FCFS와 유사
  - 할당 시간이 작으면 context switch 오버 헤드가 커짐
- response time이 핵심

# Multilevel Queue

- 프로세스가 어느 우선순위에 해당되는가
- 우선순위가 낮은 프로세스 문제. stopation

- Ready queue를 여러 개로 분할
  - foreground (interactive)
  - background (batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - foreground : RR
  - background : FCFS
- 큐에 대한 스케줄링 필요
  - Fixed priority scheduling
  - Time slice

## Multilevel Feedback Queue

<aside>
💡 Multilevel queue의 개선안

</aside>

- 프로세스가 다른 큐로 이동 가능
- 후반에 갈 수록 RR q time이 길어짐
  - CPU burst가 짧으면 빨리 끝남
  - 긴 프로세스는 우선순위가 낮아짐
  - 빠르게 도착하면 빨리 끝남

---

## Multiple-Processor Scheduling

- CPU가 여러 개인 경우
- 스케줄링이 더욱 복잡해짐
- Homogeneous processor
  - 특정 프로세서에서 수행해야 하는 프로세스
- Loading sharing
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘
  - 별개의 큐를 두기 vs. 공동 큐를 두기
- Symmetric Multiprocessing (SMP)
  - 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

## Real-Time Scheduling

<aside>
💡 Deadline 보장이 필수

</aside>

### Hard real-time systems

정해진 시간 안에 반드시 끝내도록

### Soft real-time computing

일반 프로세스에 비해 높은 priority

꼭 보장 X

## Thread Scheduling

### Local Scheduling

- User level thread
- 사용자 수준 라이브러리

### Global Scheduling

- Kernel level thread
- 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정

## Algorithm Evaluation

- 알고리즘 평가 방법

1. Queueing models
   1. 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산
2. Implementation (구현) & Measurement (성능 측정)
   1. 실측. 운영체제 내부
   2. 실제 시스템에 알고리즘을 구현해 실제 작업(workload)에 대해서 성능을 측정 비교
3. Simulation (모의 실험)
   1. 알고리즘을 모의 프로그램으로 작성해 trace를 입력으로 결과 비교
