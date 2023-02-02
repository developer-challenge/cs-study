## [파일 시스템 #1](https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f)

### File and File System

**File**

- “A named collection of related information” - 이름을 통해서 접근하는 단위 (메모리는 주소를 통해서 접근하는 장치)
- 일반적으로 비휘발성의 보조기억장치에 저장
- 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해 줌
- Operation
  - create, read, write, reposition (Lseek), delete, open, close 등
    여러개의 바이트로 구성되는 file의 어느 위치를 읽거나 쓰는지 가리키는 포인터가 있음
    → reposition(lseek) : file에 현재 접근하고 있는 위치를 수정해주는 연산

**File attribute** (혹은 파일의 **metadata)**

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
  - 파일 이름, 유형, 저장된 위치, 파일 사이즈
  - 접근 권한 (읽기/쓰기/실행), 시간 (생성/변경/사용), 소유자 등

**File system**

- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
- 파일의 저장 방법 결정
- 파일 보호 등

### Directory and Logical Disk

******\*\*\*\*******Directory******\*\*\*\*******

- **파일의 메타데이터** 중 일부를 보관하고 있는 일종의 특별한 **파일**
- 그 디렉토리에 속한 파일 이름 및 파일 attribute들
- operation
  - search for a file, craete a file, delete a file
  - list a directory, rename a file, traverse the file system(file system 전체 탐색)

******\*\*******Partition (=Logical Disk)******\*\*******

- 하나의 (물리적) 디스크 안에 여러 파티션을 두는게 일반적
- 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
- (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 **file system**을 깔거나 **swapping** 등 다른 용도로 사용할 수 있음

### open( )

![](https://i.imgur.com/6EhHsSR.png)

open - **file의 메타데이터를 메모리로 올려 놓는 것**

**open(”/a/b/c”)**

- 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
- 이를 위하여 directory path를 search
  - 루트 디렉토리 “/”를 open하고 그 안에서 파일 “a”의 위치 획득 (보통 루트 디렉토리의 위치는 미리 알려져 있음)
  - 파일 “a”를 open한 후 read하여 그 안에서 파일 “b”의 위치 획득
  - 파일 “b”를 open한 후 read하여 그 안에서 파일 “c”의 위치 획득
  - 파일 “c”를 open한다
- Directory path의 search에 너무 많은 시간 소요
  - Open을 read / write 와 별도로 두는 이유임
  - 한번 open한 파일은 read / write 시 directory search 불필요
- Open file table
  - 현재 open 된 파일들의 메타데이터 보관소 (in memory)
  - 디스크의 메타데이터보다 몇 가지 정보가 추가
    - Open한 프로세스의 수
    - File offset : 파일 어느 위치 접근 중인지 표시 (별도 테이블 필요)
- File descriptor (file handle, file control block)
  - Open file table에 대한 위치 정보 (프로세스 별)

![](https://i.imgur.com/gjVgyzK.png)

사용자 메모리 영역에서 open → I/O를 하는 system call이므로 CPU 제어권이 운영체제로 넘어감

Open file table - 전체 프로그램들이 오픈한 파일들이 어떤 것인지 관리하는 global한 테이블

root 디렉토리의 메타데이터를 먼저 올림 - root open

→ 메타데이터 내의 파일 위치정보를 통해 루트 디렉토리의 실제 내용이 어딨는지 찾음

→ a라는 파일의 메타데이터를 메모리에 올려놓음

→…→ b의 메타데이터가 메모리에 올라옴

→ open 완료

system call을 했기 때문에 open(”/a/b”)에 대한 결과 값을 리턴하게 됨

각 프로세스마다 그 프로세스가 오픈한 파일들에 대한 메타데이터 포인터를 가지고 있는 일종의 배열같은 것이 정의되어 있음 → b의 메타데이터를 가리키는 포인터가 배열 어딘가에 만들어지고 배열의 몇 번째 인덱스인지를 리턴

read(fd…)

→ system call

→ b의 메타데이터를 읽고 b의 실제 내용을 시작 위치부터 요청한 용량만큼 메모리로 읽어서 전달

(이 때 사용자에게 직접 주는 것이 아니라 운영체제가 자신의 메모리 공간 일부에다가 먼저 읽어놓음 → 사용자 프로그램에 Copy하여 전달 ⇒ 어떠한 프로그램이 동일한 파일에 동일한 위치를 요청(read)하면 disk까지 가지 않고 바로 전달 - 버퍼 캐싱)

system call을 통해 이루어지므로 무조건 CPU 제어권은 운영체제로 넘어감 - 모든 정보를 운영체제가 알기 때문에 file system에서 buffer caching환경에서는 LRU 알고리즘이나 LFU알고리즘을 자연스럽게 사용할 수 있음

### File Protection

각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?

Access Control 방법

- Access control Matrix

  ![](https://i.imgur.com/58QwkH5.png)

  - 행렬로 표시할 경우 본인만 사용하는 파일에 대해 다른 사용자는 접근권한이 전혀 없음 → 모든 사용자에 대해 행렬을 만들면 낭비

  LinkedList형식

  - **ACL(Access control list)** : 파일별로 누구에게 어떤 접근 권한이 있는지 표시
  - **Capability** : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시

  Linkedlist 형식이어도 overhead가 크다는 단점이 있음

- **Grouping - 일반적인 운영체제에서 사용하는 파일 접근 권한 제어 방법**

  - 전체 user를 owner, group, public의 세 그룹으로 구분
  - 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시 - 총 9개의 비트만 표시
  - (예) UNIX
    ![](https://i.imgur.com/OyKPvYR.png)

- **Password**
  - 파일마다 password를 두는 방법 (디렉토리 파일에 두는 방법도 가능)
  - 모든 접근 권한에 대해 하나의 password : all-or-nothing
  - 접근 권한별 password : 암기 문제, 관리 문제

### File System의 Mounting

![](https://i.imgur.com/moZ8HOY.png)

하나의 물리적인 disk를 여러개의 논리적인 disk로 나눌 수 있음

→ 각각의 논리적인 disk는 file system을 설치해서 사용할 수 있음

만약 다른 partition에 설치되어 있는 file system에 접근해야 한다면?

![](https://i.imgur.com/9oWfzq6.png)

루트 파일 시스템의 특정 디렉토리 이름에 다른 파티션의 file system을 마운트 → 다른 파일 시스템의 루트 디렉토리 접근 가능

### Access Methods

시스템이 제공하는 파일 정보의 접근 방식

- **순차 접근 (sequential access)**
  - 카세트 테이프를 사용하는 방식처럼 접근
  - 읽거나 쓰면 offset은 자동적으로 증가
- **직접 접근 (direct access, random access)**
  - LP 레코드 판과 같이 접근하도록 함
  - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음

## [파일 시스템 구현 #1](https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f)

### Allocation of File Data in Disk

disk에 파일을 저장할 때는 보통 동일한 크기의 섹터단위로 나누어서 저장을 한다 즉, 임의의 크기의 파일을 동일 크기의 블록단위로 나누어서 저장(paging 기법과 유사)

file system이나 disk 외부에서 볼 때는 각각의 동일한 크기의 저장 단위를 논리적인 블록이라고 부름

**Contiguous Allocation**

![](https://i.imgur.com/zrThyml.png)

하나의 파일이 디스크 상에 연속해서 저장되는 방법

directory : 해당 directory 밑에 있는 파일들의 메타데이터를 내용으로 함

**단점**

- external fragmentation
- File grow가 어려움 - 파일 크기를 키우는데 있어서 제약이 있음
  - file 생성시 얼마나 큰 hole을 배당할 것인가?
  - grow 가능 vs 낭비 (internal fragmentation)

**장점**

- Fast I/O
  - 한번의 seek/rotation으로 많은 바이트 transfer
  - Realtime file 용으로, 또는 이미 run 중이던 process의 swapping 용
- Direct access(=random access) 가능 - 시작 위치 + 접근하고자 하는 만큼 더하기

**Linked Allocation**

![](https://i.imgur.com/VDba3aw.png)

파일의 데이터를 디스크에 연속적으로 배치하지 않고 빈 위치면 아무곳이나 들어갈 수 있도록 배치

**장점**

- External fragmentation 발생 안 함

**단점**

- No random access - 직접접근 불가능 / 순차접근만 가능(시간이 많이 듦)
- Reliability 문제
  - 한 sector가 고장나 pointer가 유실되면 많은 부분을 잃음
- Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림
  - 512 bytes/sector, 4 bytes/pointer ⇒ 디스크와 컴퓨터 내부 사이에 데이터를 주고 받는 인터페이스가 512 bytes로 정해져 있는데 포인터로 인해 512 - 4 bytes가 되어 두 섹터에 나누어 저장될 수 있음

**변형**

- **File-allocation table (FAT)** 파일 시스템
  - 포인터를 별도의 위치에 보관하여 reliability와 공간효율성 문제 해결

**Indexed Allocation**

![](https://i.imgur.com/xGEflr5.png)

index 블록 안에 해당 파일이 저장되어 있는 위치 정보를 내용으로 기록

**\*\*\*\***장점**\*\*\*\***

- External fragmentation이 발생하지 않음
- Direct access 가능

**\*\***단점**\*\***

- Small file의 경우 공간 낭비 (실제로 많은 file들이 small) - index를 위한 블록이 필요하기 때문
- Too Large file의 경우 하나의 block으로 index를 저장하기에 부족
  - 해결방안
    1. linked scheme - 마지막 index에 또 다른 index 블록을 가리키게함
    2. multi-level index - 하나의 index 블록이 직접 파일의 위치를 가리키게 하는것이 아닌 또 다른 index 블록을 가리키게함

### UNIX 파일시스템의 구조

![](https://i.imgur.com/JRKrlgH.png)

UNIX 파일 시스템은 저장되는 구조가 크게 4가지로 구성 - Boot block, Super block, Inode list, Data block

Boot block은 어떤 파일 시스템이든 제일 앞에 나오게 됨

유닉스 파일 시스템의 중요 개념

- **Boot block**
  - 부팅에 필요한 정보 (bootstrap loader)
- **Super block**
  - 파일 시스템에 관한 총체적인 정보를 담고 있음 - 어디가 빈 블록이고 어디가 실제로 파일이 저장된, 사용중인 블록인지 관리 / 어디까지가 Inode list가 있고 어디부터 실제 Data block인지 관리
- **Inode**

  - 파일 이름을 제외한 파일의 모든 메타 데이터를 저장
    파일의 메타데이터는 파일을 가지고 있는 디렉토리에 기록되어 있음. 하지만 디렉토리에 메타데이터가 다 기록되어있는 것은 아님. 특히 UNIX의 파일 시스템의 경우 디렉토리는 지극히 일부만 가지고 있고 실제 파일의 메타데이터들은 별도의 위치에 빼서 보관 - 이 위치가 Inode list
  - 파일 하나당 Inode가 하나씩 할당
    ![](https://i.imgur.com/Q5llWjv.png)
  - 파일의 위치정보 어떻게 저장하는가? → Indexed Allocation를 변형해서(거의 그대로) 사용
    direct index, single indirect, double indirect, triple indirect 4가지로 해당 파일의 위치정보를 구성
    - 파일 크기가 매우 작으면 direct index만을 가지고 파일의 위치를 표현할 수 있음
    - 파일 크기가 매우 클 경우 indirect를 통해 접근 (single indirect는 파일의 내용을 index를 한번 거쳐서 접근)
    - 대부분의 파일은 크기가 매우 작기 때문에 Inode만 메모리에 올려 놓으면 파일의 위치를 direct로 바로 확인 가능 → 효율적임
  - Inode는 크기가 고정되어있음

- **Data block**
  - 파일의 실제 내용을 보관

### FAT File System

![](https://i.imgur.com/XhYQYoa.png)

마이크로소프트가 MS-DOS를 만들었을 때 처음 만든 file system

**FAT**

파일의 메타데이터 중 일부(위치 정보 / 나머지는 directory가 가지고 있음)를 보관

- 파일의 첫 번째 위치가 어디인지도 directory에 저장
  → Linked Allocation에서는 파일의 크기가 클 경우 두 번째 블록의 위치를 저장하는 방식으로 여러 문제점이 있었음
  → **다음 블록의 위치를 FAT이라는 별도의 배열에 저장**
  → **FAT 배열의 크기는 Disk가 관리하는 Data block의 개수만큼**
- FAT은 대단히 중요한 정보이므로 디스크에 one copy가 아니라 two copy를 진행

### Free-Space Management - 빈 공간 관리

**Bit map or bit vector**

![](https://i.imgur.com/fcUa4p3.png)

- Bit map은 부가적인 공간을 필요로 함
- 연속적인 n개의 free block을 찾는데 효과적

**\*\***Linked list**\*\***

![](https://i.imgur.com/pqMP6vo.png)

- 모든 free block들을 링크로 연결 (free list)
- 연속적인 가용공간을 찾는 것은 쉽지 않다
- 공간의 낭비가 없다

**Grouping**

![](https://i.imgur.com/vasDupW.png)

- linked list 방법의 변형
- 첫번째 free block이 n개의 pointer를 가짐
  - n-1 pointer는 free data block을 가리킴
  - 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐

**Counting**

- 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안
- (first free block, # of contiguous free blocks)을 유지

### Directory Implementation

**********\*\***********Linear list**********\*\***********

- <file name, file의 metadata>의 list - 순차적으로 저장 / field 크기 고정
- 구현이 간단
- 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요 (time-consuming)

********\*\*\*\*********Hash Table********\*\*\*\*********

- linear list + hashing
- Hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌 - file 이름을 hash 함수 적용
- search time을 없앰 - file 이름을 해쉬 함수 적용하고 적용된 해쉬 함수 결과값에 해당하는 엔트리만 찾으면 됨
- Collision 발생 가능 - 다른 file 이름에 대해서 결과값이 같은 엔트리로 매핑되는 경우

![](https://i.imgur.com/zOJAg9c.png)

********************************\*\*\*\*********************************File의 metadata의 보관 위치********************************\*\*\*\*********************************

- 디렉토리 내에 직접 보관
- 디렉토리에는 포인터를 두고 다른 곳에 보관
  - inode, FAT 등

************************\*\*************************Long file name의 지원************************\*\*************************

- <file name, file의 metadata>의 list에서 각 entry는 일반적으로 고정 크기
- file name이 고정 크기의 entry 길이보다 길어지는 경우 entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
- 이름의 나머지 부분은 동일한 directory file의 일부에 존재

![](https://i.imgur.com/MeOnCT6.png)

### VFS and NFS

Virtual File System (VFS)

- 서로 다른 다양한 file system에 대해 동일한 시스템 콜 인터페이스 (API)를 통해 접근할 수 있게 해주는 OS의 layer

Network File System (NFS)

- 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
- NFS는 분산 환경에서의 대표적인 파일 공유 방법임

![](https://i.imgur.com/qlKMhc5.png)

## [파일 시스템 구현 #2](https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f)

### Page Cache and Buffer Cache

![](https://i.imgur.com/MV1eIpl.png)

**Page Cache**

- Virtual memory의 paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
- Memory-Mapped I/O를 쓰는 경우 file의 I/O에서도 page cache 사용

**Memory-Mapped I/O**

- File의 일부를 virtual memory에 mapping시킴
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함

**Buffer Cache**

- 운영체제가 자신의 buffer cache영역에 읽어놓고 사용자 프로그램에 copy하여 넘겨줌
- 파일시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache 사용
- File 사용의 locality 활용
  - 한번 읽어온 block에 대한 후속 요청시 buffer cache에서 즉시 전달
- 모든 프로세스가 공용으로 사용
- Replacement algorithm 필요 (LRU, LFU 등)

**Unified Buffer Cache**

- 최근의 OS (Linux)에서는 기존의 buffer cache가 page cache에 통합됨

![](https://i.imgur.com/wRqOyva.png)

memory-mapped I/O의 경우 buffer cache 내용을 프로세스 virtual memory에 매핑시켜놓으면 다시 접근할 때 운영체제의 간섭없이 자신의 메모리에 접근

Unified Buffer Cache를 이용하지 않는 File I/O : buffer cache를 통해야함 - overhead 발생

### 프로그램의 실행

![](https://i.imgur.com/d7cAjqU.png)

virtual memory의 code 부분은 메모리에 올라간 뒤 쫓겨날 때 Swap area로 내려가지 않음

→ code는 read-only이기 때문에 swap area에 써놓을 필요가 없음 (이미 file system의 실행파일 형태로 code가 저장되어있음)

memory mapped I/O : 프로세스 B virtaul address 일부에 매핑

read / write system call : unified buffer cache인 경우 요청한 데이터 파일 내용이 물리적인 메모리에 올라가 있는 즉 buffer cache와 겸하고 있는 page cache에 올라와 있으면 그 내용을 copy해서 사용자 프로세스에 전달
