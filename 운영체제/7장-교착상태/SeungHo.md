# 7장 교착상태

## [교착상태 #1](https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f)

### 교착상태(deadlock)

![](https://i.imgur.com/3u7v918.png)

일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

서로 필요한 자원을 요청하고 내어주지 않음

****************\*\*****************Resource(자원)****************\*\*****************

- 하드웨어, 소프트웨어 등을 포함하는 개념
- Ex) I/O device, CPU cycle, memory space, semaphore 등
- 프로세스가 자원을 사용하는 절차
  - Request, Allocate, Use, Release

**Deadlock Example 1**

- 시스템에 2개의 tape drive가 있다
- 프로세스 P1과 P2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다

**Deadlock Example 2**

- Binary semaphores A and B
  ![](https://i.imgur.com/a8cBN3E.png)

### Deadlock 발생의 4가지 조건

**************\*\*\*\***************Mutual exclusion (상호 배제) - 독점적으로 사용**************\*\*\*\***************

- 매 순간 하나의 프로세스만이 자원을 사용할 수 있음

************\*\*************No preemption (비선점)************\*\*************

- 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

**Hold and wait (보유 대기)**

- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음

************\*\*************Circular wait (순환대기)************\*\*************

- 자원을 기다리는 프로세스간에 사이클이 형성되어야 함
- 프로세스 P0, P1, …, Pn이 있을 때
  - P0은 P1이 가진 자원을 기다림
  - P1은 P2가 가진 자원을 기다림
  - Pn-1은 Pn이 가진 자원을 기다림
  - Pn은 P0이 가진 자원을 기다림

### Resource-Allocation Graph (자원할당그래프)

데드락이 발생했는지 알아보기 위함

![](https://i.imgur.com/83NCgoU.png)

R(자원) → P(프로세스) : 해당 자원이 해당 프로세스에 속해있음

P(프로세스) → R(자원) : 프로세스가 자원을 요청했지만 아직 획득하지 못한 상태

사각형 내의 점 : 자원의 수(인스턴스)

![](https://i.imgur.com/1Yz4hpO.png)

- 그래프에 cycle이 없으면 deadlock이 아님
- 그래프에 cycle이 있으면
  - if only one instance per resource type, then deadlock → 자원의 인스턴스가 하나일 경우 데드락
  - if several instances per resource type, possibility of deadlock
    - 왼쪽의 그래프의 경우 자원이 두개이지만 사이클이 두개라서 데드락
    - 오른쪽 그래프의 경우 사이클이 하나 발생하지만 자원이 두개이므로 데드락이 발생하지 않음(2번 프로세스, 4번 프로세스가 자원을 내놓으면 해결됨)

## [교착상태 #2](https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f)

### Deadlock의 처리 방법

******************\*\*******************Deadlock Prevention******************\*\*******************

- 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

**Deadlock Avoidance**

- 자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
- 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당

******************************\*\*******************************Deadlock Detection and recovery******************************\*\*******************************

- Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover

**********\*\***********Deadlock Ignorance**********\*\***********

- Deadlock을 시스템이 책임지지 않음
- UNIX를 포함한 대부분의 OS가 채택

### Deadlock Prevention

**Mutual Exclusion**

- 공유해서는 안되는 자원의 경우 반드시 성립해야 함

**Hold and Wait**

- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 함
  방법 1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법 → 자원에 대한 비효율성 발생
  방법 2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청

**No Preemption**

- process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
- 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작됨
- State를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용 (CPU, memory)

**Circular Wait**

- 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
- 예를 들어 순서가 3인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj을 할당받기 위해서는 우선 Ri를 release해야 함

⇒ Utilization 저하, throughput 감소, starvation 문제

### Deadlock Avoidance

**Deadlock avoidance**

프로세스가 시작될 때 해당 프로세스가 평생에 쓸 자원의 최대량을 미리 알고있다고 가정하고 deadlock을 피해가는 것

- 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로부터 안전(safe)한지를 동적으로 조사해서 안전한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법

**safe state**

- 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태

**safe sequence**

- 프로세스의 sequence < P1, P2, …, Pn >이 safe하려면 Pi (1 ≤ i ≤ n)의 자원 요청이 “가용 자원 + 모든 Pj (j < i)의 보유 자원”에 의해 충족되어야 함
- 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
  - Pi의 자원 요청이 즉시 충족될 수 없으면 모든 Pj (j < i)가 종료될 때까지 기다린다
  - Pi-1이 종료되면 Pi의 자원요청을 만족시켜 수행한다

![](https://i.imgur.com/jyrbJ5y.png)

시스템이 safe state에 있으면 ⇒ no deadlock

시스템이 unsafe state에 있으면 ⇒ possibility of deadlock

****************\*\*\*\*****************Deadlock Avoidance****************\*\*\*\*****************

- 시스템이 unsafe state에 들어가지 않는 것을 보장
- 2가지 경우의 avoidance 알고리즘
  - Single instance per resource types - Resource Allocation Graph algorithm 사용
  - Multiple instances per resource types - Banker’s Algorithm 사용
  -

---

![](https://i.imgur.com/aIk5c2X.png)

- **Claim edge** Pi → Rj
  - 프로세스 Pi가 자원 Rj를 미래에 요청할 수 있음을 뜻함 (점선으로 표시)
  - 프로세스가 해당 자원 요청시 request edge로 바뀜 (실선)
  - Rj가 release되면 assignment edge는 다시 claim edge로 바뀜
- request edge의 assignment edge 변경시 (점선을 포함하여) cycle이 생기지 않는 경우에만 요청 자원을 할당
- Cycle 생성 여부 조사시 프로세스의 수가 n일 때 O(n^2)시간이 걸림

마지막 그래프는 데드락이 아니지만 데드락의 위험성이 있기 때문에 애초에 자원을 주지 않음

### Example of Banker’s Algorithm

![](https://i.imgur.com/08lIikq.png)

Allocation : 지금 할당 받은 자원 → P0 : B 1개

Max : Deadlock avoidance가 선언한 평생 사용할 maximum 자원

Available : 전체 인스턴스 중에 할당된 자원을 뺀 가용 자원

Need : 추가로 요청할 수 있는 자원

P1이 (1,0,2)를 요청 → 가용자원 (3,3,2) 충족 → 요청 가능 자원 (1,2,2) 충족 ⇒ 가능

P0이 (1,0,2)를 요청 → 가용자원 (3,3,2)이 요청 가능 자원 (7,4,3)보다 적을 경우 Banker’s Algorithm은 요청 자원 가능여부와 상관 없이 아예 요청을 받지 않음

⇒ deadlock은 발생하지 않지만 비효율적 (자원이 남지만 혹시 모를 상황에 대비하여 요청을 받지 않음)

sequence < P1, P3, P4, P2, P0 > : P1이 가용자원을 다 처리하고 반납하면 P3, → P4 → P2 → P0

각 프로세스의 최대 요청 자원을 다 충족할 수 있는 sequence가 존재하므로 safe state

### Deadlock Detection and Recovery

**Deadlock Detection**

- Resoure type 당 single instance인 경우
  - 자원할당 그래프에서의 cycle이 곧 deadlock을 의미
- Resource type 당 multiple instance인 경우
  - Banker’s algorithm과 유사한 방법 활용

**Wait-for graph 알고리즘**

- Resource type 당 single instance인 경우
- Wait-for graph
  - 자원할당 그래프의 변형
  - 프로세스만으로 node 구성
  - Pj가 가지고 있는 자원을 Pk가 기다리는 경우 Pk → Pj
- Algorithm
  - Wait-for graph에 사이클이 존재하는지를 주기적으로 조사
  - 프로세스가 n개일 경우 사이클을 찾는 것은 O(n^2) 시간이 걸림 : 화살표가 최대 n(n-1)개

**Resoure type 당 single instance인 경우**

![](https://i.imgur.com/k4vnTgE.png)

**Resource type 당 multiple instance인 경우**

![](https://i.imgur.com/PWt4e0m.png)

P1 프로세스가 A 2개, C 2개 요청 → 가용자원 A 0개, C 0개 → deadlock인가? no

deadlock이 있는지 없는지 확인할 때는 낙관적인 접근 → P0이 사용하고 있는 B 1개를 반납 할 것이라고 가정

만약 P2가 C 1개를 요청할 경우 P0 → P2 불가능(가용 자원에 A 0개, B 1개, C 0개) → deadlock 발생

**Recovery**

deadlock이 발생이 되면 recovery

- **Process termination - deadlock에 연루된 프로세스를 kill**

  - Abort **all** deadlocked processes - 모든 프로세스 중단
  - Abort **one process at a time** until the deadlock cycle is eliminated - deadlock이 없어질 때 까지 deadlock에 연루된 프로세스를 하나씩 kill

- **Resource Preemption - deadlock에 연루된 프로세스로부터 자원을 뺏는 방법**
  - 비용을 최소화할 희생양(victim)의 선정
  - safe state로 rollback하여 process를 restart
  - Starvation 문제
    - 동일한 프로세스가 계속해서 victim으로 선정되는 경우
    - cost factor에 rollback 횟수도 같이 고려

### Deadlock Ignorance

Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음

- Deadlock이 매우 드믈게 발생하므로 deadlock에 대한 조치 자체가 더 큰 overhead일 수 있음
- 만약, 시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 process를 죽이는 등의 방법으로 대처
- UNIX, Windows 등 대부분의 범용 OS가 채택
