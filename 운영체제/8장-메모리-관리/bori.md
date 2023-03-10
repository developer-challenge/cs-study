# [Chapter 8. 메모리 관리 Part-1](https://core.ewha.ac.kr/publicview/C0101020140425151219100144?vmode=f)

## Logical vs. Physical Address

우리가 사는 집마다 고유의 주소가 있듯이 메모리도 주소를 통해 접근하는 장치

### Logical address(논리적 주소 / virtual address 가상 주소)

- 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스 마다 0번지부터 시작
- CPU가 보는 주소로 CPU는 논리적 주소에 근거해 명령을 실행

### Physical Address

- 메모리에 실제로 올라가는 위치
- 낮은 주소 영역 : 운영체제(커널)
- 높은 주소 영역 : 사용자 프로세스들(여러 프로그램들이 섞여서 올라간다.)

## 주소바인딩(Address Binding)

CPU가 기계어 명령을 수행하기 위해 논리적 주소를 통해 메모리 참조를 하게 되면 해당 논리적 주소가 물리적 메모리의 어느 위치에 매핑되는지 확인해야 한다.

- 프로세스의 논리적 주소를 물리적 메모리 주소로 연결시켜주는 작업

#### Symbolic Address → Logical Address → Physical Address

- Symbolic Address
  - 프로그래머 입장에서 숫자로 된 주소를 사용하지 않고 변수나 함수의 이름 즉, 심볼로 된 주소를 사용하는데 이를 Symbolic Address라고 한다.
  - Symbolic Address를 컴파일 하면 숫자로된 프로그램의 독자적인 주소가 생성
- `Logical Address → Physical Address`으로 변환되는 시점이 언제인가?
  - 물리적 메모리의 주소가 결정되는 시기에 따라 세가지로 분류 : Compile time binding, Load time binding, Execution time binding

### Compile time binding

- 컴파일을 하는 시점에 해당 프로그램이 물리적 메모리의 몇 번지에 위치할 것인지를 결정
- 컴파일러는 절대 코드를 생성
- 절대코드(absolute code)
  - 코드가 이미 정해진 memory address로 load되는 것
  - 논리적 주소가 물리적 주소로 고정되는 것
    ⇒ `논리적 주소 = 물리적 주소`
- 물리적 메모리 주소를 변경하려면 재컴파일 ⇒ 비효율적
- 현대의 시분할 컴퓨팅 환경에서는 잘 사용하지 않는 방법

### Load time binding

- 프로그램의 실행이 시작될 때 물리적 메모리 주소가 결정
  ⇒ 프로그램이 종료될 때까지 물리적 메모리상의 위치가 고정
- 로더의 책임하에 물리적인 메모리 주소 부여
- 로더(loader) : 사용자 프로그램을 메모리에 적재시키는 프로그램
- 컴파일러가 재배치 가능 코드(relocatable code)를 생성한 경우 가능한 주소 바인딩 방식
  ⇒ 항상 특정 위치에 올라가는 것이 아니라 프로그램 실행시에 비어있는 어느 위치든지 올라갈 수 있는 방식

### Execution time binding(= Run time binding)

- 프로그램 실행 후에도 그 프로그램의 물리적 메모리 주소를 변경할 수 있는 방법
- CPU가 주소를 참조할 때마다 물리적 메모리 주소를 주소 매핑 테이블(address mapping table)을 이용하여 바인딩을 점검해야 한다.
- 실시간 바인딩 방식이 가능하기 위해 하드웨어적인 지원이 필요
  - 기준 레지스터(base register)
  - 한계 레지스터(limit register)
  - MMU(Memory Management Unit)

> #### CPU가 바라보는 주소는 Logical address일까요? Physical address일까요?
>
> Logical address
>
> - CPU가 인스트럭션을 실행하려면 논리적 주소를 읽어서 연산
> - 주소 바인딩에 의해 메모리의 시작 위치는 바뀌어도 인스트럭션 안의 코드상의 주소는 바뀌지 않아 논리적 주소로 남아있다.
> - CPU가 메모리 몇 번지에 있는 데이터를 요청하면 그 때 주소 변환을 해서 물리적인 메모리 주소를 찾은 다음 그 내용을 읽어 CPU에 전달

## Memory-Management Unit(MMU, 메모리 관리 유닛)

![](https://i.imgur.com/8xo0Q6Z.png)

#### MMU

- 논리적 주소를 물리적 주소로 매핑해주는 하드웨어 장치

#### MMU scheme(MMU 기법)

- 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(= relocation register)의 값을 더한다.
- limit register(한계 레지스터)
  - 현재 CPU에서 수행 중인 프로세스의 크기 즉, 논리적 주소의 최대값
  - 프로세스가 자신의 주소 공간을 넘어서는 메모리 참조를 하려고 하는지 체크하는 용도로 사용
    ⇒ MMU 방식을 사용하여 주소 변환을 했을 경우 CPU가 요청한 논리적 주소값과 재배치 레지스터 안에 있는 값을 더한 결과가 해당 프로세스의 주소 공간을 벗어나는 경우가 발생할 수 있다. 이것을 방지하기 위해 한계 레지스터를 사용

#### User program

- CPU와 사용자 프로그램은 논리적 주소만을 다룬다.
- 실제 물리적 주소를 볼 수 없으며 알 필요도 없다.

## Dynamic Relocation(MMU scheme)

![](https://i.imgur.com/q5lOFMb.png)

## Hadware Support for Address Translation

![](https://i.imgur.com/hXICzSv.png)

운영 체제 및 사용자 프로세스 간의 메모리 보호를 위해 사용하는 레지스터

- Relocation register(= base register) : 접근할 수 있는 물리적 메모리 주소의 최소값
- Limit register : 논리적 주소의 범위

CPU가 요청한 프로세스의 논리적 주소값이 한계 레지스터 내에 저장된 프로세스의 크기보다 작은지 확인

- 작다면 논리적 주소값에 재배치 레지스터 값을 더해 물리적 주소를 구한 다음 해당 물리적 메모리 위치에 접근 허용
- 크다면 프로세스가 존재하는 물리적 메모리의 바깥 부분 즉, 다른 프로세스의 주소 영역에 접근하려는 시도이므로 트랩(trap)을 발생시켜 프로세스를 강제 종료

## Some Treminologies(메모리 관리와 관련된 용어)

### Dynamic Loading(동적 로딩)

- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불릴 때 메모리에 적재하는 방식
- memory utilization의 향상
- 가끔씩 사용되는 많은 양의 코드의 경우 유용(예: 오류 처리 루틴)
- 운영체제의 특별한 지원없이 프로그램 자체에서 구현 가능
  - 운영체제가 라이브러리를 통해 지원할 수도 있다.

⇒ 메모리 효율성을 높이기 위해 사용하는 기법

##### \*Loading : 메모리로 올리는 것

### Overlays(중첩)

- 메모리에 프로세스의 부분 중 실제 필요한 정보만 올리는 방법
- 프로세스의 크기가 메모리보다 클 때 유용
  - 초창기 컴퓨터 시스템에서 물리적 메모리 크기의 제약으로 하나의 프로세스 조차도 메모리에 한 꺼번에 올릴 수 없을 때
  - 프로세스의 주소 공간을 분할해서 당장 필요한 부분을 메모리에 올려 실행하고 해당 부분에 대한 실행이 끝난 후 나머지 부분을 올려 실행하는 기법
- 운영체제의 지원 없이 프로그래머가 수작업으로 구현
  - Manual Overlay(수작업 중첩)
  - 프로그래밍이 매우 복잡

> #### 동적로딩과 중첩의 차이점
>
> - 중첩 : 프로그램의 크기가 물리적 메모리의 크기에 비해 커서 프로세스를 분할하여 메모리에 올리는 것
>   ⇒ 단일 프로세스만을 메모리에 올려놓는 환경에서 메모리 용량보다 큰 프로세스를 실행하기 위한 용도
> - 동적로딩 : 다중 프로그래밍 환경에서 메모리의 이용률을 향상시키기 위해 프로세스의 주소 공간 중 당장 실행에 필요한 부분을 동적으로 메모리에 올리는 것
>   ⇒ 메모리에 더 많은 프로세스를 동시에 올려놓고 실행하기 위한 용도

### Swapping(스와핑)

#### Swapping

- 프로세스 종료가 아닌 특정한 이유로 실행 중인 프로세스를 일시적으로 메모리에서 backing store로 내려놓는 것

#### Backing store(= swap area)

- 디스크
- 많은 사용자와 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간이어야 한다.
- 어느 정도의 접근 속도가 보장되어야 한다.
- 프로세스가 수행 중인 동안에만 일시적으로 저장하는 공간이므로 저장 기간이 상대적으로 짧다.

#### Swap in / Swap out

![](https://i.imgur.com/bfXXbie.png)

- 일반적으로 중기 스케줄러(swapper)에 의해 스왑 아웃 시킬 프로세스를 선정
- Priority-based CPU scheduling algorithm
  - 우선순위가 낮은 프로세스를 스왑 아웃 시킴
  - 우선순위가 높은 프로세스를 메모리에 올려놓음
- 컴파일 타임 바인딩과 로드 타임 바인딩 방식에서는 스왑 아웃된 프로세스가 다시 스왑 인될 때 원래 존재하던 메모리 위치로 스왑인
- 실행시간 바인딩은 빈 메모리 영역 아무 곳에나 스왑인 가능
- 스와핑에 소요되는 시간(swap time)은 대부분 실제 데이터를 읽고 쓰는 전송시간(transfer time)이 차지
  - 스와핑은 많은 양의 데이터를 메모리에 올리고 내리는 작업이어서 전송시간은 스왑되는 양에 비례한다.

### Dynamic Linking(동적 연결)

- 연결(linking) : 프로그래머가 작성한 소스코드를 컴파일하여 생성된 목적 파일과, 이미 컴파일된 라이브러리 파일들을 묶어 하나의 실행파일을 생성하는 과정

#### Static linking(정적 연결)

- 사용자 프로그램에 라이브러리 코드가 포함되어 실행파일이 생성
- 실행 파일의 크기가 커진다.
- 동일한 라이브러리를 각각 프로세스가 메모리에 올리므로 메모리 낭비(예: printf 함수의 라이브러리 코드)

#### Dynamic Linking

- 연결을 프로그램의 실행 시간(execution time)까지 지연시키는 방법
- 프로그램과 라이브러리가 실행 시점에 연결(link)됨
  - 실행 파일에 라이브러리가 포함되지 않음
  - 프로그램이 실행되면서 라이브러리 함수를 호출할 때가 되어서야 라이브러리에 대한 연결이 이루어짐
- 라이브러리 호출 부분에 라이브러리 루킨의 위치를 찾기 위한 스텁(stub)이라는 작은 코드를 둔다.
- 라이브러리 호출 시 스텁을 통해 해당 라이브러리가 메모리에 이미 존재하는지 확인
  - 메모리에 있다면 그 주소의 메모리 위치에서 직접 참조
  - 메모리에 없다면 디스크에서 동적 라이브러리 파일을 찾아 메모리로 적재한 후 수행
- 운영체제의 도움이 필요
- Shared library : 동적연결을 해주는 라이브러리

⇒ 동적연결은 다수의 프로그램이 공통으로 사용하는 라이브러리를 메머리에 한 번만 적재하므로 메모리 사용의 효율성을 높인다.

## Allocation of Physical Memory(물리적인 메모리 할당 방식)

- 메모리는 일반적으로 두 영역으로 나뉘어 사용
  - 운영체제 상주 영역
    - Interrupt vector와 함께 물리적 메모리의 낮은 주소 영역 사용
    - 운영체제의 커널이 이곳에 위치
  - 사용자 프로세스 영역
    - 물리적 메모리의 높은 주소 영역 사용
    - 여러 사용자 프로세스들이 이곳에 적재되어 실행
- 사용자 프로세스 영역의 할당 방법 : 프로세스를 메모리에 올리는 방식에 따라 구분
  - 연속할당(Contiguous allocation) : 프로세스의 주소 공간을 여러 개로 분할하지 않고 물리적 메모리 한 곳에 연속적으로 적재하는 방식
    - Fixed parition allocation
    - Variable partition allocation
  - 불연속할당(Noncontiguous allocation) : 하나의 프로세스를 메모리의 여러 영역에 분산해 적재하는 방식
    - Paging
    - Segmentation
    - Paged Segmentation

### Contiguous allocation(연속할당)

![](https://i.imgur.com/8GSkjgB.png)

#### 고정분할 방식(Fixed parition allocation)

- 프로그램이 들어갈 사용자 메모리 영역을 미리 분할해 놓은 방식
- 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
- 하나의 분할에는 하나의 프로그램만 적재
- 가변분할 방식에 비해 융통성이 떨어진다.
  - 동시에 메모리에 적재되는 프로그램의 수가 고정됨
  - 수행 가능한 프로그램의 최대 크기 제한
- 외부조각(external fragmentation)
  - 프로그램 크기보다 분할의 크기가 작은 경우
  - 아무 프로그램에도 배정되지 않은 빈 곳인데도 프로그램이 올라갈 수 없는 작은 분할
  - 만약 외부조각보다 작은 크기의 프로그램이 도착한다면 그 프로그램을 외부조각에 적재할 수 있다.
- 내부조각(internal fragmentation)
  - 프로그램 크기보다 분할의 크기가 큰 경우
  - 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
  - 특정 프로그램이 배정되었지만 사용되지 않는 공간
  - 내부조각에 수용할 수 있는 작은 크기의 프로그램이 있어도 공간을 활용할 수 없어 메모리가 낭비된다.

#### 가변분할 방식(Variable partition allocation)

- 메모리에 적재되는 프로그램의 크기에 따라 분할의 크기, 개수가 동적으로 변하는 방식
- 분할의 크기를 프로그램의 크기보다 크게 할당하지 않기 때문에 내부조각은 발생하지 않는다.
- 이미 메모리에 존재하는 프로그램이 종료될 경우 중간에 빈 공간이 발생하고, 이 공간이 이어서 실행될 프로그램의 크기보다 작을 경우 적재되지 못하여 외부조각이 발생할 수 있다.
- 프로그램의 크기를 고려해서 메모리를 할당하고 이를 기술적으로 관리할 수 있는 기법 필요
- Hole
  - 가용 메모리 공간
  - 다양한 크기의 홀들이 메모리 여러 곳에 산발적으로 존재
  - 프로세스가 도착하면 수용 가능한 홀을 할당
  - 운영체제는 할당된 공간과 가용 공간(hole)의 정보를 유지
  - 아래의 그림에서 어두운 영역이 가용 공간을 의미
    ![](https://i.imgur.com/Iet6jaF.png)

#### Dynamic Storage-Allocarion Problem(동적 메모리 할당 문제)

- 가변분할 방식에서 크기가 n인 프로세스를 메모리에 올릴 때 물리적 메모리 내 가용 공간 중 가장 적절한 가용 공간을 찾는 문제
- First-fit(최초적합)
  - 크기가 n이상인 가용 공간 중 가장 먼저 찾아지는 곳에 프로세스 할당
  - 시간적인 측면에서 효율적
- Best-fit(최적적합)
  - 크기가 n이상인 가용 공간 중 가장 작은 가용 공간을 찾아 프로세스 할당
  - 가용 공간들이 크기순으로 정렬되지 않은 경우 모든 가용 공간을 탐색해야하므로 시간적 오버헤드가 발생
  - 다수의 아주 작은 가용 공간들이 생성됨
  - 공간적인 측면에서 효율적
- Worst-fit(최악적합)
  - 가용 공간 중 크기가 가장 큰 곳에 프로세스 할당
  - 최적적합과 같이 모든 가용 공간을 탐색해야 함
  - 상대적으로 아주 큰 가용공간들이 생성됨

⇒ 실제 시스템에서 실험한 결과 최초적합과 최적적합이 최악적합에 비해 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐

#### Compaction(컴팩션)

- 외부 조각 문제를 해결하기 위한 방법
- 물리적 메모리 중에서 프로세스가 사용 중인 메모리 영역을 한쪽으로 몰고 가용 공간들을 다른 한 곳으로 모아 하나의 큰 가용 공간을 만드는 방법
- 비용이 많이 든다.
- 최소한의 메모리 이동으로 컴팩션 하는 방법이 필요
  ⇒ 이론적으로도 매우 복잡한 문제
- 컴팩션은 프로그램 실행 중에 프로세스의 주소가 동적으로 재배치할 수 있는 경우(실시간 바인딩 방식을 지원하는 환경)에만 수행될 수 있다.

### Noncontiguous allocation(불연속할당)

#### Paging

- 프로세스의 주소 공간을 동일한 크기의 페이지 단위로 나누어 물리적 메모리의 서로 다른 위치에 페이지들을 저장하는 방식
- 페이지 단위로 일부는 물리적 메모리에, 일부는 백킹스토어에 저장하는 것이 가능
- 물리적 메모리도 사용자 프로그램이 들어갈 수 있는 공간도 페이지와 동일한 크기의 공간(frame)으로 나눈다.
  ⇒ 페이지의 크기와 프레임의 크기가 동일하기 때문에 비어있는 프레임이 있으면 어떤 위치든 사용할 수 있어 동적 메모리 할당 문제가 발생하지 않는다.
- 하나의 프로세스가 페이지 단위로 물리적 메모리에 올리는 위치가 다르므로 논리적 주소를 물리적 주소로 변환하는 작업이 페이지 단위로 이루어진다.
  ⇒ 모든 프로세스가 각각의 주소 변환을 위한 페이지 테이블(page table)을 가지고, 이 테이블을 사용하여 논리적 주소를 물리적 주소로 변환
- 외부조각은 발생하지 않으나 내부조각은 발생 가능성이 있다.
  ⇒ 프로그램의 크기가 항상 페이지 크기의 배수가 된다는 보장이 없기 때문에 프로세스의 주소 공간 중 제일 마지막에 위치한 페이지에서는 내부조각이 발생할 수 있다.

#### Segmentation

- 프로세스의 주소 공간을 의미있는 단위의 세그먼트로 나누는 기법
- 세그먼트(segment) : 주소 공간을 기능 또는 의미 단위로 나눈 것
- 프로세스의 주소 공간은 크게 코드, 데이터, 스택으로 구성되어 있고 각 기능 단위로 세그먼트를 정의할 수 있다.
- 프로그램을 구성하는 함수를 세그먼트로 정의할 수 있다.
- 세그먼트는 특정 크기 단위로 나눈 것이 아니므로 크기가 균일하지 않다.
  ⇒ 따라서 동적 메모리 할당 문제가 발생한다.

# [Chapter 8. 메모리 관리 Part-2](https://core.ewha.ac.kr/publicview/C0101020140429132440045277?vmode=f)

## Paging Example

![](https://i.imgur.com/aQC4gaz.png)

- 프로그램을 구성하는 논리적인 메모리를 동일한 크기의 페이지로 잘라 물리적인 주소 빈 공간에 적재
- 페이지 \*테이블(page table)
  - 특정 프로세스의 몇 번째 페이지가 물리적 메모리의 몇 번째 프레임에 들어있는지 페이지별 주소 변환 정보를 가지고 있다.
    ⇒ 페이지 단위의 논리적 주소를 물리적 주소로 변환하기 위해 사용
  - 프로세스가 가지는 페이지의 수만큼 주소 변환 엔트리가 존재
- 장점 : 동적 메모리 할당 문제가 발생하지 않는다.

##### \*테이블(배열) : 인덱스를 통해 접근할 수 있는 자료구조

### 페이징의 주소 변환 기법(Address Translation Architecture)

![](https://i.imgur.com/pDhJ9Wu.png)

- CPU가 사용하는 논리적 주소를 페이지 번호(`p`)와 페이지 오프셋(`d`)으로 나누어 주소 변환에 사용
- 특정 프로세스의 `p`번째 페이지가 위치한 물리적 메모리의 시작 위치를 알고 싶다면 해당 프로세스 페이지 테이블에서 `p`번째 항목을 찾아가면 프레임 번호를 알 수 있다.
- 페이지 번호 : 페이지 테이블에 접근 시 인덱스(index)로 사용되고, 해당 인텍스의 항목(entry)에는 그 페이지의 물리적 메모리 상이 기준 주소, 즉 시작 위치가 저장된다.
- 페이지 오프셋 : 상대적 위치는 동일하기 때문에 주소 변환에 영향이 없다.

### 페이지 테이블의 구현

- 페이징 기법에서 주소 변환을 하기 위한 자료구조
- 물리적 메모리에 위치
- 실행 중인 프로세스의 페이지 테이블에 접근하기 위해 운영체제가 사용하는 2개의 레지스터
  - **Page-table base register(PTBR)** : 페이지 테이블 기준 레지스터, 페이지 테이블의 시작 위치를 가리킴
  - **Page-table length register(PTLR)** : 페이지 테이블 길이 레지스터, 페이지 테이블의 크기를 보관
- 메모리 접근 연산은 주소 변환을 위해 두 번의 메모리 접근이 필요(비용이 많이 든다.)
  - 페이지 테이블에 접근하는 것
  - 변환된 주소에서 실제 데이터에 접근하는 것
- 페이지 테이블 접근 오버헤드를 줄이고 메모리 접근 속도롤 향상시키기 위해 **associative register** 또는 **translation look-aside buffer(TLB)** 사용
  - translation look-aside buffer(TLB) : 고속의 주소 변환용 하드웨어 캐시, 일종의 캐시 메모리

### Paging Hardware with TLB

![](https://i.imgur.com/3UbXykD.png)

- 페이지 테이블에서 빈번하게 참조되는 일부 엔트리를 캐싱
- 메인 메모리보다 접근 속도가 빠른 하드웨어로 구성
- CPU가 논리적인 주로를 주게되면 메모리 상의 페이지 테이블에 접근하기 전에 TLB를 먼저 검색
- TLB에 있는 정보에 의해 주소 변환을 할 수 있는지 확인
- 요청되는 페이지에 대한 주소 변환 정보가 TLB에 존재한다면 바로 물리적 메모리의 프레임 번호를 얻을 수 있다.
  ⇒ 메모리에 한 번의 접근으로 해결
- TLB에 주소 변환 정보가 없을 경우 페이지 테이블을 통해 일반적인 주소 변환을 하고 실제 데이터에 접근하므로 메모리에 2번 접근하게 된다.
- TLB를 사용한 주소 변환의 경우 프로세스의 모든 페이지에 대한 주소 변환 정보를 TLB가 가지고 있지 않기 때문에 페이지 번호와 이에 대응하는 프레임 번호가 쌍으로 저장되어야 한다.
- 특정 페이지에 대한 주소 변환 정보가 TLB에 있는지 확인하기 위해 TLB의 모든 항목(entry)을 탐색해야 하고 이에 따른 오버헤드가 발생
  ⇒ 오버헤드를 줄이기 위해 TLB의 구현에는 일반적으로 병렬탐색(parellel search)이 가능한 연관 레지스터(associative register)를 사용
- 병렬탐색(parellel search) : TLB 내의 모든 항목을 동시에 탐색할 수 있는 기능

### Associative register(연관 레지스터)

- Associative register(TLB) : 병렬탐색이 가능
  - TLB에는 페이지 테이블 중 일부만 존재
- Associative translation
  - 페이지 테이블 중 일부가 연관 레지스터에 보관
  - 만약 해당 페이지 번호가 연관 레지스터에 있을 경우 곧바로 프레임 번호를 얻음
  - 그렇지 않으면 물리적 메모리에 있는 페이지 테이블로부터 프레임 번호를 얻음
  - TLB는 문맥 교환이 일어날 때 이전 프로세스의 주소 변환 정보를 담고있던 TLB 내용을 모두 지운다(flush)

### Effective Access Time(EAT, 메모리 접근 시간)

- 연관 레지스터에 접근하는 시간은 𝛆, 메모리에 접근하는 시간은 1이라고 할 때 𝛆는 1보다 충분히 작은 값이다.
- 요청된 페이지에 대한 주소 변환 정보가 연관 레지스터에 존재할 확률(Hit ratio) = 𝛂
- `EAT = (1 + 𝛆)𝛂 + (2 + 𝛆)(1 - 𝛂) = 2 + 𝛆 - 𝛂`
  - `(1 + 𝛆)𝛂` : 페이지의 주소 변환 정보가 TLB에 존재하는 경우
  - `(2 + 𝛆)(1 - 𝛂)` : 페이지의 주소 변환 정보가 TLB에 존재하지 않는 경우

### Two-Level Page Table(2단계 페이지 테이블)

![](https://i.imgur.com/snK4ZMy.png)

- 현대의 컴퓨터는 주소 공간이 매우 큰 프로그램을 지원
  - 32비트 주소 체계를 사용하는 컴퓨터에서는 2<sup>32</sup>byte(=4GB)의 주소 공간을 갖는 프로그램을 지원할 수 있다.
  - 페이지의 크기가 4KB라면, 1M개의 페이지 개수가 얻어지고 1M개의 페이지 테이블 엔트리가 필요하게 된다.
    - 각 페이지 엔트리가 4byte일 때 프로세스 당 4MB의 페이지 테이블 필요
      > 컴퓨터 시스템에서 2<sup>10</sup> = K(Kilo), 2<sup>20</sup> = M(Mega), 2<sup>30</sup> = G(Giga)로 정의
  - 각 페이지마다 페이지 테이블을 가지고 있는데 모든 페이지 테이블을 메모리에 넣으면 실제 사용 가능한 메모리 공간이 크게 줄어든다.
    ⇒ 대부분의 프로그램은 4GB의 주소 공간 중 극히 일부만 사용하므로 페이지 테이블 공간이 심하게 낭비된다.
- 페이지 테이블에 사용되는 메모리 공간의 낭비를 줄이기 위해 **2단계 페이징 기법**을 사용
- 2단계 페이징 기법에서 외부 페이지 테이블(outer page table)과 내부 페이지 테이블(inner page table)의 두 단계에 걸친 페이지 테이블 사용
- 사용되지 않는 주소 공간에 대해서는 외부 페이지 테이블의 항목을 null로 설정하여, 여기에 대응하는 내부 페이지 테이블을 생성하지 않는 것

⇒ 페이지 테이블이 차지하는 메모리 공간을 줄여 공간적인 이득을 볼 수 있지만, 주소 변환을 위해 접근해야 하는 페이지 테이블의 수가 증가하므로 시간적인 손해가 뒤따른다.

#### 2단계 페이징의 주소 변환 기법

![](https://i.imgur.com/TLT8gRa.png)

- 2단계 페이징 기법에서는 프로세스의 논리적 주소를 두 종류의 페이지 번호(P<sub>1</sub>, P<sub>2</sub>)와 페이지 오프셋(d)으로 구분
  - P<sub>1</sub> : 외부 페이지 테이블의 인덱스
  - P<sub>2</sub> : 내부 페이지 테이블의 인덱스
- 외부 페이지 테이블로부터 P<sub>1</sub>만큼 떨어진 위치에서 내부 페이지 테이블의 주소를 얻는다.
- 내부 페이지 테이블로부터 P<sub>2</sub>만큼 떨어진 위치에서 요청된 페이지가 존재하는 프레임의 위치를 얻는다.
- 해당 프레임으로부터 d만큼 떨어진 곳에서 원하는 정보에 접근한다.
- 내부 페이지 테이블의 크기는 페이지의 크기와 동일하다.
  - 내부 페이지 테이블 자체를 페이지화 하여 하나의 프레임에 보관

#### Two-Level Paging Example

32비트 주소 체계에서 페이지의 크기가 4KB(= 2<sup>12</sup>byte)라고 가정

- 논리적 주소의 구성
  - 20bit의 페이지 번호
  - 12bit의 페이지 오프셋
- 페이지 테이블 자체가 페이지로 구성되었기 대문에 페이지 번호는 다음과 같이 나뉜다.(각 페이지 테이블 엔트리가 4byte)
  - 10bit의 페이지 번호
  - 10bit의 페이지 오프셋

![](https://i.imgur.com/mvPG5BJ.png)

# [Chapter 8. 메모리 관리 Part-3](https://core.ewha.ac.kr/publicview/C0101020140502151452123728?vmode=f)

## Multilevel Paging and Performance

- 주소 공간이 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 논리적 주소의 물리적 주소 변환에 더 많은 메모리 접근 필요
- TLB를 통해 메모리 접근 시간을 줄일 수 있음
- 4단계 페이지 테이블을 사용하는 경우 EAT
  - 메모리 접근시간 : 100ns
  - TLB 접근 시간 : 20ns
  - TLB hit ratio : 98%
    ⇒ EAT(effective memoty access time) : 128ns

> EAT
> = (메모리 접근 시간 + TLB 접근 시간) × hit ratio + ((메모리 접근 횟수 × 메모리 접근 시간) + TLB 접근 시간)(1 - hit ratio)
> = 0.98 × (100ns + 20ns) + 0.02 × ((5 × 100ns) + 20ns)
> = 0.98 × 120ns + 0.02 × 520ns
> = 128ns

⇒ TLB를 통해 대부분의 주소 변환을 한다면 다단계 페이지 테이블의 시간 오버헤드가 크지 않다.

## Memory Protection(메모리 보호)

페이지 테이블의 각 엔트리에는 주소 변환 정보와 함께 메모리 보호를 위한 Protection bit와 Valid-invalid bit를 두고 있다.

### Protection bit(보호비트)

- 각 페이지에 대한 (연산에 대한) 접근 권한을 담고 있다.
- 각 페이지에 대해 읽기-쓰기/읽기전용의 접근 권한을 설정하는데에 사용

### Valid(v) / Invalid(i) Bit in a Page Table(유효-무효 비트)

![](https://i.imgur.com/8fyjGgJ.png)

- 프로그램 주소 공간에는 사용되지 않은 영역이 존재하는데 이 사용되지 않는 영역에 대해서도 엔트리가 생성되야 한다.
  - 테이블이라는 자료구조 특성상 위에서부터 아래로 인덱스를 통해 접근하기
  - 따라서 모든 엔트리가 생성되고, 사용 여부를 유효-무효 비트로 표시한다.
- 해당 페이지의 내용이 유효한지에 대한 내용을 담고 있다.
- 유효(v) : 해당 메모리 프레임에 그 프로세스를 구성하는 유효한 내용이 존재함을 의미(접근이 허용됨)
- 무효(i) : 해당 주소의 프레임에 유효한 내용이 없음을 뜻함(접근 불허)
  - 프로세스가 그 주소 부분을 사용하지 않는 경우
  - 해당 페이지가 물리적 메모리에 올라와있지 않고 백킹스토어(swap area)에 있는 경우
- invalid 엔트리의 프레임 번호에 표기된 0이 0번의 프레임 번호를 의미하는 것인지 아닌지를 유효-무효 비트를 통해 구분한다.

## Inverted Page Table(역페이지 테이블)

#### 페이지 테이블로 인한 메모리 공간의 낭비가 심한 이유?

- 모든 프로세스 별로 그 논리적 주소에 대응하는 모든 페이지에 대해 페이지 테이블 엔트리가 존재
- 대응하는 페이지가 메모리에 있든 아니든 페이지 테이블에는 엔트리로 존재

⇒ 이 문제를 해결하기 위해 역페이지 테이블 기법 사용

### Inverted Page Table

![](https://i.imgur.com/1PpwhdO.png)

- 각 프로세스마다 페이지 테이블을 두지 않고, 시스템 전체(system-wide)에 페이지 테이블을 하나만 두는 방법
- 페이지 테이블 엔트리가 논리적 주소의 프로세스 갯수만큼 존재하는 것이 아니라 물리적 주소의 프레임 수만큼 존재
- 각 페이지 테이블의 엔트리는 각각의 물리적 메모리의 페이지 프레임이 담고 있는 내용 표시
  - 프로세스 번호(`pid`)
  - 그 프로세스 내의 논리적 페이지 번호(`p`)

#### 역페이지 테이블의 단점

- 주소 변환이라는 것은 논리적 주소를 물리적 주소로 변환하는 것인데 역페이지 테이블은 물리적 주소를 통해 논리적 주소를 얻는 것이므로 목적에 맞지 않다.
- 역페이지 테이블의 주소 변환 요청이 들어오면, 그 주소를 담은 페이지가 물리적 메모리에 존재를 확인하려면 페이지 테이블 전체를 탐색해야 한다.
- 페이지 테이블의 장점이 인덱스를 통해 위에서 p번째 떨어진 것을 바로 검색해서 찾는 것인데 역페이지 테이블은 이 장점이 없어짐

⇒ 시간적인 오버헤드가 존재

#### 단점 해결 방안

- 별도의 하드웨어인 연관 레지스터를 이용하여 테이블 전체 항목에 대한 병렬 탐색을 가능하게 한다.
  ⇒ 이를 통해 시간적인 오버헤드를 줄일 수 있다.

## Shared Page

동일한 프로그램을 여러 개의 프로세스로 실행하고 있을 경우 각 프로세스의 code 부분은 모두 동일할 것이다. 이 동일한 부분을 따로 메모리에 적재하지 않고 공유하는 것

![](https://i.imgur.com/dyIj8VS.png)

#### Shared code(공유 코드)

- 여러 프로세스에 의해 공통으로 사용될 수 있도록 작성된 코드
- Re-entrant code(재진입 가능 코드) 또는 Pure code(순수 코드)라고도 불린다.
- 공유 코드의 조건(제약점)
  - 읽기전용(read-only)이어야 한다.
  - 모든 프로세스의 논리적 주소 공간에서 동일한 위치에 있어야 한다.
    ⇒ Symbolic Address를 컴파일하면 숫자로된 프로그램의 독자적인 주소인 논리적 주소가 생성되고, CPU가 인스트럭션을 실행하려면 논리적 주소를 읽어서 연산을 합니다. 주소 바인딩에 의해 메모리의 시작 위치가 바뀌어도 인스트럭션 내의 코드에는 논리적 주소가 남아있으므로
- 예 : test editors, compilers, window systems

#### Shared Page(공유 페이지)

- 공유 코드를 담고 있는 페이지
- 여러 프로세스에 의해 공유되는 페이지이므로 물리적 메모리에 하나만 적재되어 메모리의 효율성을 높인다.
  ⇒ 특정 페이지를 공유하는 모든 프로세스의 주소 공간에서 동일한 페이지 번호를 가져야 한다.

#### Private code and data(Private Page, 사유 페이지)

- 프로세스들이 공유하지 않고 프로세스 별로 독자적으로 사용하는 페이지
- 각 프로세스들은 독자적으로 메모리에 올림
- 사유 페이지는 해당 프로세스의 논리적 주소 공간 중 어떠한 위치에 있어도 무방하다.

## Segmentaion

- 프로그램의 의미 단위인 여러 개의 세그먼트(segment)로 구성
  - 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
  - 크게는 프로그램 전체를 하나의 세그먼트로 정의
  - 일반적으로 code, data, stack을 하나의 세그먼트로 정의
- 세그먼트는 다음과 같은 논리적인 단위(Logical unit)들이다.
  - main()
  - function
  - global variables
  - stack
  - symbol table, arrays

### Segmentaion Architecture

- 논리적 주소가 **<segment-number, offset>** 으로 구성
  - segment-number : 해당 논리적 주소가 프로세스 주소 공간 내에서 몇 번째 세그먼트에 속하는지 나타냄
  - offset : 세그먼트 내에서 얼마만큼 떨어져 있는지에 대한 정보

#### Segment table

- 각 세그먼트별 물리적 주소가 다르므로 주소 변환을 위해 세그먼트 테이블 사용
- 각 세그먼트 테이블 엔트리는 기준점과 한계점을 가진다.
  - 기준점(base) : 물리적 메모리에서 그 세그먼트의 시작 위치
  - 한계점(limit) : 세그먼트의 길이
    ⇒ 페이징 기법과 달리 각 세그먼트의 길이가 균일하지 않으므로 세그먼트의 위치 정보과 길이의 정보를 함께 보관
- 세그먼트 테이블의 엔트리 수는 프로그램에서 사용하는 세그먼트의 수와 동일하다.
- 세그먼트는 물리적 메모리의 어디에 위치하는가? 세그먼테이션 주소 변환

![](https://i.imgur.com/upjZiRD.png)

#### 세그먼테이션의 주소 변환 기법

아래의 두 가지 레지스터를 사용하여 주소 변환

- Segment-table base register(STBR, 세그먼트 테이블 기준 레지스터)
  - 물리적 메모리에서 세그먼트 테이블의 위치
- Segment-table length register(STLR, 세그먼트 테이블 길이 레지스터)
  - 프로그램이 사용하는 세그먼트의 수
- 세그먼테이션 기법에서 주소 바인딩하기 전 확인하는 두 가지
  - 요청한 세그먼트 번호가 STLR에 저장된 값보다 작은 값인가?
    - STLR에 저장된 값보다 큰 값을 요청한 것은 존재하지 않는 세그먼트에 대한 접근 시도를 의미
    - 트랩을 발생시켜 접근을 차단
  - 논리적 주소의 오프셋 값이 그 세그먼트의 길이보다 작은 값인가?
    - 오프셋 값이 더 큰 경우에도 유효한 접근이 아니므로 트랩을 발생시켜 접근을 차단
- 두 가지 사항을 모두 만족한 경우 유효한 메모리 접근 요청으로 판단하여, 세그먼트의 시작 위치(base)에 오프셋을 더하여 주소 변환

#### 세그먼테이션의 장점

- Protection
  - 각 세그먼트 별로 protection bit가 존재
  - 각 엔트리
    - Valid bit = 0 ⇒ illegal segment
    - Read/Write/Excution 권한 bit
- Sharing
  - Shared segment
  - Same segment number
    ![](https://i.imgur.com/c9laJO6.png)

⇒ 주소 공간의 일부를 공유하거나 특정 주소 공간의 접근 권한을 제어하고자 할 때 이는 어떤 의미 단위로 수행되므로 크기 단위 단위로 수행하는 페이징 기법보다 효과적이다.

#### 세그먼테이션의 단점

- Allocation
  - 세그먼트는 의미 단위로 나누기 때문에 그 길이가 균일하지 않다.
  - 메모리 관리의 가변분할 방식과 동일한 문제가 발생 ⇒ 외부 조각 발생
  - 세그먼트를 어느 가용 공간에 할당할 것인지 결정하는 문제가 발생
    - First fit(최초적합) 또는 Best fit(최적적합) 방식을 사용

#### 페이징 기법과 세그먼테이션 기법의 비교

- 테이블을 위한 메모리 낭비는 페이징 기법이 더 심하다.
  - 일반적인 페이지 단위가 4KB로 작아 페이지의 수가 매우 많은 반면 세그먼트의 수는 훨씬 적다.

# [Chapter 8. 메모리 관리 Part-4](https://core.ewha.ac.kr/publicview/C0101020140509142939477563?vmode=f)

## Segmentation with Paging(Paged Segmentation)

- 먼저 프로세스를 의미 단위인 세그먼트로 나눈다.
- 그리고 그 세그먼트를 페이지 단위로 나눈다
  - 세그먼트는 동일한 크기 페이지들의 집합으로 구성되고, 하나의 세그먼트 크기를 세이지 크기의 배수가 되도록 한다.
    ⇒ 세그먼트 단위에서 프로세스 간의 공유나 권한 접근 보호가 이루어져 페이징 기법에서의 약점을 해결한다.
- 물리적 메모리에 적재하는 단위는 페이지 단위로 한다.
  ⇒ 물리적 메모리에 적재하는 단위를 페이지로 하면 세그먼테이션 기법에서 발생하는 외부 조각의 문제점이 해결된다.

### 페이지드 세그먼테이션 기법에서 주소 변환

![](https://i.imgur.com/yhYWfO9.png)

- 주소 변환을 위해 외부의 세그먼트 테이블과 내부의 페이지 테이블, 두 단계의 테이블을 이용
  - 하나의 세그먼트가 여러 개의 페이지로 구성되므로 각 세그먼트마다 페이지 테이블을 가진다.

**`<세그먼트 번호(s), 오프셋(d)>`으로 구성된 논리적 주소**

- 세그먼트 번호를 통해 세그먼트 테이블의 해당 위치에 접근
- 각 세그먼트 엔트리는 `<세그먼트 길이(segment length), 세그먼트의 페이지 테이블 시작주소(page-table base)>`로 구성
- 이때 세그먼트 길이를 넘어서는 메모리 접근 시도인지 확인
  - 세그먼트 길이(`segment length`)와 논리적 주소 중 하위 비트인 오프셋(`d`)을 비교
  - 오프셋이 더 크면 유효하지 않은 접근이므로 트랩 발생
- 유효한 접근인 경우 오프셋을 `<세그먼트 내에서의 페이지 번호(p), 페이지의 오프셋(d')>`로 나뉨
- 세그먼트 엔트리의 페이지 테이블 시작주소(`page-table base`)를 얻고, 페이지 테이블 엔트리에서 물리적 메모리에서 페이지 프레임 위치(`p`)를 얻게된다.
- 이 위치에서 페이지 오프셋(`d'`) 만큼 떨어진 곳이 물리적 메모리 주소가 된다.

> 이 챕터에서 주요한 내용은 주소 변환이다.
> 프로세스가 논리적인 주소를 가지고 있고, CPU가 이 논리적인 주소를 물리적인 메모리 주소로 변환하여 참조한다.
>
> #### 주소 변환에서 운영체제의 역할은?
>
> - 주소변환을 위해 운영체제가 하는 일은 없다.
> - 전부 하드웨어가 처리(MMU)
> - 운영체제가 필요한 경우는 메모리가 아닌 I/O 장치에 접근할 때이다.
