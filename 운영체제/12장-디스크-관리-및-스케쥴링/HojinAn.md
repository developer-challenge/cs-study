## [디스크 관리 및 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140523151255773807?vmode=f)

### Disk Structure

- Disk Scheduling
- 최소의 단위는 sector. 내부에서 관리
- 읽고 쓰는 요청은 disk controller가 관리
- 외부 접근 단위는 logical block 단위로 disk를 바라보게 된다. sector에 mapping되어 들어간다.
- disk 접근은 logical block 단위로, 1차원 배열처럼 취급
- 어떤 조각에 logical block을 위치 시킬 것인가 결정.
- 특별히 sector 0은 logical block에서도 0번에 해당. 어떤 시스템이든 booting 관련 정보는 0번 sector에 담긴다.

### Disk Management

- physical formatting(low-level formatting)  
  공장에서 disk를 처음 만들게 되면, disk controller가 읽고 쓸 수 있는 sector 단위로 나눠 놓는다. => 이 과정이 formatting.

  - 각각의 sector는 외부 요청이 들어오는 logical block을 저장하기도 하지만 logical block 외의 부가적 저장 공간이 따라 붙는다.  
    header + 실제 data(보통 512B, logical block) + trailer
  - header, trailer에 잘 저장되었는지를 보는 ECC(Error-Correcting Code)나 주소 mapping 위한 sector 번호를 부가적으로 저장한다. 하나의 data를 저장하기 위해서 metadata가 필요하듯이
  - ECC: data 저장할 때 512bytes data만 저장하는게 아니라 data를 축약한 finger print와 같이 작게 요약한 정보. header, trailer에 같이 저장.  
    꺼내갈 때 header, trailer 같이 읽혀서 저장되어 있던 ECC 값과 저장되어 있는 data에 대해서 만든 ECC 값을 비교  
    => 같다면 잘 저장되어 있다는 뜻. 다르면 저장 과정에서 문제가 생긴 것.  
    모든 에러를 검출할수는 없어도 좋은 함수라면 상당 부분 많은 에러를 검출 가능
    - ECC를 어느 규모로 두느냐에 따라서 에러를 수정까지 할 수도 있고 검출만 할 수도 있고. 너무 크면 수정 못하겠지만 어느정도 작다면 ECC를 통해서 수정까지 가능

- Partitioning

  - 하나의 독립적인 logical disk로 만들어주는 작업
  - 하나의 disk를 하나 이상의 logical disk로 만든다.
  - OS는 물리적 disk에는 관심 없고 logical 디스크를 다룬다.

- Logical formatting

  - 각각의 Partition을 file system나 swap area로 쓸수 있다.
  - 해당 Partition에 file system을 설치하는 것을 logical formatting이라고 부른다.
  - FAT, Inode, free space 등 구조 포함

- Booting
  - 처음 전원을 켜면 메모리는 다 비어져 있다.
  - CPU는 메모리만 접근 가능하지 disk는 접근할 수 없다. 어떻게 이루어지나?
  - 메모리 영역 중 전원이 나가도 내용이 유지되는 ROM이라는 소량의 메모리 영역이 있다. 부팅을 위한 아주 간단한 'small bootstrap loader'가 있음.
  - 전원을 켜면 CPU의 제어권이 처음에 ROM을 가리킴
  - ROM의 small bootstrap loader가 CPU에서 instruction 형태로 실행  
    => hard disk 0번 sector 내용(boot block)을메모리에 올리고 실행하라고 지시
  - boot block => file system에서 OS 커널 위치를 찾아서 메모리에 올려서 실행하라고 시킴

### Disk Scheduling

- 접근 시간Access time의 구성
  - 3가지로 구성
  - Seek time: disk head가 읽고 쓰고 요청한 track으로 이동하는 시간
    - 해당 cylinder 위치로 원 바깥에서 안쪽으로 head 이동하며 track 찾는다.
    - 가장 큰 시간 요소. 기계 장치가 이동을 하는 것이므로 상당히 시간이 오래 걸림
  - Rotational latency
    - 해당하는 위치로 이동을 했다면 disk는 계속 회전을 하고 있고
    - 해당 cylinder 이동 후 해당 sector로 회전하는데 걸리는 시간
    - seek time보다는 10분의 1정도로 작은 시간
  - Transfer time
    - 실제로 data를 읽고 쓰는 시간. 즉, 전송하는 시간
    - 오래 걸릴 것 같지만 실제로 굉장히 작은 시간
- 실제로 Seek time이 가장 오래 걸리기 때문에 고속 입출력을 위해서는 한번의 seek로 많은 양을 transfer하는 것이 좋다.
- 매번 seek하면 비효율적
- disk bandwidth: disk 성능 나타내는 용어. 단위 시간당 disk로부터 전송되는 byte 수가 얼마인지를 표현
- disk bandwidth가 높아지려면(=효율적이려면) 가능한 한 seek time을 줄이는 게 좋다. 거의 seek time에 의해 좌우 됨.
- 그래서 disk scheduling이 필요. 가능한 seek time 줄여서 bandwidth 높이려는 목적.
  - disk 요청 들어온 순서대로 하면 비효율적. 늦게 들어와도 안쪽 요청이 쌓여 있다면 먼저 처리하고 바깥으로 나가고. 전체적인 이동 거리 줄이는 방향

### Disk Scheduling Algorithm

알고리즘이 구현되는 곳이 disk 내부가 아니기 때문에 I/O 스케쥴러가 disk보다 위쪽에 OS쪽에 존재, 정확한 disk 상 위치는 모를 수 있음. cylinder의 정확한 물리적 위치는 몰라도 논리 block이 sector에 mapping 되어 있으므로 그것으로 해겨하게 된다. disk 내부에서도 스케쥴링 할 수 있는 방법이 있긴 함. 구현에서는 잘 쓰지 않는다. `실린더 번호`로 요청을 처리

- FCFS(First Come First Service)

  ![](https://i.imgur.com/89lNiPw.png)

  - 그냥 들어온 순서대로 처리. 안밖이 번갈아 들어오면 많은 이동, 낮은 요청 처리. 대단히 비효율적이 될 수 있다.

- SSTF(Shortest Seek Time First)

  ![](https://i.imgur.com/wVPws61.png)

  - 현재 head 위치에서 가장 가까운 요청 먼저 처리. 이동거리가 줄어드는 것은 사실.
  - starvation 문제가 발생할 수 있다.

- SCAN

  ![](https://i.imgur.com/Ma9CnEi.png)  
  ![](https://i.imgur.com/PwUMZUi.png)

  - 가장 간단하면서도 획기적인 방법.
  - 엘리베이터 스케쥴링이라고도 부름.
  - queue 요청에 상관 없이 상황에 좌우 받지 않고 그냥 가장 안에서 바깥, 바깥에서 안으로 반복하며 이동
  - head 이동 거리도 짧아지고, starvation 문제도 발생하지 않음.
  - 비교적 공정하면서도, 이동 거리 측면에서도 효율적.
  - 위치에 따라서 대기시간이 다르다는 문제가 있음. 가운데 부분은 기다리는 시간의 기대가 짧다. 왕복해서 돌아오는 시간이 비교적 짧으므로. 가장 자리는 한번 돌아오기까지 계속 기다려야 한다. 즉, 대기 시간의 편차 문제.

- C-SCAN

  ![](https://i.imgur.com/XZDfj3m.png)

  - Circular SCAN의 줄임말.
  - disk head가 처리하면서 지나가고, 반대쪽으로는 그냥 지나가고.
  - 이동 거리는 다소 길어질 수 있으나, queue에 들어온 요청들의 대기 시간이 균일해지는 특징.

- Other Algorithms
  - 기본적으로는 SCAN을 base로 함
  - N-SCAN: head가 가기 전 요청들은 처리하고 중간에 들어오는 것은 돌아오는 이동 때 처리. 들어오는 도중 요청은 그 회차에는 처리하지 않는 방법. 요청을 기다리는 시간의 분포가 약간 균일해지는 특징
  - LOOK & C-LOOK
    - SCAN의 비효율을 개선
    - 이동하던 중 그 방향에 더 이상 요청이 없다면 방향을 바꾼다.
    - 각각 SCAN과 C-SCAN을 개선

### Disk-Scheduling Algorithm의 결정

- 엘리베이터를 생각해보자.
- 어떤 스케쥴링이 좋은 알고리즘인가
- 현대 시스템에서는 SCAN에 기반한 알고리즘들을 많이 쓰고 있음. disk head 이동거리를 줄일 수 있으므로
- file을 어떻게 할당하느냐도 성능에 영향을 미침. 연속할당, 산발 할당 등.
- 한꺼번에 처리하는 특징. disk I/O 성능 향상
- OS 커널 내부 모듈과는 별도로 만들어 필요할 때 다른 알고리즘으로 교체해서 사용하도록 하는 것이 바람직하다.

## [디스크 관리 및 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140527124647396004?vmode=f)

### Swap-Space Management

- 하드 disk를 사용하는 이유
  1. 메모리가 휘발성이기 때문 => file system(영속적 유지)
  2. DRAM 메모리 공간이 한정 => swap area(메모리 연장 공간 느낌으로)
- Swap-space
  - 물리적 disk를 partitioning해서 logical memory로 만든다.
  - 각각의 logical disk는 OS가 독립적인 disk로 간주
  - 각각의 logical disk에다가 file system이나 swap area로 사용 가능
  - file system
    - 512byte의 sector 단위로 data 저장
    - 방식은 여러가지 continuous allocation, linked allocation, indexed allocation
    - FAT, unix
  - swap area
    - 프로그램 실행동안 swap area에 머물러 있던 process 주소 공간은 프로그램 종료시 어차피 사라질 내용이다.
    - 물리적 메모리의 연장 느낌이기 때문에 쫓아낼 때도 최대한 빨리 쫓아 내야 하고, swap area 내용이 page fault 날 때도 최대한 빨리 메모리에 올려줘야 한다.
    - diks 접근 시간의 대부분은 seek time. 따라서 swap area에서는 공간 효율성보다는 시간 효율성에 집중한다. 어차피 사라질 내용들이므로 공간 효율성은 중요하지 않다.
    - 따라서 시간을 줄이기 위해서 매우 큰 단위의 data를 순차적으로 올리고 내리도록 한다.
    - file system에서는 512byte였던 데에 반해, swap area에서는 보통 512KB 대용량으로 올리고 내리고 해준다. 경우에 따라 조금 다르게 한다.
    - 짧은 시간에 빠르게 서비스가 될 수 있도록

### RAID

- Redundant Array of Independent Disks
- 저렴한 disk 여러개를 묶어서 같이 사용하는 것.
- 방식이 여러개
- 여러 disk에 data를 어느 정도 중복 저장할 것인가 등
- 중복 저장하기도 분산 저장하기도

- 장점 2가지 정도

  1. 디스트 처리 속도 향상: 중복 저장시 host 컴퓨터에서 data 읽어오라 요청하면 여러 군데에서 병렬적으로 조금씩 조금씩 읽어올 수 있다. 서로 협력 => 더 빠름  
     기법: interleaving(여기저기서 조금씩 읽어오는 방법), striping(조금씩 나눠서 조금씩 저장하는 방법)
  2. 신뢰성 보장: 중복 저장하다보면 하나에서 failure 발생해도, 멀쩡한 disk에서 읽어올 수 있기 때문에 reliability 올릴 수 있다.  
     => Mirroring, shadowing

- 통째로 중복 저장하는 단순 RAID도 있고, 오류 알고 복구 가능할 정도로만 parity를 저장하는 방법도 있음. 공간 효율도 높이고 신뢰성 올리는 방법도 사용
