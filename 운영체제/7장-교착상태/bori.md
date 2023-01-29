# [Chapter 7. 교착상태 Part-1](https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f)

## The Deadlock Problem

#### Deadlock

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

#### Resource(자원)

- 하드웨어, 소프트웨어 등을 포함하는 개념
  예) I/O device, CPU cycle, memory space, semaphore 등
- 프로세스가 자원을 사용하는 절차 : Request(요청), Allocate(획득), Use(사용), Release(반납)

#### Deadlock Example 1

- 시스템에 2개의 tape drive가 있고, 각 프로세스는 하나의 tape drive를 읽어서 다른 tape drive에 복사를 하는 작업 진행하려고 한다.
- 프로세스 P<sub>1</sub>과 P<sub>2</sub> 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있는 상태

#### Deadlock Example 2

- 바이너리 세마포어 A, B
- P<sub>0</sub>는 A를 먼저 획득하고 B를 획득하려고 하는 상황에서 CPU를 빼앗김
- P<sub>1</sub>는 B를 먼저 획득한 다음 A를 획득하려고 하지만 P<sub>0</sub>이 A를 가지고 있는 상태이므로 CPU가 어느 프로세스로 가더라도 작업이 진행되지 않는다.

| P<sub>0</sub> | P<sub>1</sub> |
| ------------- | ------------- |
| P(A);         | P(B);         |
| P(B);         | P(A);         |

## Deadlock 발생의 4가지 조건

#### 1. Mutual exclusion(상호 배제)

- 매 순간 하나의 프로세스만이 자원을 사용할 수 있다.

#### 2. No preemption(비선점)

- 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않는다.
- 다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 빼앗을 수 없어야 한다.

#### 3. Hold and wait(점유 대기)

- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있는다.
- 최소한 하나의 자원을 점유하고 있으면서 다른 프로세스에 할당되어 사용하고 있는 자원을 추가로 점유하기 위해 대기하는 프로세스가 있어야 한다.

#### 4. Circular wait(순환 대기)

- 자원을 기다리는 프로세스 간에 사이클이 형성되어야 한다.
- 프로세스들이 순환 형식으로 서로를 기다리고 있어야 한다.
- 프로세스 P<sub>0</sub>, P<sub>1</sub>, ..., P<sub>n</sub>이 있을 때
  - P<sub>0</sub>은 P<sub>1</sub>이 가진 자원을 기다림
  - P<sub>1</sub>은 P<sub>2</sub>이 가진 자원을 기다림
  - P<sub>n-1</sub>은 P<sub>n</sub>이 가진 자원을 기다림
  - P<sub>n</sub>은 P<sub>0</sub>이 가진 자원을 기다림

## Resource-Allocation Graph(자원할당 그래프)

![](https://i.imgur.com/vWUvDSq.png)

- 자원할당 그래프를 통해 교착상태인지 아닌지 확인

![](https://i.imgur.com/j8dGoB4.png)

- 그래프에 사이클이 없으면 교착상태가 아니다
- 그래프에 사이클이 있으면
  - 자원 당 하나의 인스턴스만 있다면 교착상태다.
  - 자원 당 여러 인스턴스가 존재한다면 교착상태의 가능성이 있다.
- 예제1 : 교착상태
- 예제2 : P<sub>2</sub>와 P<sub>4</sub>가 각 자원을 사용하고 반납하면 사이클이 없어지므로 교착상태가 아니다.

## Deadlock의 처리

- Deadlock Prevention
  - 자원 할당 시 교착상태의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
- Deadlock Avoidance
  - 자원 요청에 대한 부가적인 정보를 이용해서 교착상태의 가능성이 없는 경우에만 자원 할당
  - 시스템의 상태(state)가 원래 상태로 돌아올 수 있는 경우에만 자원 할당
- Deadlock Detection and recovery
  - 교착상태 발생은 허용하되 그에 대한 detection 루틴을 두어 교착상태 발견시 recover
- Deadlock Ignorance
  - 교착상태를 시스템이 책임지지 않음
    - 프로세스가 느려지는 등 문제가 생기면 프로그램을 삭제하는 등 사람이 직접 해결
  - UNIX를 포함한 대부분의 OS가 채택

⇒ Deadlock Prevention와 Deadlock Avoidance는 교착상태가 발생하는 것을 미리 방지하는 방법이고, Deadlock Detection and recovery와 Deadlock Ignorance는 교착상태가 발생하는 것을 허용하고 그 후 처리하는 방법이다.

## Deadlock Prevention

자원 할당 시 교착상태의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

### Mutual Exclution

- 공유해서는 안되는 자원의 경우 반드시 성립해야 함

### Hold and Wait

- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
- 방법1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법
  - 해당 프로세스는 wait가 생기지 않는다.
  - 매 시점마다 달라지는 필요한 자원들을 처음부터 모두 할당받는다면 자원을 비효율적으로 사용하게 된다.
- 방법2. 자원이 필요한 경우 보유 자원을 모두 놓고 다시 요청

### No Preemption

- 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
- 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
- State를 쉽게 save하고 restore 할 수 있는 자원에서 주로 사용(CPU, memory)

### Circular Wait

- 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
- 예를 들어 순서가 3인 자원 R<sub>i</sub>를 보유 중인 프로세스가 순서가 1인 자원 R<sub>j</sub>를 할당받기 위해서는 우선 R<sub>i</sub>를 release 해야한다.

⇒ 교착상태를 원천적으로 막을 수 있지만 자원에 대한 이용률 저하, 처리량 감소, 기아 현상 문제가 발생한다.

## Deadlock Avoidance

- 자원 요청에 대한 부가정보를 이용해서 자원 할당이 교착상태로부터 안전한지 동적으로 조사하여 안전한 경우에만 자원을 할당
  - 자원 요청에 대한 부가적인 정보를 이용해서 교착상태의 가능성이 없는 경우에만 자원을 할당
- 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하는 것이 가장 단순하고 일반적인 모델이다.

#### Safe state

- 시스템 내의 프로세스들에 대한 safe sequence 가 존재하여 모든 프로세스가 정상적으로 종료할 수 있는 상태

#### Safe sequence

- 교착 상태를 발생 시키지 않고 자원을 할당하는 순서
- 프로세스의 sequence <P<sub>1</sub>, P<sub>2</sub>, ..., P<sub>n</sub>>이 안전하려면 P<sub>i</sub>(1 ≤ i ≤ n)의 자원 요청이 **"가용 자원 + 모든 P<sub>j</sub>(j < i)의 보유자원"** 에 의해 충족되어야 함
- 조건을 만족하려면 다음 방법으로 모든 프로세스의 수행을 보장
  - P<sub>i</sub>의 자원 요청이 즉시 충족될 수 없으면 모든 P<sub>j</sub>(j < i)가 종료될 때까지 기다린다.
  - P<sub>i-1</sub>이 종료되면 P<sub>i</sub>의 자원요청을 만족시켜 수행한다.

![](https://i.imgur.com/Lhes5g6.png)

- 시스템이 safe state에 있으면 ⇒ No Deadlock
- 시스템이 unsafe state에 있으면 ⇒ Possibility of Deadlock
  - 교착상태의 가능성이 있다는 것을 의미하는 것이지 `unsafe state == 교착상태`를 의미하지 않는다.
- Deadlock Avoidance : 시스템이 unsafe state에 들어가지 않는 것을 보장
- Avoidance algorithm
  - Single instance per resource types
    ⇒ Resource Allocation Graph Algorithm
  - Multiple instance per resource types
    ⇒ Banker's Algorithm

### Resource Allocation Graph Algorithm(자원 할당 그래프 알고리즘)

자원당 인스턴스가 하나인 경우 사용하는 알고리즘

![](https://i.imgur.com/VdfMdXb.png)

#### Claim edge(P<sub>i</sub> ⇢ R<sub>j</sub>)

- 프로세스 P<sub>i</sub>가 자원 R<sub>j</sub>를 미래에 요청할 수 있음을 뜻함(점선 화살표)
- 프로세스가 해당 자원 요청시 request edge로 바뀜(실선 화살표)
- R<sub>j</sub>가 release 되면 assignment edge는 다시 claim edge로 바뀐다.
- request edge의 assignment edge 변경시 (점선을 포함하여) 사이클이 생기지 않는 경우에만 요청 자원을 할당한다.
  ⇒ 교착상태가 발생할 수 있는 가능성이 있다면 회피
  ![](https://i.imgur.com/meZrfYE.png)
- 사이클 생성 여부 조사시 프로세스의 수가 n일 때 O(n<sup>2</sup>) 시간이 걸린다.

### Banker's Algorithm(은행원 알고리즘)

자원당 인스턴스가 여러 개인 경우 사용하는 알고리즘

![](https://i.imgur.com/HL6kGhC.png)

- 5개의 프로세스 P<sub>0</sub>, P<sub>1</sub>, P<sub>2</sub>, P<sub>3</sub>, P<sub>4</sub>가 있다.
- 3가지 타입의 자원 A, B, C가 있고 각각 10개, 5개, 7개의 인스턴스가 존재한다.
- `Allocation` : 현재 프로세스에 할당되어 있는 자원의 수
- `Max` : 프로세스들이 필요로 하는 각 자원별 최대 할당량
- `Available` : 각 자원이 프로세스에게 추가로 할당 할 수 있는 가용 자원의 수
- `Need`(`Max` 자원의 수 - `Allocation` 자원의 수) : 추가로 요청할 수 있는 자원의 수

⇒ `Need`의 수보다 `Available`의 수가 클 경우 사용 가능한 자원으로 충족을 할 수 없으므로(교착상태 가능성) 사용가능한 자원이 있어도 자원을 할당하지 않는다.

- Banker's Algorithm은 프로세스가 항상 자원 요청을 최대로 할거라고 가정하고, 그 최대 요청이 현재 가용 자원으로 충족이 되는지 확인 후 자원을 할당한다.
- 가용 자원으로 다른 프로세스들이 처리를 끝낸 후 모든 자원들이 반납되면 자원의 수가 부족했던 프로세스에게 할당하여 처리를 진행
- 교착상태를 만들진 않지만 비효율적이다.
  - 자원이 남아 있어도 혹시 모를 상황(교착상태의 가능성)을 대비해 할당하지 않기 때문
- Banker's Algorithm은 어떤 프로세스가 자원을 요청했을 때 해당 요청을 받아들일지 받아들이지 않을지를 결정만 하는 것

# [Chapter 7. 교착상태 Part-2](https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f)

## Deadlock Detection and recovery

### Deadlock Detection

- Single instance per resource types
  - 자원할당 그래프에서의 사이클이 곧 교착상태를 의미
- Multiple instance per resource types
  - 은행원 알고리즘과 유사한 방법을 활용

#### Wait-for graph 알고리즘 - Single instance per resource types

- Wait-for graph
  - 자원할당 그래프의 변형
  - 프로세스만으로 node 구정
  - P<sub>j</sub>가 가지고 있는 자원을 P<sub>k</sub>가 기다리는 경우 P<sub>k</sub> → P<sub>j</sub>
- 알고리즘
  - Wait-for graph에 사이클이 존재하는지를 주기적으로 조사
  - 프로세스가 n개 있을 때, Wait-for graph에서 사이클을 찾는데 걸리는 시간은 O(n<sup>2</sup>)

![](https://i.imgur.com/kedZDIA.png)
⇒ 그래프(a)에서 자원을 빼고 각 화살표를 프로세스 간 연결해주면 그래프(b)가 된다.

#### Multiple instance per resource types

![](https://i.imgur.com/w1daRUv.png)

- Deadlock Detection은 자원을 요청하면 자원을 할당
  - 어떤 프로세스가 최대로 요청 자원이 얼마나 될 지 미리 알 필요가 없다.
- 가용 자원이 얼마나 있는지, 가용 자원으로 처리할 자원이 있는지 확인
- 가용 자원으로 처리할 수 있는 프로세스가 있는지 확인하여 자원 할당
- 프로세스가 자원을 반납하면 가용 자원과 합치면서 위 과정 반복

⇒ 보수적인 은행원 알고리즘에 비해 낙관적인 알고리즘

### Recovery

#### Process termination(프로세스 종료)

- 교착상태인 모든 프로세스를 강제 종료
- 교착상태 사이클이 없어질 때까지 프로세스를 하나씩 강제 종료

#### Resource Preemption(자원 선점)

교착상태인 프로세스의 자원을 뺏는 방법

- 비용을 최소화할 희생 프로세스(victim)를 선정
- 희생 프로세스를 safe state로 rollback하여 프로세스를 재시작
- 기아 현상 문제
  - 동일한 프로세스가 계속해서 희생 프로세스 선정되는 경우
  - 단순히 비용만 최소화 하지 않고 비용 요소(cost factor)에 rollback 횟수도 같이 고려하여 해결

## Deadlock Ignorance

- 교착상태가 일어나지 않는다고 가정하고 아무런 조치를 취하지 않는 방법
- 교착상태는 매우 드물게 발생하므로 교착상태에 대한 조치 자체가 더 큰 오버헤드일 수 있다.
- 만약, 시스템에 교착상태가 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 프로세스를 종료시키는 등의 방법으로 대처
- UNIX, Windows 등 배부분의 범용 OS가 채택한 방법
