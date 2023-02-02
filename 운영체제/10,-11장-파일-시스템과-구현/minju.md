# [파일 시스템 #1](https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f)

## File and File System

### File

연관된 정보들의 집합

일반적으로 비휘발성의 보조기억장치에 저장

운영체제는 다양한 저장장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌

Operation : create, read, write, reposition, delete, open, close 등

> reposition : 파일을 접근하는 위치를 수정해주는 연산

### File attribute (file metadata)

파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들

- 파일 이름, 유형, 저장된 위치, 파일 사이즈
- 접근 권한 (읽기, 쓰기, 실행), 시간 (생성, 변경, 사용), 소유자 등

### File system

- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타 데이터 , 디렉토리 정보 등을 관리
- 파일의 저장 방법 결정
- 파일 보호 등

## Directory and Logical Disk

### Directory

파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일

그 디렉토리에 속한 파일 이름 및 파일 attribute들

#### 동작 방식

search, create, delete / list, rename, traverse system (시스템 횡단)

### Partition (=logical disk)

- 하나의 물리적 디스크 안에 여러 파티션을 두는게 일반적
- 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
- 물리적 디스크를 파티션으로 구성한 뒤, 각각의 파티션에 file system을 깔거나 swapping등 다른 용도로 사용 가능

## open( )

파일의 메타데이터(저장 위치 포함)를 메모리로 올려놓는 것

### open("/a/b/c") 를 동작하는 경우

1. 사용자 메모리에서 open, 디스크로부터 파일 c 의 메타데이터를 메모리로 가지고 옴
2. 커널 메모리 영역이 이를 위해 directory path를 search
   - 루트 디렉토리 '/'를 open하고 그 안에서 파일 "a"의 위치 회득
   - 파일 'a'를 open하고 그 안에서 파일 "b"의 위치 회득
   - 파일 'b'를 open하고 그 안에서 파일 "c"의 위치 회득
   - 파일 'c'를 open

read에 필요한 내용은 운영체제가 자신의 메모리 영역(커널)에 카피해둔 뒤 사용자 메모리에 전달 -> 다른 프로그램이 같은 작업 요청시 같은 내용 전달 가능 = `buffer caching`
read자체가 시스템 콜이기 때문에 운영체제로 CPU 제어권이 넘어감.

Directory path의 search에 너무 많은 시간을 소요

- open을 read/write 명령과 별도로 두는 이유
- 한번 오픈한 파일은 read/write시 directory search 불필요

### Open file table

현재 오픈된 파일들의 메타데이터 보관소 (In memory)
디스크의 메타데이터보다 몇가지 정보 추가

- 오픈한 프로세스의 수
- `file offset`: 파일 어느 위치 접근 중인지 표시 (별도 테이블 필요)

### File descriptor (file handle, file control block)

open file table에 대한 위치 정보 (프로세스 별)

## File Protection

각 파일에 대해 누구에게 어떤 유형의 접근 (Read, write, execution)을 허락할 것인가?

### Access control 방법

#### 1. Access control Matrix

- Access control list : 파일 별로 누구에게 어떤 접근 권한이 있는지 표시
- Capability : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시

#### 2. Grouping

- 전체 user를 owner, group, public 의 세 그룹으로 구분
- 각 파일에 대해 세 그룹의 접근 권한 (rwx)을 3비트씩으로 표시 ex) UNIX

#### 3. Password

- 파일마다 Password를 두는 방법 (디렉토리에도 가능)
  - 모든 접근 권한에 대해 하나의 password : all-or-nothing
  - 접근 권한 별 password : 암기문제, 관리 문제

## File System의 Mounting

다른 디스크(파티션)에 있는 파일 시스템에 접근하기 위해 사용하는 방법

## Access Methods

시스템이 제공하는 파일 정보의 접근 방식

### 순차접근

- 카세트 테이프를 사용하는 방식처럼 접근
- 읽거나 쓰면 offset은 자동적으로 증가

### 직접접근

- LP 레코드판과 같이 접근하도록 함
- 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음

# [파일 시스템 구현 #1](https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f)

## Allocation of File Data in Disk

디스크에 파일을 저장하는 방법

### Contiguous Allocation

하나의 파일이 디스크상에 연속해서 존재하는 방법

directory는 파일의 메타데이터를 담고 있음. (file, start, limit)

#### 단점

- 외부 단편화 발생
- 파일은 중간중간 크기가 변경될 수 있음 -> file grow가 어려움
  - File 생성시 얼마나 큰 hole 배당?
  - grow 가능 vs 낭비 (내부 단편화)

#### 장점

- Fast I/O
  - 한번의 seek/rotation으로 많은 bite transfer
  - realtime file용으로 또는 이미 run중이던 process의 swapping용
- Direct access (=random access) 가능

> 대부분 오버헤드 시간 = root를 찾아 헤매는 시간

### Linked Allocation

하드 디스크임에도 불구하고 연속 할당과 달리 **직접 접근 불가능**

directory (file, start, end)

#### 단점

- 랜덤 접근 불가
- Reliability 문제 : 한 sector가 고장나 포인터 유실 시 많은 부분을 잃음
- pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어트림

#### 장점

- 외부 단편화 발생 안함

#### 변형

`File allocation table` 파일 시스템 : 포인터를 별도의 위치에 보관해 reliabiliy와 공간 효율성 문제 해결

### Indexed Allocation

파일 내용을 저장하는게 아니라 내용이 들어있는 블록의 위치가 담긴 index table을 가지고 있는 블록을 이용

directory (file, index block)

#### 단점

- 작은 파일의 경우 공간 낭비 (실제로 많은 파일들이 적은 용량)
- 너무 큰 파일의 경우 하나의 블록으로 index를 저장하기에 부족
  - 해결 방안
    - linked scheme
    - multi-level index

#### 장점

- 외부 단편화 발생하지 않음
- 직접 접근 가능

## UNIX 파일시스템의 구조

파일 시스템의 중요 개념

1. Boot block

- 부팅에 필요한 정보 (bootstrap loader)

2. Super block

- 파일 시스템에 관한 총체적인 정보 포함

3. Inode ⭐️

- 파일 이름을 제외한 파일의 모든 메타 데이터를 저장
- 크기가 고정되어 있음 (포인터 갯수 제한)

4. Data block

- 파일의 실제 내용을 보관

## FAT File System

1. Boot block

- 부팅에 필요한 정보 (bootstrap loader)

2. FAP

- 메타데이터 중 일부 (위치 정보) 보관
- 각각의 블록에 다음 블록에 대한 정보 가짐
- 보통 2 copy 이상 보관하며 의존성 줄임 (분실 예방)

3. Root directory

- 메타 데이터 보관

4. Data Block

- 파일의 실제 내용을 보관

## Free-Space Management

비어있는 블록을 어떻게 관리하는 가

### 1. Bit map or bit vector

- bit map은 부가적인 공간을 필요로 함
- 연속적인 n개의 free block을 찾는데 효과적

### 2. Linked list

- 모든 Free block들을 링크로 연결(free list)
- 연속적인 가용 공간을 찾는 것은 쉽지 않다
- 공간의 낭비가 없다

### 3. Grouping

- linked list 방법의 변형
- 첫번째 free block이 n개의 pointer를 가짐
  - n-1 pointer는 free data block을 가리킴
  - 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐

### 4. Counting

- 프로그램들이 종종 여러개의 연속적인 block을 할당하고 반납한다는 성질에 착안 -> 연속적인 빈 블록 찾기 좋음
- first free block, number of contiguous free block을 유지

## Directory Implementation

디렉토리 파일에 어떻게 메타데이터를 저장할 것인가

### Linear list

- 메타 데이터에 대한 크기 정보 보유
- <file name, file의 metadata>의 리스트
- 구현이 간단
- 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요 (Time-consuming)

### Hash table

- linear list + hashing
- hash table은 file name을 이 파일의 Linear list의 위치로 바꿔줌
- search time을 없앰
- 충돌 발생 가능

### File의 metadata의 보관 위치

1. 디렉토리 내에 직접 보관
2. 디렉토리에는 포인터를 두고 다른 곳에 보관 (inode, FAT 등)

### Long file name의 지원

- <file name, file의 metadata>의 리스트에서 각 entry는 일반적으로 고정 크기
- file name이 고정 크기의 entry 길이보다 길어지는 경우, entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
- 이름의 나머지 부분은 동일한 directory file의 일부에 존재

## VFS and NFS

### Virtual File System (VFS)

서로 다른 다양한 file system에 대해 동일한 시스템 콜 인터페이스 (API)를 통해 접근할 수 있게 해주는 OS의 Layer

### Network File System (NFS)

분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음

NFS는 분산환경에서의 대표적인 파일 공유 방법

> Client : VFS -> NFS -> network -> NFS -> VFS : Server

# [파일 시스템 구현 #2](https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f)

## Page Cache and Buffer Cache

### Page cache

- Virtual memory의 Paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
- memory-mapped I/O를 쓰는 경우 file의 I/O에서도 page cache 사용

> 운영체제에게 주는 정보가 제한적

### Memory-mapped I/O

- file의 일부를 virtual memory에 mapping 시킴
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함

> read, write 시스템 콜을 사용하지 않고 메모리를 읽고 쓰는

### Buffer cache

- 파일시스템을 I/O 연산은 메모리의 특정 영역은 buffer cache 사용
- file 사용의 locality 활용 : 한번 읽어온 block에 대한 후속 요청시 buffer cache에서 즉시 전달
- 모든 프로세스가 공용으로 사용
- replacement algorithm 필요 (LRU, LFU 등)

> 파일 시스템 관점에서 사용 (운영체제에 CPU 제어권 제공)

### Unified Buffer Cache

- 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨.

### 파일 입출력 방법

#### 1. Unified buffer cache 사용 ❌

page cache에 복제해서 사용하는 오버헤드 존재

#### 2. Unified buffer cache 사용 ✅

## 프로그램의 실행

가상 페이지가 물리적 메모리의 일부에 매핑
