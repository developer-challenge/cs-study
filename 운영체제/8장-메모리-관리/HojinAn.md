# [메모리 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140425151219100144?vmode=f)

## Logical vs. Physical Address

- 논리적 주소(=virtual address) vs. 물리적 주소
- 물리적 메모리는 통째로 0번째부터 관리.  
  맨 아래 OS 커널 위에 여러 프로그램 섞여서 올라가있음.
- 논리적 주소 각자가 독립적인 메모리 주소가 있지만  
  실행이 되기 위해서는 실제 물리적 메모리 어딘가로 올라가야 하고,  
  그 과정에서 주소가 바뀐다.  
  => 이를 주소 변환, 주소 바인딩이라 한다.
- 주소 바인딩: 주소를 결정하는 것  
  Symbolic 주소(소스코드) -(컴파일)-> Logical 주소(실행파일) -([언제?](#주소바인딩address-binding))-> Physical 주소
- 프로그램을 만들 때는 숫자 주소를 가지고 프로그래밍하지 않는다. 변수 이름, 함수 이름으로 사용하고 호출한다. 프로그래머 입장에서는 Symbolic 주소를 사용

## 주소바인딩(Address Binding)

- 3가지 시점으로 나눌 수 있다.

1. 컴파일시(Compile time binding)  
   (Symbolic->Logical)compile 시점에 물리적 메모리 주소가 결정. Logical이 사실 Physical과 같은 것.  
   Physical로 올릴 때 Logical과 같은 주소로 올려야 한다.  
   대단히 비효율적. 지금은 안 씀. 과거에는 사용되기도 했음.  
   compile시 Physical까지 결정되므로 생성된 실행 파일 코드를 absolute code라고 부름.  
   다시 올리고 싶으면 코드도 다시 작성해야 함
2. 실행시작시(Load time binding)  
   compile까지는 Logical만 결정. 실행시키면 비어있는 Physical에 binding  
   relocated code
3. 실행중간(Execution time binding, Runtime binding)  
   실행시처럼 실행 후 결정되는 것은 같음. 하지만 Physical이 실행 도중 바뀔 수 있다.  
   현대의 컴퓨터 시스템은 당연히 Runtime binding 지원  
   CPU가 메모리 주소를 요청할 때마다 그 때마다 binding을 check 해야함. => HW 지원 필요(MMU)

CPU가 바라보는 주소는 무엇일까?  
=> 당연히 Physical? Nope!(ㄷㄷ). CPU도 Physical이라 Physical 바라볼 것 같지만  
CPU는 `Logical Address`를 바라본다.  
시작 위치는 바뀌지만  
실행 파일에 적혀져있는 다른 코드 참조 주소는 logical 주소다. 실행을 시작하면 코드 내 logical 주소 그대로 메모리로 올라가기 때문에(따로 compile등이 없다.)  
코드가 다른 코드 주소 요청을 하면 그 때마다 binding된 주소를 변환 해줘야 한다.

## Memory-Management Unit(MMU)

- 주소 변환을 지원해주는 `Hardware`(SW가 아님)
- 기본적 MMU는 register 2개를 통해 주소를 변환한다.
  1. base register(relocation register): 접근 가능 메모리 주소 최소값
  2. limit register: logical 주소 범위

## Dynamic Relocation

![](https://i.imgur.com/hhW3RQ6.png)

1. p1의 logical 메모리(0~3,000), Physical 메모리에는 14,000부터 올라가 있음
2. CPU가 346 요청
3. 시작 위치(base register) + logical 위치 => 해당 physical 주소 반환

## Hadware Support for Address Translation

- limit register에는 process의 크기가 담겨 있다. limit보다 큰 요청은 악의적인 시도이기 때문에 막는다.

![](https://i.imgur.com/gbywk07.png)  
논리 도식도 limit 확인 후 relocation

## Some Treminologies

- Dynamic Loading
  - 프로그램을 메모리에 동적으로 올린다?: 필요할 때마다 올린다.
  - Process 주소 공간 전체를 메모리에 올리는 것이 아니라 그 때 그 때 필요할 때마다 올리는 방식. 사실 자주 사용되는 부분은 한정적.
  - Paging 기법 등을 통해 필요한 부분만 올리는 것은 사실 OS가 관리하는 것
  - 엄밀히 말해서 Dynamic Loading은 OS가 하는 것을 말하는 것이 아니라 프로그래머가 직접 하는 개념. 즉, Dynamic Loading !== Paging
  - OS가 제공하는 Paging등의 라이브러리를 통해 사용자가 Dynamic Loading을 할 수 있다 정도?
  - 용어가 섞여서 사용되고 있다.

> Linking  
> compile 후 link에서 실행 파일 만듦  
> 여러 군데 존재하던 compiled file들을 하나의 실행 파일로 묶는 과정  
> 여러 소스 코드를 따로 작성해서 linking하기도 하고, 내가 작성하지 않은 라이브러리 함수를 가져다 linking하기도 하고 => 실행 파일 안에 라이브러리 코드도 포함

- Dynamic Linking

  - vs Static linking: 라이브러리가 내 실행 파일 코드에 이미 포함
  - 라이브러리 코드가 내 실행 파일 안에 포함되지 않고, 실행 시키면 라이브러리 위치를 찾을 수 있는 stub이라는 작은 코드(포인터)를 둠.
  - 코드 실행하다가 라이브러리 stub에 도달하면 해당 라이브러리를 찾는다. 보통 별도의 파일 형태
  - 찾아서 필요하면 메모리에 올리고, 메모리에 있다면 그 주소를 찾아가서 라이브러리 실행
  - Dynamic Linking을 해주는 라이브러리를 shared library라고 부름. ex. shared object(Linux), DLL(Dynamic Linking Library, Windows)

- Overlays

  - 똑같은 내용 같은데, 초창기 컴퓨터 시스템에서 아주 작은 메모리도 올리는 것이 불가능했다. 큰 프로그램을 쪼개서 프로그래머가 수작업. 대단히 불편.
  - OS의 지원이 없다. 프로그래머가 직접 코드를 통해서 무엇을 올리고 내리고를 정해야하기 때문에 실제로 필요한 정보만 올린다.
  - dynamic loading도 비슷하지만, 라이브러리 지원을 받기 때문에 어떤 걸 올리고 내리고까지 자세하게 코딩할 필요는 없다.

- Swapping
  - Process를 메모리에서 통째로 하드디스크 backing store(=swap area)로 쫓아내는 것
  - ![](https://i.imgur.com/UnADvpu.png)
  - 중기 스케줄러가 Swap out할 Process를 선택한다. 그래서 다른 말로 Swapper
    - CPU 우선 순위가 낮은 Process를 내쫓는다.(swap out)
  - 주소 binding과 연결지어 생각  
    compile이나 loadtime 바인딩이면, 다른 주소가 비어있어도 쫓겨난 주소 그대로 올라와야 함 => swapping 효과 별로  
    좀 더 효율적 사용이 되려면 runtime 바인딩 지원돼야 함.
  - Swap은 상당히 많은 양의 이동이기에 파일 입출력과 다르게 disk 접근 시간의 대부분이 transfer 시간(swap 양에 비례)이다.
  - 원칙대로는 통째로 swap이 맞지만, 최근에는 전체가 아닌 주소 공간이 잘게 쪼개져서 쫓겨나는 형태도 swap이라고 한다.

> Disk에서는 Seek time(disk header 이동 시간)이 대부분의 시간, transfer 시간은 미미.

## Allocation of Physical Memory

- 낮은 주소에 OS 커널 상주, 높은 주소 사용자 프로그램
- 사용자 프로그램 관리 방법 2가지
  1. 연속 할당(Contiguous Allocation): Process 주소 공간 통째로 올라가는 것
  2. 불연속 할당(Noncontiguous Allocation): Process 주소 공간 쪼개서 일부 올리고 안올리고 하는 것. 현대 시스템 사용(당연히). 같은 크기로 잘라서 쓰는 Paging

### Contiguous Allocation

- 고정 분할 방식(Fixed partition)
  - 프로그램 들어갈 사용자 메모리 영역을 미리 partition으로 나누어 놓는 것
  - 낭비되는 조각 발생
    - 외부 조각(External Fragment): 올리려는 프로그램 공간보다 partition이 작아서 사용되지 못한 partition
    - 내부 조각(Internal Fragment): 올리려는 프로그램 공간보다 partition이 커서 할당은 되지만 사용되지 못하고 남는 partition 공간
    - 그 때 그 때 달라질 수는 있다.
  - 굳이 미리 나눠놓을 필요가 있겠는가
- 가변 분할 방식(Variable partition)
  - 프로그램 들어갈 사용자 메모리 영역을 partition으로 미리 나눠 놓지 않는 것
  - 실행될 때마다 메모리에 차곡차곡 올려놓는 방식.
  - 먼저 들어왔던 프로그램이 종료됐는데, 새로 들어올 프로그램이 그 공간보다 클 경우. 그 자리는 비어있게 되고 외부조각이 된다.
  - 가변 분할 방식이어도 외부 조각이 생길 수 있다. 내부 조각은 생기지 않으나
  - 프로그램 종료시 `hole(가용 메모리 공간)`이 산발적으로 생김. OS가 hole도 고려해서 관리해줘야 함.
  - Dynamic Storage-Allocation Problem
    - 가변 분할 방식에서 hole이 여러 군데 있기 때문에 어떤 hole에 프로그램을 집어 넣을 것인가 결정하는 문제
    - 일단은 프로그램보다 hole이 커야 하고 어디에 넣을 것인가 3가지 알고리즘
      size n일 경우 hole은 n 이상임을 상정
    1. First-fit: 제일 처음 발견되는 hole에 할당.
    2. Best-fit: 다 살펴보고 제일 작은 hole에 할당.
    3. Worst-fit: 다 살펴보고 제일 큰 hole에 할당. 가장 어리석음
       당연하지만 Worst보다 First(overhead), Best(미래성)가 속도, 공간 측면에서 효과적
  - compaction
    - 작은 여러 hole 들을 한 군데로 밀어서 하나의 큰 hole로 바꾸는 방법
    - 매우 비용이 많이 드는 작업.
    - 전체 프로그램 binding과 연결된 문제이기 때문에 고려할 것이 많다.
    - 적어도 runtime binding이 지원돼야 함
    - 전체 다 미는 것보다는 작은 이동으로 큰 hole을 만들면 효율. 하지만 그것을 결정하는 것도 간단하지 않음.

### Noncontinuous Allocation

연속 할당의 문제들로부터 비교적 자유로움.  
크게 2가지로 나눠볼 수 있음

1. Paging
   - 프로그램 주소 공간을 같은 페이지 크기로 쪼갠다. 페이지 단위로 메모리에 올리거나 backing store에 내려놓거나 한다.
   - 물리적 메모리 공간도 똑같은 크기로 자르게 되는데, 그것을 페이지 프레임(Page Frame: Page가 들어갈 수 있는 공간)이라고 함
   - hole의 크기가 균일하지 않던 문제, 한 군데로 미는 등의 행동이 필요가 없어진다. 어차피 비어있는 공간은 Page Frame 크기만큼으로 정해져 있기 때문에 어떤 Page든지 어떤 공간에든 들어갈 수 있다.
   - 주소 변환이 복잡(MMU): Page마다 주소 변환을 해줘야 하기 때문에 주소 binding이 더 복잡
   - Physical:frame / Logical:page
   - page table 사용해 logical -> physical 주소 변환
   - 내부조각 발생 가능 -> 크게 영향이 있지는 않다.
2. Segmentation
   - 단순 같은 크기가 아니라 의미있는 단위로 자른다.
   - 프로그램 주소 공간 = code + data + stack 3가지의 의미있는 공간
   - 그래서 Segmentation에서는 보통 code segment, data segment, stack segment로 잘라서 필요시 메모리 다른 위치에 올릴 수 있도록 하는 방법. 더 잘게 쪼갤 수도 있다.
   - Segment: 프로그램 주소 공간을 구성하는 의미 단위
   - 크기가 균일하지는 않기 때문에, hole에서의 문제가 발생

# [메모리 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140429132440045277?vmode=f), [#3](https://core.ewha.ac.kr/publicview/C0101020140502151452123728?vmode=f)

## Paging

프로그램을 구성하는 logical 메모리를 동일 크기의 page로 잘라서 각각physical 메모리에 동일 크기의 frame 비어있는 곳에 올라갈 수 있게 해주는 기법

### Paging Example

- 주소 변환을 위해 page table 사용.

page table: 각각의 논리적 page들이 어디 물리적 page frame에 올라가 있는가 주소 변환 위한 table. 논리적 page의 개수만큼 entry를 갖는다.

![](https://i.imgur.com/SaWsYfp.png)

### Address Translation Architecture

![](https://i.imgur.com/PGve6QV.png)

1. CPU가 logical 주소를 주면  
   주소 앞 부분은 table에서의 page 번호(p, offset), 뒷 부분이 logical 주소(d)
2. page table에서 page 번호를 가지고 frame 번호(f)를 찾는다.
3. 앞부분 frame 주소 f + d = physical 주소
4. 변환한 주소로 물리적 주소 찾아감

offset은 변화가 없고 앞부분만 변화

보통 page 크기는 4KB.  
굉장히 큰 것임. page table로 100만개 entry 필요. 프로그램마다 page table 필요  
register에 넣을 수 없다. 그렇다고 하드 disk에 넣을 수도 없다. cache에 넣기도 용량이 너무 큼. 그래서 page table은 `Memory`에 집어넣는다.  
=> 이 말은 곧 이 역시 주소 변환을 필요로 한다는 것인데, 결국 모든 메모리 접근 연산에는 `2번의 메모리 접근`이 있는 것  
1 page table 접근, 2 실제 data 메모리 접근

### Implementation of Page Table

- base와 limit으로 사용되던 2개의 register가 paging 기법에서는 PTBR, PTLR로 사용된다.
  - Page-table base register: 메모리 상 page table이 어디있는지, 시작 위치 갖고 있음
  - Page-table length register: page table의 길이를 가지고 있음.
- 이런 2번의 접근은 상당히 비용이 큰 것 => 속도 향상을 위해 별도의 HW 사용. 일종의 cache. CPU와 메인 메모리 사이

### Paging Hardware with TLB

![](https://i.imgur.com/vc38Kb7.png)

- 사실 page table도 physical memory에 그려주는게 맞음.
- 속도 개선을 위한 TLB 추가적인 HW
- cache 메모리는 OS에게는 감추어진 계층. 메인 메모리에서 빈번히 사용되는 데이터를 cache 메모리에 저장해서 더 빨리 접근할 수 있게 해준다. 그와 비슷함
- 주소 변환을 위한 별도의 cache: TLB. data 보관과는 용도가 다름.
- 빈번히 참조되는 table entry를 caching
- 메인 메모리보다 빠른 속도
- page table 접근 전에 먼저 TLB를 확인
- 해당 entry가 TLB에 있다면(TLB hit) -> 바로 변환  
  이 경우는 메모리에 1번 접근하는 것
- 해당 entry가 TLB에 없다면(TLB miss) -> page table  
  이 경우 메모리 2번 접근
- page table 전체를 갖고 있는 것이 아니다
- 논리적 page 번호 p와 그 값에 해당하는 frame 번호 f를 쌍으로 가지고 있어야 한다. -> page table과의 차이점
- 특정 항목 검색이 아닌 전체를 검색. 전체 search는 비용이 많이 든다.  
  => parallel search가 가능한 associative registers를 이용해 구현
- page table이 프로그램마다 하나씩이므로 TLB 역시 프로그램마다 다른 정보
  => context switch마다 flush해서 정보를 비워내야 함
- ![](https://i.imgur.com/IH2O5Cv.png)  
  메모리 접근 hit시 1번, miss시 2번이니까 식이 이렇게 되고, ε은 1보다 매우 작은 값이고, ɑ는 1에 가까운 값이므로 EAT는 2보다 훨씬 작은 값이 된다.

### Two-Level Page Table

page table이 2단계  
Why? 컴퓨터는 보통 1. 빠르게하던지, 2. 공간을 줄이던지 목적을 가짐  
=> 속도는 줄지 않겠지. 결국 `공간을 줄이기 위한 목적`

현대 컴퓨터는 메모리 주소 체계가 굉장히 큼(32, 최근은 64 bit address). 32bit =
각 프로그램 메모리 크기와 실제 Physical 메모리 크기는 독립적. 물론 Physical 메모리가 크면 여러 프로그램이 올라와 있을 수 있으므로 빠를 것. Virtual 메모리가 더 커도 실행 가능.  
프로그램마다 Virtual 메모리의 Maximum 크기는 메모리 표시 주소 체계에 따라 다름.

메모리 주소는 byte 단위로 매겨짐.  
32bit를 사용한다고 하면, 표현 가능한 서로 다른 정보는 2^32 = 4G개  
=> 따라서 32bit를 이용해서 표시할 수 있는 메모리 주소 2^32byte로 표시 가능  
=> 각 프로그램이 가질 수 있는 최대 메모리 크기는 4GB!  
이 4GB를 page 단위(각 page는 4KB)로 쪼개면 M(100만)개가 나온다.  
=> page table도 100만개 이상의 entry 필요  
그런데 page table은 memory에 들어간다. 그리고 프로그램마다 메모리에 다 들어가면 공간 낭비 심함.(page table도 1 entry/byte => 프로그램 하나당 page table 4MB)  
=> `따라서` 2단계 page table을 필요로 한다.

실제 프로그램이 사용하는 공간은 지극히 일부. code, data, stack 외에 사용되지 않는 공간이 상당 부분 있다. but page table은 연속된 주소 번호로 조회하므로 중간을 비울 수 없다. 공간 낭비.

- Two-Level Paging Example  
  32bit일 경우  
  ![](https://i.imgur.com/eRpr1Qo.png)
  outer(p1) + inner(p2) + offset(d)

- Address-Translation Scheme  
  ![](https://i.imgur.com/vEoPCNf.png)
  1. outer 번호(p1)로 inner 번호 pointer 조회
  2. 얻어진 inner table + p2로 물리적 page frame 번호 찾음
  3. frame + d로 physical 메모리 찾음

innter table의 크기가 page 크기와 똑같다. 즉, table이 메모리의 page frame 하나로 들어간다. 4KB. entry 하나가 보통 4B이므로 entry 개수 1K개

p1, p2, d는 몇 bit가 되어야 하나? 안쪽부터 차례대로 따라올라오면 된다.

- d offset의 bit 개수는 12  
  => page 하나가 4KB = 2^12B  
  => page 안에서 하나의 B가 얼마나 떨어져 있는지 보려면 12bit(2^12개)의 정보만 있으면 된다.
- 나머지 20은?
  - inner table 하나의 크기 4KB. 각 entry는 4B이므로 1K(2^10)개  
    => 10bit(2^10개) 필요
  - outer는 남는 10bit

> 64bit 주소 체계라면? page 크기 그대로 4B. 2단계 테이블 -> 생각해봐~

사실 공간적으로도 손해다. 1 level 때랑 inner가 똑같이 쓰면서, outer가 추가 됐으므로  
그러면 왜 사용?  
=> 1 level에서는 사용하지 않는 maximum logical 메모리 크기에 맞춰서 page table를 다 만들어줘야 했음. 하지만 2 level에서는 outer는 logical 메모리 크기만큼 만들어진다고 해도, 사용되지 않는 메모리에 대해서는 inner에서 만들어지지 않고 pointer가 null 상태.  
다 차있다면 2단계가 손해겠지만, 그만큼 null 상태인 entry가 많다는 것  
=> 즉, inner table의 공간을 줄일 수 있기 때문에 2 level을 사용한다.

### Multilevel Paging and Performance

- 여러 단계의 페이지 단계를 두면 테이블 공간을 더 줄일 수 있다. 하지만 한 번 주소 변환을 위해 여러 테이블을 접근(= 메모리에 여러번 접근)해야 함(ex. 4단계면 5번 접근). overhead가 크다.
- 그래도 TLB(= 캐시 메모리)를 사용하기 때문에 지나치게 오래 걸리지는 않는다.
- ![](https://i.imgur.com/7P8svQ3.png)
  - 520 = TLB 20 + miss로 인해 테이블 4번 접근 400 + 메모리 접근 100

### Valid (v)/ Invalid (i) Bit in a Page Table

- page table에는 사실 frame number뿐만 아니라 추가 bit가 있다. valid-invalid 정보.
- ![](https://i.imgur.com/bGvWbrd.png)
- 위 그림에서도 6, 7번 page는 없어도, 주소 체계에서 해당 table은 만들어지고 사용되지 않는 정보 invalid 표시. 의미 없는 정보라는 뜻.
- valid: 실제로 Physical 메모리에 올라와 있다.
- invalid: 1) 이 page가 프로그램에서 사용되지 않거나, 2) 메모리에 없고 swap area에 있거나

### Memory Protection

- Protection bit: 어떤 연산에 관한 접근 권한이 있느냐(write/read/read-only)
  - code는 read-only(CPU가 읽어서 instruction 실행하는 용도이므로)고 data, stack은 read/write를 다 줘야 한다.
  - 애초에 하나의 Process가 다른 Process의 page에 접근할 수는 없으므로 이에 관한 protection은 아니다.
- Valid-invalid bit

### Inverted Page Table

많은 용량을 차지하는 것이 문제였음. 너무 많은 entry는 문제. 심지어 Process별. 공간 overhead 큼. 이를 해결하고자 나왔음. 원래 page table통한 주소 변환을 역발상으로 뒤집는다.

- Inverted Page Table Architecture
  - ![](https://i.imgur.com/3XRGoF2.png)
  - 원래는 Process마다 page table이 필요.
  - 하지만 Inverted 구조에서는 시스템 내 page table이 단 하나 존재(System wide하게 단 하나)
  - page table의 entry가 ~~Process의 page 개수 만큼이 아니라~~, physical 메모리의 page frame 개수만큼 존재
  - physical 메모리의 frame number f로부터 logical 메모리의 page number p + d를 구한다.
  - 하지만 주소 변환은 logical로부터 physical을 구하는 것이 아닌가?
  - 어찌보면 목적에 맞지 않는 table.
  - 결국 해당하는 f를 찾기 위해서 page table의 entry를 전부 봐야한다.
  - 그렇다면 도대체 Why? -> 공간 줄이기 위해서, 대신 시간적 overhead
  - CPU가 pid+p+d를 준다  
    -> table에서 pid+p를 검색  
    -> 찾아진 위치가 몇번째 entry인지 확인  
    -> 해당 entry 번호가 f  
    -> f + d
  - time overhead가 너무 크므로 전부 보는 것보다는 TLB처럼 associative register 이용해서 entry 병렬 검색하도록 하면 시간 줄일 수 있다.

### Shared Page

다른 Process와 공유할 수 있는 page.  
같은 code를 사용하는 경우가 있을 수 있다. shared code.  
각각을 다 따로 올리는 것이 아니라 shared code는 같은 frame을 공유  
다른 말로 Re-entrant code(=pure code)재진입가능 코드

1. `read-only`로 세팅
2. `동일한 logical address`에 위치해야 한다.  
   동일한 physical address는 당연한 것.  
   앞에서 봤듯이 code 안에 이미 logical address에 관한 내용이 있기 때문

IPC와는 좀 다르다. 주소 공간 mapping까지는 같은데,

- IPC에서는 한 Process가 write하면 다른 Process가 read하고 이런 느낌이었다면
- Shared code(page)에서는 read-only만 가능

- Shared Pages Example  
  ![](https://i.imgur.com/Iqq3hZQ.png)  
  그림처럼 같은 logical address를 가져야 한다.

# [메모리 관리 #4](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)

## Segmentation

프로그램을 의미 단위로 여러개의 segment로 쪼갠 것. 더 잘게 쪼개려면 code 중에서도 main(), function, global variables 등으로 더 쪼갠다.

### Segmentation Architecture

- 2가지로 구성

1. segment-number s
2. offset d

Segment table

- segment별로 주소변환 해야하므로

Segment-table base register(STBR)

- segment table의 시작 위치

Segment-table length register(STLR)

- segment table의 길이: segment의 개수
- s < STLR

### Segmentation Hardware

![](https://i.imgur.com/CQFjBtm.png)

1. CPU가 논리주소(segment 번호 s, offset d)
2. segment table로 가서 s번째에 적힌 base 조회  
   page table과의 차이는 segment별로 길이가 다를 수 있으므로 길이 정보 limit을 함께 담고 있다.
3. STLR 비교
   1. STLR보다 큰 s -> 잘못된 시도. trap
   2. segment table의 limit보다 큰 d -> 잘못된 offset. trap
4. 정상적이면 -> base + d가 physical 메모리 주소

vs Paging

- Paging은 page 크기가 균일. offset이 page 크기에 의해 결정.
  - segment 길이는 offset으로 표현할 수 있는 bit 수 이상은 아니다.
- Paging base 주소가 frame 번호로 주어지면 된다.
  - segment에서는 길이가 다 다르므로 어디에서 시작하는지 정확한 byte 단위 주소를 줘야 한다.

Allocation: 단점

- first fit / best fit을 써야 함
- 외부 조각 발생 문제, hole 생기는 문제
- 균일하지 않아서 생기는 문제

장점

- 의미 단위이므로 Protection을 할 경우 Paging보다 더 용이. Paging에서는 접근 권한이 섞여있을 수 있어서 보완 필요.
- Sharing에서도 Paging보다 유리하다.

### Example of Segmetation

paging는 개수가 상당히 많은 반면, segment는 몇개 없다.
table을 위한 메모리 낭비가 심한 것은 paging

![](https://i.imgur.com/SssUXoc.png)

### Sharing of Segments

segment를 공유하는 경우

![](https://i.imgur.com/wdO1qfV.png)

두 Process에서 동일한 editor copy 공유

### Segmentation with Paging

- Segment 하나가 여러개의 page로 구성
- 각 프로그램은 segment의 주소 정보, offset 갖고 있음
- 기존 Segment에서는 조합해서 바로 phsical 주소로 갔다. segment가 통째로 메모리에 올라가기도 했으므로
- 여기에서는 메모리에 올라갈 때 page 단위로 쪼개져서 올라간다.  
  장점? Allocation 문제가 생기지 않는다. 중간 중간 hole 문제 없음. 메모리에는 page 단위로 올라가니깐  
  또 공유, 보안 업무는 segment table 레벨에서 수행. R/W인지 Read-only인지
- 사실 segment만 쓰는 시스템은 거의 없다. 이렇게 섞어서 쓰지. segment 하나가 여러개의 page => 따라서 segment 하나당 여러개의 page table.  
  그래서 segment table을 따라가면 page table의 시작 위치가 있다.
- segment의 limit을 보면 page table의 entry 개수를 알 수 있다. 길이 벗어나는 요청은 trap
- segment 길이보다 offset이 커도 잘못된 요청. 실제 segment 길이와 요청한 offset 길이도 비교.
- segment의 각 page의 frame 번호 알기 위해 segment table에서의 d를 page 번호 p + page offset d'로 쪼갠다.

### Allocation of Physical Memory

사실 메모리 관리라고 하는 것은 특히 물리적 메모리에 관한 내용이 주. 주소 변환이 주 내용  
주소 변환은 OS의 역할이 아니라 앞서 소개한 것과 같이 MMU와 같은 HW 역할  
Process 하나가 CPU 실행하면서 주소를 요청할 경우 OS의 도움을 전혀 받지 않는다. 만약 OS의 도움을 받는다? 메모리 주소 요청할 때마다 CPU 제어권이 OS에 넘어간다는 것. 말이 안됨.

`주소 변환은 무조건 하드웨어`

I/O 장치 접근은 OS가 끼어들어야 한다.

사실상 OS의 역할이 없던 강의. Virtual Memory부터 중요해진다.

- Contiguous allocation
- NonContiguous allocation
  - [Paging](#Paging)
  - [Segmentation](#Segmentation)
  - Paged Segmentation
