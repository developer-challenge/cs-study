# [Chapter 9. 가상 메모리 Part-1](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)

물리적인 메모리의 주소 변환은 운영체제가 관여하지 않는다.
가상 메모리는 전적으로 운영체제가 관여한다.

이 챕터부터는 페이징 기법을 사용한다고 가정
(대부분의 시스템에서 페이징 기법을 사용)

## Demand Paging(요구 페이징)

- 실제로 필요할 때(요청이 있을 때) page를 메모리에 올리는 것을 의미

#### Demand Paging의 장점

- I/O 양의 감소 : 프로세스 전체를 메모리에 올리는 데 소요되는 입출력 오버헤드가 줄어듬
- 메모리 사용량 감소
- 빠른 응답시간
- 더 많은 사용자(프로세스) 수용

#### Valid-Invalid bit 사용

![](https://i.imgur.com/vqkxTws.png)

- Valid(v) : 메모리에 상주하고 있음을 의미(in memory)
- Invalid(i) : 사용되지 않는 주소 영역 또는 페이지가 물리적 메모리에 없음을 의미(not in memory)
- 프로세스 실행 전 모든 페이지 엔트리는 invalid로 초기화
- 특정 페이지가 참조되어 메모리에 적재되는 경우 해당 페이지는 valid로 변경
- 메모리에 적재되어 있던 페이지가 디스크 스왑 영역으로 쫓겨날 때 다시 invalid로 변경
- 주소 변환 시 페이지가 메모리에 올라와 있지 않아 invalid bit로 세팅되어 있다면
  ⇒ Page fault(페이지 부재)가 일어났다고 말한다.

## Page Fault(요구 페이징의 페이지 부재 처리)

- CPU가 invalid page에 접근하면 주소 변환을 담당하는 하드웨어인 MMU가 page fault trap(페이지 부재 트립)을 발생시킨다.
- CPU 제어권이 커널모드로 전환되어 page fault handler(페이지 부재 처리 루틴)이 호출된다.

#### page fault 처리 과정

1. 먼저, 운영체제는 해당 페이지에 대한 접근이 유효한지 확인

- 사용하지 않는 주소에 접근(bad address), 해당 페이지에 대한 접근 권한을 위반했을 경우(protection violation)
  ⇒ 해당 프로세스를 종료시킴(abort process)
- 해당 페이지에 대한 접근이 적법한 경우 2번

2. 비어있는 프레임(free frame)을 얻는다.

- 비어있는 프레임이 없다면 기존 메모리에 올라와 있는 페이지 중 하나를 디스크로 쫓아내 뺏어온다(replace).

3. 물리적 메모리에 비어있는 프레임을 할당받아 해당 페이지를 디스크에서 메모리로 읽어온다.

- Disk I/O 작업은 오랜 시간이 소요되므로 Disk I/O가 끝날 때가지 이 프로세스는 CPU를 빼앗기고 block 상태가 된다.
- Disk I/O 작업이 끝나면 인터럽트가 발생하고, 페이지 테이블 엔트리에 해당 페이지를 valid bit로 설정 및 프레임 번호를 기록
- 프로세스를 ready queue로 이동 → dispatch later

4. 이 프로세스가 다시 CPU를 할당받아 running 상태로 변경
5. 이 전에 중단되었던 인스트럭션부터 실행을 재개

### Steps in Handling a Page Fault

![](https://i.imgur.com/Y0ywUtf.png)

① 메모리 레퍼런스가 있어 주소 변환을 위해 페이지 테이블 확인
② 테이블 엔트리에 invaild 상태이므로 page fault trap 발생하며 CPU가 운영체제에게 넘어감
③ 해당 페이지는 백킹스토어에 존재
④ 운영체제가 백킹스토어에 있는 페이지를 메모리의 비어있는 프레임으로 읽어온다.
⑤ 디스크 입출력 작업이 끝나면 페이지 테이블 엔트리에 프레임 번호를 기록하고, valid bit로 설정한다.
⑥ CPU를 다시 할당받아 중단되었던 프로세스를 다시 실행

### Performance of Demand Paging(요구 페이징의 성능)

- Page fault가 발생하면 요청된 페이지를 디스크로부터 메모리로 읽어오는 막대한 오버헤드가 발생한다.
  ⇒ Page fault가 적게 발생할수록 요구 페이징의 성능은 향상될 수 있다.
- Page Fault Rate(페이지 부재 발생 비율) : 0 ≤ P ≤ 1
  - `P = 0` : Page fault가 한 번도 발생하지 않는 경우
  - `P = 1` : 모든 참조 요청에서 Page fault가 발생한 경우
  <pre>
  EAT(Effective Access Time)
  = (1 - P) × 메모리 접근 시간
  * P × (Page fault 처리 오버헤드
  * [메모리에 빈 프레임이 없는 경우 swap-out 오버헤드]
  * 요청된 페이지의 swap-in 오버헤드
  * 프로세스 재시작 오버헤드)
  </pre>

## Page replacement(페이지 교체)

- 물리적 메모리에 빈 프레임이 존재하지 않는 경우 메모리에 올라와 있는 페이지 중 하나를 디스크로 쫓아내 메모리에 빈 공간을 확보하는 작업
  ⇒ Page replacement(페이지 교체)
- 어떤 프레임을 빼앗아올지 결정해야한다.
- 동일한 페이지가 여러 번 메모리에 쫓겨났다가 다시 들어올 수 있다.

⇒ 가까운 미래에 참조될 가능성이 가장 적은 페이지를 선택하는 것이 좋다.

![](https://i.imgur.com/fvaOsOM.png)

#### Replacement Algorithm(교체 알고리즘)

- Page Fault Rate를 최소화 하는 것이 목표
- 페이지 교체 알고리즘의 성능 평가
  - 주어진 page reference string(페이지 참조열)에 대해 page fault가 얼마나 발생하는지 조사
  - page reference string : 참조되는 페이지들의 번호를 시간 순서에 따라 나열한 것
- page reference string의 예 : `1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5`

### Optimal Algorithm(최적 페이지 교체)

- 물리적 메모리에 존재하는 페이지 중 가장 먼 미래에 참조될 페이지를 쫓아내는 것
- 빌레디의 최적 알고리즘(Belady's optimal algorithm) 또는 MIN, OPT등의 이름으로 부른다.
- 이 알고리즘은 미래에 어떤 페이지가 어떤 순서로 참조될지 미리 알고 있다는 전제하에 알고리즘을 운영(실제 시스템에서 온라인으로 사용할 수 없음)
  ⇒ Offline algorithm
- Offline Optimal Algorithm은 다른 어떠한 알고리즘 보다 가장 적은 Page Fault Rate를 보장하
  ⇒ 다른 알고리즘의 성능에 대한 상한선(upper boubd)을 제공

#### Example of Optimal Algorithm

![](https://i.imgur.com/UGkpmrf.png)

- page reference string : `1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5`
- 물리적 메모리의 페이지 프레임의 갯수 : 4
- 메모리 내 빨간색 페이지 번호 : 페이지 부재 발생 의미
- 메모리 내 분홍색 페이지 번호 : 페이지 부재가 일어나지 않고 메모리에서 바로 참조한 것을 의미
- 알고리즘 동작 방식
  - 초기에는 메모리가 비어있으므로 처음 4회까지의 페이지 참조시 Page fault가 발생
  - 그 다음 5회, 6회에 참조되는 페이지는 이미 메모리에 올라와 있으므로 Page fault가 발생하지 않는다.
  - 7회에서 page 5를 참조하려고 할 때 Page fault가 발생하는데, 메모리에 빈 공간이 없으므로 현재 메모리에 올라와 있는 페이지 중 하나를 선정한다.
  - 이때 최적 알고리즘을 통해 가장 먼 미래에 참조될 페이지인 page 4를 선택하여 내쫓고 그 자리에 page 5를 적재한다.
  - 총 12회의 페이지 참조가 일어나는 동안 총 6번의 Page fault가 발생한다.

### FIFO(First In First Out) Algorithm

- 페이지 교체 시 물리적 메모리에 가장 먼저 올라온 페이지를 먼저 내쫓는다.
- 페이지의 향후 참조 가능성을 고려하지 않는다.
  - 가장 먼저 메모리에 올라온 페이지가 가장 많은 참조가 이루어져도 이 페이지를 내쫓는다.

⇒ 비효율적인 상황이 발생

#### FIFO Anomaly(Belady's Anomaly, FIFO의 이상현상)

![](https://i.imgur.com/U4qdZ98.png)

- 위 그림에서 페이지 프레임이 3개인 경우에는 9번의 Page fault 발생, 프레임이 4개인 경우 10번의 Page fault이 발생함
  ⇒ 물리적 메모리의 공간이 늘어났는데 오히려 성능이 나빠짐
- 이와 같이 FIFO 알고리즘에서 메모리를 증가시켰음에도 불구하고 Page fault가 오히려 늘어나는 상황을 FIFO Anomaly 라고 한다.

## LRU(Least Recently Used) Algorithm

- 페이지 교체 시 가장 오래전에 참조된 페이지를 내쫓는다.
  ⇒ 마지막 참조 시점이 가장 오래된 페이지를 교체(과거의 기록을 통해 결정)

#### Example of LRU Algorithm

![](https://i.imgur.com/Wy8yUh8.png)

- page reference string : `1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5`
- 물리적 메모리의 페이지 프레임의 갯수 : 4
- 알고리즘 동작 방식
  - 초기에 페이지 프레임이 모두 비어있으므로 4회까지 4번의 Page Fault 발생
  - 5, 6회는 메모리에 존재하므로 Page Fault 발생하지 않는다.
  - 7회에서 page 5가 참조될 때 Page Fault 발생하고, LRU 알고리즘을 통해 마지막 참조 시점이 가장 오래된 page 3번과 page 5번이 교체된다.
  - 총 12회의 페이지 참조가 일어나는 동안 총 6번의 Page fault가 발생

## LFU(Least Frequently Used) Algorithm

- 과거 참조 횟수(reference count)가 가장 적은 페이지를 내쫓는다.
- 최저 참조 횟수를 가진 페이지가 여러 개인 경우,
  - 임의의 하나를 선정
  - 성능 향상을 위해서는 상대적으로 오래전에 참조된 페이지를 쫓아내는 것이 효율적

#### LFU의 장점

- LRU는 직전의 참조된 시점만을 반영하지만 LFU는 참조 횟수를 통해 장기적인 시간 규모에서의 참조 성향을 고려하므로 page의 인기도를 비교적 정확히 반영할 수 있다.

#### LFU의 단점

- 참조 시점의 최근성을 반영하지 못한다.
- LRU보다 구현이 복잡하다.

## LRU와 LFU 알고리즘 비교

### LRU와 LFU 알고리즘 예제

![](https://i.imgur.com/5EtPWAc.png)

### LRU와 LFU 알고리즘 구현

![](https://i.imgur.com/tcFTwZ8.png)

#### LRU

- 위에서 아래로 갈 수록 참조 시간 순서에 따라 가장 오래전에 참조된 페이지에서 가장 최근에 참조된 페이지가 정렬된 상태
- Linked list 방식
- 특정 페이지가 참조되었을 때 list의 가장 아래로 이동시킨다.
- 페이지 교체 시 다른 페이지를 비교할 필요 없이 가장 위에 있는 페이지를 선택하면 되므로 O(1)의 시간복잡도를 가진다.

#### LFU

- LRU와 비슷하게 한 줄의 Linked list 방식으로 구현한다면 모든 페이지의 참조 횟수를 비교해야하므로 O(n)의 시간 복잡도를 가지게 된다.
  ⇒ 따라서 Heap 자료구조를 이용하여 구현
- 자식이 두 개씩 있는 이진트리 구조로 아래로 갈수록 참조 횟수가 많은 페이지가 위치하고 페이지 횟수가 늘어나면 자식들과 비교하며 아래로 이동
- 페이지 교체 시 루트에 있는 페이지를 선택하고 Heap을 재구성
  ⇒ O(log n)의 시간복잡도를 가진다.

# [Chapter 9. 가상 메모리 Part-2](https://core.ewha.ac.kr/publicview/C0101020140513133424380501?vmode=f)

## 다양한 캐싱 환경

#### 캐싱 기법

- 한정된 빠른 공간인 캐시에 요청된 데이터를 저장해 두었다가 후속 요청시 캐시로부터 직접 서비스하는 방식
- Paging system 외에도 Cache memory, Buffer caching, Web caching 등 다양한 분야에서 사용
  ⇒ 가상 메모리의 페이징 시스템에서 한정된 빠른 공간은 물리적인 메모리이고, 느린 저장 장치는 백킹스토어다. 따라서 가능하면 물리적인 메모리에서 직접 서비스하고 요청한 페이지가 메모리에 없을 때(페이지 부재가 발생했을 때)만 백킹스토어에서 물리적인 메모리로 읽어온다.

#### Replacement Algorithm에서 캐시 운영의 시간 제약

- Replacement Algorithm에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간을 소비해서는 안된다.
  ⇒ 실제 시스템에서 사용할 수 없음
- 캐시 안에 n개의 항목이 있는데, 특정 항목을 선택할 때 캐시 내의 모든 것을 스캔하는 경우 O(n)의 시간복잡도를 가지게 되고 이것은 시간적인 오버헤드가 큼을 나타낸다.
- Buffer caching / Web caching의 경우 O(1)에서 O(log n)정도까지 허용
- Paging system의 경우
  - Page fault인 경우에만 운영체제가 관여
  - 페이지가 이미 메모리에 존재하는 경우 참조시간 등의 정보를 운영체제가 알 수 없다.
  - O(1)인 LRU의 리스트 조작조차 불가능하다.

### Paging system에서 LRU, LFU가 가능한가?

![](https://i.imgur.com/Wstam2s.png)

- 프로세스 A가 CPU를 할당받아 실행 중이고, MMU를 통해 논리적 주소를 물리적 주소로 변환하고 물리적 메모리에 있는 내용을 CPU가 읽어온다.
- 주소 변환 과정은 운영체제의 관여없이 하드웨어가 담당한다.
- 만약 요청한 페이지의 페이지 테이블 항목에 invalid bit를 가진다면 Page fault trap이 발생하면서 CPU의 제거권이 운영체제로 넘어가게 된다.
- 운영체제가 Page fault가 발생한 페이지를 백킹스토어에서 물리적 메모리로 올리기 위해 페이지 하나를 쫓아내야 한다.
  - 이때 비어있는 프레임이 있다면 그 프레임에 페이지를 올리면 된다.
- 위 일련의 과정을 수행하면서 LRU, LFU 알고리즘을 적용할 수 있을까?
  - 프로세스가 실행 중이고 물리적인 메모리에 페이지가 올라가있는 경우 운영체제는 관여하지 않으므로 운영체제는 해당 페이지에 접근시간(참조시간) 또는 참조 횟수를 알 수 없다.
  - Page fault가 일어나 CPU가 운영체제로 넘어오면 운영체제는 디스크에 있던 페이지가 물리적 메모리로 올라간 시간을 알 수 있다.
  - 따라서 Paging system에서 운영체제한테 모든 정보가 주어지지 않는다.

⇒ LRU, LFU 알고리즘은 Paging system에서 사용할 수 없다.
(Buffer caching이나 Web caching에서는 사용할 수 있다.)
⇒ 따라서, Paging system에서 Clock Algorithm을 사용한다.

## Clock Algorithm(클럭 알고리즘)

- LRU를 근사(approximation)시킨 알고리즘으로 오랫동안 참조되지 않은 페이지 중 하나를 교체하는 알고리즘
  - 최근에 참조되지 않은 페이지를 교체 대상으로 선정 ⇒ LRU와 유사
  - 교체되는 페이지의 참조 시점이 가장 오래되었다는 것을 보장하지 못함 ⇒ LRU와 차이점
- Clock Algorithm의 여러 명칭
  - Second chance algorithm
  - NUR(Not Used Recently) 또는 NRU(Not Recently Used)

![](https://i.imgur.com/Ed9SDIz.png)

- Circular linked list 구조로 각 프레임마다 Reference bit가 존재
- 메모리에 올라와 있는 페이지의 Reference bit(참조비트)를 순차적으로 조사하여 교체 대상 페이지를 선정
- 페이지가 참조될 때 하드웨어에 의해 Reference bit가 1로 자동 세팅된다.
- 포인터를 순차적으로 이동하면서 Reference bit가 1인 페이지는 모두 0으로 바꾸고 지나간다.
- Reference bit가 0인 페이지를 만나면 교체 대상이 된다.
- 포인터가 한 바퀴 도는 동안(second chance) 다시 참조되지 않은 페이지(Reference bit가 0인 페이지)를 교체하는 것
- 자주 사용되는 페이지라면 second chance가 올 때 Reference bit가 1이다.

⇒ 이 알고리즘은 최근에 참조가 일어나지 않은 페이지를 교체하는 알고리즘이지만 LRU처럼 가장 오랫동안 참조되지 않았다는 것을 보장하지는 않다는 차이점이 있다.

#### Clock Algorithm의 개선

- Reference bit와 함께 Modified bit(Dirty bit)를 함께 사용
- 페이지가 read로 참조될 수 있지만 write로도 참조될 수 있다.
- Modified bit가 0이면 백킹스토어에서 물리적인 메모리로 올라온 이후로 write가 발생하지 않았음을 의미
  - 이런 페이지가 메모리에서 쫓겨나는 경우 이미 동일한 복사본이 백킹스토어에 존재하므로 메모리에서 쫓아내기만 하면 된다.
- Modified bit가 1이면 물리적인 메모리에 올라온 이후 적어도 한 번은 write가 일어났음을 의미하므로 메모리에서 쫓겨날 때 백킹스토어에 있는 복사본에 이 변경사항을 반영한 후 메모리에서 지운다.
- `Reference bit = 1` : 최근에 참조된 페이지
- `Modified bit = 1` : 최근에 변경된 페이지(I/O를 동반하는 페이지)

## Page Frame의 Allocation(페이지 프레임의 할당)

- Allocation Problem : 프로세스 여러 개가 동시에 수행되는 상황에서는 각 프로세스에 얼마만큼의 메모리 공간(page frame)을 할당할 것인지 결정해야 한다.

#### Allocation의 필요성

- CPU에서 메모리 참조 명령을 실행할 때 일반적으로 여러 페이지를 동시에 참조하게 된다.
  (명령을 실행할 때 프로세스 주소 공간의 코드, 데이터, 스택 등 각기 다른 영역을 참조하기 때문에)
  ⇒ 명령을 정상적으로 수행하기 위해 최소 할당되어야 하는 page frame 수가 있다.
- 반복문(loop)을 실행중인 프로세스의 경우 반복문을 구성하는 페이지들은 한꺼번에 메모리에 올라가는 것이 유리하다.
  ⇒ 최소한의 할당이 없으면 매 반복(loop/iteration)마다 page fault가 발생하여 시스템의 성능이 떨어지게 된다.

#### Allocation Scheme(Allocation Algorithm)

- Equal Allocation(균등할당): 모든 프로세스에게 페이지 프레임을 균일하게 할당
- Proportional Allocation(비례 할당): 프로세스의 크기에 비례해 페이지 프레임을 할당
- Priority Allocation(우선순위 할당): 프로세스의 우선순위에 따라 페이지 프레임을 다르게 할당

## Global vs. Local Replacement

- 교체할 페이지를 선정할 때, 교체 대상이 될 프레임의 범위를 어떻게 정할지에 따라 교체 방법을 구분할 수 있다.

### Global Replacement(전역 교체)

- 전체 메모리를 여러 프로세스가 공유해서 사용하고 교체 알고리즘에 근거해서 페이지 교체(replace)가 일어날 때 다른 프로세스에 할당된 프레임을 빼앗아 올 수 있는 방식
- 전체 시스템 차원에서 더 자주 참조되는 페이지가 메모리에 올라가기 때문에 프로세스별 프레임 할당량을 조절하는 또 다른 방법이 될 수 있다.
- FIFO, LRU, LFU 등의 알고리즘을 물리적 메모리 내에 존재하는 전체 페이지 프레임들을 대상으로 적용(Global Replacement)하는 경우 해당
- Working set, PFF 알고리즘도 Global Replacement 방법으로 사용될 수 있다.

### Local Replacement(지역 교체)

- 해당 프로세스에게 할당된 프레임 내에서만 페이지 교체(replacement)를 할 수 있다.
- 프로세스마다 페이지 프레임을 미리 할당하는 것을 전제로 한다.
- FIFO, LRU, LFU 등의 알고리즘을 프로세스별로 독자적으로 운영하는 경우 해당

## Thrashing(스레싱)

- 프로세스가 원활하게 수행되기 위한 최소한의 페이지 프레임을 할당받지 못한 경우 성능상의 문제가 발생
- Page fault rate(페이지 부재율)이 매우 높아짐
- CPU utilization(이용률)이 현저히 낮아짐

### Thrashing Diagram

![](https://i.imgur.com/agJ5x8H.png)

#### 스레싱 발생 시나리오

- 운영체제는 CPU 이용률이 낮으면 MPD를 높인다.
  - 다중 프로그래밍의 정도(Multi-programming Degree: MPD): 메모리에 동시에 올라가 있는 프로세스의 수
- MPD가 과도하게 높아지면 각 프로세스에게 할당되는 메모리의 양이 지나치게 감소한다.
- 그렇게 되면 각 프로세스가 원할하게 수행되기 위한 최소한의 페이지 프레임을 할당받지 못하는 상태가 되어 페이지 부재가 빈번하게 발생한다.
- 이 상황에서 운영체제는 MPD를 높여야 한다고 판단하여 다른 프로세스를 메모리에 추가하게 된다.
- 프로세스당 할당된 페이지 프레임의 수가 더욱 감소하여 페이지 부재가 더욱 빈번히 발생
- 프로세스는 페이지의 swap in/swap out으로 매우 바쁘지만 CPU는 대부분의 시간에 일을 하지 않게 된다.
- Low throughput

⇒ 이러한 상황을 스레싱이라고 부른다.

MPD를 조절하여 CPU 이용률을 높이는 동시에 스레싱 발생을 방지하는 방법에는 Working-Set Algorithm(워킹셋 알고리즘)과 Page-Fault Frequency Scheme(페이지 부재 빈도 알고리즘)이 있다.

## Working-Set Model

#### Locality of reference

- 프로세스는 일정 시간 동안 특정 주소 영역을 집중적으로 참조한다.
  ⇒ 이렇게 집중적으로 참조되는 페이지들의 집합을 locality set(지역성 집합)이라고 한다.

#### Working-Set Model

- Working-Set Algorithm에서 Locality에 기반하여 프로세스가 일정 시간 동안 원활히 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 페이지들의 집합을 Working set이라 정의
- Working-Set Model에서 프로세스의 워킹셋 전체가 메모리에 올라갈 수 있는 경우에만 메모리를 할당하여 프로세스를 수행
  - 그렇지 않은 경우 프로세스에게 할당된 메이지 프레임들을 모두 반납한 후 스왑 아웃 시킨다(suspend).
- 이와 같은 방법으로 MPD를 조절하고 스레싱을 방지

## Working-Set Algorithm

#### Working set 결정 방법

- Working set window를 통해 알아냄
- window size가 ∆인 경우, 시각 t<sub>i</sub>에서의 워킹셋 WS(t<sub>i</sub>)을 time interval[t<sub>i</sub>-∆, t<sub>i</sub>]사이에 참조된 서로 다른 페이지들의 집합으로 정의
- 워킹셋에 포함된 페이지는 메모리에 유지되고 그렇지 않은 페이지들은 메모리에서 쫓겨나게 된다.

⇒ 참조된 시점부터 ∆시간 동안은 메모리에 유지하고, 그 시점이 지나면 메모리에서 버린다.

![](https://i.imgur.com/bsLbLEc.png)

#### Working-Set Algorithm

- 프로세스들의 워킹셋 사이즈의 합이 페이지 프레임의 수보다 큰 경우
  - 일부 프로세스를 스왑 아웃시켜 남은 프로세스의 워킹셋을 우선적으로 충족시켜준다. ⇒ MPD를 줄임
- 워킹셋을 다 할당하고도 페이지 프레임이 남는 경우
  - 스왑 아웃되었던 프로세스에게 워킹셋을 할당 ⇒ MPD를 높임

#### Window size ∆

- 워킹셋을 제대로 탐지하기 위해서는 window size를 잘 결정해야 한다.
- ∆ 값이 너무 작으면, locality set을 모두 수용하지 못할 우려
- ∆ 값이 너무 크면, 여러 규모의 locality set을 수용할 수 있으나 MPD가 감소해 CPU 이용률이 낮아질 우려
- ∆ 값이 ∞이면 전체 프로그램을 구성하는 페이지를 워킹셋으로 간주

## PFF(Page-Fault Frequency) Scheme

![](https://i.imgur.com/382ThRm.png)

- 프로세스의 page fault rate을 주기적으로 조사하고 이 값에 근거해서 각 프로세스에 할당할 메모리 양을 동적으로 조절
- page fault rate의 상한값(upper bound)과 하한값(lower bound)을 두고,
  - page fault rate가 상한값을 넘으면 프레임을 더 할당한다.
    이때 추가로 할당할 빈 프레임이 없다면 일부 프로세스를 스왑 아웃 시켜 메모리에 올라가 있는 프로세스의 수를 조절한다.
  - page fault rate가 하한값 이하로 떨어지면 할당된 프레임 수를 줄인다.

## Page Size의 결정

- 페이지 사이즈는 보통 4KB를 사용하지만 주소 체계가 32비트 주소 체계에서 64비트 주소 체계로 바뀌고 메모리 크기도 점점 커진다.
- 메모리 크기는 크지만 페이지 사이즈가 작을 경우 페이지 테이블이 차지하는 메모리가 커진다.
- 메모리 크기가 커지면 그에 따라 페이지 사이즈도 커질 필요가 있다.

#### 페이지 크기를 감소시키면

- 페이지의 수가 증가
- 페이지 테이블의 크기 증가
- 내부조각 감소
- Disk transfer의 효율성 감소
  - Seek/rotation vs. transfer
- 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
  - Locality 활용 측면에서는 페이지 크기가 큰 것이 좋다.

⇒ 현재는 페이지 크기를 점점 크게 한다.
