[디스크 관리 및 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140523151255773807?vmode=f)

# Disk Structure

## Logical block

- 디스크의 **외부**에서 보는 디스크의 단위 정보 저장 공간들
- 주소를 가진 1차원 배열처럼 취급
- 정보를 전송하는 최소 단위

## Sector

- 디스크를 **내부**에서 관리하는 최소 단위 -> 읽고쓰는 요청은 디스크 관리자가
- logical block이 물리적인 디스크에 매핑된 위치
- sector 0 은 최외곽 실린더의 첫 트랙에 있는 첫번째 섹터 (logical에서도 0번)
  - 어떤 파일 시스템을 쓰던간 부팅과 관련된 정보 포함

# Disk Management

## physical formatting (low-level formatting)

- 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
- 각 섹터는 `header` + `실제 데이터 (512bytes)` + `trailer` 로 구성
  - data : 디스크 내부의 sector에 저장
  - header, trailer : 주소 변환을 위한 섹터 번호, 에러 처리 등 데이터가 잘 들어갔는지 확인하기 위한 정보 저장
- 헤더와 트레일러는 sector number, ECC(Error-correcting code) 등의 정보가 저장되며 controller가 직접 접근 및 운영

> ECC : 데이터에 대한 축약된 코드 ex)해쉬 함수 -> 체크썸 같은 느낌
> 모든 오류를 다 잡아내진 못함.

## Partitioning

- 나눠진 섹터들을 하나로 합침.
- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- OS는 이것을 독립적 disk로 취급 (logical disk)

> 파티셔닝의 용도
>
> 1. 파일 시스템
> 2. swap area

## Logical formatting

- 파일 시스템을 만드는 것
- FAT, inode, free space 등의 구조 포함

## Booting

파일 시스템 내부의 운영체제가 메모리에 올라오는 것 = booting

- ROM에 있는 small bootstrap loader의 실행
- sector 0 (boot block)을 load(메모리에 올림)해 실행
  - -> 파일시스템에서 운영체제 커널의 위치를 찾아 메모리에 올림
- sector 0 : full bootstrap program
- OS를 디스크에서 load해 실행

> CPU에는 메모리만 접근 가능, 하드디스크는 접근 불가

# Disk Scheduling

디스크 접근 시간

![](https://i.imgur.com/Dme6J1E.png)

## Access time의 구성

### seek time

헤드를 해당 실린더로 움직이는데 걸리는 시간 -> 가장 오래 걸림

> 해당 track (cylinder)으로 이동하기 위해선 기계 자체가 움직여야 함.

### rotational latency

헤드가 원하는 섹터에 도달하기까지 걸리는 회전 지연 시간

seek time / 10 = rotational latency

### transfer time

실제 데이터의 전송 시간

굉장히 적은 시간이 소요
고속 transfer의 경우 한번 seek에 많은 tranfer

## Disk bandwidth

단위 시간 당 전송된 바이트의 수
높을 수록 효율적

## Disk scheduling

seek time을 최소화하는 것이 목표
seek time = seek distance

# Disk Scheduling Algorithm

디스크 스케쥴러는 logical block을 보며 스케쥴링 (sector의 정확한 위치를 알지 못함)

## FCFS(First Come First Service)

들어온 순서대로 처리

![](https://i.imgur.com/IR49ioc.png)

## SSTF (Shortest Seek Time First)

현재 헤드 위치에서 가장 가까운 위치부터 처리

![](https://i.imgur.com/IkFWXaI.png)

> `starvation` 문제 발생

## SCAN (= 엘리베이터 알고리즘)

disk arm이 디스크 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리

한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청 처리

대부분의 알고리즘이 SCAN을 기반으로 동작

> 실린더 위치에 따라 대기 시간이 다름

## C-SCAN (circular scan)

![](https://i.imgur.com/6NbjLpQ.png)

헤드가 한쪽 끝에서 다른쪽으로 이동하며 (한 방향으로만) 가는 길문에 있는 모든 요청을 처리

SCAN보다 균일한 대기 시간 제공

## Other Algorithms

### N-SCAN

- SCAN의 변형 알고리즘
- 지나는 도중에 들어온 요청은 다음 이동때 처리

### LOOK and C-LOOK

기존 SCAN의 경우 무조건 끝으로 이동해야함.

LOOK and C-LOOK은 이동 중 더이상 요청이 없다면 끝으로 이동하지 않고 방향 변경

![](https://i.imgur.com/s9KStbu.png)

# Disk-Scheduling Algorithm의 결정

SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음

File의 할당 방법에 따라 디스크 요청이 영향을 받음

- 요청을 merge해 한꺼번에 처리하는 방식 사용

디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직

[디스크 관리 및 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140527124647396004?vmode=f)

# Swap-Space Management

## Disk를 사용하는 두 가지 이유

- memory의 휘발성 -> 해결 방법 : `file system`
- 프로그램 실행을 위한 메모리 공간 부족 -> 해결 방법 : `swap space (swap area)`

## Swap-space

- virtual memory system에서는 디스크를 메모리의 연장 공간으로 사용
- 파일 시스템 내부에 둘 수도 있으나 별도 파티션 사용이 일반적
  - seek time을 줄여 공간 효율성 < 속도 효율성
    - 어처피 사라질 내용이니 공간 효율성이 덜 중요
  - 일반 파일보다 짧은 시간 존재 및 자주 참조
  - block의 크기 및 저장 방식이 일단 파일 시스템과 다름

![](https://i.imgur.com/2tO3FCA.png)

# RAID

여러개의 디스크를 묶어서 사용

## 사용 목적

### 1. 디스크의 처리 속도 향상

- 여러 디스크에 block의 내용을 **분산 저장**
- 병렬적으로 읽어 옴 (interleaving, striping)

### 2. 신뢰성 향상

- 동일 정보를 여러 디스크에 **중복 저장**
- 하나의 디스크가 고장나도 다른 디스크에서 읽어오기 가능 (mirroring, shadowing)
- 중복 저장 방법
  - 1. 전부 중복 저장
  - 2. 에러시 해결 할 수 있을 정도만 중복 저장 (parity)
