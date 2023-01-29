[교착상태 #1](https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f)

[교착상태 #2](https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f)

# The Deadlock Problem

## Deadlock

일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

## Resource

하드웨어, 소프트웨어 등을 포함하는 개념
ex) I/O device, CPU Cycle, memory space, semaphore 등

### 프로세스가 자원을 사용하는 절차

- Request
- Allocate
- Use
- Release

# Deadlock 발생의 4가지 조건

## Mutual exclusion (상호 배제)

매 순간 하나의 프로세스만이 자원을 사용할 수 있음

## No preemptive

프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

## Hold and wait

자원을 가진 프로세스가 다른 자원을 기다릴 때, 보유 자원을 놓지 않고 계속 가지고 있음

## Circular wait

자원을 기다리는 프로세스간에 사이클이 형성되어야 함

# Resource-Allocation Graph(자원할당그래프)

Deadlock이 발생하는지 알아보기 위해 사용하는 그래프

![](https://i.imgur.com/gcgR3uW.png)

그래프에 cycle이 없으면 deadlock이 아님
만약 cycle이 존재한다면

- 자원당 하나의 인스턴스만 갖고 있다면 deadlock
- 자원당 여러 인스턴스를 갖고 있으면 deadlock일 가능성이 있음

# Deadlock의 처리 방법

## Deadlock Prevention

자원 할당시 deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
-> `Utilization` 저하, `throughput` 감소, `startvation` 문제

### mutual exclustion

공유해서는 안되는 자원의 경우, 반드시 성립해야 함

### hold and wait

프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.

1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법
   - 모두 다 받아 hold (wait할 일이 없음)
   - 자원에 대한 비효율성 발생
2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청

### No Preemptive

Process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
모든 필요한 자원을 얻을 수 있을 때 그 프로레스는 다시 시작
State를 쉽게 저장하고 복구할 수 있는 자원에서 주로 사용 (`CPU`, `memory`)

### Circular Wait

모든 자원 유형에 할당 순서를 정해 정해진 순서대로만 자원 할당
예를 들어 순서가 3인 자원 R1을 보유 중인 프로세스가 순서가 1인 자원 R2를 할당받기 위해선 우선 R1을 release 해야한다.

## Deadlock Avoidance

자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당

가장 단순하고 일반적인 모델은 **프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언**하도록 하는 방법

평생 쓸 자원의 양을 알고 있다고 가정

### safe state

시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태

### safe sequence

프로세스의 sequence들(p1,p2...)이 안전하려면 프로세스의 자원 요청이 **가용자원 + 모든 프로세스의 보유 자원**에 의해 충족 되어야 함.
조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장

- 프로세스의 자원 요청이 즉시 충족될 수 없으면 모든 프로세스가 종료될 때까지 기다린다.
- 앞선 프로세스가 종료되면 현재 프로세스의 자원 요청을 만족시켜 수행한다.

시스템이 `safe state`에 있으면 => no deadlock
시스템이 `unsafe state`에 있으면 => deadlock 가능성 있음

자원별 하나의 인스턴스를 갖고 있을 때 => `Resource Allocation Graph algorithm` 사용
자원별 여러 인스턴스를 갖고 있을 때 => `Banker's Algorithm` 사용

### Resource Allocation Graph algorithm

![](https://velog.velcdn.com/images/deli-ght/post/01027389-42eb-4146-8ed5-acb2a64adf74/image.png)

deadlock의 가능성이 조금이라도 있는 경우 절대 사용하지 않음

### Example of Banker's Algorithm

![](https://velog.velcdn.com/images/deli-ght/post/2f4e35bc-0eea-47c7-bdaf-9aeeea42b869/image.png)

가용자원을 가지고 요청양을 충족할 수 있는가를 확인

## Deadlock Detection and recovery

Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover

### deadlock detection

#### 자원 당 하나의 인스턴스를 갖는 경우

자원 할당 그래프에서의 cycle이 곧 deadlock을 의미

#### 자원 당 여러 인스턴스를 갖는 경우

banker's algorithm과 유사한 방법 활용

### Wait for graph 알고리즘

자원당 인스턴스가 하나뿐인 경우

#### wait-for graph

- 자원 할당 그래프의 변형
- 프로세스만으로 node 구성
- p1이 가진 자원을 p2가 기다리는 경우 p2 -> p1

#### 알고리즘

wait-for graph에 사이클이 존재하는 지를 주기적으로 조사
싸이클을 찾는 오버헤드 -> O(n^2)

> Request는 추가 요청 가능량이 아닌 현재 실제로 요청한 자원량

### Recovery

#### 1. Process termination

deadlock에 연루된 프로세스를 모두 종료

#### 2. Resource Preemption

비용을 최소화할 victim 선정
-> deadlock이 없어질 때까지 하나씩 죽임

해결 방법 : safe state로 rollback 하여 process를 restart

#### starvation 문제

- 동일한 프로세스가 계속해서 victim으로 선정되는 경우
- cost factor에 rollback 횟수도 같이 고려

## Deadlock Ignorance

Deadlock을 시스템이 책임지지 않음 -> 인간지능 이용

deadlock이 생각보다 자주 발생하지 않기 때문에 이를 해결하기 위하 들이는 오버헤드가 더 큼
UNIX를 포함한 대부분의 OS가 채택
