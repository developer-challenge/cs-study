[메모리 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140425151219100144?vmode=f)

# Logical vs. Physical Address

## Logical address (=virtual address)

- 프로그램 실행시 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스마다 0번지부터 시작
- CPU가 보는 주소는 `logical address`

## Physical address

- 메모리에 실제 올라가는 위치
- 물리적 메모리는 통으로 관리되며 물리적인 주소 공간이 정해지면서 주소가 변경됨 = `주소 바인딩`

# 주소바인딩(Address Binding)

주소를 결정하는 것

Symbolic Address -> Logical Address -> (✅ 이 지점이 언제인지) -> Physical address

## Compile time binding

주소 변환 시점 : `컴파일`

물리적 메모리 주소가 컴파일 시 알려짐
시작 위치 변경시 재컴파일
컴파일러는 `절대 코드 (absolute code)` 생성
-> 주소를 변경하고 싶다면 컴파일을 새로 해야함

## Load time binding

주소 변환 시점 : `실행 시작`

Loader의 책임하에 물리적 메모리 주소 부여
컴파일러가 `재배치 가능 코드 (relocatable code)`를 생성한 경우 가능

## Execution time binding (=Run time binding)

주소 변환 시점 : `실행 중에 물리적 주소가 바뀌는 경우`

수행이 시작된 이후에도 프로세스의 메모리상 위치를 옮길 수 있음
CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)
그때 그때 주소 변환을 하기 위해 하드웨어적인 지원이 필요
(ex) base and limit registers, MMU )

> **CPU가 바라보는 주소 = Logical address**

# Memory-Management Unit(MMU)

logical address를 physical address로 매핑해주는 하드웨어

## MMU scheme

사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register (=relocation register)의 값을 더함

## user program

`logical address`만 다룸
실제 physical address를 볼 수 없으며 알 필요가 없다.

# Dynamic Relocation

![](https://i.imgur.com/ZcFxqP1.png)

# Hadware Support for Address Translation

![](https://i.imgur.com/rfcz3Kn.png)

운영체제 및 사용자 프로세스간의 메모리 보호를 위해 사용하는 레지스터
CPU가 주는 논리주소가 사용 중인 메모리 주소 크기를 벗어나지 않는지 확인

벗어난 요청인 경우 trap 발생 -> CPU 제어권이 운영체제로 넘어감

## Relocation register (=base register)

접근할 수 있는 물리적 메모리 주소의 최소값

## Limit register

논리적 주소의 범위

# Some Treminologies

## Dynamic Loading

프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load 하는 것 -> `Memory utilization`의 향상

가끔씩 사용되는 많은 양의 코드의 경우 유용 (ex. 오류 처리 루틴)

**운영체제의 특별한 지원 없이** 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)

> `loading` : 메모리로 올리는 것

## Dynamic Linking

Linking을 실행 시간까지 미루는 기법

> `Linking` : 여러 군데 존재하는 컴파일 된 파일을 모아 실행 파일을 만드는 것

### Static linking

- 라이브러리가 프로그램의 실행 파일 코드에 포함됨
- 실행 파일의 크기가 커짐
- 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비

### Dynamic linking

- 라이브러리가 실행시 연결됨
- 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub라는 작은 코드를 둠
- 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고, 없다면 디스크에서 읽어옴
- 운영체제의 도움이 필요

## Overlays

메모리에 프로세스의 부분 중 실제로 필요한 정보만을 올림
프로세스의 크기가 메모리보다 클 때 유용
운영체제의 지원없이 사용자에 의해 구현
작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현

- Manual Overlay
- 프로그래밍이 매우 복잡

## Swapping

프로세스를 일시적으로 메모리에서 backing store로 쫒아내는 것
원칙적인 개념은 프로그램이 메모리에서 통째로 쫒겨나는 것

### Backing store (=swap area)

디스크 : 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간

### Swap in / Swap out

![](https://i.imgur.com/BHAFPxO.png)

일반적으로 중기 스케줄러에 의해 swap out 시킬 프로세스 선정
priority-based CPU scheduling algorithm

- 우선순위가 낮은 프로세스 : swapped out
- 우선순위가 높은 프로세스 : 메모리에 올려놓음

Compile time 혹은 load time binding 에서는 원래 메모리 위치로 swap in 해야함.
Execution time binding 에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음 -> swapping이 효과적으로 동작

swap time은 대부분 transfer time (swap되는 양에 비례하는 시간)

# Allocation of Physical Memory

메모리는 일반적으로 두 영역으로 나뉘어 사용

1. OS 상주 영역
   - interrupt vector와 함께 낮은 주소 영역 사용
2. 사용자 프로세스 영역
   - 높은 주소 영역 사용

## 사용자 프로세스 영역의 할당 방법

- Contiguous allocation (연속 할당)
- NonContiguous allocation (불연속 할당)

# Contiguous allocation (연속 할당)

각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것

## fixed partition allocation (고정 분할 방식)

- 물리적 메모리를 몇개의 영구적 분할로 나눔
- 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
- 분할당 하나의 프로그램 적재
- 융통성이 없음
  - 동시에 메모리에 load되는 프로그램의 수가 고정됨
  - 최대 수행 가능 프로그램 크기 제한
- internal fragmentation 발생 (external fragmentation도 발생)
-

## variable partition allocation (가변 분할 방식)

- 프로그램의 크기를 고려해 할당
- 분할의 크기, 개수가 동적으로 변함
- 기술적 관리, 기법 필요
- external fragmentation 발생

## hole

가용 메모리 공간

- 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
- 프로세스가 도착하면 수용 가능한 hole을 할당

운영체제는 다음의 정보를 유지

1. 할당 공간
2. 가용 공간 (Hole)

## Dynamic Storage Allocation Problem

가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

1. First-fit
   - size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
2. Best-fit
   - size가 n 이상인 가장 작은 hole을 찾아서 할당
   - hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 Hole의 리스트를탐색해야함
   - 많은 수의 아주 작은 hole들이 생성
3. Worst-fit
   - 가장 큰 Hole에 할당
   - 역시 모든 리스트를 탐색해야 함
   - 상대적으로 아주 큰 hole들이 생성됨.

> `first-fit`과 `best-fit`이 `worst-fit`보다 속도와 공간 이용률 측면에서 효과적

## Compaction

hole을 한군데로 몰아 external fragmentation 해결하는 방법
매우 비용이 많이 드는 방법
최소한의 메모리 이동으로 compaction하는 방법 ->
프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우(run time binding)에만 수행

# Noncontiguous allocation (불연속 할당)

하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
**(현대의 시스템이 사용하고 있음)**

- Paging : 페이지를 구성하는 address space를 페이지 단위로 쪼개는 방법
- Segmentation : 주소 공간을 의미 있는 공간으로 쪼개는 방법
- Paged Segmentation

---

[메모리 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140429132440045277?vmode=f)

# Paging

- Process의 virtual memory를 동일한 사이즈의 page단위로 나눔
- virtual memory의 내용이 page 단위로 noncontiguous하게 저장됨
- 일부는 backing storage에, 일부는 physical memory에 저장

## basic method

- physical memory를 동일한 크기의 `frame`으로 나눔
- logical memory를 동일한 크기의 `page`로 나눔 (frame과 같은 크기)
- 모든 가용 frame들을 관리
- `page table`을 사용해 logical address를 physical address로 변환
- external fragmentation ❌
- internal fragmentation ✅

## Page table 실행

page table은 메인 메모리에 상주
`Page-table base register(PTBR)`가 page table을 가리킴
`Page-talbe length register(PRLR)`가 테이블 크기를 보관
-> 기존의 Register 대체

모든 메모리 접근 연산에는 2번의 memory access 필요

- page table 접근 1번, data/intruction 접근 1번

속도 향상을 위해 associative register 혹은 translation look-aside buffer(TLB) 라 불리는 고속의 lookup hardware cache 사용

## Associative Register (TLB)

parallel search가 가능 -> TLB에는 page table 중 일부만 존재

### Address translation

page table 중 일부가 associative register에 보관

page number in associative register
✅ : 곧바로 frame number를 얻음
❌ : 메인 메모리에 있는 page table로부터 frame number를 얻음

TLB는 문맥 교환 때 flush(remove old entries)

## Paging Hardware with TLB

페이지 별로 TLB에 다른 정보가 담김

![](https://i.imgur.com/5cJY5Ae.png)

> Associative register lookup time < 1

## Two-Level Page Table

![](https://i.imgur.com/VPkU29e.png)
-> page table을 사용하기 위한 공간을 효율적으로 사용하기 위한 방식

현대의 컴퓨터는 주소 공간이 매우 큰 프로그램 지원
ex) 32bit address 사용시 2^32(4G)의 주소 공간

page table 자체를 page로 구성

사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 null
-> 대응하는 `inner page table`이 없음

### Address-Translation Scheme

![](https://i.imgur.com/C7qfqLv.png)

p1 은 outer page table의 index
p2 는 outer page table의 page에서의 변위 (displacement)

안쪽 page table의 크기는 메모리의 페이지 크기와 같음

## Multilevel Paging and Performance

Address space가 더 커지면 다단계 페이지 테이블 필요

각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요

캐쉬 메모리를 통해 메모리 접근 시간을 줄일 수 있음

4단계 페이지 테이블을 사용하는 경우
메모리 접근 시간이 100ns, 캐쉬 메모리 접근 시간이 20ns, 캐쉬 적중률이 98%인 경우,
effective memory access time = 0.98 _ 120 + 0.02 _ 520 = 128 ns
=> 결과적으로 메모리 접근 시간을 28%만 다운

## Valid (v)/ Invalid (i) Bit in a Page Table

![](https://i.imgur.com/rhOkmfm.png)

테이블 자료구조 특성상 위에서 부터 페이지 사용 여부에 관계없이 존재하는 페이지 테이블이 작성되어야함.

## Memory Protection

Page table의 각 entry마다 아래의 bit를 둔다.

### Protection bit

page에 대한 접근 권한 (read/write/read-only)

### valid-invalid bit

`valid` : 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음 (접근 허용)

`invalid` : 해당 주소의 frame에 유효한 내용이 없음 (접근 불허)

- 프로세스가 그 주소 부분을 사용하지 않는 경우
- 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우

## Inverted Page Table

기존 page table의 공간 오버헤드를 해결하기 위한 방안

### page table이 매우 큰 이유

- 모든 프로세스 별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
- 대응하는 page가 메모리에 있는 아니든 간에 page table에는 entry로 존재

### Inverted Page Table Architecture

기존 page table을 뒤집어 page frame 하나당 page table에 하나의 entry를 둔 것 (system-wide)

시스템 안에 페이지 테이블 하나만 존재

pid를 함께 저장

- 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시 (process-id, process의 logical address)
- 단점 : 테이블 전체를 탐색해야함
- 해결 방안 : associative register 사용 (expensive)

## Shared Page

![](https://i.imgur.com/zJOcLVZ.png)

같은 프로그램을 여러개 사용하는 경우, 공통적으로 사용되는 부분을 메모리 공유

### Shared Code

Re-entrant code (pure code)

두가지 조건이 만족해야함.

1. read-only로 하여 프로세스간 하나의 code만 메모리에 올림
2. 모든 프로세스의 logical address space에서 동일한 위치에 있어야 함.

### Private code and data

공유할 필요가 없는 각자의 코드와 데이터가 포함된 각 프로세스들은 독자적으로 메모리에 올림
logical address space의 아무곳에 와도 무방

# Segmentataion

프로그램은 의미단위인 여러개의 segment로 구성
작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
크게는 프로그램 전체를 하나의 세그먼트로 정의 가능
일반적으로 code, data, stack 부분이 하나씩 세그먼트로 정의

segment는 다음과 같은 logical unit들

![](https://i.imgur.com/Wg4eUZp.png)

## Segmentation Architecture

Logical address는 다음 두가지로 구성

- segment-number
- offset

### protection

각 세그먼트 별로 protection bit가 있음
각각의 entry 별로
유효한 bit = 0 -> 불법적인 segment
Read/ Write / Execution 권한 bit

### Sharing

- shared segment
- same segment number

segment는 의미 단위기 때문에 공유와 보안에 있어 paging보다 효과적

### allocation

first fit / best fit
external fragmentation 발생

segment의 길이가 동일하지 않으므로 가변 분할 방식에서와 동일한 문제점 발생

### segment table

각 테이블의 entry는 `base`, `limit`을 가짐

- base : segment의 물리적 주소의 시작점
- limit : segment의 길이

### segment-table base register (STBR)

물리적 메모리에서의 segment table의 위치

### segment-table length register (STLR)

프로그램이 사용하는 segment의 수
segment number s는 STLR보다 작아야 함.

## Segmentation Hardware

![](https://i.imgur.com/BqpPfdS.png)

1. CPU가 논리 주소를 주면 이를 Segment 번호와 offset으로 변환
2. 시작 위치는 register가 갖고 있음
3. segment table에 시작위치에서 offset만큼 떨어진 위치에 가면 세그먼트가 물리적 메모리 위치값을 가짐
4. 일정한 크기로 나뉜 페이지 기법과 달리 세그먼트는 일정하기 않기 때문에 table entry가 offset 값을 알고 있음

## Example of Segmetation

![](https://i.imgur.com/0D32vlN.png)

> 페이징이 공간 낭비가 더 심함

## Sharing of Segments

![](https://i.imgur.com/2r8dm8S.png)

## Segmentation with Paging

페이징과 세그먼트를 혼합해서 사용하는 방법

pure segmentation과의 차이점
: segment-table entry가 segment의 base address를 가지고 있는 것이 아니라 segment를 구성하는 page table의 base address를 가지고 있음.

### 장점

- 페이지 크기별로 메모리에 올라가기 때문에 allocation 문제가 안생김
- 의미단위로 하는 일은 segment table을 이용

메모리 관리(주소 변환)에서 운영체제의 역할 : 없음 ❌
전부 하드웨어의 역할
I/O 장치 접근 시에는 운영체제가 개입

[메모리 관리 #3](https://core.ewha.ac.kr/publicview/C0101020140502151452123728?vmode=f)

[메모리 관리 #4](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)
