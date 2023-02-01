# [가상 메모리 #1](https://core.ewha.ac.kr/publicview/C0101020140509151648408460?vmode=f)

## Demand Paging

### 실제로 필요할 때 Page를 메모리에 올리는 것

- I/O양의 감소
- 메모리 사용량 감소
- 빠른 응답 시간
- 더 많은 사용자 수용

### Valid / Invalid bit의 사용

### invalid의 의미

- 사용되지 않는 주소 영역인 경우
- 페이지가 물리적 메모리에 없는 경우

처음에는 모든 page entry가 invalid로 초기화
페이지가 메모리에 올라가면 valid로 변환
address translation시에 invalid bit이 set 되어 있으면 `page fault`

## Page Fault

invalid page를 접근하면 MMU가 trap을 발생 시킴 (page fault trap)

Kernel mode로 들어가서 page fault handler가 발생

### 처리 방법

1. invalid reference (bad address, protection violance) => process 중단
2. 비어있는 page frame 가져옴 (없으면 뺏기)
3. 해당 페이지를 disk에서 memory로 읽어옴
   1. disk I/O가 끝나기까지 이 프로세스는 CPU를 선점 당함 (block)
   2. Disk read가 끝나면 page table entry 기록, valid/invalid bit = 'valid'
   3. ready queue에 process를 insert -> dispatch later
4. 이 프로세스가 CPU를 잡고 다시 running
5. 아까 중단되었던 intruction 재개

### performance of demand paging

page fault rate 0 <= p <=1.0

- `p = 0` : no page faults
- `p = 1` : 모든 레퍼런스가 잘못

### Effective access time

(1-p) \* memory access (페이지 fault가 안나는 비율) + p

> replacement 알고리즘은 Page fault rate이 최대한 낮아지도록 만들어야함

### Free frame이 없는 경우

#### Page replacement

- 어떤 frame을 빼앗아올지 결정해야함
- 곧바로 사용되지 않을 page를 쫒아내는 것이 좋음
- 동일한 페이지가 여러번 메모리에서 쫒겨났다가 다시 들어올 수 있음

#### Replacement Algorithm

- 목표 : page-fault rate를 최소화하는 것
- 알고리즘의 평가 : 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
- reference string의 예 (1,2,3,4,1,2,5,1,2,3,4,5)

## Optimal Algorithm

page fault를 가장 적게 하는 알고리즘

MIN (OPT) : 가장 먼 미래에 참조되는 page를 replace

미래의 참조를 아는 방법 ❌
따라서 `offline algorithm`라고 불림 = 실제로 사용되지 않음

다른 알고리즘의 성능에 대한 upper bound 제공
(belady's optimal algorithm, MIN, OPT 등으로 불림)

## FIFO(First In First Out) Algorithm

먼저 들어온 것을 먼저 내쫒음

`FIFO Anomaly` : 프레임을 늘리면 페이지 성능이 더 안좋아짐

## LRU(Least Recently Used) Algorithm

가장 많이 사용되는 알고리즘
가장 오래 전에 참조된 것을 지움

## LFU(Least Frequently Used) Algorithm

참조 횟수가 가장 적은 페이지를 지움

최저 참조 횟수인 page가 여러개 있는 경우

- 여러 page 중 임의로 선정
- 성능 향상을 위해 가장 오래전에 참조된 page를 지우게 구현 가능

장단점

- 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도 정확히 반영
- 참조 시점의 최근성 반영 x
- LRU보다 구현이 어려움

## LRU와 LFU 알고리즘의 예제 및 구현

### LRU

`Linked list` 형태로 페이지 관리 -> O(1)

### LFU

하나하나 비교를 해가면서 교체 -> O(n) 🥲
따라서 `heap` 형태로 페이지 관리 -> O(log n)
(루트에 있는 노드를 제거하는 형태)

# [가상 메모리 #2](https://core.ewha.ac.kr/publicview/C0101020140513133424380501?vmode=f)

## 다양한 캐싱 환경

### 캐싱 기법

한정된 빠른 공간 (= 캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐쉬로부터 직접 서비스하는 방식

Paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용

cache memory : 물리 메모리에 접근하기 전에 확인하는 캐쉬메모리
buffer caching : 디스크의 파일 시스템
web caching : 웹 페이지 방문시 사용되는 캐싱 메모리

### 캐쉬 운영의 시간 제약

교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음

buffer caching이나 web caching의 경우 O(1)~ O(log n)까지 허용

#### Paging system의 경우 LRU, LFU 가능?

- Page fault인 경우에만 OS가 관여 (운영체제는 반쪽자리 정보만 가지고 있게 됨)
- 페이지가 이미 메모리에 존재하는 경우 참조 시각 등의 정보를 OS가 알 수 없음
- O(1)인 LRU의 list 조작조차 불가능

-> 그래서 사용하는 알고리즘 : Clock alogorithm

## Clock Algorithm (Second chance alogorithm, NUR, NRU)

LRU의 근사 알고리즘

reference bit을 사용해 교체 대상 페이지 선정 (circular list)

- reference bit가 0인 것을 찾을 때 까지 포인터를 하나씩 앞으로 이동
- 포인터 이동하는 중 reference bit 1을 모두 0으로 변경
- reference bit이 0인 것을 찾으면 그 페이지를 교체
- 한바퀴 돌아와서도 0이면 그때는 replace 당함 (자주 사용되는 페이지라면 second chance가 올 때 1)

Clock algorithm의 개선

- reference bit과 modified bit (dirty bit)을 함께 사용
- `reference bit == 1` : 최근 참조된 페이지
- `modified bit == 1` : 최근 변경된 페이지 (I/O 동반)

## Page Frame의 Allocation

어떤 프로그램이 원할하게 실행되기 위해선, 일련의 페이지들이 메모리에 함께 올라와야 효율적

Allocation problem : 각 프로세스에 얼만큼의 page frame을 할당?

### Allocation의 필요성

- 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조
  - 명령어 수행을 위해 최소한 할당되어야 하는 Frame의 수 존재
- Loop를 구성하는 Page들은 한꺼번에 allocate 되는 것이 유리함
  - 최소한의 allocation이 없으면 매 Loop 마다 page fault

### Allocation scheme

`equal allocation` : 모든 프로세스에 똑같은 갯수 할당
`proportional allocation` : 프로세스 크기에 비례해 할당
`prioriy allocation` : 프로세스의 priority에 따라 다르게 할당

## Global vs. Local Replacement

### Global replacement

- replace시 다른 프로세스에 할당된 frame 빼앗기
- 프로세스별 할당량을 조절하는 방법
- FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용시 해당
- Working set, PFF 알고리즘 사용

### Local replacement

- 프로그램에 할당을 해놓은 다음 자신에게 할당된 frame내에서만 replacement (다른 프로세스에 영향 X)
- FIFO, LRU, LFU 등의 알고리즘 프로세스별로 운영시

## Thrashing

프로세스의 원할한 수행에 필요한 최소한의 Page frame 수를 할당받지 못하는 경우

- page fault rate ⬆️
- CPU utilization ⬇️
- OS는 MPD를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가(Higher MPD)
- 프로세스 당 할당된 frame의 수 ⬇️
- 프로세스는 Page의 swap in/ swap out으로 busy
- 대부분의 시간 동안 CPU는 한가
- Throughput ⬇️

## Working-Set Model

### Locality of reference

프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조
이 때 참조되는 페이지들의 집합 = `locality set`

### Working-set Model

ㅎㅎ
locality에 기반해 프로세스가 일정 시간동안 원활하게 수행되지 위해 한꺼번에 메모리에 올라와있어야 하는 page들의 집합 = `Working set`

프로세스의 Working set 전체가 메모리에 올라와 있어야 수행
그렇지 않는 경우, 모든 frame 반납 후 swap out (suspend)

- thrashing을 방지
- multiprogramming degree를 결정

## Working-Set Algorithm

Working set의 결정
=> working set window(델타 시간)를 통해 알아냄

## PFF(Page-Fault Frequency) Scheme

Page-fault rate의 상한값과 하한값을 두어 일정값을 유지

- page fault rate이 상한값을 넘으면 frame을 더 할당
- page fault rate이 하한값 이하면 할당 frame 수를 줄임

빈 frame이 없으면 일부 프로세스를 swap out

Page fault가 너무 많이 발생한다 = working set이 보장이 안됨.

## Page Size의 결정

페이지 사이즈를 감소시키면

- 페이지 수 ⬆️
- 페이지 테이블 크기 ⬆️
- internal fragmentation ⬇️
- disk transfer의 효율성 ⬇️
- 필요한 정보만 메모리에 올라와 메모리 이용 효율적 (Locality 측면에서는 안좋음)

> trend : larger page size
