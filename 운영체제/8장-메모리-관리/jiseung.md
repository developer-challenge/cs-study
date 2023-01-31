# [메모리 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140425151219100144?vmode=f)

## Logical vs. Physical Address

### Logical Address(=virtual address)

- 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스마다 0번지부터 시작
- CPU가 보는 주소는 logical address이다.
  - CPU는 하드웨어지만 instruction을 실행하려면 Logical address를 보아야 연산이 가능
  - 주소변환을 해서 Physical address를 전달 받아야 함

### Physical address

- 메모리가 실제 올라가는 위치

## 주소바인딩(Address Binding)

- 주소를 결정하는 것
- Symbolic Address → Logical Address → Physical address
  - Symbolic Address
    - 이름(Symbol)을 붙여 메모리 공간의 주소를 사용
    - 컴파일로 변환
- 언제 결정이 되는가?
  - Compile time binding
    - 컴파일 시점에 Physical address 결정
    - 컴파일러는 절대 코드 (absolute code)를 생성
    - 시작 위치가 변경되면 재컴파일된다. (주소를 바꾸려면 컴파일 다시 해야 한다)
    - Logical address == Physical address
    - 미리 결정되므로 굉장히 비효율적. 과거 하나의 프로그램만 실행할 때 사용함
  - Load time binding
    - 프로그램 실행 후 결정
    - Loader의 책임 하에 Physical address 부여
    - 컴파일러가 재배치가능코드(relocatable code)를 생성한 경우 가능
  - Execution time binding(=Runtime binding)
    - 프로그램 실행 후 결정. 실행 도중에도 프로세스의 메모리 상 위치를 옮길 수 있음
    - CPU가 주소를 참조할 때마다 binding을 점검(address mapping table)
    - 하드웨어적 지원 필요(base and limit registers, MMU)
    - 현대 시스템

## Memory-Management Unit(MMU)

- 주소를 결정하는 것
- Symbolic Address → Logical Address → Physical address
  - Symbolic Address
    - 이름(Symbol)을 붙여 메모리 공간의 주소를 사용
    - 컴파일로 변환

## Dynamic Relocation

- 주소를 결정하는 것
- Symbolic Address → Logical Address → Physical address
  - Symbolic Address
    - 이름(Symbol)을 붙여 메모리 공간의 주소를 사용
    - 컴파일로 변환

## Hadware Support for Address Translation

- 주소를 결정하는 것
- Symbolic Address → Logical Address → Physical address
  - Symbolic Address
    - 이름(Symbol)을 붙여 메모리 공간의 주소를 사용
    - 컴파일로 변환

## Some Treminologies

- 주소를 결정하는 것
- Symbolic Address → Logical Address → Physical address
  - Symbolic Address
    - 이름(Symbol)을 붙여 메모리 공간의 주소를 사용
    - 컴파일로 변환

# [메모리 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140429132440045277?vmode=f)

# [메모리 관리 #3](https://core.ewha.ac.kr/publicview/C0101020140502151452123728?vmode=f)

## Allocation of Physical Memory

### 메모리는 일반적으로 두 영역으로 나뉘어 사용된다.

- OS 상주 영역
  - Interrupt vector와 함께 낮은 주소 영역 사용
- 사용자 프로세스 영역
  - 높은 주소 영역 사용

> 사용자 프로세스 영역의 할당 방법에는 연속 할당과 불연속 할당이 있다.

# Contiguous allocation(연속 할당)

- 각각의 프로세스가 메모리의 연속된 공간에 적재되도록 하는 것

외부 조각 : 프로그램 크기보다 분할의 크기가 작은 경우. 배정되지 않은 빈 곳이지만 프로그램이 골라갈 수 없는 작은 분할
내부 조각 : 프로그램 크기보다 분할의 크기가 큰 경우. 하나의 분할 내부에서 발생하지 않는 사용되지 않는 메모리 조각. 특정 프로그램에 배정되었지만 사용되지 않는 공간

## Fixed partition allocation(고정 분할 방식)

- 물리적 메모리를 몇 개의 영구적 분할(partition)로 나눔
- 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
- 분할당 하나의 프로그램 적재
- 융통성이 없음
  - 동시에 메모리에 load되는 프로그램의 수가 고정됨
  - 최대 수행 가능 프로그램 크기 제한
- 내부 조각, 외부 조각 발생

## Variable partition allocation(가변 분할 방식)

- 프로그램의 크기를 고려해서 할당
- 분할의 크기, 개수가 동적으로 변함
- 기술적 관리 기법 필요
- 외부 조각 발생

## Hole

- 가용 메모리 공간
- 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
- 프로세스가 도착하면 수용가능한 hole을 할당
- 운영체제는 다음 정보를 유지
  - 할당 공간
  - 가용 공간(hole)

## Dynamic Storage-Allocation Problem

> 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
> First-fit, Best-fit이 Worst-fit보다 속도, 공간 이용률 측면에서 효과적

- First-fit
  - Size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
- Best-fit
  - Size가 n이상인 가장 작은 hole을 찾아서 할당
  - Hole list가 정렬되지 않은 경우 O(n)
  - 많은 수의 아주 작은 hole들이 생성됨
- Worst-fit
  - 가장 큰 hole에 할당
  - O(n)
  - 상대적으로 아주 큰 hole들이 생성됨

## Compaction

> external fragmentation 문제를 해결하는 한 가지 방법

- 사용중인 메모리 영역을 한 군데로 몰고 hole들을 다른 한 곳으로 모아 큰 block을 만드는 것
- 매우 비용이 많이 듦
- 최소한의 메모리 이동으로 compaction하는 방법. 복잡함
- 프로세스의 주소가 실행 시간(runtime)에 동적으로 재배치 가능한 경우에만 수행할 수 있다.

# Noncontiguous allocation(불연속 할당)

- 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음

## Paging

- Process의 virtual memory를 동일한 사이즈의 page 단위로 나눔
- Virtual memory의 내용이 page 단위로 noncontiguous하게 저장됨
- 일부는 backing storage, 일부는 physical memory에 저장

### Basic Method

- physical memory를 동일한 크기의 frame으로 나눔
- logical memory를 동일 크기의 page로 나눔(page size == frame size)
- 모든 가용 frame들을 관리
- page table을 사용해 logical address를 physical address로 변환
  - 2개의 register로 관리할 수 없음
  - 페이지 개수만큼의 entry 존재
  - 인덱스를 통해 접근할 수 있는 자료구조
- External fragmentation X
  - 일정한 크기이므로
- Internal fragmentation O
  - 딱 나눠떨어지지 않으므로 생길 수 있음

## Paging Example

-

## Address Translation Architecture

- Address translation
  - page table 중 일부가 associative register에 보관되어 있음
    - 만약 해당 page#가 associative register에 잇는 경우 곧바로 frame#을 얻음
    - 그렇지 않은 경우 main memory에 있는 page table로부터 frame#을 얻음
  - TLB는 context switch 때 flush (remove old entries)
    - 프로세스마다 다른 TLB

## Implementation of Page Table

- Page table은 main memory에 상주
  - 프로그램 별로 존재. 용량 큼
- **Page-table base register (PTBR)** : page table
- **Page-table length register** : 테이블 크기
- 모든 메모리 접근 연산에 2번의 memory access필요
- page table 접근 1번, 실제 data/instructoin 접근 1번
- 속도 향상
  - associative register(translation look-aside buffer, TLB) 고속의 lookup hardware cache

## Paging Hardware with TLB

### Associative registers (TLB)

- parallel search가 가능
- TLB에는 page table 중 일부만 존재
- Address translation
  - page table 중 일부가 associative register에 보관되어 있음
    - 만약 해당 page#가 associative register에 잇는 경우 곧바로 frame#을 얻음
    - 그렇지 않은 경우 main memory에 있는 page table로부터 frame#을 얻음
  - TLB는 context switch 때 flush (remove old entries)
    - 프로세스마다 다른 TLB

## Two-Level Page Table

2단계 page table (page of page table)
속도 개선 X
공간 개선 O

- 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
  - page table 자체를 page로 구성
  - 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 null(대응하는 inner page table이 없음)
    - 주소 공간 절약
- Address-Translation Scheme
- Example

  - logical address (on 32-bit machine with 4K page size)의 구성
    - 20bit의 page number
    - 12bit의 page offset
  - page table은 page로 구성되므로 page number은 다음과 같다 (각 page table entry는 4B)
    - 10-bit page number
    - 10-bit page offset
  - logical address

    p1은 outer page table의 index
    p2는 outer page table의 page에서의 변위(displacement)

## Multilevel Paging and Performance

- Address space가 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요
  - TLB를 통해 메모리 접근 시간 줄일 수 있음
    - 캐시 메모리를 통해 메모리 접근 시간 줄일 수 있음
- 4단계 페이지 테이블

## Valid (v)/ Invalid (i) Bit in a Page Table

- entry마다 부가적인 bit 저장
- Valid(v) / Invalid(i)
  - 실제로 page가 물리적 메모리에 올라가 있는지

## Memory Protection

> Page table의 각 entry마다 아래의 bit를 둔다

- Protection bit
  - page에 대한 접근 권한(read/write/read-only)
- Valid-invalid bit
  - valid : 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음(접근 허용)
  - invalid : 해당 주소의 frame에 유효한 내용이 없음(접근 불허)
    - 프로세스가 그 주소 부분을 사용하지 않는 경우
    - 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우

## Inverted Page Table

- Page Table이 매우 큰 이유
  - 모든 process별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
  - 대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재
- Inverted page table
  시스템에 page table이 한 개 존재
  - Page frame 하나당 page table에 하나의 entry를 둔 것 (system-wide)
    - 물리적 메모리의 프레임 개수만큼 페이지 존재
  - 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시
    - process-id, process의 logical address
  - 단점
    - 테이블 전체 탐색 필요
    - 공간 효율성 O 시간 효율성 X
      - entry를 전부 검색해야 함
      - 논리적인 페이지 번호 저장 + 프로세스 id 저장
  - 해결책
    - Associative register 사용(expensive)

## Shared Page

- Shared Code
  - 공유할 수 있는 코드는 같은 frame으로 매핑
  - Re-entrant Code(Pure Code)
  - read-only로 해서 프로세스 간에 하나의 code만 메모리에 올림
    - text editors, compilers, window systems
  - 제약 조건
    - Shared code는 모든 프로세스의 logical address space에서 동일한 위치에 있어야 함
- Private code and data
  - 각 프로세스들은 독자적으로 메모리에 올림
  - Private data는 logical address space의 아무 곳에 와도 무방

# [메모리 관리 #4](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)

## Segmentation Architecture

> 프로그램의 주소 공간을 의미 있는 단위(Segmentation)로 자름
> 필요시에 다른 물리적인 위치에 올림
> 각 함수를 다른 segment. 크기가 다름

- 프로그램은 의미 단위인 여러 개의 segment로 구성
  - 작게는 프로그램을 구성하는 함수 하나하나를 segment로 정의
  - 크게는 프로그램 전체를 하나의 segment로 정의 가능
  - 일반적으로는 code, data, stack 부분이 하나씩의 segment로 정의됨
- 다음과 같은 local unit

  - main(), function, global variables, stack, symbol table, arrays

- Logical address는 다음 두 가지로 구성
  - <segment-number, offset>
- Segment table
  - 주소 변환
  - each table entry has
    - base : starting physical address of the segment
    - limit : length of the segment
- Registers
  - Segment-table base register (STBR)
    - 물리적 메모리에서의 segment table의 위치
  - Segment-table length register (STLR)
    - 프로그램이 사용하는 segment의 수
      - segment number

## Segmentation Hardware

segment number이 잘못되었는지도 체크

- limit : 세그먼트의 길이가 일정하지 않으므로 limit이 필요
- segment table length register : segment nubmer과 비교해서 잘못되었으면 trap(addressing error) 발생

### Segmentation Architecture (Cont.)

> 공유 및 보안에서 의미적인 일에 유리함
> 테이블 메모리 낭비가 더 적음
> but, 조각날 가능성

- Protection
  - 각 세그먼트 별로 protection bit가 있음
  - Each entry :
    - Valid bit = 0 → illegal segment
    - Read/Write/Execution 권한 bit
      ⇒ 의미 단위 권한 부여 장점
- Sharing
  - Shared segment
  - same segment number
    ⇒ segment는 의미 단위이므로 공유(sharing)와 보안(protection)에 있어 paging보다 효과적
- Allocation
  - first fit/best fit
  - external fragmentation 발생
    ⇒ segment의 길이가 동일하지 않으므로 가변분할 방식과 동일한 문제점 발생

## Example of Segmetation

- segment별로 물리적 메모리에 위치

## Sharing of Segments

- 공유 segment
  - segment number 동일
  - 같은 physical address
- Private segment
  - 별도

## Segmentation with Paging

- Paged Segmentation
  - Segment를 여러개의 Page로 구성하는 기법
- Pure segmentation과의 차이점
  - segment에 시작위치가 담겨 있지 않음. 메모리에 페이지 단위로 올라감 ⇒ allocation 문제 해결
  - Segment-table entry가 segment의 base address를 가지고 있는 것이 아니라 segment를 구성하는 page table의 base address를 가지고 있음
  - 의미 단위 - Segment 레벨
  - 물리적 메모리 - Page 레벨
  - 관리가 편함
