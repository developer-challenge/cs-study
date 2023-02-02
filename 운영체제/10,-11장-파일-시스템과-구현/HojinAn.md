## [파일 시스템 #1](https://core.ewha.ac.kr/publicview/C0101020140516150939191200?vmode=f)

### File and File System

- File: 하드 disk에 저장하는 단위
- 메모리는 주소를 통해 접근하는 장치. 반면, File은 이름을 통해 접근하는 단위
- 관련 정보를 이름을 가지고 저장하는 객체 모음 이라 할 수 있음
- 비휘발성의 보조기억장치(하드디스크)에 저장
- Operation

  - create/delete, read/write, reposition(lseek, 현재 접근 위치를 수정해주는 연산), open/close(file의 metadata를 옮기는 작업)

- File attribute(metadata): file을 관리하기 위한 정보.

  - 파일 이름, 유형, 위치, 사이즈
  - 접근 권한(읽/쓰/실행), 시간(생성/변경/사용), 소유자

- File system
  - OS에서 file 관리하는 부분
  - file, file의 metadata, 디렉토리 정보 등 관리

### Directory and Logical Disk

- 역시 하나의 file. 다만 file의 내용이 directory 정보를 담고 있는 것. directory 밑에 존재하는 file이 무엇인지에 관한 정보. 즉 directory 관한 metadata
- operation

  - list, create, delete, search, rename, traverse file system

- Partition(=Logical Disk)
  - OS가 보는 논리적 디스크
  - 하나의 physical disk 안에 여러 partition이 일반적
  - 여러 physical 합쳐서 하나의 partition으로 구성하기도
  - file system을 깔거나, swapping에 이용하기도

### open( )

- file의 metadata를 메모리에 올려두는 것. 시스템 콜
- 특정 file의 metadata와 content(내용)이 있을 것
- metadata에 file의 위치에 관한 data도 담겨 있음
- open() => metadata가 메모리로 올라온다.
- 보통 root directory의 위치는 미리 알려져 있음.

![](https://i.imgur.com/Eln0MEG.png)

- fd: file descriptor
- root부터 a, b로 거쳐 open() 실행
- Process별 PCB 안에 file의 metadata에 관한 pointer 가지고 있음
- open을 통해 메모리에 올라온 b의 data는 fd가 가지고 있음. 그 다음부터는 read, write로 읽는다. 대신 이 때 해당 fd로 read(fd), write(fd)
- OS가 미리 fd 읽어놓은 것 들고 있음. 다음부터 다시 disk가는 것이 아니라 읽어둔 것 제공: buffer caching
- paging 기법에서 OS가 끼어들지 못하는 HW 접근으로 clock algorithm 썼던 것과 달리 file read/write system에서는 buffer cache에 요청 내용이 있든 없든 CPU 제어권이 OS로 넘어감. 시스템 콜로 요청한 것이니까. OS가 판단.
- buffer cache: file system에서는 LRU, LFU 자연스래 사용. 그림에서 b의 content 메모리에 올라온 영역. 각 Process마다 동일한 content 요청에 대해 system-wide하게 참조하게 한다.
- global한 table과 Process별 table(per-process fd table)도 있고.
- file 내 접근 offset은 Process마다 따로 가지고 있어야 한다.
- 그래서 open file table은 2개로 나누어 1. Process 무관 system-wide하게 하나, 2. Process가 어디로 접근하는지 별도로 가지고 있는 offset을 따로 두는 것이 일반적.

### File Protection

- file에 대한 접근 권한.
- vs memory protection: read/write 권한 있냐 없냐만 이야기 했음. 메모리는 Process마다 별도로 갖고 있기 때문. Process 혼자만 보는 것이었음. 접근 권한이 무엇인지만 보면 됐음
- file protection은 여러 사용자, 프로그램이 같이 사용할 수 있음. 접근 권한이 누구에게 있는지, 접근 연산이 무엇이 가능한지를 같이 봐줘야 함
- Access Control 방법 3가지 정도

1. Access control matrix  
   ![](https://i.imgur.com/RbXiBS5.png)
   - 행렬 행, 열에 각각 사용자, 권한을 기준으로 정리
   - file은 엄청 많을텐데, 하나 사용하려고 행렬 칸 전부 만드는 것은 낭비
   - Linked List 형태로 만드는 것을 생각 가능. 주체에 따라 2가지 나눌 수 있음.
     - file 주체(Access control list): file별로 어떤 사용자에게 접근 권한이 있는지 묶는다.
     - 사용자 주체(capability): 사용자 중심으로 사용자에게 권한 있는 file들을 연결해 놓는다.
   - 어떤 방법이든 하나만 하면 된다.
   - 이렇게 해도 추가적인 overhead가 크다는 문제.  
     => 그래서 일반적으로 Grouping 사용
2. Grouping
   - 모든 사용자에 대해 접근권한 다루는 것이 아니라 file에 대해 접근 권한을 3가지로 분류
   - 소유주, 동일 그룹 사용자, 외부 사용자 3분류
   - 이를 표시하기 위해 총 9개의 bit만 있으면 된다.
   - 권한을 rwx 3bit씩 3그룹으로 표시
   - ex. rwxr--r--(owner/group/other)
3. Password
   - file마다 password. directory에 두는 방법도 가능.
   - 접근 권한 별로 따로 둬야함
   - 기억하기 어려움
   - 암기, 관리 문제

따라서 보통 Grouping으로 한다.

### File System의 Mounting

- 하나의 물리적 disk를 partition(논리적 disk)으로 쪼갤 수 있음. 각각 논리적 disk는 file system 설치해 사용 가능.
- disk1에서 disk3로 접근하고자 한다면?  
  ![](https://i.imgur.com/BYBSio2.png)  
  => mount를 통해 해결

### Access Methods

- 접근 방법
- 순차 접근sequential access: 카세트 테이프. a, b 순서일 때 b를 보려면 a를 지나야 함.
- 직접 접근(임의 접근)direct access, random access: LP 레코드 판

직접 접근을 어떻게 관리하느냐에 따라서 순차 접근만 가능한 경우도 있음

## [파일 시스템 구현 #1](https://core.ewha.ac.kr/publicview/C0101020140520134614002164?vmode=f)

### Allocation of File Data in Disk

disk에 file 저장할 때는 동일 크기의 sector 단위로 나누어서 저장. 외부에서 볼 때는 논리적 block 단위  
임의 크기의 file을 동일 크기의 block 단위로 나눠 저장

- Contiguous Allocation  
  ![](https://i.imgur.com/5NULM8T.png)
  - 하나의 file이 disk 상에서 연속해서 저장
  - 위 그림에서 보면 directory는 file의 metadata를 내용으로 함. file이 5개 있고, 저장 위치와 길이 정보
  - 단점
    - 외부 조각화external fragmentation. 비어있어도 활용되지 않는 공간. hole 문제
    - file은 크기가 중간 중간 바뀔 수 있는데, file grow에 제한이 됨.  
      빈 공간을 미리 확보하는 방법도 있겠지만 내부 조각화가 발생하고 그보다 더 커지면 똑같이 grow 제한
  - 장점
    - Fast I/O  
      하드 disk에서 대부분 시간은 head 이동 시간. 또 head 이동 시간은 file 크기와는 상관 없다.  
      한 번 seek으로 여러 파일 읽어올 수 있다는 장점  
      swapping처럼 공간보다 속도가 더 중요한 경우 or Realtime용 file(deadline이 있는)
    - 직접 접근direct access 가능: Linked에서는 불가능
- Linked Allocation
  ![](https://i.imgur.com/MsZRx1t.png)
  - 연속적으로 저장하지 않고 빈 곳이 있으면 linked list로 연결하는 것
  - 시작 위치만 directory에 있고 이후 주소들은 각 위치마다 있음.
  - 장점
    - 외부 조각 발생하지 않음. 비어있는 곳 어디든 들어가므로.
  - 단점
    - 직접 접근이 안된다. linked list이므로 건너 뛰는 게 불가능하다. 순차접근만 가능.
    - disk head가 다음 위치를 찾기 위해 seek 이동을 계속 해줘야 한다.
    - reliability 문제: 하나의 bad sector => 이후 내용 전부 유실
    - 공간 효율성 문제: 보통 하나의 sector는 512bytes(512bytes/sector). 512byte에서 pointer 위한 4byte를 빼면 508byte. 그러면 512크기를 2개의 sector에 나눠 저장하는 문제도 발생 가능. pointer가 갉아 먹는 용량
  - 변형: 효율적으로 바꿀 수 있다.
    - FAT(File-allocation table) 파일 시스템
- Indexed Allocation
  ![](https://i.imgur.com/jiBISTu.png)
  - 직접 접근이 가능하게 하기 위해서, 위치 정보를 바로 directory가 갖고 있는 것이 아니라 index 정보만 갖고 있도록.
  - index block은 file정보를 담고 있는 것이 아니라 위치 정보를 갖고 있다.
  - 그림에서는 5개의 block에 정보가 저장되어 있음
  - 4번째 접근 위해 3개를 다 볼 필요 없이 바로 10으로 직접 접근 가능
  - hole 문제도 해결
  - 장점
    - 외부 조각 없다.
    - 직접 접근 가능
  - 단점
    - 아무리 작은 file이어도 block이 최소 2개 이상 필요(index block, data block). 따라서 매우 작은 file의 공간 낭비
    - 굉장히 큰 파일의 경우 하나의 index block으로 표시할 수 없음
      해결 방안 1. linked scheme(끝에 다음 index block 주소), 2. multi-level index(2단계 table 사용하듯이)  
      => 하지만 둘다 공간 문제는 있음

실제로는 어떻게 사용하는가?

### UNIX 파일시스템의 구조

![](https://i.imgur.com/vx9TLLJ.png)

- 가장 기본적인 파일시스템 구조. 이후 구조들은 이를 효율적으로 개선
- 하나의 논리적 disk인 Partition
- Boot block
  - 어떤 file system이거 boot block이 가장 먼저 나온다. 약속. 0번 block은 booting에 필요한 정보(bootstrap loader)
- Super block
  - file system에 관한 총체적인 정보
  - 어디가 빈 block이고, 어디가 실제 file이 저장된 사용 중인 block인지 관리. Inode list에 관한 정보도 관리
- Inode(index node) list
  - 앞에서 file의 metadata가 directory에 저장되는 것처럼 설명했지만, 실제로는 directory가 metadata를 다 갖고 있지는 않다.
  - 실제 metadata들은 따로 Inode list에 빼서 보관한다.
  - file 하나 당 inode 하나씩 할당 된다. 해당 file의 metadata를 inode가 갖고 있다.
  - 소유주(owner), 접근 권한, 최종 수정 시각, 위치 정보 등
  - 주로 위치 정보를 집중해서 설명할 것
  - ![](https://i.imgur.com/xq0aabq.png)
  - directory file로 가면 file 이름과 inode 번호를 갖고 있다.
  - 기본적으로 indexed allocation으로 file 위치 정보를 저장하고 있다.
  - inode는 기본적으로 정해져 있음. pointer 개수도 유한.
  - direct로 data로 가는 block(작은 data)
  - 큰 파일은 single indirect, double indirect, triple indirect로 찾아 가도록
  - 대부분 파일은 크기가 굉장히 작고, 큰 파일은 가끔 있기 때문에 효율적인 구조다.
- Data block
  - file 실제 내용 보관

### FAT File System

![](https://i.imgur.com/YchEMgV.png)

- MS에서 MS DOS 만들 때 처음 만든 시스템
- Windows 계열에서 사용, 모바일 기기도 사용하기도
- Boot block
- FAT
  - file의 metadata 중 위치 정보만 FAT에 담는다.
  - 나머지 metadata는 directory가 갖고 있음. file 이름 뿐 아니라 소유주, 접근 권한, 첫번째 위치까지도 directory가 갖고 있다.
  - data block 다음 block 정보를 FAT이라는 별도 테이블에 담고 있다.
  - data block 개수만큼 FAT 배열의 크기가 정해진다.
  - FAT에 담긴 숫자는 다음 block이 어딘지에 대한 정보. 무엇이 담겨있든 상관 없다. 다음 block을 저장하는 것 뿐.
  - 그림에서는  
    217번 block갔다가 FAT의 217-618보고  
    다시 618번 block으로 가고 FAT의 618-339보고  
    다시 339번 block으로 가고 FAT의 339-EOF 끝
  - FAT만 있으면 다음 위치 확인 가능
  - 직접 접근이 가능하다는 장점
    - 4번째 보려면 FAT 내에서 위치 파악해서 바로 이동 가능
  - data block과 FAT 분리  
    => bad data block 문제여도 FAT 정보에는 문제 없어서 유실 되지 않음  
    => reliability 개선
  - FAT은 중요한 정보여서 보통 2 copy 이상 저장하고 있음
- Root directory
- Data block

### Free-Space Management

- 비어 있는 block은 어떻게 관리할 것인가?
- bit map/vector  
  ![](https://i.imgur.com/eA4XkwI.png)
  - 1은 할당된 block, 0은 비어있는 block
  - 부가적인 공간을 필요로 함. 하지만 bit 단위라 그렇게 크지는 않음.
  - 연속적인 n개의 free block 찾는데 효과적
- linked list  
  ![](https://i.imgur.com/36xHrac.png)
  - 비어 있는 block을 linked list로 관리
  - 공간 낭비 없다는 장점
  - 연속적인 공간 찾기는 비교적 힘들다.
  - 실제로는 쓰기가 쉽지 않다.
- grouping  
  ![](https://i.imgur.com/YTxVc4h.png)
  - linked list 변형
  - 비어 있는 위치가 index block과 같은 역할
  - 첫번째 free block에 n개의 pointer
  - linked list보다는 효율적이지만 여전히 연속적 공간 찾기는 쉽지 않다.
- counting
  - 연속적 free block 찾기 쉬움
  - `(first free block, # 연속 free block)`을 쌍으로 유지 관리

### Directory Implementation

- directory 밑에 file의 metadata를 관리하는 특별한 file

어떻게 저장

- Linear list
  - <file 이름, file의 metadata>를 순차적으로 저장
  - 크기가 가변적이지 않고 고정 => 따라서 순차적으로 확인하면 된다. 구현 간단
  - 연산에 대해 시간이 필요. linear search로 찾기 때문에
- Hash Table
  - file의 이름을 그냥 저장하는 것이 아니라
  - hash func => 특정 버무이 내 값이 나옴. Hash function F  
    `1<= F(file name) <=n`
  - hash func으로 구한 값의 entry에 file의 metadata 저장
  - 효율적 사용 가능. search time 제거
  - hash를 이용하기 때문에 collision 발생이 가능하다.

metadata 보관 위치

- directory 내에 직접 저장
- directory는 pointer만, 다른 곳(inode(unix), FAT등)에 저장

긴 file name 지원

- metadata는 보통 고정된 길이. 하지만 file name의 길이가 달라질 수 있다. 제한 할 수 있지만 비효율적
- 길이를 한정해두고, entry 마지막에 거꾸로 넘치는 부분에 저장하고 포인터

### VFS and NFS

![](https://i.imgur.com/xBWvPuR.png)

VFS(Virtual File System)

- 사용자가 file system을 접근할 때는 시스템 콜을 해야한다. 그런데 다양한 file system(FAT, unix등)마다 다른 system call interface를 사용한다면 굉장히 혼란스러움
- 어떤 file system이 실제로 사용되든 상관없이 사용할 수 있도록 VFS 인터페이스 제공
- 사용자 입장에서 동일한 인터페이스 사용할 수 있게 됨.

NFS(Network File System)

- 분산 시스템에서는 네트워크 통해 파일 공유 가능
- local storage에 있는 file system이 아니라 원격에 구성되어 있는 file system에 접근하기 위해서는 네트워크를 통해야 한다.
- 이 때 NFS 인터페이스를 통해 접근
- VFS를 통해 요청하는데, 자신한테 없으므로 NFS가 발동, RPC를 통해 원격 접속
- 서버에서도 이를 받아주는 인터페이스 존재
- 요청에 대한 응답을 보내준다.
- client, server 모두 NFS 모듈이 있어야 한다.

## [파일 시스템 구현 #2](https://core.ewha.ac.kr/publicview/C0101020140523142954456205?vmode=f)

### Page Cache and Buffer Cache

![](https://i.imgur.com/PR0t7dq.png)

- 보통 page 하나는 4kb, block 하나는 512bytes

Page Cache

- 캐싱의 관점에서 가상 메모리 page 시스템에서 사용하는 page frame을 page cache라고 부른다.
- swap area보다 빠르다.
- Virtual Memory System 관점에서의 용어
- OS에 주어지는 정보 제한적
- 보통 4kb 단위

Memory-Mapped I/O

- file에 접근할 때 원래은 open()해주고 read()/write() 시스템콜을 통해서 해야하는데,
- file 일부를 Virtual memory에 mapping 시켜놓고 사용하는 것
- 시스템 콜을 통해 read/write하는 것이 아니라, memory에 읽고 쓰는 작업임에도 file에 data를 읽고 쓰게 하는 효과
- 메모리 접근 연산을 통해 파일의 입출력 수행

Buffer Cache

- file system에 들어와서  
  file에 대해 사용자 요청을 disk에서 읽고 전달하고 끝이 아니라,  
  읽어온 내용을 OS가 자신의 영역 일부에 저장해두고  
  똑같은 요청이 오면 disk까지 가지 않고 Buffer Cache를 읽는다.
- File System 관점에서의 용어
- 어차피 System Call이 있기 때문에 OS가 모두 알고 관리 가능
- block은 보통 512B.

Unified Buffer Cache

- 최근에는 Page cache와 Buffer cache를 합쳐 같이 관리하는 OS가 많다. Linux도
- 같이 관리하면서 기존 보통 512B로 관리하던 buffer cache도 4kb로 관리
- buffer cache, page cache 미리 공간 구분하지 않고 같이 Page 단위로 관리. 필요할 때 file이 필요하면 page를 buffer cache로 사용하기도 하면서 사용.
- 미리 나누지 않고 사용하다가 맞춰서 준다.

![](https://i.imgur.com/3EknOMe.png)

- 기존 file data 읽고 쓰는 방법은 2가지
  1. read()/write() 시스템 콜
  2. memory-mapped I/O  
     memory-mapped I/O 시스템콜  
     => 주소공간 일부를 file에 매핑.  
     => buffer cache 저장까지는 똑같음.  
     => 그리고 page cache에 copy  
     => 사용자가 page cache에다가 메모리 읽고 쓰듯이 요청하면, read/write가 된다.  
     커널의 도움 없이 file 읽고 쓴다.
  - read()/write() 할 때는 buffer cache에 있든 없은 시스템 콜을 해줘야 한다.
  - memory-mapped를 사용하면, page cache에 올라온 내용은 OS의 도움 없이 사용자가 직접 자신의 주소에서 사용한다.
  - 기존 방법인 양쪽 모두 file 입출력에서 buffer cache를 반드시 통과함
  - buffer cache를 page cache에 적재해야 하는 overhead 발생
- 최근 Unified Buffer Cache에서는 경로가 단순해진다.
  - read()/write() 시스템 콜: disk에 있든, buffer cache(=page cache)에 있든 OS에 CPU 제어권 넘어감. OS가 이미 올라와 있나 확인, disk로 가든 cache에 있던거 주던지 함.
  - memory-mapped I/O: mapping 거치고 난 후에는 사용자 프로그램 주소에 buffer cache(=page cache)가 이미 mapping되어 있는 것. 이 프로그램은 page cache에 직접 읽고 쓰고 할 수 있다.  
    좀 더 단순한 과정

### 프로그램의 실행

![](https://i.imgur.com/RIFYcbC.png)

- 사실 Process 주소 공간의 code 부분은 메모리에 올라간 후 쫓겨날 때 swap area로 내려가지 않는다.
- Why? code 부분은 read-only. code는 file system 내 실행 파일에 저장되어 있다. 그래서 swap area에 써줄 필요가 없다.
- 여기에서 Memory Mapped I/O가 나오는데, 실제로 실행 파일 실행 시킬 때 loader라는 SW가 Memory Mapped I/O를 사용하는 부분이 바로 Process의 code 부분이다.
- code는 별도의 swap area를 가지고 있지 않고, file 내의 코드가 그대로 Process에 mapping.
- 실행하다가 메모리에 없는 code를 만나면 swap area로 가지 않고 file system에서 가져온다. mapping되어 있는 file에서 가져온다. 대표적 예시

![](https://i.imgur.com/xvFT31k.png)

- mmap() 시스템 콜로 mapping 요청

![](https://i.imgur.com/p4mOHN7.png)

- mapping 이후는 데이터 파일 가져올 때 메모리에 접근해서 가져옴.
- 메모리에서 쫓겨난다면, file에다가 변경 내용 기록하고 삭제

![](https://i.imgur.com/ykEaw5e.png)

- A도 요청한다면 같은 내용이 이미 메모리에 있고, buffer cache 역할을 해준다.

![](https://i.imgur.com/3iBnJWX.png)

- buffer cache(=page cache) 내용을 copy해서 A에게 전달.
- B는 가져오는 것이 아니고 Process 내에 이미 mapping 되어 그냥 사용
- mmap을 하면 시스템콜 없이 Process 내에서 해결 가능한 copy overhead가 없는 장점
- 이미 올라와 았는 내용은 OS의 도움을 받을 필요가 없다
- 주의할 점: 같은 file을 여러 Process가 mapping한다면 즉, share한다면 일관성consistency 문제 발생. A에서는 가져와 읽기만 해서 이런 문제 없음
