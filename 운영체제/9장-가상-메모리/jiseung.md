# [가상 메모리 #1](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)

## Demand Paging

> 요청이 있으면 그 페이지를 메모리에 올린다.

- 실제로 필요할 때 page를 메모리에 올리는 것
  - I/O 양의 감소
  - Memory 사용량 감소
  - 빠른 응답 시간
  - (멀티 프로그래밍 환경에서) 더 많은 사용자 수용
- Valid/Invalid bit의 사용
  - Invalid의 의미
    - 사용되지 않는 주소 영역
    - 페이지가 물리적 메모리에 없는 경우
  - 처음에는 모든 page entry가 invalid로 초기화
  - address translation 시에 invalid bit이 set되어 있으면 `page fault`
    - 요청한 페이지가 메모리에 없음
    - CPU → 운영체제 (Trap, Software Interrupt)
  - invalid page에 접근하면 MMU가 trap 발생 (page fault trap)
  - Kernel mode로 들어가서 page fault handler가 invoke됨
  - 다음 순서로 page fault 처리
    - Invalid reference? (bad address, protection violation) ⇒ abort process
    - Get an empty page frame (없으면 뺏어온다. replace)
    - 해당 페이지를 disk에서 memory로 읽어온다.
      - disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt당함 (block)
      - Disk read가 끝나면 page tables entry 기록, valid/invalid bid = `valid`
      - ready queue에 process를 insert → dispatch later
    - 이 프로세스가 CPU를 잡고 다시 running
    - 아까 중단되었던 instruction을 재개

## Page Fault

### Performance of Demand Paging

- Page Fault Rate : 0 ≤ P ≤ 1.0
- page fault → overhead

## Free frame이 없는 경우

### Page Replacement

- 어떤 frame을 빼앗아올지 결정해야 함
- 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
- 동일한 페이지가 여러번 메모리에서 쫓겨났다가 다시 들어올 수 있음

### Replacement Algorithm

- page-fault rate을 최소화하는 것이 목표
- 알고리즘의 평가
  - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
- reference string의 예
  - 1,2,3,4,1,2,5,1,2,3,4,5

## Optimal Algorithm

> page fault를 가장 적게 하는 알고리즘

- MIN(OPT) : 가장 먼 미래에 참조되는 page를 replace
- Offline Algorithm
  - 미래의 참조를 미리 다 알고 있음
- 다른 알고리즘의 성능에 대한 upper bound 제공
  - Belady’s optimal altorithm, MIN, OPT

## FIFO(First In First Out) Algorithm

> 가장 먼저 들어온 것을 먼저 내쫓음

- FIFO Anomaly (Belady’s Anomaly)
  - more frames ⇒ more page faults

## LRU(Least Recently Used) Algorithm

> 가장 오래 전에 참조된 것을 지움

## LFU(Least Frequently Used) Algorithm

> 참조 횟수(reference count)가 가장 적은 페이지를 지움

- 최저 참조 횟수인 page가 여럿 있는 경우
- LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다

## LRU와 LFU 알고리즘의 예제 및 구현

- LRU : 리스트로 구현할 경우 O(1). 비교 과정이 필요 없다.
  - 가장 최근에 참조된 페이지만 알고 있으면 됨
- LFU : Min Heap으로 구현할 경우 O(log N)
  - 참조 횟수를 보면서 비교해야함. 단순 리스트로 구현할 경우 O(N)

# [가상 메모리 #2](https://core.ewha.ac.kr/publicview/C0101020140513133424380501?vmode=f)

## 다양한 캐슁 환경

### 캐싱 기법

- 한정된 빠른 공간(캐시)에 요청된 데이터를 저장해두었다가 후속 요청 시 캐시로부터 직접 서비스하는 방식
- paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용

### 캐시 운영의 시간 제약

- 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
- Butter caching이나 Web caching
  - 허용 범위 : O(1) ~ O(log n)
  - LRU, LFU
- paging system
  - page fault인 경우에만 OS 관여
  - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
  - O(1)인 LRU의 리스트 조작조차 불가능(LRU, LFU를 사용할 수 없다)
    - 운영체제에게 CPU가 넘어가지 않으므로, 정보가 별로 없음
      - 메모리에 그 페이지가 없으면 알 수 없다.
      - 같은 페이지 재참조 여부 알 수 없음.
      - 어떤 페이지가 최근에 참조되었는지도 모름

## Clock Algorithm

> LRU의 근사(approximation) 알고리즘

- Second chance algorithm, NUR(Not Used Recently), NRU(Not Recently User)
- Reference bit을 사용해서 교체 대상 페이지 선정. Circular List (하드웨어)
  - 0, 1 : Circular list를 한바퀴 돌면서 참조가 있었는지 여부. LRU와 비슷한 효과
  - Reference bit이 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
  - 포인터 이동 중에 reference bit 1은 모두 0으로 바꿈
  - Reference bit이 0인 것을 찾으면 그 페이지를 교체
  - 한바퀴 돌고 와서도(second chance) 0이면 그때는 replace 당한다.
  - 자주 사용되는 페이지라면 second chance가 올 때 1
- 개선
  - reference bit와 modified bit(dirty bit)을 함께 사용
    - modified bit이 0인 것 먼저
  - reference bit = 1 : 최근에 참조된 페이지
  - modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지)
    - 교체할 때 backing storage 갱신 필요

## Page Frame의 Allocation

> 미리 할당해주고 page fault를 최소화하자

- Allocation problem : 각 process에 얼만큼의 page frame을 할당할 것인가?
- Allocation의 필요성
  - 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조
    - 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있음
  - Loop를 구성하는 page들은 한꺼번에 allocate되는 것이 유리함
    - 최소한의 allocation이 없으면 매 loop마다 page fault
- Allocation Scheme
  - Equal allocation : 모든 페이지에 똑같은 개수 할당
  - Proportional allocation : 프로세스 크기에 비례하여 할당
  - Priority allocation : 프로세스의 priority에 다라 다르게 할당

## Global vs. Local Replacement

- Global replacement
  - Replace 시 다른 process에 할당한 frame을 빼앗아 올 수 있다.
  - Process별 할당량을 조절하는 또 다른 방법
  - FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용 할 때 해당
  - Working set, PFF 알고리즘 사용
- Local replacement
  - 자신에게 할당된 frame 내에서만 replacement
  - FIFO, LRU, LFU 등의 알고리즘을 process 별로 운영할 때 사용

## Thrashing

> 최소 메모리 할당이 없어서 page fault가 지나치게 일어나는 상황

- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지 못한 경우 발생
- Page fault rate이 매우 높아짐
  - CPU utilization이 낮아짐. 할 일 없음
- OS는 MPD(Multiprogramming degree)를 높여야 한다고 판단
  - 또 다른 프로세스가 시스템에 추가됨 (higher MPD)
  - 프로세스 당 할당된 frame의 수가 더욱 감소
- 프로세스는 page의 swap in/swap out으로 매우 바쁨
- 대부분의 시간에 CPU는 한가함
- low throughput

⇒ degree of multiprogramming을 조절 해야 함

## Working-Set Model

- Locality of reference
  - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다.
  - 집중적으로 참조되는 해당 page들의 집합을 locality set이라 함
- Working-set Model
  - Locality에 기반해 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합을 Working-set이라고 한다.
  - Working-set 모델에서는 process의 working-set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 swap out(suspent)
  - Thrashing을 방지함
  - Multiprogramming degree를 조절함

## Working-Set Algorithm

- Working set의 결정
  - Working set window를 통해 알아냄
  - window size가 x인 경우
    - 시각 t에서의 working set WS
      - Time interval[t-x, t] 사이에 참조된 서로 다른 페이지들의 집합
    - Working set에 속한 page는 메모리 유지. 속하지 않음 것은 버림
      - 즉, 참조된 후 x시간동안 해당 page를 메모리에 유지한 후 버림

## PFF(Page-Fault Frequency) Scheme

- page-fault rate의 상한값과 하한값을 둔다.
  - page fault rate이 상한값을 넘으면 frame을 더 할당
  - page fault rate이 하한값 이하면 할당 frame 수를 줄인다.
- 빈 frame이 없으면 일부 프로세스를 swap out

## Page Size의 결정

- Page size를 감소시키면
  - 페이지 수 증가
  - 페이지 테이블 크기 증가
  - Internal fragmentation 감소
  - Disk transfer의 효율성 감소
    - Seek/rotation vs. transfer
  - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
    - Locality의 활용 측면에서는 좋지 않음
- Trend : Larger page size
