# [파일 시스템 #1](https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f)

## File and File System

## File

- A named collection of related information
- 일반적으로 비휘발성의 보조기억장치(하드디스크)에 저장
- 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌
- Operation
  - create, read, write, reposition(lseek), delete, open, close

### File attribute (metadata)

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
  - 파일 이름, 유형, 저장된 위치, 파일 사이즈
  - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

### File system

- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
- 파일의 저장 방법 결정
- 파일 보호 등

## Directory and Logical Disk

### Directory

> Directory도 파일

- 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
- 그 디렉토리에 속한 파일 이름 및 파일 attribute들
- operation
  - search for a file, create a file, delete a file, list a directory, rename a file, traverse the file system

### Partition(Logical Disk)

- 하나의 (물리적)디스크 안에 여러 파티션을 두는게 일반적
- 여러개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
- (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음

## open()

> open(”/a/b/c”)

- 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
- 이를 위해 directory path를 search
  - 루트 디렉토리 `/`를 open하고 그 안에서 파일 `a`의 위치 취득
  - 파일 `a`를 open한 후 read하여 그 안에서 파일 `b`의 위치 취득
  - 파일 `b`를 open한 후 read하여 그 안에서 파일 `c`의 위치 취득
  - 파일 `c`를 open한다.
- Directory path의 search에 너무 많은 시간 소요
  - Open을 read/write와 별도로 두는 이유
  - 한번 open한 파일은 read/write 시 directory search 불필요
- **Open file table**
  - 현재 open된 파일들의 메타데이터 보관소 (in memory)
  - 디스크의 메타데이터보다 몇 가지 정보 추가
    - Open한 프로세스 수
    - File offset: 파일 어느 위치 접근 중인지 표시 (별도 테이블 필요)
- File descriptor (file handle, file control block)
  - Open file table에 대한 위치 정보 (프로세스 별)

## File Protection

> 각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?

### Access Control 방법

- Access control Matrix
  - Access control list: 파일별로 누구에게 어떤 접근 권한이 있는지 표시
  - Capability: 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시
- **Grouping**
  - 전체 user를 owner, group, public의 세 그룹으로 구분
  - 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시
  - 예) UNIX
- Password
  - 파일마다 password를 두는 방법(디렉토리 파일에 두는 방법도 가능)
  - 모든 접근 권한에 대해 하나의 password: all-or-nothing
  - 접근 권한별 password: 암기 문제, 관리 문제

## File System의 Mounting

- 하나의 물리적 디스크를 여러개의 논리적 디스크로 나누고 있음
- 각 논리적 디스크에 file system
- root file system : 다른 partition의 file system에서 접근하기 위한 것 ⇒ Mounting
- Mounting된 file system에 접근한다 = 다른 partition의 file system에 접근한다.

## Access Methods

> 시스템이 제공하는 파일 정보의 접근 방식

- 순차 접근 (sequential access)
  - 카세트 테이프를 사용하는 방식처럼 접근
  - 읽거나 쓰면 offset은 자동적으로 증가
- 직접 접근 (direct access, random access)
  - LP 레코드 판과 같이 접근하도록 함
  - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음

# [파일 시스템 구현 #1](https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f)

## Allocation of File Data in Disk

### Contiguous Allocation

- 동일한 크기의 논리적인 블럭으로 나누어서 저장
- 하나의 파일이 연속해서 저장되는 방식
- 파일 크기가 균일하지 않으므로 외부 조각 발생
- 단점
  - external fragmentation
  - file grow가 어려움
    - file 생성시 얼마나 큰 hole을 배당할 것인가?
    - grow 가능 vs. 낭비(internal framentation)
- 장점
  - Fast I/O
    - 한번의 seek/rotation으로 많은 바이트 transfer
    - Realtime file용으로, 또는 이미 run중이던 process의 swapping용
  - Direct access(=random access) 가능

### Linked Allocation

- 장점
  - External fragmentation 없음
- 단점
  - 직접 접근 불가능
  - No random access. 순차 접근 해야함
  - Reliability 문제
    - 한 sector가 고장나 pointer가 유실되면 많은 부분을 읾음
  - Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림
    - 512bytes/sector, 4 bytes/pointer
- 변형
  - File-allocation table (FAT) 파일 시스템
    - 포인터를 별도의 위치에 보관하여 reliability와 공간효율성 문제 해결

### Indexed Allocation

- 장점
  - External fragmentation 발생하지 않음
  - Direct access 가능
- 단점
  - Small file의 경우 공간 낭비 (실제로 많은 file들이 small)
  - Too Large file의 경우 하나의 block으로 index를 저장하기에 부족
    - linked scheme : 인덱스 블럭을 채우다가 다른 인덱스 블럭을 가리킴. 여러개의 인덱스 블럭
    - multi-level index : 인덱스 블럭이 n단계

## UNIX 파일시스템의 구조

- 어떤 파일 시스템이든 Boot block이 entry
- Index allocation
- 작은 Inode들로 큰 파일 표시

  - direct blocks의 포인터

- Boot block : 부팅에 필요한 정보
- Superblock : 파일 시스템에 관한 총체적인 정보
- **Inode** : 파일 이름을 제외한 파일의 모든 메타 데이터를 저장
- Data block : 파일의 실제 내용을 보관

## FAT File System

> Linked Allocation 단점 극복

- Book block
- FAT
  - 파일 메타데이터의 일부 (위치)
  - FAT에서 데이터에 대한 주소를 모두 가지고 있음. Data block의 다음 block 정보를 관리
  - 중간에 문제가 생겨도 ok
  - direct access 가능
  - 2copy 이상 저장
- Root directory
- Data block
  - directory file에 모든 정보가 담겨 있음

## Free-Space Management

> 비어있는 블록을 어떻게 관리할 것인가?

### Bit map or bit vector

- Bit map은 부가적인 공간을 필요로 함
- 연속적인 n개의 free block을 찾는데 효과적

### Linked list

> Linked Allocation

- 모든 free block들을 링크로 연결 (free list)
- 연속적인 가용공간을 찾는 것은 쉽지 않다.
- 공간 낭비가 없다.

### Grouping

> Index Allocation

- linked list의 변형
- 첫번째 free block이 n개의 pointer를 가짐
  - n-1 pointer는 free data block을 가짐
  - 마지막 pointer가 가지는 block은 또 다시 n pointer를 가짐
- 마찬가지로 연속적인 가용곤간을 찾기 어려움

### Counting

- 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착한
- first free block, # of contiguous free blocks 유지

## Directory Implementation

### Linear list

- <file name, file의 metadata>의 list
- 크기를 고정해서 관리
- 구현이 간단
- 디렉토리 내에 파일이 있는지 찾기 위해 linear search 필요 (time-consuming)

### Hash Table

- Linear list + hashing
- Hash table은 file name을 이 파일의 Linear list의 위치로 바꾸어줌
- search time을 없앰
- Collision 발생 가능
  - 서로 다른 파일이 같은 데이터에 매핑될 수도

### File의 metadata의 보관 위치

- 디렉토리 내에 직접 보관
- 디렉토리에는 포인터를 두고 다른 곳에 보관
  - inode, FAT 등

### Long file name의 지원

- <file name, file의 metadata>의 list에서 각 entry는 일반적으로 고정 크기
- file name이 고정 크기의 entry 길이보다 길어지는 경우 entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
- 이름의 나머지 부분은 동일한 directory file의 일부에 존재

## VFS and NFS

### Virtual File System (VFS)

- 서로 다른 다양한 file system에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해주는 OS의 Layer

### Network File System (NFS)

- 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
- NFS는 분산 환경에서의 대표적인 파일 공유 방법

# [파일 시스템 구현 #2](https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f)

## Page Cache and Buffer Cache

### Page Cache

- Virtual momory의 paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
- Memory-Mapped I/O를 쓰는 경우 file의 I/O에서도 page cache를 사용
- Clock Algorithm

### Memory-Mapped I/O

- File의 일부를 virtual memory에 mapping시킴
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함

### Buffer Cache

- 파일시스템을 위한 I/O 연산은 메모리의 특정 영역인 buffer cache 사용
- File 사용의 locality 활용
  - 한번 읽어온 block에 대한 후속 요청 시 buffer cache에서 즉시 전달
- 모든 프로세스가 공용으로 사용
- Replacement algorithm 필요 (LRU, LFU 등)

### Unified Buffer Cache

- 최근 OS에서는 기존의 buffer cache가 page cache에 통합됨

### Page cache and buffer cache

- 단위가 page인 것은 동일
- 커널 메모리 영역 : buffer cache. 입출력 시
- 사용자 메모리 영역 : page cache. 적재되었는가
- 최근에는 이 둘을 구분하지 않음. 필요할 때 할당 (Unified buffer cache)

- file open → r/w system call
  - 운영체제가 file system에 있는 것을 buffer cache로 가져옴
  - 사용자가 buffer cache에 있는 것을 copy해서 사용
- memory-mapped I/O

  - file에 mapped된 내용을 page cache에 가져옴
  - 자신의 메모리처럼 접근(운영체제/커널의 도움 없이)

- file open → r/w system call
  - 운영체제가 file system에 있는 것을 buffer cache로 가져옴
  - 사용자가 buffer cache에 있는 것을 copy해서 사용
- memory-mapped I/O
  - buffer cache = page cache

## 프로그램의 실행

- 사용자 프로그램 실행 중 memory-mapped I/O 사용 가능

  - 코드 부분에서 memory-mapped 사용
  - 주소공간에 mapping되어 있음

- system call or memory-mapped
- mmap()
  - 운영체제가 데이터파일 일부를 주소 공간에 매핑
  - page fault → 물리적 메모리에 적재
  - page mapping
  - 이후 운영체제의 도움 없이 접근
  - 메모리 해제는 swap area로 보내지는 것이 아니라 file system
  - 더 빠름
  - mapping이 share되면 일관성 문제
- system call
  - 물리적 주소 공간을 자신의 메모리 공간에 copy
  - consistency 문제 적음
