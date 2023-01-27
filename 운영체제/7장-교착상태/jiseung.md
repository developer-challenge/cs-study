# [교착상태 #1](https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f)

# [교착상태 #2](https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f)

## The Deadlock Problem

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태
- Resource(자원)
  - 하드웨어, 소프트웨어 모두 포함
    - I/O device, CPU cycle, memory space, semaphore …
  - 프로세스가 자원을 사용하는 절차
    - Request, Allocate, Use, Release
- Deadlock Example 1
  - 두 개의 프로세스가 각각 하나의 자원을 가지고 다른 하나를 기다리고 있음
- Deadlock Example 2
  - Binary semaphores A and B
  - lock, block

## Deadlock 발생의 4가지 조건

- Mutual exclusion(상호 배제)
  - 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
- No preemption(비선점)
  - 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
- Hold and wait(보유/점유 대기)
  - 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음
- Circular wait(순환 대기)
  - 자원을 기다리는 프로세스 간에 사이클이 형성되어야 함

## Resource-Allocation Graph(자원할당그래프)

- Vertex

  - Process `P`
  - Resource `R`

- Edge

  - Request (`P→R`)
  - Assignment (`R→P`)

- 그래프 안에 cycle이 없으면 deadlock이 아니다.
- 그래프에 cycle이 있으면
  - 자원 당 인스턴스가 하나라면 deadlock
  - 자원에 인스턴스가 여러개라면 deadlock일 수도 있고, 아닐 수도 있다.

## Deadlock의 처리 방법

## Deadlock Prevention(예방)

> 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

### Mutual Exclusion

- 공유하면 안되는 자원의 경우 반드시 성립해야 함

### Hold and Wait

- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
  - 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법
  - 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청(자진 반납)

### No Preemption

- process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
- 모든 필요한 자원을 얻을 수 있을 때 그 프로세스 다시 시작
- State를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용(CPU, memory)

### Circular Wait

- 모든 자원 유형에 할당 순서를 정해 정해진 순서대로만 자원 할당

> Utilization 저하, throughput 감소, starvation 문제. 비효율적

## Deadlock Avoidance(회피)

- 자원 요청에 대한 부가적인 정보를 이용해 deadlock으로부터 안전(safe)한지 동적으로 조사해서 안전한 경우에만 자원을 할당
  - 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당
  - 시스템이 safe state에 있으면 no deadlock, 시스템이 unsafe state에 있으면 posibility of deadlock
    - 시스템이 unsafe state에 들어가지 않는 것을 보장
  - safe state(no deadlock)
    - 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태
  - safe sequence
    - 프로세스의 sequence가 safe하려면 프로세스들의 자원 요청이 `가용 자원 + 모든 P의 보유 자원`에 의해 충족되어야 함
      - 자원 요청이 즉시 충족될 수 없으면 기다린다.
      - 기다리고 나서 자원 요청을 만족시켜 수행한다.
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법
- avoidance 알고리즘
  - Single instance per resource types
    - Resource Allocation Graph algorithm
  - Multiple instances per resource types
    - Banker’s Algorithm

### Resource Allocation Graph Algorithm

- `Claim edge` `P->R`
  - 프로세스 `P`가 자원 `R`을 미래에 요청할 수 있음 (점선)
  - 프로세스가 해당 자원 요청 시 `Request Edge`로 바뀜 (실선)
  - 자원 `R`이 release되면 assginment edge → claim edge
- `Request edge`의 `assignment edge` 변경 시 (점선을 포함해) cycle이 생기지 않는 경우에만 요청 자원을 할당한다.
- Cycle 생성 여부 조사 시 프로세스의 수가 n일 때 O(n^2)시간이 걸린다.

### Banker’s Algorithm

<aside>
💡 최악의 경우를 가정. 보수적으로 가능한지 판단(안전함)
</aside>

- 가정
  - 모든 프로세스는 자원의 최대 사용량을 미리 명시
  - 프로세스가 요청 자원을 모두 할당 받은 경우 유한 시간 안에 이들 자원을 다시 반납한다.
- 방법
  - 자원 요청 시 safe 상태를 유지할 경우에만 할당
  - 총 요청 자원의 수가 가용 자원의 수보다 적은 프로세스를 선택
    - 그런 프로세스가 없으면 unsafe 상태
    - 그런 프로세스가 있으면 그 프로세스에게 자원 할당
  - 할당받은 프로세스가 종료되면 모든 자원을 반납
  - 모든 프로세스가 종료될 때까지 이러한 과정 반복

## Deadlock Detection and recovery(탐지-회복)

- Deadlock 발생은 허용. 그에 대한 detection 루틴을 두어 deadlock 발견시 recover

### Deadlock Detection

- Resource type 당 single instance인 경우
  - 자원 할당 그래프에서 cycle이 곧 deadlock을 의미
  - Wait-for graph
    - 자원할당 그래프의 변형
      - 자원을 제외하고 프로세스 만으로 그래프를 간결하게 구성
        cycle만 확인해서 deadlock 여부 확인
    - 프로세스만으로 node 구성
    - Pj가 가지고 있는 자원을 Pk가 기다리는 경우 Pk → Pj
    - Algorithm : Wait-for graph에 사이클이 존재하는지를 주기적으로 조사. O(n^2)
- Resource type 당 multiple instance인 경우
  - Banker’s algorithm과 유사한 방법 활용

### Deadlock Recovery

- Process termination
  - Abort all deadlocked processes
    - Deadlock에 연루된 모든 프로세스 종료
  - Abort one process at a time until the deadlock cycle is eliminated
    - Deadlock이 해결될 때까지 프로세스 하나씩 종료
- Resource Preemption
  - 비용을 최소화할 victim 선정
  - safe state로 rollback하여 process를 restart
  - Starvation 문제
    - 동일한 프로세스가 계속해서 victim으로 선정되는 경우
      - cost factor에 rollback 횟수도 같이 고려

## Deadlock Ignorance(무시)

- Deadlock을 시스템이 책임지지 않음
- UNIX 포함 대부분의 범용 OS가 채택
- Deadlock이 매우 드물게 발생하므로 deadlock에 대한 조치 자체가 더 큰 overhead일 수 있음
- 시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사용자가 느낀 후 직접 process를 종료해서 대처하도록
