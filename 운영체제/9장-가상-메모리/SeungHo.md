## [가상 메모리 #1](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)

### Demand Paging - 요청이 있으면 페이지에 올림

- 실제로 필요할 때 page를 메모리에 올리는 것
  - I/O 양의 감소
  - Memory 사용량 감소
  - 빠른 응답 시간
  - 더 많은 사용자 수용
- Valid / Invalid bit의 사용
  - Invalid의 의미
    - 사용되지 않는 주소 영역인 경우
    - 페이지가 물리적 메모리에 없는 경우
  - 처음에는 모든 page entry가 invalid로 초기화 → page가 메모리에 올라가면 valid로 전환
  - address translation 시에 invalid bit이 set되어 있으면 ⇒ **“page fault”**

### Memory에 없는 Page의 Page Table

![](https://i.imgur.com/y2gIS7F.png)

A, C, F는 물리적인 메모리에 올라와 있기 때문에 0번, 2번, 5번 페이지는 valid

나머지 페이지들은 backing store에 내려와 있음 → invalid

프로그램을 구성하는 페이지는 A ~ F까지이지만 주소공간에서 7번까지의 주소 영역을 지원해주기 때문에 page table에는 7번까지의 entry가 만들어진다 (G, H는 사용이 안되는 페이지 → invalid)

CPU가 논리 주소를 주고 메모리 몇번지인지 보려고 할 때(주소 변환을 할 때) invalid이면 해당 페이지는 disk에서 memory로 올림 → I/O작업 : 사용자 프로세스가 직접 못하는 일

⇒ “page fault” → CPU는 자동적으로 운영체제에 넘어가게 됨

### Page Fault

- invalid page를 접근하면 MMU가 trap을 발생시킨다 (page fault trap)
- Kernel mode로 들어가서 page fault handler (page fault를 처리하는 코드)가 invoke됨
- 다음과 같은 순서로 page fault를 처리한다
  1. Invalid reference? (eg. bad address, protection violation / 잘못된 주소이거나 권한이 없을 때) ⇒ abort process
  2. Get an empty page frame (없으면 뺏어온다: replace) - 메모리에 빈 페이지 공간이 없으면 쫓아냄
  3. 해당 페이지를 disk에서 memory로 읽어온다 - 매우 느린 작업 ⇒ CPU preempt
     1. disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함 (block)
     2. Disk read가 끝나면 page tables entry 기록, valid/invalid bit = “valid”
     3. ready queue에 process를 insert → dispatch later
  4. 이 프로세스가 CPU를 잡고 다시 running
  5. 아까 중단되었던 instruction을 재개

### Steps in Handling a Page Fault

![](https://i.imgur.com/DA7A2nn.png)

1. 메모리 reference → 주소변환 → page table invalid
2. trap → CPU가 운영체제에 자동으로 넘어감
3. 운영체제는 backing store에 있는 페이지를 물리적인 메모리로 올려놓음
4. 해당하는 frame 번호를 page table의 entry에 기록하고 valid로 전환 → restart instruction

### Performance of Demand Paging

- Page Fault Rate 0 ≤ p ≤ 1.0
  - if p = 0 no page faults
  - if p = 1, every reference is a fault
- Effective Access Time = (1 - p) \* memory access + p(OS & HW page fault overhead + [swap page out if needed] + swap page in + OS & HW restart overhead)

### Free frame이 없는 경우

- Page replacement
  - 어떤 frame을 빼앗아올지 결정해야 함
  - 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
  - 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음
- Replacement Algorithm
  - page-fault rate을 최소화하는 것이 목표
  - 알고리즘의 평가
    - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
  - reference string의 예 - 시간순서에 따라서 page들을 서로 다른 번호를 붙여놓고 page들이 참조된 순서로 나열해 놓은 것
    1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5.

### Page Replacement

![](https://i.imgur.com/rPY7twL.png)

write가 발생하면 변경된 내용을 memory에서 backing store에 써줘야함

반면, backing store에 있는 것을 memory에 올린 다음에 쫓겨날 때 까지 변경된 것이 없으면 메모리에서 그냥 지워버리면 됨

### Optimal Algorithm

![](https://i.imgur.com/i0AhYK0.png)

미래에 참조되는 페이지들을 미리 다 안다고 가정

처음에는 메모리가 비어있기 때문에 당연하게도 1, 2, 3, 4번 페이지를 올리는데 page fault가 발생함

- 미래의 참조를 어떻게 아는가?
  - Offline algorithm - 실제 시스템에서 사용될 수 없음
- 다른 알고리즘의 성능에 대한 **upper bound** 제공 → 이 알고리즘으로 최적의 상황을 참조
  - Belady’s optimal algorithm, MIN, OPT 등으로 불림

### FIFO(First In First Out) Algorithm

![](https://i.imgur.com/qoyEnYQ.png)

메모리 frame을 늘려줬는데 성능이 더 안좋아지는 경우가 발생하기도 함

### LRU(Least Recently Used) Algorithm

![](https://i.imgur.com/yNUGOJs.png)

메모리 관리 측면에서 가장 많이 쓰는 알고리즘

들어온 다음에 재사용 된 것을 고려함

### LFU(Least Frequently Used) Algorithm

참조 횟수(reference count)가 가장 적은 페이지를 지움

- 최저 참조 횟수인 page가 여럿 있는 경우
  - LFU 알고리즘 자체에서는 여러 page 중 임의로 선정
  - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있음
- 장단점
  - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
  - 참조 시점의 최근성을 반영하지 못함
  - LRU보다 구현이 복잡함

### LRU와 LFU 알고리즘의 예제 및 구현

![](https://i.imgur.com/7djP7It.png)

LRU : 가장 참조된지 오래 된 페이지를 삭제하지만 그 페이지가 참조된 횟수를 고려하지 못함

LFU : 가장 적게 참조된 페이지를 삭제하지만 그 페이지의 참조 시점을 고려하지 못함

![](https://i.imgur.com/fpYnBen.png)

LRU : page replace 시 제일 위의 페이지 삭제 - LinkedList방식

LFU : New reference 시 참조 횟수를 비교해야하기 때문에 heap영역 방식으로 구현 (LinkedList 방식으로 구현할 경우 O(N))

## [가상 메모리 #2](https://core.ewha.ac.kr/publicview/C0101020140513133424380501?vmode=f)

### 다양한 캐슁 환경

**캐슁 기법**

- 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐쉬로부터 직접 서비스하는 방식
- paging system 외에도 cache memory(주소 변환을 위한 cache memory : TLB / CPU에서 메모리 접근 시 메인 메모리에 직접 접근하는 것이 아니라 CPU와 메인메모리 사이의 cache memory라는 좀 더 빠른 계층을 먼저 접근), buffer caching(파일 시스템에 대한 read, write 요청을 메모리에서 빠르게 서비스하는 방식), Web caching(웹 서버로부터 이미 읽어온 웹 페이지를 캐쉬에 저장해놓고 제공) 등 다양한 분야에서 사용

paging system에서의 캐쉬는 메인 메모리 ↔ 물리적인 메모리(캐쉬)에 없을 때 page fault → backing store에서 물리적인 메모리로 읽어옴

************\*\*************\*\*************\*\*************캐쉬 운영의 시간 제약************\*\*************\*\*************\*\*************

- 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
- Buffer caching이나 Web caching의 경우
  - O(1)에서 O(log n)정도까지 허용
- Paging system인 경우
  - page fault인 경우에만 OS가 관여함
  - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
  - O(1)인 LRU의 list 조작조차 불가능

### Paging System에서 LRU, LFU 가능한가?

![](https://i.imgur.com/GUQDW3V.png)

page table 접근 시 invalid일 경우 page fault가 발생하고 disk 접근이 필요하기 때문에 trap 발생 → CPU의 제어권이 프로세스 A로부터 운영체제로 넘어감 → 운영체제가 page fault가 났던 페이지를 disk에서 읽어서 메모리에 올려놓음

⇒ 이 과정에서 필요하면 replacement를 해야함

but 운영체제는 가장 오래전에 참조된 페이지나 가장 많이 참조된 페이지를 알 수 없음

→ 프로세스가 사용하는 페이지가 이미 메모리에 올라와 있는 경우는 하드웨어적으로 주소변환을 하고 CPU로 읽어들임 즉, 운영체제에 CPU가 안넘어가므로 접근 시간을 운영체제는 알 수 없음

반면에 page fault가 발생하여 disk에서 메모리로 페이지를 올리게 되는 경우는 CPU 제어권이 운영체제에 넘어와서 개입하기 때문에 접근 시간과 같은 정보를 알 수 있음

이러한 이유로 paging system 즉 virtual memory system에서는 LRU, LFU를 사용할 수 없음

### Clock Algorithm

![](https://i.imgur.com/fsiTRic.png)

- LRU의 근사 (approximation) 알고리즘 - 시계바늘이 한번 더 돌아오는 동안에 참조되지 않은 페이지를 삭제
- 여러 명칭으로 불림
  - Second chance algorithm - 기회를 한 번 더 줌
  - NUR (Not Used Recently) 또는 NRU (Not Recently Used) - 최근에 사용되지 않은 페이지를 쫓아냄
- 이미 메모리에 존재하는 페이지에 대해서 참조가 되면 reference bit을 1로 세팅 : 운영체제가 하는 일이 아닌 하드웨어가 하는 일
- Reference bit을 사용해서 교체 대상 페이지 선정 (circular list)
- reference bit가 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
- 포인터 이동하는 중에 reference bit 1은 모두 0으로 바꿈
- Reference bit이 0인 것을 찾으면 그 페이지를 교체
- 한 바퀴 되돌아와서도 (=second chance) 0이면 그때에는 replace 당함
- 자주 사용되는 페이지라면 second chance가 올 때 1

**Clock algorithm의 개선**

- reference bit과 modified bit (dirty bit)을 함께 사용
- reference bit = 1 : 최근에 참조된 페이지
- modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지)
  메모리에 올라와있는 어떤 페이지를 쫓아내야 할 때 modified bit이 0이면 삭제, 1이면 적어도 한 번 CPU에서 write하여 수정한 것이므로 backing store에 변경 내용을 반영한 후 삭제

### Page Frame의 Allocation

Allocation problem : 각 process에 얼마만큼의 page frame을 할당할 것인가?

각각의 프로그램에게 어느정도의 메모리 페이지를 나눠주자

Allocation의 필요성

- 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조
  - 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있음
- Loop를 구성하는 page들은 한꺼번에 allocate 되는 것이 유리함
  - 최소한의 allocation이 없으면 매 loop 마다 page fault

**Allocation Scheme**

- **Equal allocation** : 모든 프로세스에 똑같은 개수 할당
- **Proportional allocation** : 프로세스 크기에 비례하여 할당
- **Priority allocation** : 프로세스의 priority에 따라 다르게 할당

### Global vs. Local Replacement

**Global replacement → 굳이 미리 할당하지 않고 FIFO, LRU, LFU 등의 알고리즘을 사용하면 알아서 프로세스 별로 메모리 할당량이 자동으로 조절**

- Replace 시 다른 process에 할당된 frame을 빼앗아 올 수 있음
- Process별 할당량을 조절하는 또 다른 방법임
- FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용시에 해당
- Working set, PFF 알고리즘 사용

**Local replacement - 프로그램에 할당을 해 놓은 후**

- 자신에게 할당된 frame 내에서만 replacement
- FIFO, LRU, LFU 등의 알고리즘을 process 별로 운영시

### Thrashing

- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지 못한 경우 발생
  - 프로그램에 메모리가 너무 적게 할당되어 page fault가 지나치게 자주 일어나는 경우
- Page fault rate이 매우 높아짐
- CPU utilization이 낮아짐 - CPU가 인스트럭션 살행을 하려고 하면 그 페이지가 메모리에 없음 → I/O를 해야함
- OS는 MPD (Multiprogramming degree)를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가됨 (higher MPD)
- 프로세스 당 할당된 frame의 수가 더욱 감소
- 프로세스는 page의 swap in / swap out으로 매우 바쁨
- 대부분의 시간에 CPU는 한가함
- low throughput

### Thrashing Diagram

![](https://i.imgur.com/j0sqOpS.png)

degree of multiprogramming : 지금 메모리에 올라와있는 프로그램의 개수

CPU utilization : CPU 이용률

메모리에 올라와있는 프로그램의 개수가 많아지면 각 프로세스가 너무 작은 frame 가지게 됨

### Working-Set Model

Locality of reference

- 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다
- 집중적으로 참조되는 해당 page들의 집합을 locality set이라 함

Working-set Model

- Locality에 기반하여 프로세스가 일정 시간 동안 원할하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합을 **Working Set**이라 정의함
- Working Set 모델에서는 process의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 swap out (suspend)
- Thrashing을 방지함
- Multiprogramming degree를 결정함

### Working-Set Algorithm

![](https://i.imgur.com/gONiGaf.png)

Working set을 미리 알 수 없음 → 과거를 통해 Working set 추정 → 과거 Δ시간동안 참조된 페이지를 Working set이라고 간주

**Working-Set Algorithm**

- Process들의 working set size의 합이 page frame의 수보다 큰 경우
  - 일부 process를 swap out시켜 남은 process의 working set을 우선적으로 충족시켜 준다 (MPD를 줄임)
- Working set을 다 할당하고도 page frame이 남는 경우
  - Swap out 되었던 프로세스에게 working set을 할당 (MPD를 키움)

**Window size Δ**

- Working set을 제대로 탐지하기 위해서는 window size를 잘 결정해야 함
- Δ값이 너무 작으면 locality set을 모두 수용하지 못할 우려
- Δ값이 크면 여러 규모의 locality set 수용
- Δ값이 무한이면 전체 프로그램을 구성하는 page를 working set으로 간주

### PFF(Page-Fault Frequency) Scheme

![](https://i.imgur.com/aGWo18D.png)

- page-fault rate의 상한값과 하한값을 둔다
  - Page fault rate이 상한값을 넘으면 frame을 더 할당한다
  - Page fault rate이 하한값 이하이면 할당 frame 수를 줄인다
- 빈 frame이 없으면 일부 프로세스를 swap out

### Page Size의 결정

**Page size를 감소시키면**

- 페이지 수 증가
- 페이지 테이블 크기 증가 → page table을 위한 메모리 낭비가 심해짐
- Internal fragmentation 감소
- Disk transfer의 효율성 감소
  - Seek/rotation vs. transfer
- 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
  - Locality의 활용 측면에서는 좋지 않음

**Trend**

- Larger page size
