## [교착상태 #1](https://core.ewha.ac.kr/publicview/C0101020140411151510275738?vmode=f), [교착상태 #2](https://core.ewha.ac.kr/publicview/C0101020140415131030840772?vmode=f)

Deadlock: 교착 상태

공동의 같은 것들을 필요로 하면서 가진 것은 내놓지 않는 상태. 무한히 상대의 것만 기다리는 상태

### The Deadlock Problem

- 일련의 Process들이 서로가 가진 자원(resorce)을 기다리며 block된 상태
- Resource: HW, SW 포함하는 개념
- Process가 Resourece를 사용하는 절차
  1. Request 요청
  2. Allocate 획득, 할당
  3. Use 사용
  4. Release 반납

### Deadlock 발생의 4가지 조건

4가지 중 하나라도 만족하지 않으면 Deadlock은 발생하지 않는다.

1. Mutual exclusion(상호 배제): 매 순간 하나의 Process만 자원 사용 가능 => 여러 Process면 발생 X
2. No preemption(비선점): 빼앗기지 않는다. => 자원을 빼앗기면 발생 X
3. Hold and wait(보유대기): 내 자원은 hold, 다른 자원 wait
4. Circular wait(순환대기): 각자 필요 자원이 꼬리에 꼬리를 물고 있는 형태

### Resource-Allocation Graph(자원할당그래프)

![](https://i.imgur.com/k5IyCKx.png)
R은 자원, 안의 점은 instance 개수, P는 프로세스  
R->P: 자원 R이 프로세스 P에 속해있다.  
P->R: 프로세스 P가 자원 R을 요청했다.

- 이 그림은 Deadlock인가?
  - 아니다.  
    일단 그림안에 cycle이 없으면 deadlock이 아니다.
- 그림 안에 cycle이 있다?  
  -> 각 R마다 하나의 instance만 있으면 deadlock / 여러 개면 가능성 있는 상태, 따져야 함

![](https://i.imgur.com/D0QASwL.png)
왼쪽은 Deadlock O / 오른쪽은 Deadlock X

### Deadlock의 처리 방법

위로 갈 수록 강한 처리 방법  
위의 2개(prevention, avoidance)는 미연 방지, 아래 2개(detection and recovery, ignorance)는 방치  
Deadlock 자체가 빈번한 event가 아니기 때문에 현대 OS에서는 미연에 방지하는 등의 처리가 overhead가 더 비효율적이라 생기면 그냥 kill하는 ignorance 방식 채택

#### Deadlock Prevention

발생요건 4가지 중 하나를 미리 처리하는 것

1. 상호배제 => 사용자가 어떻게 하기는 어려움
2. 비선점 => preemption 할 수 있도록 해줌.  
   CPU는 주로 preemptive scheduling. 따라서 deadlock 없음. CPU는 빼앗고 다시 회복하면 그 전 상태를 기억하므로 선점 스케쥴링 가능
3. 보유대기 => 기다리는 상황에서는 보유하지 않도록
   1. Process 시작시 모든 필요자원 할당 받도록: 종료될 때 모든 것 반납할 것이므로(모든 것 hold라서 wait가 없다) -> 자원의 비효율성
   2. 요청해야하는 경우 hold 모두 놓고서 요청: 자진 반납으로 해결(hold 제거)
4. 순환대기 => 자원에 번호를 매긴다. 획득 순서를 정해놓는 것

=> 생기지도 않은 deadlock 방지를 위해 제약조건을 달아놓기 때문에 비효율적 / 이용률(utilization) 저하, 처리량(throughput) 감소(CPU 입장 스케쥴링 평가 척도 하락), Starvation 문제

#### Deadlock Avoidance

자원 요청에 대한 부가 정보를 이용 -> deadlock 가능성 없을 때(safe)만 할당, unsafe할 경우 자원이 남아있어도 할당하지 않는다.  
평생 쓸 자원의 최대량을 알고 있다고 가정 => 최악의 상황을 가정해서 deadlock 가능성 판단 가능

- Resource Allocation Graph algorithm: 자원 당 instance 하나일 때  
  ![](https://i.imgur.com/5xDRFuT.png)
  실선: 가지고 있는 상태, 점선: 지금 갖고 있지는 않지만 언젠가는 요청한다.
- Example of Banker's Algorithm: 자원 당 instance 여러 개일 때  
  ![](https://i.imgur.com/81cET3W.png)
  (10, 5, 7)로부터 총 할당(7, 2, 5) 빼서 (3, 3, 2)  
  Max table을 받고 추가 요청 가능 table을 만들어서 판단.  
  그림에서도 P_0이 지금 (0, 2, 0)만큼 요청해도 최대(7, 4, 3)을 원할 가능성이 있기 때문에 unsafe라 판단, 다른 P들이 놓아야 자원 할당  
  대단히 보수적인 알고리즘이기때문에 최악을 상정하고 요청 판단. 잠재적으로 줄 수 없으면 안 줌. P_1, P_3의 요청은 다 받아준다.  
  다들 반납하고 Available이 Need보다 커지면 그 때 대기 중인 P_0에도 할당
  프로세스 처리 순서(sequance)가 존재하므로 safe  
  하지만 대단히 비효율적. 자원이 남는데도 할당하지 않으므로

--- 여기까지는 미연에 방지 ---

--- 여기까지는 방치 후 처리 ---

Deadlock은 빈번한 event가 아니다. 비효율적으로 처리하지 말자

#### Deadlock Detection and recovery

- Detection
  table을 그려서도 확인 가능
  - 자원당 instance 1개일 때
    ![](https://i.imgur.com/uWg0S0D.png)
    자원 할당 그래프(Resource-Allocation Graph)를 자원 빼버리고 간단히 그린 것이 오른쪽 그래프 wait-for graph  
    wait-for graph에서 cycle 찾는 overhead: O(n^2) -> n개에 대해 n-1개의 화살표가 있을 수 있으므로
  - 자원당 instance n개일 때
    ![](https://i.imgur.com/gx6XdK1.png)
    max를 미리 알 필요는 없다. 아무것도 요청하지 않는 P 처리해서 반납되는 자원들로 판단해간다.
- Recovery
  - Process termination: P kill
    - 모든 P kill
    - 하나씩 kill 해가며 확인
  - Resource Preemption: R 빼앗기
    - victim 선정
    - 자원 빼앗아 deadlock 해결
    - 같은 패턴으로만 victim을 선정하면 starvation 문제(victim 입장) 발생

#### Deadlock Ignorance

deadlock 일어나든 안일어나든 상관하지 않는 것. 방지, detection 모두 시스템 overhead 큼.  
발생시 사용자가 알아서 처리. OS는 신경쓰지 않음.  
대부분의 OS가 채택한 방식
