# [디스크 관리 및 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140523151255773807?vmode=f)

## Disk Structure

> logical block과 sector 매핑

- logical block
  - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
  - 주소를 가진 1차원 배열처럼 취급
  - 정보를 전송하는 최소 단위
  - sector에 매핑
- Sector
  - 디스크 컨트롤러가 쓸 수 있는 단위
  - 디스크를 구성하는 최소 단위
  - Logical block이 물리적인 디스크에 매핑된 위치
  - sector 0은 최외곽 실린더의 첫 트랙에 있는 첫번째 sector
    - 0 : booting 정보

## Disk Management

### Physical formatting (Low-level formatting)

- 디스크를 컨트롤러가 읽고 쓸 수 있도록 sector들로 나누는 과정
- 각 sector는 header + 실제 data(보통 512 bytes) + trailer로 구성
- header와 trailer는 sector number, ECC(Error-Correcting Code, 축약된 코드 - 에러 검출 및 수정)등의 정보가 저장되며 controller가 직접 접근 및 운영

### Partitioning(파티셔닝)

- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- OS는 이것을 독립적 disk로 취급 (logical disk)

### Logical formatting

- 파일 시스템을 만드는 것
- swap area로도 사용 가능
- FAT, inode(UNIX), free space 등의 구조 포함

### Booting

- CPU는 하드디스크에 접근할 수 없음
  - CPU제어권 → ROM
- **ROM**에 있는 “small bootstrap loader”의 실행
  - sector 0(boot block)을 load하여 실행
  - sector 0은 “full Bootstrap loader program”
    - 파일 시스템에서 운영체제 커널을 올려서 실행하게 함
- OS를 디스크에서 load하여 실행

## Access time의 구성

> 디스크를 읽고 쓰려면?

- Seek time
  - 헤드를 해당 실린더(track)로 움직이는데 걸리는 시간
- Rotational Latency
  - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전 지연 시간
  - Seek time보다 적은 시간
- Transfer time
  - 실제 데이터의 전송 시간
  - 굉장히 작은 시간

## Disk Scheduling

- Seek time을 최소화하는 것이 목표
- Seek time , seek distance

## Disk Scheduling Algorithm

- FCFS(First Come First Service)
- SSTF(Shortest Seek Time First)
- SCAN
- C-SCAN
- Other Algorithms

## Disk-Scheduling Algorithm의 결정

### FCFS(First Come First Service)

> 들어온 순서대로 처리

- 비효율적

### SSTF(Shortest Seek Time First)

> 현재 헤드에서 가장 가까운 위치

- 이동 거리가 줄어들긴 하지만
- starvation 발생 가능성

### SCAN

> 엘리베이터 스케줄링
> 주로 사용

- disk arm이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동한다.
- 장점
  - 이동거리가 짧아짐
  - 비교적 공정
- 문제점
  - 실린더 위치에 따라 대기 시간이 다르다.

### C-SCAN(Circular SCAN)

> 비교적 공정한 SCAN

- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
- 장점
  - 이동거리 다소 길어지지만 SCAN보다 균일한 대기 시간 제공

### Other Algorithms

- N-SCAN
  - SCAN의 변형 알고리즘
  - 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service
  - 장점
    - 큐에 들어온 job들의 대기시간 공정
- LOOK and C-LOOK
  - SCAN이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동
  - LOOK과 C-LOOK은 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 한다.

## Disk-Scheduling Algorithm의 결정

> SCAN 기반

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음
- File의 할당 방법에 따라 디스크 요청이 영향을 받음
  - 연속 할당 vs 산발 할당
- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직

# [디스크 관리 및 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140527124647396004?vmode=f)

## Swap-Space Management

- Disk를 사용하는 두 가지 이유
  - memory의 휘발성(volatile)한 특성 → file system처럼 유지해야 함
  - 프로그램 실행을 위한 memory 공간 부족 → swap space (swap area)
    - mpd 증가에 따라 swap area의 용도
- Swap-space
  - 물리적 디스크 → 파티셔닝 → swap area
  - Virtual memory system에서는 디스크를 memory의 연장 공간으로 사용
  - 파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
    - 공간 효율성보다는 속도 효율성 우선
      - 물리 공간의 연장선. 빠르게 해제되어야 함
      - seektime 줄여야함
      - 없어질 내용이므로 공간 효율성 < 속도 효율성
    - 일반 파일보다 훨씬 짧은 시간 존재하고 자주 참조됨
    - 따라서 blockdml 크기 및 저장 방식이 일반 파일시스템과 다름
      - 큰 단위. 짧은 시간 빠른 서비스

## RAID

- Redundant Array of Independent Disks
  - (저렴한) 여러 개의 디스크를 묶어서 같이 사용
  - 중복 저장
- 사용 목적
  - 디스크 처리 속도 향상
    - 여러 디스크에 block의 내용을 분산 저장
    - 병렬적으로 읽어옴(Interleaving, striping)
  - 신뢰성(reliability) 향상
    - 동일 정보를 여러 디스크에 중복 저장
    - 하나의 디스크가 고장(failure) 시 다른 디스크에서 읽어옴(Mirroring, shadowing)
    - 단순한 중복 저장이 아니라 일부 디스크에 **parity**를 저장하여 공간의 효율성을 높일 수 있다.
      - parity : 간략하게 저장
        - 오류 확인
