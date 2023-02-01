## [가상 메모리 #1](https://core.ewha.ac.kr/publicview/C0101020140509151648408460?vmode=f)

Virtual Memory는 전적으로 OS가 관리

### Demand Paging

- 요청이 있으면 그 page를 메모리에 올리겠다.
- 일단 paging 기법 가정. 실제로 대부분의 시스템은 paging 기법 사용.
- I/O 양, 메모리 사용량 감소. - 프로그램 주소 공간에서 실제로 빈번히 사용되는 양은 지극히 제한적. 대부분의 프로그램은 방어적으로 코드가 짜여있다. 그래서 잘 사용하지 않으면 잘 안올린다. 따라서 demanding paging을 사용하면 I/O 양 감소
- 빠른 응답 시간(관점에 따라서는 다를 수도) - 한정된 메모리 공간을 더 잘 사용하기 때문에 I/O 시간 줄고 하면서 응답시간 좋아진다 생각
- 더 많은 사용자 수용

![](https://i.imgur.com/IgYBmdJ.png)

- Valid/Invalid 사용

  - Invalid
    1. 물리적 메모리에 없고 backing store
    2. 프로그램이 사용하지 않는 주소 영역(page entry는 결국 주소 공간 크기만큼 만들어지기는 하기 때문)  
       최초에는 물론 전부 다 invalid 상태. page가 메모리에 올라가면 해당 entry가 valid로 바뀜

- logical에서 page를 필요로 해서 page table을 봤더니 없을 경우?  
  메모리에 없다는 뜻이므로 disk에서 메모리로 올려야 한다. => I/O 작업. 이는 Process가 직접 할 수는 없다.  
  => `page fault` 발생: 요청한 page가 메모리에 없다.  
  => CPU는 자동적으로 OS로 넘어감. trap. software interrupt.  
  => OS가 CPU를 사용해서 fault난 페이지를 메모리에 올린다.

### Page Fault

- 처리 루틴이 OS에 정의되어 있음.
- invalid page 접근 => MMU => trap (page fault trap)
- Kernel mode - page fault handler invoke(발생)
- page fault 처리 순서
  1. Invalid reference?(ex. bad address(미사용), protection violation(접근 권한 위배)) => abort process
  2. 빈 page frame 획득(비어있는 것이 없다면 뺏어온다: replace)
  3. 해당 page를 획득한 page frame에 넣는다. disk -> memory. 대단히 오래 걸리는 작업(disk가 메모리보다 수십만~백만배 느림)
     1. file 입출력과 마찬가지로 disk I/O 끝날때까지 CPU preempt => Process block  
        1.1. disk I/O controller에게 I/O 부탁  
        1.2. Ready state의 Process에 CPU 위임.  
        1.3. I/O 끝나면 다시 interrupt
     2. Disk read가 끝나면 page table entry에 기록, valid bit로 표시
     3. Block 되어 있던 Process를 Ready Queue에서 꺼냄
  4. 다시 CPU 잡고 Running. 이 때는 page fault 발생하지 않음
  5. 중단 되었던 instruction을 CPU가 실행

![](https://i.imgur.com/HI8ezGB.png)

#### Performance of Demanding Paging

- Page Fault Rate 0(no page faults) <= p <= 1.0(every fault)
- p가 낮게 해줘야 함.
- ![](https://i.imgur.com/k3WLB0D.png)
  - p에 곱하는 값이 어마어마한 시간

#### Free frame이 없는 경우

- frame을 쫓아내야 함: page raplacement
- OS가 함.
- 이를 수행하는 알고리즘을 Replace Algorithm이라고 함. 가급적 page fault rate p가 낮아지도록 해야 함. 0에 가깝도록.
- 주어진 page reference string에 대해 page fault 조사

#### Page Replacement

![](https://i.imgur.com/t3vtLP1.png)

1. victim 선정해서 disk로 쫓아냄.  
   만약 메모리 올라왔을 때  
   내용이 변경됐다면(Write 발생) 변경된 내용도 backing store에 써줘야 함.  
   변경이 없다면 그냥 메모리에서 삭제만 하면 됨.
2. 쫓아낸 frame의 page frame entry -> invalid로 바꿈
3. 그 frame 자리에 새로운 page 올려 놓음
4. 새로 올라온 page의 frame을 page table에 적고 valid로 바꿈

### Optimal Algorithm

- 어떤 알고리즘이 가장 좋은 알고리즘인가?  
  -> Optimal Algorithm. page fault를 가장 적게 하는 알고리즘.
- 실제로는 미래를 알 수 없다. 하지만 Optimal에서는 미래 사용될 page를 미리 알고 있다고 가정.
- Offline algorithm: reference string을 미리 다 안다고 가정 하에 알고리즘 운영.
- MIN(OPT): 가장 먼 미래에 참조되는 page를 replace
- ![](https://i.imgur.com/BZaHETa.png)
  - 빨간색은 page fault, 연보라는 메모리 직접 참조 의미
  - 5번이 올 때 가장 먼 미래의 4번을 내쫓음.
  - 총 6번의 page faults  
    => 어떤 알고리즘도 이 보다 적을 수는 없다.
- 당연히 실제로 불가능하고 다른 알고리즘에 대한 성능 척도(upper bound) 제공: Belady's optimal algorithm, MIN, OPT 등으로 불림
  - 어떤 알고리즘이 optimal과 비슷한 수치가 나온다면, 좋은 것

다음 알고리즘들은 미래를 모르는 상태에서 실제로 사용하는 알고리즘들  
미래를 모르지만 잘 예측해야 함.  
미래를 모를 때 가장 참고할만한 단서?  
=> 과거를 보면 됨.

### FIFO(First In First Out) Algorithm

- 메모리에 먼저 들어온 페이지를 먼저 쫓아낸다.
- 앞 예제(reference string)에 적용해본다면 다음과 같다.
- ![](https://i.imgur.com/dUNghUm.png)
- 특이하게도 frame 개수가 늘면 page faults가 더 많아진다. frame이 늘어났으므로 성능이 더 좋아져야 할텐데 더 나빠지는 상황이 발생할 수 있다. 이런 이상황 상황을 FIFO Anomaly
- FIFO Anomaly(Belady's Anomaly): frame ⬆ ⇏ page fault ⬇ (성능이 더 나빠질 수도 있다)

### LRU(Least Recently Used) Algorithm

- 제일 많이 쓰는 알고리즘
- 가장 최근에 덜 사용된 page를 내쫓는 알고리즘. 가장 오래 전에 사용된 page
- 예제에 적용
- ![](https://i.imgur.com/x9Che0I.png)
  - 5번이 들어왔을 때 가장 오래전에 사용된 3번 내쫓음
- 미래를 모르면 과거를 보는 것
- 비교적 page faults rate 줄일 수 있다.

### LFU(Least Frequently Used) Algorithm

- 가장 덜 빈번하게 사용된 page 쫓아낸다.
- 참조 횟수 제일 적은 page 쫓아낸다.
- 최저 참조 횟수가 여러개라면?
  - 명시하고 있지 않다. 아무거나 쫓아내도 된다.
  - 성능을 높이고자 한다면 참조 시점이 더 오래된 page를 내쫓도록 구현 가능

### LRU와 LFU 알고리즘의 예제 및 구현

- 서로 장단점이 있다.

![](https://i.imgur.com/IOcoNO1.png)

- LRU는 마지막 시점만 보기 때문에 이전 기록은 고려하지 않는다.
- LFU는 참조 횟수만 보기 때문에 시점을 고려하지 않는다.
- 각각의 장단점 보완을 위해 많은 연구가 있었음.

#### LRU, LFU `구현`

- LRU

  - ![](https://i.imgur.com/dSwa2Lu.png)
  - Linked List로 구현
  - LRU ~ MRU(Most RU)
  - 새로 참조하면 제일 아래 MRU 자리로 이동
  - O(1)의 시간 복잡도: 쫓아내기 위해 비교가 필요없다.
  - Linked List로 안하고 시간 기록 table로 비교하면 매번 다 확인해야하므로 대단히 비효율적

- LFU
  - ![](https://i.imgur.com/Y7oS1D1.png)
  - 비슷하게 한 줄의 Linked List로 만든다면 바로 맨 뒤로 가지 않는다.
  - 참조 개수를 하나 하나 비교를 하며 내려가야 한다.
  - O(n) 시간 복잡도
  - 따라서 heap으로 구현해야 함
  - ![](https://i.imgur.com/2H5wI23.png)
  - 새로 참조하고서 밑의 개수들과 계속해서 비교하며 자리를 바꾼다. 제거시는 head 제거하면 됨

## [가상 메모리 #2](https://core.ewha.ac.kr/publicview/C0101020140513133424380501?vmode=f)

앞서 살펴본 LRU, LFU에서 사용한 caching은 컴퓨터 시스템 전체적으로 많이 사용한다.

### 다양한 캐슁 환경

- 캐슁 기법: 한정된 빠른 공간(=cache)에 데이터를 저장해놓았을 때, 같은 데이터 요청시 느린 저장장치까지 가지 않고 cache에서 바로 서비스 제공하면 빠름
- paging system에서 말고  
  cache memory(CPU와 main memory 사이),  
  buffer caching(File System W/R 요청 메모리에서 빠르게 서비스),  
  Web caching(Web 페이지 응답 데이터 저장, 앞 2개는 속도 차이 때문이지만, 지리적으로 멀어서 오래 걸려서 사용) 등 다양히 사용
  - paging system에서는 swap area
  - buffer caching에서는 disk file system에 해당
- 시간 제약
  - Buffer caching이나 Web caching: O(1) ~ O(log n) 허용
  - Paging System
    - 엄청난 제약 조건
    - page fault가 발생하면 trap이 발생해서 CPU 제어권이 OS로 넘어가게 된다.
    - 앞서 이야기한 것처럼 메모리에 올리고 내리는 것은 OS의 역할이 아니라 HW 역할
    - LRU, LFU를 사용한다면 가장 오래된 정보, 덜 참조된 정보를 OS가 알 수 있는가?  
      => `NO`
      - LRU에서 이미 메모리에 있던 page를 참조한다치면, OS에 CPU가 넘어가지 않고, 접근 시간을 OS가 모른다.(Process 동작 중 알아서 접근했으므로), page fault가 난 경우에만 trap을 통해 OS가 접근 시간을 알 수 있다  
        => 반쪽짜리 정보
    - 이런 상황에서 LRU, LFU는 Linked List, Heap으로 조작이 필요. 하지만 자료구조 관리할 수 있는 정보를 OS가 모른다.  
      메모리에서 알아서 참조하면 OS가 CPU를 못받아서 시점을 모름, 참조 횟수도 모르게 됨.
    - 결국 LRU, LFU는 Paging System에서 사용하는 알고리즘이 아니다(?? 반전 Again..?)
    - 그러면 Paging System에서 필요한 알고리즘은?  
      => Clock Algorithm

### Clock Algorithm

- LRU 근사(approximation)시킨 알고리즘
- Second chance algorithm, NUR(Not Used Recently), NRU로도 불림
- ![](https://i.imgur.com/lz76Hc5.png)
  - 각 사각형은 page frame을 의미
  - 각 page frame의 reference bit을 1로 세팅(page가 참조 되었음을 표시)
    - 이 일은 OS가 아닌 하드웨어가 하는 일
  - page fault가 발생
    1. 시계 방향으로 돌면서 reference bit 1이 있으면 최근에 적어도 한 번 참조 됐다는 뜻
    2. 1 -> 0으로 바꾸고 시계 방향으로 이동
    3. reference bit이 0이면 victim 선정
  - reference bit = 0이 의미하는 것?  
    => 시계 한바퀴 도는동안 한번도 참조 되지 않았다.  
    1이면 한 번은 참조 되었다.
- HW는 page frame 참조시 reference bit을 1로 바꾸는 역할.  
  OS는 뭘 쫓아낼 지 결정시 시계방향 돌면서 1 -> 0, 0은 내쫓는다.  
  이런 알고리즘이 Clock Algorithm
- reference bit = 0이 가장 오래됨을 의미하지는 않는다. 적어도 오랫동안 참조되지 않았다는 의미이므로 LRU 유사
- LRU에서의 MRU로의 이동을 OS가 할 수 없으므로 HW의 도움 받는 것. HW의 bit 변환의 도움을 받는다.
- Virtual Memory, Paging System에서 일반적으로 사용하는 알고리즘이다.
- 추가적으로 modified bit(dirty bit)도 두고 있다.
  - modified bit: write 참조 정보
  - reference bit 0이어서 내쫓을 때
    - modified bit도 0이면 바뀐 정보가 없으므로 그냥 제거
    - modified bit가 1이면 메모리 올라온 이후 CPU가 한번은 Write해서 수정한 것이므로 backing store에 수정 내용 반영해야 함.
  - modified 1이면 disk에 쓰고 쫓아내야하므로 일이 더 많다. 따라서 가능하면 modified 0을 우선적으로 쫓아내면 좀 더 빠를 수 있다.

### Page Frame의 Allocation

- LRU, LFU 등에서는 기계적으로 page를 내쫓고 올리지만, 실제 프로그램 실행에는 일련의 page들이 메모리에 같이 올라가야 효율적이다.
  - Loop 구성 page들은 한꺼번에 allocate 되는 것이 유리: 최소한의 allocation이 없다면 매 loop마다 page fault
- 각 Process에 얼마만큼의 frame 할당할 것인가?
- 명령어 수행 위해 최소 할당돼야 하는 일련의 frame 개수가 있음. 최소 할당보다도 적으면 page fault
- instruction, data
- Allocation Scheme: 프로그램마다 일정 메모리를 주자
  - Equal allocation: 모든 Process에 똑같은 개수. 비효율적일 수 있음
  - Proportional allocation: Process 크기에 비례
  - Priority allocation: Process priority 따라 다르게
- 프로그램마다 필요로 하는 메모리를 할당해줘서 page fault가 적게 나도록 하는 목적

### Global vs. Local Replacement

Global replacement

- 미리 할당하지 않고도 Process별로 알아서 FIFO, LRU, LFU 등의 알고리즘을 하다보면 알아서 allocation 되는 메모리 조절 효과가 있음.
- allocation을 한다면 Working set, PFF 알고리즘 사용
- 다른 프로그램의 page도 쫓아내는 방법
- 매번 경쟁해서 많이 갖고 적게 갖고 결정

Local replacement

- 프로그램에 allocate 한 다음, 새로 쫓아낼 때 자신에게 할당된 page 쫓아내는 방법
- FIFO, LRU, LFU를 Process 별로 운영시
- 자신의 몫을 나눠주고(Process) 알아서 관리

### Thrashing

- 어떤 프로그램에게 최소 메모리도 할당되지 않으면 page fault가 자주 발생.
- Process에 메모리가 너무 적게 allocate돼서 page fault가 지나치게 자주 일어나는 상황을 `Thrashing` 이라고 함.
- Thrashing Diagram
  - ![](https://i.imgur.com/67qm3v1.png)
  - degree of multiprogramming: 현재 메모리에 올라가 있는 프로그램 개수
  - 프로그램 개수 증가에 따라 이용률도 증가하다가 어느 지점부터 이용률이 확 떨어지는데, 그 시점이 thrashing
  - 너무 많은 프로그램 개수는 각각이 다 너무 작은 메모리 allocate
- 각 프로그램마다 너무 적은 page frame allocate  
  => Page fault rate 높아짐  
  => CPU 이용률 낮아짐(CPU가 할 일이 없어짐)  
  => OS는 MPD(MultiProgramming Degree)를 높여야 한다고 판단  
  => MPD 또 증가  
  => page frame 더 감소  
  => page swap in/out으로 바쁘지만 CPU는 한가한 상태: 굉장히 비효율적 상태
- 이를 막기 위해서는 MPD를 조절해줘서 프로그램이 어느 정도의 메모리를 확보할 수 있도록 해줘야 한다.  
  => 이를 위한 알고리즘: Working-Set, PFF

### Working-Set Model

- Process들이 원활히 실행되기 위해서는 어느 정도의 메모리가 필요하다.
- Locality of reference: Process는 특정 시간동안 일정 장소만 집중적으로 참조한다. ex) loop동안은 loop 안의 page만 집중 참조.
  - 이렇게 집중적으로 참조되는 page들의 집합을 locality set이라 함.
  - working set이라는 용어를 좀 더 일반적으로 사용
- 어떤 프로그램이 실행될 때 그 순간 메모리에 꼭 올라와 있어야 하는(= 빈번히 참조되는) 메모리의 집합: Working Set
- Process의 Working Set 전체가 메모리에 올라와 있어야 수행.  
  전체가 올라올 수 없으면 모든 frame 반납 -> swap out(suspend)
- suspend 덕분에 thrashing 방지, MPD 결정

### Working-Set Algorithm

- Working set을 미리 알 수 없기 때문에 과거 기록을 통해 알아낸다.
- 현재 시점부터 window size(Δ)만큼 과거 기록은 현재의 Working Set이 된다. 해당 Working Set을 메모리에 올려놔야 한다.
- 현재 메모리 공간이 Working Set보다 부족하면 다 내려놓고, 여유 생길 때까지 suspend.  
  => 다른 Process들의 working set이라도 우선 보장
- Δ만큼 과거 기록을 유지한다는 말은 참조된 page는 Δ시간 동안 메모리에 유지하고 버린다는 말

### PFF(Page-Fault Frequency) Scheme

- 역시 MPD 조절하며 Thrashing 방지
- Working Set과 다르게 직접 page fault rate를 보는 것
- 현재 시점 시스템에서 page fault가 얼마나 나는지 본다.
- 특정 프로그램이 얼마나 page fault를 내는지 본다.  
  많은 page fault가 일어나는 Process에 메모리를 더 준다.
- Process마다 필요로 하는 frame 수는 다르다.
- page fault rate 상한(upper bound), 하한(lower bound)
  - upper bound보다 크면, Working Set이 보장되지 않은 상황으로 간주 => frame ⬆️ => page-fault rate ⬇️
  - lower bound보다 작으면, 쓸데없이 너무 많은 frame 보유로 간주 => frame ⬇️(빼앗음) => page-fault rate ⬆️
- frame을 더 줘야하는 상황인데 빈 frame이 없다면 마찬가지로 swap out으로 남아있는 Process라도 보장 => Thrashing 방지

### Page Size의 결정

- Paging 기법에서는 동일한 크기의 page로 잘라서 사용하게 된다.
- 보통 4KB 사용.
- 주소 체계도 64bit 체계로 바뀌고, 메모리 크기도 점점 커지는 상황. 이럴 때 page 사이즈가 작다면 너무 많은 page table entry => 낭비
- 메모리 크기가 커짐에 따라 page size도 따라 커질 필요가 생김. 최근에는 4KB가 아니라 대용량 page를 사용하는 전략 등장.

Page size ⬇️  
=> 잘게 많이 썰어야 해서 Page 개수 ⬆️  
=> page table 크기 ⬆️  
=> 잘게 썰어지므로 내부 조각화 ⬇️  
=> disk transfer 효율성 ⬇️: seek/rotation vs transfer
disk head가 이동해서 seek해야하는데 seek 시간이 대단히 오래 걸림. 가능하면 한 번 이동으로 많은 데이터를 읽는 것이 좋다.
따라서 page size가 큰 게 좋다.

- 필요한 정보만 메모리에 올라와 메모리 이용이 효율적일 수는 있음.
- Locality 측면에서는 code가 연속해서 순서대로 참조되는 것이 좋기 때문에 page size가 큰게 좋다. 처음 fault나도 이후로는 fault 없이 참조하기 때문에 더 효율적인 측면이 있음
