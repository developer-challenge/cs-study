# JDC-230202

# [Chapter 10. 파일 시스템](https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f)

## File and File System

#### File

- "A named collection of related information" : 관련 정보를 고유한 이름을 가지고 접근하거나 구분할 수 있다.
- 일반적으로 비휘발성의 보조기억장치에 저장
- 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌(device special file)
- 파일에서 정의되는 연산(operation)
  - create, read, write, delete, reposition, open, close 등
  - reposition(lseek) : 파일 내에서 특정 위치부터 읽거나 쓰고 싶을 경우 현재 접근하고 있는 파일의 위치는 수정해주는 연산자
  - open : 파일을 디스크에서 메모리로 파일의 메타데이터를 올려주는 연산자

#### File attribute(File metadata)

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 파일에 대한 전반적인 정보
  - 파일 이름, 유형, 저장된 위치, 파일 사이즈
  - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

#### File system

- 운영체제에서 파일을 관리하는 소프트웨어 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
- 파일의 저장 방법 결정
- 파일 보호 등

## Directory and Logical Disk

#### Directory

- 디렉토리 내에 있는 파일 이름 및 파일의 메타데이터들을 보관하는 일종의 특별한 파일
  - 파일의 메타데이터 전부를 디렉토리에 저장하기도 하고 일부는 디렉토리에, 다른 일부는 다른 곳에 저장하기도 한다.
- 디렉토리에서 정의되는 연산
  - 디렉토리 내 파일 목록, 파일 검색, 파일 생성, 파일 삭제, 파일의 이름 변경
  - traverse the file system : 파일 시스템 전체를 탐색

#### Partition(= Logical Disk)

- 디스크에는 논리적인 디스크와 물리적인 디스크가 있는데, 운영체제가 보는 디스크는 물리적인 디스크를 의미
- 하나의 (물리적) 디스크 안에 여러 파티션을 두는게 일반적
  - 하나의 디스크를 여러 파티션으로 나누면 각각이 논리적인 디스크가 된다.
- 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
  - 경우에 따라서 여러 개의 물리적인 디스크를 하나로 합쳐서 논리적인 디스크를 구성하기도 한다.
- (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있다.
  - 디스크의 용도를 크게 file system의 용도, swap area 용도로 나눌 수 있다.

## `open()` 시스템 콜

![](https://i.imgur.com/jwMVeHm.png)

- 파일의 메타데이터를 메모리에 올려놓는 작업

### `open()`의 과정

![](https://i.imgur.com/6kGKik6.png)

`open("/a/b")`
디스크로부터 파일 b의 메타데이터를 메모리에 올려놓는 과정

- 먼저, 디스크에 있던 root의 메타데이터를 메모리에 올린다.(Open file table)
  - 운영체제는 root의 메타데이터를 알고 있다.
- 메타데이터에는 파일의 위치 정보가 있으므로 root content의 위치를 찾아간다.
  - root content는 root 디렉토리 내의 파일들의 메타데이터가 존재
  - root content > 파일 a의 메타데이터에 접근
- 파일 a의 메타데이터를 메모리에 올린다.(Open file table)
- a content의 위치를 찾아가고, a content에 존재하는 파일 b의 메타데이터에 접근
- 파일 b의 메타데이터를 메모리에 올린다.(Open file table)
- `open()` 시스템 콜이 종료되면서 반환값을 프로세스에 전달한다.
  - 반환값 : Open file table내의 파일 b의 메타데이터가 위치하는 포인터 값(file descriptor)
  - 각 프로세스는 메타데이터의 포인트가 저장되어 있는 일종의 배열인 file descriptor table을 가진다.
  - 파일 b의 file descriptor는 file descriptor table의 인덱스 값이다.
    ⇒ 이 값을 통해 read, write 요청을 할 수 있다.
- `read()` 시스템 콜이 발생하면,
  - file descriptor를 통해 Open file table 내 파일 b의 메타데이터를 찾아간다.
  - b의 메타데이터를 통해 b content를 메모리로 읽어온다.
  - 이때 메모리로 읽어온 내용은 사용자 프로그램에 직접 전달하는 것이 아니라 운영체제가 자신의 메모리 공간 일부인 버퍼 캐시에 먼저 읽어온 후 복제하여 전달한다.
- 만약 동시에 다른 프로세스들이 동일한 파일의 동일한 내용을 요청한다면
  - 버퍼 캐시에 요청한 내용이 있는 없든, 시스템 콜에 의해 CPU 제어권이 운영체제에게 넘어간다.
  - 버퍼 캐시 내용을 확인한 후, 버퍼 캐시 내에 요청한 내용이 없다면 위의 과정을 수행하
  - 버퍼 캐시 내에 요청한 내용이 있다면 버퍼 캐시에서 사용자 프로세스로 내용을 전달한다.

⇒ 파일 시스템에서는 모든 정보를 운영체제가 알고 있으므로 LRU, LFU 알고리즘을 사용할 수 있다.

## File Protection

각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?

### Access Control 방법

#### Access Control Matrix

![](https://i.imgur.com/yGe5bud.png)

위 행렬의 모든 칸을 만들면 공간 낭비가 되므로 주체를 누구로 하느냐에 따라 두 가지의 linked-list 방식을 이용한다.

- Access Control List(ACL): 파일 별로 누구에게 어떤 접근 권한이 있는지 표시
- Capability: 사용자 별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시

⇒ 하지만 이 방법도 부가적인 오버헤드가 크다.

#### Grouping

- 일반적으로 사용하는 방법
- 전체 사용자를 owner, group, public의 세 그룹으로 구분
- 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시
  ⇒ 하나의 파일에 대해 접근 권한을 나타내기 위해 총 9비트만 사용
- UNIX에서 채택하는 방식
  ![](https://i.imgur.com/FtaPkhm.png)

#### Password

- 파일마다 패스워드를 두는 방법
  - 디렉토리 파일에 패스워드를 두는 방법도 가능
- 모든 접근 권한에 대해 하나의 패스워드 : all-or-nothing
- 접근 권한 별 패스워드에 대한 암기 문제, 관리 문제가 발생함

## File System의 Mounting

![](https://i.imgur.com/aMDYs0H.png)

- 하나의 물리적인 디스크를 파티션을 통해 여러 개의 논리적인 디스크로 나눌 수 있고, 각각의 논리적인 디스크에는 파일 시스템을 설치할 수 있다.
- 루트 파일 시스템(root file system) 내에 모든 파일을 접근이 가능하다.

#### 만약 다른 논리적 디스크 내의 파일 시스템에 접근하려면?

![](https://i.imgur.com/4wvgzLb.png)

- 루트 파일 시스템 특정 디렉토리 이름에 다른 디스크의 파일 시스템 이름을 Mounting하는 방식을 통해 다른 디스크의 루트 디렉토리에 접근할 수 있는 것

## Access Methods

시스템이 제공하는 파일 정보의 접근 방식

#### 순차 접근(Sequential Access)

- 가장 단순한 방법으로 파일의 정보가 레코드 순서대로 처리
  ⇒ 카세트테이프를 사용하는 방식과 동일하게 접근
- 현재 위치에서 읽거나 쓰면 offset이 자동으로 증가하고, 뒤로 돌아가기 위해선 되감기가 필요하다.

#### 직접 접근(Direct Access, Random Access)

- 파일의 레코드를 임의의 순서로 접근 가능
  ⇒ LP 판을 사용하는 방식과 동일
- 읽기나 쓰기의 순서에 제약이 없으며 현재 위치를 유지할 수 있다면 이를 통해 순차 접근 기능도 구현할 수 있다.

> #### A, B, C 라는 내용이 저장되어 있을 때, A를 보고 C를 보고싶다면?
>
> - 순차 접근 : 순차적으로 B에 반드시 접근을 해야지만 C에 접근할 수 있다.
> - 직접 접근 : B를 건너뛰고 바로 C에 접근할 수 있다.
>   ⇒ 하지만 직접 접근이 가능한 매체라도 데이터를 어떻게 관리하느냐에 따라서 순차 접근만 가능한 경우가 있다.

# [Chapter 11. 파일 시스템 구현 Part-1](https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f)

## Allocation of File Data in Disk

- 파일 데이터를 디스크에 할당하는 방법으로 다음 세 가지 방식이 존재
  - Contiguous Allocation
  - Linked Allocation
  - Indexed Allocation
- 디스크에 파일을 저장할 때 임의의 크기의 파일을 동일한 크기의 섹터(블럭) 단위로 나누어 저장
  ⇒ 파일 시스템이나 디스크 외부에서 볼 때는 각각의 동일한 크기의 저장 단위를 "논리적인 블럭"이라고 부른다.

### Contiguous Allocation(연속 할당)

![](https://i.imgur.com/iVCvsys.png)

- 하나의 파일이 디스크 상에 연속해서 저장되는 방법
  - 여러 개의 블록으로 구성된 파일을 디스크에 저장할 때 각 블럭들이 연속된 번호를 부여 받아 저장된다.
- 디렉토리는 디렉토리 내 파일들의 메타데이터를 담고 있으므로, 각 파일의 시작 위치와 파일의 길이에 대한 정보가 저장되어 있다.

#### 단점

- 외부 단편화 발생
  ⇒ 각 파일들의 길이가 균일하지 않아 비어있는 블럭들의 크기가 균일하지 않은 hole이 생성된다.
- 파일의 수정을 통해 크기가 변경될 수 있으나, 파일이 크기를 키우는데 있어서 제약이 있다.
  - 파일의 커질 가능성을 고려해서 미리 큰 공간을 할당한다면 내부 단편화가 발생할 수 있다.

#### 장점

- 빠른 I/O 가능
  - 대부분의 접근 시간은 디스크 헤더가 이동하는 시간
  - 한 번의 seek/rotation으로 많은 바이트(데이터)를 한 번에 transfer 가능
    ⇒ 모두 연속해서 붙어 있으므로 추가적인 탐색 비용이 소요되지 않는다.
  - Realtime file용
  - 이미 실행 중인 프로세스의 swapping 용
- 직접 접근(Direct access) 가능

### Linked Allocation(연결 할당)

![](https://i.imgur.com/vj5L89S.png)

- 연속적으로 할당하지 않고 빈 위치면 자유롭게 할당
- 디렉토리는 파일의 시작위치와 끝나는 위치에 대한 정보만 가지고 있다.
- 파일의 시작 위치에 있는 블럭을 찾아가서 파일의 내용이 더 있다면, 다음 읽어야 할 위치를 linked list처럼 포인터로 가리켜 다음 블럭의 정보를 찾아간다.

#### 장점

- 외부 단편화 발생하지 않음

#### 단점

- 직접 접근이 불가능하고, 순차 접근만 가능
- Reliability 문제
  - 한 sector가 고장나(bad sector) 포인터가 유실되면 많은 부분을 잃게 되는 신뢰성 문제도 있다.
- 포인터를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨린다.

⇒ 이러한 단점을 보완하기 위해 FAT(File-allocation table)이라는 파일 시스템을 사용

- 포인터를 별도의 위치에 보관하여 신뢰성 문제와 공간 효율성 문제를 해결
  - 보통 512 bytes/sector, 4byte/pointer 단위인데, 포인터가 차지하는 공간 때문에 두 섹터로 나뉘어 저장되는 경우 발생

### Indexed Allocation(인덱스 할당)

![](https://i.imgur.com/QhP5GSm.png)

- 디렉토리에 각 파일의 인덱스 블록의 위치 정보를 저장
  - 인덱스 블록 : 파일이 저장된 위치들이 나열되어있다.
- 직접 접근을 위해 특정 파일의 인덱스 블록 위치를 찾아가고, 인덱스 블록에서 원하는 블록의 위치를 찾아 접근

#### 장점

- 외부 단편화 발생하지 않음
- 직접 접근 가능

#### 단점

- 파일이 작은 경우, 포인터가 차지하는 공간으로 인해 두 블록으로 나누어져 공간 낭비
  - 실제로 많은 파일들이 작다.
- 파일이 너무 큰 경우, 하나의 인덱스 블록으로 모든 블록의 위치를 저장하기 부족
  - 해결 방안 : linked scheme, multi-level index
  - linked scheme : 하나의 인덱스 블록에 모든 블록을 저장할 수 없는 경우, 마지막 인덱스에 다른 인덱스 블록의 위치를 저장하여 해결
  - multi-level index : 하나의 인덱스 블럭이 직접 파일의 위치를 가리키는 것이 아니라 또 다른 인덱스를 가르킨다.(2단계 페이지 테이블처럼 두 번 거치기)

## UNIX 파일시스템의 구조

![](https://i.imgur.com/SmWZL93.png)

- 가장 기본적인 파일시스템 구조

#### UNIX 파일 시스템의 중요 개념

- Boot block
  - 부팅에 필요한 정보(bootstrap loader)
  - 어떤 파일 시스템이든 제일 앞에(0번) 위치
- Superblock
  - 파일 시스템에 관한 총제적인 정보
    - 비어있는 블럭/사용중인 블럭의 위치
    - Inode list 위치
    - Data block 위치
- Inode list(Index node list)
  - 실제 파일시스템의 구현에서 디렉토리가 모든 메타데이터를 가지고 있지는 않다.
  - UNIX의 경우, 디렉토리는 파일의 메타데이터 일부만 가지고 있고, 나머지는 별도의 위치인 Inode list에 보관
  - 파일 하나당 Inode가 하나씩 할당(위 그림에서 빨간색 작은 네모)
  - Inode : 파일의 이름을 제외한 파일의 모든 메타데이터를 보관

> #### 파일의 위치 정보는 어떻게 저장하고 있나요?
>
> - UNIX는 Indexed Allocation을 사용하고 있다.
> - Inode의 크기는 고정되어 있어 위치 정보를 나타내는 포인터의 개수도 유한한다.
> - 그럼에도 불구하고 가급적 작은 Inode를 가지고 굉장히 큰 파일을 어떻게 표현할 수 있어야 한다.
> - 작은 파일인 경우, direct blocks을 통해 파일의 위치를 바로 알 수 있다.
> - 파일의 크기가 커질 수록 single indirect(1개의 index block을 거침), double indirect(2개의 index block), triple indirect(3개의 index block)를 통해 파일의 위치를 알 수 있다.
>
> ⇒ 이런 방식을 통해 한정된 크기의 Inode를 가지고 큰 파일의 위치 정보를 나타낼 수 있다.

## FAT File System

![](https://i.imgur.com/ui8Ab6l.png)

- 마이크로소프트사가 msdos를 만들었을때 처음 만든 시스템
- Boot block : 모든 파일시스템 공통적인 부분으로 부팅 관련 정보를 가지고 있다.
- FAT
  - 파일의 메타데이터의 위치정보를 별도의 테이블(배열)로 보관
  - 나머지 메타데이터와 그 파일의 첫 번째 블럭 위치까지 디렉토리에서 보관
- Linked Allocation의 문제점을 해결
  - Reliability 문제
    - 다음 블럭의 위치가 실제 데이터에 저장되어 있지 않고, FAT에 따로 보관하여 포인터 하나가 유실되어도 실제 데이터에는 문제가 발생하지 않는다.
    - FAT은 중요한 정보이므로 여러 개의 복제본을 가지고 있는다.
  - 직접 접근 : FAT은 이미 메모리에 올려진 상태이고, 이 상태에서 특정 블럭의 위치를 FAT의 테이블에서 찾아 해당 블럭에 대한 데이터에 직접 접근 가능
  - 공간 효율성 : 512bytes 충분히 활용 가능

## Free-Space Management

- 중간에 비어있는 블럭 관리

#### Bit map or bit vector

![](https://i.imgur.com/LZ3FcHJ.png)

- UNIX의 경우 각 블록에 번호가 있고, Super block에 해당 블록의 사용여부를 비트로 나타낸 방식
  - `bit[i] = 0` : `block[i]`가 비어있음을 의미
  - `bit[i] = 1` : `block[i]`가 할당되어있임을 의미
- 비트맵의 크기(길이)는 블럭의 크기(길이)와 동일
- 파일 시스템이 파일이 새로 생성되거나 파일의 크기가 커진다면 비어있는 블럭에 할당하고, 파일이 삭제되면 bit를 `0`으로 변경하여 관리
- 비트맵은 디스크에 부가적인 공간을 필요로 한다.
- 비트맵을 통해 연속적인 빈 블록을 찾는데 효과적

#### Linked list

![](https://i.imgur.com/2sreRyX.png)

- 모든 비어있는 블럭들을 linked list로 연결한 방식(free list)
- 비어있는 블록의 첫 번째 위치만 포인터로 가지고 있고, 각 블럭에 다음 비어있는 블럭의 위치를 가리키는 포인터가 있다.
- 비트맵에 비해 추가적인 공간 낭비가 없다.
- 연속적인 가용공간을 찾는 것은 쉽지 않다.

#### Grouping

- linked list 방법의 변형
- 첫 번째 비어있는 블록이 index 역할을 하여 나머지 비어있는 블록들의 위치를 저장하는 방식
  - 첫번째 free block이 n개의 pointer를 가짐
  - n-1개의 pointer는 free data block을 가리킴
  - 마지막 pointer가 가리키는 block은 또 다시 n pointer
- linked list보다는 빈 블록을 한꺼번에 찾기에 효율적이지만 연속적인 가용공간을 찾는 것은 쉽지 않다.

#### Counting

- 연속적인 빈 블럭을 표시하기 위해 빈 블록의 첫 번째 위치와 이 위치에서 몇 개까지 비어있는지에 대한 정보를 쌍을 이루어 보관
- (first free block, number of contiguous free blocks)을 유지
- 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안

## Directory Implementation(디렉토리 구현)

### Linear list

![](https://i.imgur.com/0q3q5e5.png)

- <파일 이름, 파일의 나머지 메타데이터> 의 list
- 메타데이터의 크기는 고정해서 관리
- 디렉토리 내에 파일이 있는지 찾기 위해서는 선형 탐색(linear search)이 필요
  - 특정 파일을 찾을 때 모든 것을 검색해야하므로 시간이 오래걸린다.(time-consuming)
  - O(N)의 시간복잡도

### Hash Table

![](https://i.imgur.com/ibXqcOF.png)

- linear list + hashing 방식
- hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌
- 파일의 이름에 hash function을 적용하여 나온 값을 해당 파일 테이블의 엔트리에 저장
- 특정 파일을 찾을 때 해당 파일이름에 해시함수를 적용한 값을 구하여, 순차적으로 접근할 필요 없이 그 값의 엔트리만 찾아보면 된다.
  - search time 줄어듦
  - O(1)의 시간복잡도
- Collision 발생 가능
  - Collision : 서로 다른 파일 이름에 대해 결과값이 같은 엔트리에 매핑되는 것

### File 메타데이터의 보관 위치

- 디렉토리 내에 직접 보관
- 디렉토리에는 포인터를 두고 다른 곳에 보관
  - Inode, FAT 등

### Long file name의 지원

- <file name, file의 메타 데이터>의 리스트에서 각 엔트리의 크기는 일반적으로 고정되어 있다.
- file name이 고정 크기의 엔트리 길이보다 길어지는 경우 엔트리의 마지막 부분에 file name의 뒷 부분이 위치한 곳의 포인터를 두는 방법
- file 이름의 나머지 부분은 동일한 directory file의 일부에 존재한다.

## VFS and NFS

![](https://i.imgur.com/NAROlEQ.png)

### Virtual File System(VFS)

- 여러 종류의 파일 시스템 존재
- 사용자가 파일 시스템에 접근 시 시스템 콜을 하는데, 시스템 종류별로 다른 시스템콜 인터페이스를 사용한다면 사용자가 혼란스러울 것이다
- 보통은 어떤 파일시스템이 사용되든지 상관없이 윗 계층에 VFS라는 인터페이스를 둔다.
- 서로 다양한 file system에 대해 동일한 시스템콜 인터페이스(API)를 통해 접근할 수 있게 해주는 운영체제의 계층

### Network File System(NFS)

- 분산 시스템에서는 네트워크를 통해 로컬 스토리지의 파일 시스템이 아닌 원격의 파일 시스템에 접근하여 파일이 공유
  ⇒ NFS를 사용하여 접근
- NFS는 분산 환경에서의 대표적인 파일 공유 방법
- 분산 시스템에서는 네트워크를 통해 파일을 공유하기 위해 NFS client, NFS server가 이용된다.

## Page Cache and Buffer Cache

#### Page Cache

- 가상 메모리의 페이징 시스템에서 사용하는 페이지 프레임(page cache)을 캐싱의 관점에서 설명하는 용어
- Memory-Mapped I/O를 쓰는 경우 파일의 I/O에서도 페이지 캐시를 사용한다.

#### Memory-Mapped I/O

- 파일의 일부를 가상 메모리에 매핑시킴
- 매핑한 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 한다.

#### Buffer Cache

- 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 버퍼 캐시를 사용한다.
- 파일 사용의 지역성 활용
  - 한 번 읽어 온 블록에 대한 후속 요청 시, 버퍼 캐시에서 즉시 전달
- 모든 프로세스가 공용으로 사용
- Replacement algorithm 필요(LRU, LFU 등)

#### Unified Buffer Cache(통합 버퍼 캐시)

- 최근의 운영체제에서는 기존의 버퍼 캐시가 페이지 캐시에 통합됨
- 버퍼 캐시를 페이지 단위로 관리
- 운영체제에서 물리적인 메모리를 관리하는 루틴에 페이지 캐시와 버퍼 캐시를 같이 관리

![](https://i.imgur.com/dOQLuQ5.png)

![](https://i.imgur.com/5kEcVfR.png)

- Unified Buffer Cache 사용하지 않는 경우
  - 버퍼캐시 : open() -> read/write 시스템콜 -> 운영체제가 버퍼캐시 확인 있으면 전달, 없으면 파일 시스템에서 읽어서 전달
  - memory-mapped I/O : 처음에 시스템콜(memory mapped하겠다고 알림) -> 주소 공간 중 일부를 파일로 매핑 -> 디스크의 파일을 읽어오고 버퍼캐시 사용하는것까지도 똑같다. -> 읽어온 내용을 페이지 캐시에 카피 -> 다음부터는 운영체제 관여 없이 내 메모리 영역에 데이터를 읽고 씀 (메모리 접근을 통해 파일 입출력 사용)
    - 버퍼캐시는 둘 중 무엇을 쓰던 통과해야한다. 왼쪽 그림에서 양쪽 모두 버퍼캐시에 내용을 자신의 페이지 캐쉬에 복제하는 오버헤드가 있다.
- Unified Buffer Cache 사용하는 경우
  - 버퍼 캐시를 따로 두지 않고, page cache와 buffer cache가 share되어 사용
  - Page cache 자체가 사용자 프로그램의 논리적인 주소영역에 매핑되었기 때문에 page cache에 직접 읽고 쓰는 것이 가능

# [Chapter 11. 파일 시스템 구현 Part-2](https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f)

## 프로그램의 실행

![](https://i.imgur.com/XKho6ne.png)

- 프로그램이 실행되면 실행 파일이 프로세스가 되고 프로세스만의 독자적인 주소 공간이 만들어 진다.
  - 이 공간은 코드, 데이터, 스택으로 구분
- 당장 사용될 부분은 물리적 메모리에 올라가고, 당장 사용되지 않는 부분은 스왑 영역으로 내려간다.
- 이때 코드 부분(read only)은 메모리에 올라간 다음 쫓겨날 때, 스왑 영역으로 내리지 않는다.
  - 이미 파일 시스템에 있기 때문에 필요 없으면 물리적 메모리에서 지운다.
  - 나중에 필요하면 스왑 영역에서 올리는 것이 아니라 파일 시스템에서 가져오면 된다.

![](https://i.imgur.com/AaMx1Pl.png)

- 프로세스가 일반 데이터 파일을 I/O할 경우
- `mmap()` 을 호출하면 Memory Mapped I/O 방식으로 파일을 I/O할 수 있고, `mmap()` 은 시스템 콜이므로 운영 체제에 CPU의 제어권이 넘어간다.

![](https://i.imgur.com/Ci3L8SY.png)

- Unified Buffer Cache에서는 page cache는 buffer cache를 겸한다. - 어떤 프로세스가 read() 시스템콜을 했는데, 요청한 데이터가 page cache로써 다른 프로세스에 `mmap()`으로 매핑되어 있는 상태라면 그냥 올라가있는 buffer cache에서 copy해서 사용할 수 있다.

#### Memory-Mapped I/O의 장점

- 일단 올라간 파일의 내용을 직접 접근할 수 있어 더 빠르다.
- 캐시에 올라온 내용을 자신의 주소공간에 카피하는 오버헤드가 없이 맵핑되어 바로 전달된다.

#### Memory-Mapped I/O의 단점

- 물리적 메모리의 내용을 share에서 사용할때 일괄성 문제에 주의해야 한다.
- read/write 시스템콜의 경우 카피해서 사용하기 때문에 일괄성 문제는 없다.
