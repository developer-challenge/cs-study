## [프로세스 관리 #1](https://core.ewha.ac.kr/publicview/C0101020140321144554159683?vmode=f)

### 프로세스 생성 (Process Creation)

- 누가 Process를 만드는가? -> 부모 Process가 자식 Process를 만든다. 보통 `복제 생성`(stack, code, data, PC, register 모두 그대로). 여러 자식을 두기 때문에 트리(계층 구조)를 갖는다.
- 자원을 1. OS로부터 받거나 2. 부모와 공유한다.
- 자원의 공유
  1. 부모 자식이 모든 자원 공유
  2. 일부 공유
  3. 전혀 공유 X -> 원칙적으로는 공유하지 않음
- 부모가 자식 프로세스를 만드는 순간부터 별도의 프로세스기 때문에 서로 경쟁하는 사이가 된다. CPU, 메모리 점유를 위한 경쟁.

> `Copy-on-write(COW)`  
> Linux 등 효율적 OS에서는 메모리 낭비를 줄이고자 가능한 것들은 공유하게 하려고 한다. PC만 copy해서 같은 공간 가리키는 식. 내용이 달라지면 각자의 길을 가게되면, 그 때서야 다른 공간 가리킴  
> write가 발생해야 copy하겠다~. 그 전까지는 부모의 것을 그대로 사용하자. 잘게 쪼개서 달라진 부분만 copy 뭐 이런 식. 암튼 가능하면 공유하면 효율적. 하지만 원칙은 복제다.

수행

- 부모 자식 공존하며 수행되는 모델
- 자식 종료(terminate)까지 부모가 기다리는(wait) 모델

- 자식이 부모의 주소 공간을 복사(binary ans OS data) -> 새로운 프로그램을 덮어 씌울 수 있다.

`fork()` 시스템 콜(OS를 통해서만 가능하기 때문)을 통해 새로운 Process 생성

- 부모 그대로 복사
- 주소 공간 할당

`exec()` 시스템 콜 통해 새로운 프로그램을 메모리에 올림

보통 프로세스 생성 과정은 fork(), exec()의 두 단계로 이루어진다.

### 프로세스 종료 (Process Termination)

- `exit` 시스템 콜로 종료를 OS에 알려 줌(Process 마지막 명령 수행 후 , 자발적)
  - Process 세계에서는 자식이 부모보다 먼저 종료되는 특징
  - 자식이 부모에게 output data 보냄(via `wait`)
  - 각종 자원 OS에 반납
- 부모가 `abort` 시스템콜로 자식 종료(비자발적, 강제 종료)
  - 자식이 할당 자원 한계치 넘어섬
  - 자식에 할당된 task가 더 이상 필요하지 않음
  - 부모가 종료(exit)되는 경우(자식이 먼저 종료되어야 하므로 자식 모두 종료하고 자신이 종료된다) : 단계적인 종료

## [프로세스 관리 #2](https://core.ewha.ac.kr/publicview/C0101020140325134428879622?vmode=f)

### 프로세스와 관련한 시스템콜

#### fork()

`create a child(copy)`

![](https://i.imgur.com/eryBK9b.png)

자식 Process의 경우 부모의 fork()가 실행된 이후의 시점부터 기억해서 실행하기 때문에 무한히 fork()가 이루어지지 않는다.

파생 문제

1. 복제본과 부모가 구분되지 않는 문제
2. 똑같은게 만들어지므로 똑같은 제어흐름을 따르게 되는 문제

=> 이를 해결하기 위해 부모와 자식을 구분해준다. fork() 실행시 return 값이 다르다.  
부모의 경우 fork() returns(=pid) 양수
자식의 경우 fork() returns 0

결국 pid 값으로 구분. 이를 통해서 서로 다른 제어흐름을 가도록 할 수도 있다.
부모가 fork() 전에 무언가 실행했다면, 그 시점을 기억한채로 fork()가 이루어지기 때문에 자식에서는 그 무언가가 실행되지 않는다.

#### exec()

`overlay new image` -> 도커..!

복제를 통해 생성했더라도 다른 프로그램을 실행할 수 있도록 해야할 텐데, 이때 필요한 것이 `exec()`
프로그램이 완전 새로운 Process로 태어나게 해주는 역할.

![](https://i.imgur.com/UZrY0fV.png)

execlp를 만나면 완전히 새로운 프로그램으로 덮어씌운다. exec 내에 있는 프로그램의 완전 시작부터 새로 하는 것  
한번 exec한다면 되돌아올 수가 없다.
하지만 꼭 자식을 만들어야만 exec() 할 수 있는 것은 아니다. 그냥 fork() 안하고 exec할 수도 있다. 짚고 넘어가면 좋은 것이, exec() 이후의 코드들은 실행되지 않는다. 절대 되돌아올 수 없다.

#### wait()

`sleep until child is done`

Process가 sleep 되도록 한다(blocked 상태)

보통 부모 Process가 자식을 만들고 wait() 시스템 콜 호출 -> 자식 종료될 때까지 커널 => 부모 sleep(blocked) -> 자식 종료시 커널 => 부모 awaken(ready)

위 그림대로 한다면 pid > 0일 경우 wait()를 넣는다. -> 그러면 child들 실행이 종료될 때까지 기다렸다가 다 종료되고 나면 wait()를 빠져나온다.

보통 shell prompt들이 자식 생성 후 wait()로 기다리는 모델

#### exit()

`frees all the resources, notify parent`

프로세스 종료 시킬 때 호출하는 시스템 콜

명시적으로 할 수도 있고, 컴파일러가 적절한 위치에 넣어주기도 한다.

- 자발적 종료
  - 마지막 statement or 명시적 exit statement
  - 명시적으로 적지 않아도 main return 자리에 컴파일러가 넣어준다.
- 비자발적 종료
  - 부모가 자식 죽이는 경우
    - 할당 자원 넘어서
    - 필요하지 않아서
  - 사람이 키보드로 kill, break (Ctrl+c)
  - 부모가 본인이 종료하는 경우
    - 부모 종료 전에 자식 먼저 종료

### 프로세스 간 협력

- 원칙적으로 Process는 대단히 독립적

독립적 프로세스(Independent process)

협력 프로세스(Cooperating process)

프로세스 간 협력 메커니즘(IPC: InterProcess Communication)  
즉, 프로세스 간 정보를 주고 받는 법

1. message passing
2. shared memory

> thread  
> 정확히 Process간 협력으로 보기는 어렵지만, thread끼리는 공유가 굉장히 쉬움. 따라서 thread 간은 협력이 가능

### Message Passing

- A -> B, B -> C, ... 프로세스는 원체 독립적(공유 변수 없음)이라 직접 전달할 수는 없고, `커널`을 통해 전달한다.

2가지 방식. 공통적인 것은 커널을 통해서 전달한다는 것

1. Direct Communication

- 명시적 표시(통신하고자 하는 Process 이름)

2. Indirect Communication

- mailbox(또는 port)에 메시지 간접 전달
- 그럼 누가 꺼내보는가? 명시를 안해서 아무나 꺼내볼 수 있음.

### Interprocess communication

![](https://i.imgur.com/ILpycAj.jpg)

- shared memory로 작업하면, 프로세스간 공유하는 메모리 영역이 있다. 물리적 메모리를 일부 맵핑한 것.
- shared memory하려고 해도 커널에게 이야기해서 맵핑하고 share 하도록. 커널이 한번 묶어주고 나서는 그 이후는 프로세스간 신뢰하면서 작업해야함

### CPU and I/O Bursts in Program Execution

어떤 프로그램이든지 다음과 같은 과정을 거친다.
![](https://i.imgur.com/mcXVcZq.png)
파일 읽어오는 오래 걸리는 작업... 등등 I/O 기다리다가(wait) CPU instruction 쭉 쓰다가, ...  
결국 I/O하는 단계, CPU 계속 작업하는 단계가 번갈아가면서 수행된다.

- CPU burst, I/O burst 두 가지로 나눠 볼 수 있다.
- 결국 프로그램이 실행된다는 것은 CPU burst와 I/O burst가 반복적으로 이루어지는 것이라고 볼 수 있다. 단, 프로그램 종류따라 빈번하게 이뤄지는 경우, 빈번하지 않은 경우가 있다.
- 사용자 interactive한 프로그램은 빈번한 프로그램
- 과학 계산용 프로그램은 한번 입력 받은 것을 오랫동안 CPU burst이므로 빈번하지 않다.

### CPU-burst Time의 분포

![](https://i.imgur.com/1Wcvt0p.png)

- CPU burst가 짧은 경우가 많고, 긴 경우가 적더라
- CPU burst가 짧은 것을 I/O bound job, 긴 것을 CPU bound job이라 부른다.
- CPU를 I/O bound job이 많이 사용한다는 해석을 적절해 보이지 않음. 다만 짧게 자주 쓰는 것일 뿐. job의 종류가 섞여있다 정도.
- 여러가지 방식의 job이 있다는 사실 여기에서 CPU scheduling이 필요. 공평보다는 효율적 사용

### 프로세스의 특성 분류

- I/O bound process
  - I/O에 많은 시간 필요한 job
- CPU bound process
  - 계산 위주의 job
  - CPU 연속 사용 시간 큼

### CPU Scheduler & Dispatcher

- CPU Scheduler: 누구에게 CPU 줄지를 결정하는 것
- Scheduler는 HW, SW인가? 사실은 OS 안에서 CPU 스케줄링을 해주는 code가 있는 것. 그 부분을 그냥 Scheduler라고 한다. Dispatcher도 마찬가지.
- Dispatcher: 누구에게 줄지 결정 됐으면, `CPU를 넘겨주는 역할`

스케쥴링 언제 필요한가?

1. Running -> Blocked: I/O 요청(시스템 콜)시 어차피 CPU 못쓰므로
2. Running -> Ready: CPU 계속 쓰고 싶지만 timer 만료 됐으므로 timer interrupt
3. Blocked -> Ready: I/O 작업이 끝날 경우. Priority가 높은 경우는 바로 Running으로 돌려야하므로 스케쥴링 필요.
4. Terminate: Process 종료로 할 일이 없으므로 넘겨야 하므로

1, 4는 자진 반납(nonpreemptive)  
나머지 모두는 강제 빼앗음(preemptive)
