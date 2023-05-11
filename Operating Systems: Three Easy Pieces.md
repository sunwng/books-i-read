> 책 링크: [https://pages.cs.wisc.edu/~remzi/OSTEP/](https://pages.cs.wisc.edu/~remzi/OSTEP/)
> 
> 이 책을 읽는 이유: 공룡책을 통해 익혔던 OS 지식을 심화 버전인 이 책을 통해 점검하고 리뷰하기 위함
> 

### CH01. A Dialogue on the Book

- 공감되는 공자의 명언:
    
    “들은 것은 잊어버리고, 본 것은 기억하고, 직접 해본 것은 이해한다.”
    
    → 책을 읽지만 말고 손 끝으로 익혀보자
    

### CH02. Introducing to Operating Systems

- 이 책은 크게 세 파트로 나뉜다
    
    : Virtualization (가상화) / Concurrency (동시성) / Persistence (영속성)
    
- Virtualization
    - OS의 첫번째 역할
    - Virtualizing CPU
        - 하나의 CPU (하나의 Processor) 를 여러개의 가상 CPU로 만들어서 많은 프로그램들이 동시에 작동하는 것처럼 보이게함
    - Virtualizinng Memory
        - 각 프로세스가 각자의 가상 메모리 주소 공간을 갖게 함
        - CPU를 가상화함으로써 여러 프로세스가 동시에 작동하는 것처럼 보이게 했는데, 이 프로세스들이 독립된 메모리 공간을 갖게 해줌 → 서로 방해 x
- Concurrency
    - OS의 세번째 역할
    - 많은 프로그램들이 한 프로세스 내에서 멀티 스레드 구조를 갖게 되는데, 한 프로세스 내에서는 메모리 주소 공간을 공유하므로 공유 자원을 사용하는 것을 관리해야만 함
    - 어떤 연산이 일어나게되면 (1) 메모리 → 레지스터 (2) 연산 (3) 레지스터 → 메모리 의 작업이 순서대로 진행되는데, 문제는 이 작업들이 atomic하지 않다는 것임
- Persistence
    - OS의 네번째 역할
    - 메모리로 사용되는 RAM은 휘발성임 → 어딘가에 저장해야함
    - 즉 file system을 관리해야 함
    - file system의 존재 목적은 단순히 저장뿐만 아니라, 여러 프로세스가 한 데이터를 공유하기 위함도 있음
- 설계 목표
    - 위와 같은 ‘편리하게’ 제공하는 것이 OS의 목표임
    - 또한 ‘효율적으로’ 제공해야함 (오버헤드를 최소화, 성능을 극대화)

---

# Part 1. Virtualization

### CH04. The Abstraction: The Process

- Time Sharing
    - OS가 자원을 공유하기 위한 기술을 말함
    - 반대의 개념으로는 Space Sharing 이 있음 (e.g. 디스크 저장 공간)
- Process
    - 실행중인 프로그램
    - OS는 많은 프로세스를 실행시킬 수 있어야 함
    - 하지만 CPU 자원에는 한계가 있음
    
    → 가상화를 통해 해결
    
    - 여러 구성 요소가 존재 (Machine State)
        - Memory (Address Space)
        - Registers
            - Address Space 상의 많은 명령들은 Register를 읽거나 업데이트함
            - 대표적인 예시
                - Program Counter (PC): 어떤 명령까지 실행됐는지 체크하는 Register
                - Stack Pointer: 마지막 Stack을 가르키는 Register
    - Process의 생성 과정
        1. 대상 프로그램의 코드와 정적 데이터 (초기 데이터) 를 Process의 Address Space (메모리)에 올림
            
            * 과거의 OS는 프로세스 생성 시 프로그램읭 모든 데이터를 Address Space에 올렸지만 (Eager loading), 현대의 OS는 프로세스가 필요로 할때 관련 데이터를 올림 (Lazy loading)
            
        2. Memory에서 적당한 공간을 해당 프로세스의 Stack 공간으로 할당함
        3. Memory에서 적당한 공간을 해당 프로세스의 Heap 공간으로 할당함
        4. Entry point를 찾고 실행시킴 (보통 `main()`)
        5. OS가 CPU의 제어권을 프로세스에게 넘김
- Process States
    - Process의 State는 과거 Computer System에 따르면 3가지 중 하나를 가짐
        - Running: Processor 위에서 실행 중인 상태
        - Ready: 실행되기를 기다리는 상태
        - Blocked: Disk I/O 등의 이유로 Ready 상태가 되지 못한 상태
- Data Structure
    - OS가 Process를 관리하기 위해서는 적당한 Data Structure로 모든 Process를 표현해야함
    - 그리고 일단 Process List를 갖고 있음
    - Register Context: 사용중인 Register의 상태/내용
    → Register Context를 교체하기 때문에 Context Switching이라고 하는 거임
    - 이외에도 Process State, Process Memory 시작 포인트, Process Memory 사이즈, PID 등 많음
    - 이 Data Structure 는 Process Control Block (PCB) 라고 불리기도 함

### CH05. Interlude: Process API

→ Process와 관련한 UNIX 시스템의 유용한 API를 살펴봄

- `fork()`
    - Process를 생성함
    - Parent Process의 복사본인 Child Process가 생성됨
    - Child Process는 생성된 시점 (`fork()` 호출 시점) 부터 실행됨
    - fork() 의 return으로 Parnet Process는 Child Process의 PID, Child Process는 0 을 받음
- `wait()`
    - Child Process의 종료를 기다림
    - `waitpid()` 를 사용하면 Child Process를 특정할 수 있음
- `exec()`
    - Parent Process와 다른 Process를 생성함
    - Child Process가 `exec()`를 통해 다른 Process를 실행시키면 해당 Process로 덮어쓰여짐
- 이렇게 설계한 이유?
    - Process 생성은 기존의 Process를 복사하여 만드는 것이 편함
    - 이후 다른 Process로 덮어 쓰기 전, 추가로 처리해줘야할 것들이 있다면 `exec()` 실행 전 실행 가능함
    - 이게 책에서 설명하는 이유인듯

### CH06. Mechanism: Limited Direct Execution

- Restricted Operations
    - 모든 Process가 모든 System Call에 권한을 가진다면 엉망진창일 것
        
        → User mode / Kernel mode 로 Process의 mode를 나눔
        
    - User mode에서는 제한된 System Call 권한을 가짐
    - 사용하지 못하는 System Call을 사용하기 위해서는 Kernel의 System Call을 호출해야함
- Switching Between Processes
    - Process가 CPU를 점유하고 있다면, OS Process는 영원히 제어권을 갖지 못하게 됨
    (재부팅만이 방법)
    - 가장 좋은 방법은 timer interrupt 를 사용하는 것
    - timer interrupt가 발생하면 현재 Process를 멈추고 interrupt handler가 실행됨
    → OS Process가 다시 제어권을 갖게 됨
    - 이제 주기적으로 OS Process가 제어권을 가져올 수 있기에, 여러 Process 중 어떤 Process를 다음에 실행시킬지 정할 수 있음 → Scheduler의 역할
    - 그리고 여기서 CPU를 점유하는 Process가 다른 Process로 바뀌는 것이 바로 Context Switch

### CH07. Scheduling: Introduction

→ 이제 어떻게 다음으로 실행시킬 Process를 정할지 알아볼 차례 (여러번 봐서 지겨운 주제)

- Scheduling Metric
    - Process 선정 Policy를 평가할 때 기준이 필요함
    - 간단하게 `완료시간 - 도착시간` 으로 하는 turnaround time으로 비교
    - + `첫 시작시간 - 도착시간` 으로 하는 response time 도 사용
- FIFO (First In, First Out)
    - 말 그대로 먼저 온 순
    - 그럴리는 없겠지만, 만약 정확히 같은 시간에 도착한 프로세스들에 대해서는 어떻게 선택할지 의문
    - 프로세스가 CPU를 선점하고 빼았기지 않는다면, 각 프로세스의 작업 시간에 따라 성능은 오락가락함
- SJF (Shorted Job First)
    - 시작 가능한 Process Pool 에서 작업 시간이 가장 짧은 것부터 시작
    - 하지만, 실제로는 작업 시간을 알 수 없음
- STCF (Shortest Time-to-Completion First)
    - SJF를 선점형으로 개선한 것
- RR (Round Robin)
    - 위의 Policy들은 모두 Response Time 성능이 좋지 않음
    - 그래서 일정 time slice동안 모든 프로세스를 동등하게 실행하는 Policy가 제안됨
    - 그리고 그 time slice 는 당연히 time interrupt 의 배수여야함
    (그래야 OS Process가 time interrupt 시마다 몇번째인지 체크하고 프로세스를 교체함)
    - time slice가 작을수록 response time 성능은 증가함
    - 하지만 context switching 수가 증가한다는 tradeoff가 존재함
    - 또한 Turnaround time 역시 증가함
- I/O 고려
    - Disk I/O 가 일어나면, 해당 Process는 I/O 완료까지 CPU를 사용하지 않음 → 이걸 고려
    - Disk I/O 시작 시, Scheduler는 해당 Process를 Blocked State로 변경 후 다른 Process 실행
    - Disk I/O 완료 시, 해당 Process를 Ready State로 변경

### CH08. Scheduling: The Multi-Level Feedback Queue

- MLFQ (Multi-Level Feeadback Queue)
    - 현대적인 Scheduler
    - 목표
        - Turnaround time 최적화
            - Turnaround time 성능은 SJF와 같은 Policy에서 좋은 성능을 보였고 이 성능을 높이려면 일반적으로 Process의 작업 시간을 알고 있어야 했음
        - Response time 최소화
            - RR과는 다르게, Turnaround time 성능을 해치지 않으면서 Response time을 최소화하고자 함
    - 기본
        - 각각 다른 Priority level이 할당된 여러개의 Queue로 구성
        - 다음 실행할 Process 선택 시 Priority를 사용 (높은 Priority Queue에 있는 Process 선택)
        - 같은 Queue 내의 Process에 대해서는 RR
        - Priority는 Process의 Observed behavior를 통해 결정 (한번 결정되고 끝 아님)
            - 예를 들어, 키보드 I/O 가 많았던 Process라면 Interactive해야할 것으로 판단하고 높은 Priority 부여
            - I/O 없는 CPU Intensive한 Process라면 낮은 Priority 부여
    - How to Change Priority
        - Process가 새로 들어오면, 일단 가장 높은 Priority에 할당
        - Process가 여러 time slice를 사용할수록, Priority 감소
        - Process가 자신의 time slice가 끝나기전에 CPU 사용을 멈춘다면 (I/O 등의 이유로), Priority 유지
    - 즉 SJF처럼 행동하고 싶은데 Process의 작업 시간을 알지 못하기 때문에, 처음 들어온 Process는 짧을 수 있다고 가정하고 가장 높은 Priority에 할당하는 것
    - 문제점
        - 높은 Priority의 Process가 엄청 많다면 → 낮은 Priority의 Process는 기아 상태
        - 만약 Process를 time slice가 끝나기 직전마다 CPU를 내려놓게 한다면 → 높은 Priority 유지 가능
    - The Priority Boost
        - 첫번째 문제를 해결하기 위해, 주기적으로 (time period $S$) 모든 Process를 가장 높은 Priority Queue로 올려버리는 전략을 추가할 수 있음
        - $S$ 의 값에는 정답이 없음
        - 기아 상태를 해결 가능
        - CPU-bound한 Process가 갑자기 Interactive한 성격을 띄게 될 경우도 대응 가능
    - Better Accounting
        - 두번째 문제를 해결하기 위해, 해당 Priority에서 가능한 time slice를 정해두고 사용 시간을 누적한 후 이를 넘기면 Priority를 감소시킴
    - 근본적인 질문
        - 몇개의 Queue로 구성할지, time period는 몇으로 설정할지, 할당 time slice는 몇으로 설정할지 등 정해야할 Parameter가 너무 많음
        - 하지만 모두 정답이 있는 것은 아님
        - 딥러닝의 힘을 빌리기 좋은 Parameter 결정 문제일지도..

### CH09. Scheduling: Proportional Share

- Proportional-share Scheduler
    - Fair-share Scheduler라고도 불림
    - Turnaround time과 Response time을 최적화 하기 위해 나온게 MLFQ 였다면, 이건 모든 Process에게 일정 CPU time을 보장해주는 것이 목적
    - Ticket이라는 개념을 사용
        - 1 Ticket은 일정 CPU time을 사용할 수 있는 권리
        - 전체 Ticket Number 중 OS는 랜덤하게 하나를 뽑음
        - Ticket이 많을수록 해당 Process는 뽑힐 확률이 증가함
    - 근데, OS가 어떻게 알맞은 Ticket의 수를 Process에게 할당할지 의문
    - 만약, CPU 사용 시간이 길지 않다면 → 랜덤하게 뽑는 것은 평균으로 회귀하지 못함 (불균형하게 할당됨)
        - 이를 해결하기 위해 제안된 것은 Stride라는 개념을 사용하는 것
        - Stride는 각 Ticket Number의 역수
        - 모든 Process는 시작할 때 pass value가 0으로 초기화된 상태로 시작
        - 그리고 OS는 pass value가 최소인 Process를 실행시키고 해당 Process의 pass value에 Stride를 더해줌
        - 이렇게되면 나름? Ticket number가 높은 Process의 선택 빈도도 오르고 Ticket number가 낮은 Process도 실행될 수 있음
- CFS (Completely Fair Scheduler)
    - Linux의 Scheduler
    - Fair-share 개념을 한정적으로 적용
    - virtual runtime (`vruntime`) 이라는 개념을 사용
        - 모든 Process는 실행되고 정지될 때마다 사용한 시간에 비례하여 `vruntime`이 증가함
        - 그리고 Scheduler는 가장 낮은 `vruntime`을 갖는 Process를 실행시킴
    - 하지만, 각 Process를 얼마나 실행시켜야하는지 알 수 없음
    - 이를 위해 `sched_latency` 라는 parameter를 사용
        - 기본적으로 `sched_latency`는 48ms로 설정
        - 그리고 Scheduler는 매 순간마다 `sched_latency`를 Ready 상태의 전체 Process 수로 나눈 값을 time slice 값으로 설정
    - 근데, 만약 Ready 상태의 Process가 너무 많으면 time slice는 엄청 작아지고 Context Switching 오버헤드가 심해지지 않나?
    - 이걸 해결하기 위해 `min_granularity` 라는 parameter를 사용하고, 이건 time slice의 최소값임
    (보통 6ms로 설정된다고 함)
    - 또한, `nice` 개념을 사용하여 Process의 Priority를 관리
        - nice는 -20 ~ +19 의 범위를 갖는 값
        - 낮을수록 Priority가 높음
        - 각 nice value에 따른 weight가 정의되어있음 (e.g. -20은 88761, 19는 15)
        - 그리고 $weight_i / weight sum$ 의 비율을 `sched_latency` 에 곱해주어 각 Process의 실질적인 CPU 사용 시간을 계산
        - 즉, 전체 weight 중 얼마나 weight를 많이 차지하는지를 본다는 것
        - 그리고 `vruntime`을 실행 시간에 아까 구한 비율의 역수를 곱하여 더해줌
        (Priority가 높을수록 vruntime 증가폭이 작아짐)
    - Process 목록을 찾는 것도 오버헤드이므로
        
        → 실행 중 or 실행 가능인 Process를 Red-Black Tree 구조로 보관함
        
    - Sleep or Block 상태에서 돌아온 Process는 상대적으로 작은 `vruntime`으로 인해 CPU를 독점할 수 있다는 문제가 있음
        
        → Process가 다시 실행가능해지면, 전체 Process 중 최소 `vruntime` 값으로 설정해줘서 해결함
        

### CH10. Multiprocessor Scheduling

→ 요즘은 Multicore (Multi CPUs) 가 기본이지만, 위에서 본 Scheduling 방법들은 Single CPU를 가정하였음

- Cache
    - Multiprocessor 와 Singleprocessor 구조에서의 큰 차이는 Cache 라고 함
    - Cache는 Main Memory에서 데이터를 가져올 때 오래걸리기에, CPU에서 가까운곳에 위치시키고 자주 사용할 데이터의 복사본을 저장해두는 곳
    - Cache는 Locality 의 이점을 살림 (따지자면 Temporal Locality)
    - 한 CPU는 각자의 Cache를 갖게 되기에, 한 CPU가 업데이트한 Value는 그 CPU의 Cache에 업데이트됨
    → 다른 CPU가 같은 데이터를 보게될 때, 업데이트된 데이터와 Main Memory 상의 데이터가 달라지는 문제 발생
    - 이를 해결하기 위해 bus system 등을 사용하여 동기화를 해야함
- Multiprocessor Scheduling Architecture
    - Process 실행 시 Cache도 교체해야 하기 때문에, Scheduling 시 Process는 같은 CPU에 두도록 고려하는 것이 좋음 (Cache Affinity)
    - SQMS (Single Queue Multiprocessor Scheduling)
        - 가장 간단한 방법
        - 그냥 Queue 하나로 처리해버림
        - 하지만, 여러 개의 CPU가 하나의 Queue를 공유하기에 당연히 Lock을 사용하며 이건 CPU 수가 증가할수록 성능 감소를 심하게 함
        - 또한, Cache Affinity를 고려하기 힘듦
    - MQMS (Multi Queue Multiprocessor Scheduling)
        - CPU마다 각자 독립적인 Queue를 갖도록 함
        - 처음 Process가 들어오면, 정해둔 규칙에 의해 한 Queue에 배정함
        (규칙은 RR 등 정하기 나름)
        - 하지만, Queue 마다 다 다른 Process가 독립적으로 존재하기에 모든 Process가 동일하게 자원을 사용하지 못하게 됨 (Load Imbalance)
        - 이를 해결하기 위해, Migration 이라는 전략이 추가됨
            - 말 그대로, 부하가 싶한 Queue (CPU) 에서 적은 Queue로 Process를 옮김
            - 하지만 OS가 언제 Migration할지 어떻게 알까?
            - Work Stealing이라는 기술이 기본적으로 사용됨
            - Process가 적은 Queue가 다른 Queue들의 사이즈를 보면서 Process를 가져옴
    - 현재까지도 국룰인 방법은 없다고 함
    - Linux에서는 3가지를 가장 대표적으로 사용한다고 함
        - O(1) Scheduler → Multiple Queue 사용
            - Priority-based
        - CFS → Multiple Queue 사용
        - BFS (그 BFS 아님) → Single Queue 사용
            - Earliest Eligible Virtual Deadline First 라는 방식이라고 함..

### CH13. The Abstraction: Address Spaces

→ 이제는 Memory를 Virtualization하는 방법에 대해 시작

- 옛날 얘기
    - Time sharing에 대한 필요가 시작했을 즈음에는
    - 여러 Process를 실행시키기 위해, Switching때마다 관련 모든 데이터를 Disk에 저장하고 한 Process가 Memory 전부를 점유했음
    - 당연히도 이건 너무 느렸음
    - 그래서 메모리는 공유하되, Register-level 의 정보만 교체하기로 하였음
    - 빨라지긴 했으나, 실행 중인 Process가 다른 Process의 정보 (Memory에 있는)에 접근할 수 없게 만들어야했음
- Address Space
    - 위의 문제를 해결하고 메모리를 관리하기 위해 사용되기 시작
    - 시작 부분에 프로그램의 코드가 올라감 (Code 영역)
        - 수정될일이 없는 정적인 데이터이므로 올리기 쉽고 변하지 않는 크기이므로
    - 이후에는 프로그램 실행 중 동적으로 크기가 변하는 Heap 과 Stack 영역이 존재함
    - Heap은 Code영역 끝나는 지점부터, Stack은 Address Space가 끝나는 지점부터 시작함
    - 당연히 Heap의 주소 영역은 Positive하게 커질 것이고, Stack의 주소 영역은 Negative하게 커질 것
- 이 Address Space를 Process마다 독립적으로 갖고 있게 하는 것이 Memory Virtualization
- 이를 위해서는, Process가 명령하는 Address Space 상에서의 Address 를 Physical Memoty 상의 Address 로 변환이 가능해야함
- Memory Virtualization에서 중요한 것?
    - Transparency; Process는 자신의 Address Space가 Virtualization되었다는 것을 몰라야함
    - Efficiency; 이것으로 인해 Process가 느려지거나, Memory를 많이 사용하게 되면 안됨
    - Protection; Process가 다른 Process의 Address Space를 침범하면 안됨 
    (OS의 Address Space 역시 보호되어야함)

### CH14. Interdule: Memory API

- Types of Memory
    - Stack
        - Compiler에 의해 할당되고 해제됨
        - 함수가 실행되고, 그 내부에 지역 변수가 선언된다면 자동으로 Stack에 메모리가 할당됨
        - 그리고 해당 함수가 종료되면, 그 안에 있던 변수들이 모두 Stack 메모리에서 해제됨
    - Heap
        - 사용자에 의해 할당되고 해제됨
        - C 에서는 `malloc()` 을 사용하여 할당
        - 마찬가지로 `free()` 로 해제

### CH15. Mechanism: Address Translation

- CPU Virtualization과 마찬가지로 Efficiency와 Control을 핵심 개념으로 설게됨
- Address Translation
    - 한 Process가 자신의 Address Space를 온전히 잘 사용할 수 있도록 도움
    - Virtual Address를 Pyhysical Address로 변환함
- Dynamic Relocation
    - 변환하는 Mechanism
    - Base Register와 Bounds Register 를 필요로 함 (각각 각 프로세스의 Address Space의 시작 위치와 크기) → CPU 의 도움을 필요로 한다는 뜻
    - Physical Adress = Virtual Adress + Base 이기 때문
    - Bound Register는 Address Translation 전, 접근의 유혀성을 검증하여 다른 Process의 Address Space를 보호함
    - Base / Bounds Register를 포함한 Address Translation을 위한 요소를 Memory Management Unit (MMU) 라고 퉁쳐서 부르기도 함
- 여기서 궁금한점, 멀티 코어의 경우 모든 CPU가 하나의 MMU를 공유하는 것인가?
    - ChatGPT에게 물어본 결과 일반적으로는 아니라고함
    - CPU의 각 코어는 각자의 MMU를 사용하여 각자의 Memory를 관리함
    - 공유 Memory를 활용한다면, 이 Space를 위한 MMU 추가적으로 필요
- 그렇다면 OS의 역할은?
    - MMU에 의해 Out of Bound Exception 발생 시 처리 (보통 해당 Process 종료)
    - Program이 실행되어 Process가 생성될 때, 비어있는 Memory Space를 찾고 할당해야함
        
        → 이를 위해 Free List 라는 자료구조를 사용
        
    - 또한 종료된 Process의 Memory를 해제해야 함
    - 그리고 위에서 본대로 코어마다 MMU가 다르고, 그 뜻은 Base / Bounds Pair 값이 다르다는 것이므로 Context Switching 시 해당 Process의 Base / Bounds Pair 값을 위해 이 Pair 를 그때 그때 Memory에 저장해야함

### CH16. Segmentation

- 전에 본 구조에는 한계점이 존재함
    - 각 Process의 Address Space가 연속적으로 존재한다는 가정이 있었음
    - 여기서 만약 한 Process가 사용하지 않는 Memory는 놀게 되는데 꼭 존재해야 하나?
    - 또한, 이로 인해 새로 생성되는 Process의 Address Space가 할당될 연속적인 Space가 없다면?
- Segmentation
    - 위의 한계를 극복하기 위해 제안된 개념
    - Physical Memory 에 Address Space를 쪼개서 저장 (Code / Heap / Stack 으로)
    - 각 Segment는 각자의 최대 Size (Bounds)를 가진채로 따로 저장되며, 할당될수록 Size는 커져감
    - 이렇게 하면 하나의 Base / Bounds Pair 만으로 구현이 어렵기때문에 각각의 Segment에 대한 Base / Bounds Pair를 MMU는 저장해야함
    - 또한, Virtual Address 역시 수정되어야함
        - 어떤 Segment에 있는지 알려주는 구간과 해당 Segment에서 얼마나 떨어져있는지를 알려주는 Offset을 포함하도록
    - 그리고 Stack 의 경우, 반대 방향으로 채워지는데 이것 역시 고려되어야함
- Support for Sharing
    - Code의 경우, 다른 Process의 Code들과 함께 공간에 존재해도 됨 (Read-only 라면)
    → Memory Space 효율 증가
    - 이를 위해 Protection Bit를 사용하여 Read-only인지를 판단하게 함
- 이 경우, 각 Segment들이 제각각 다른 Size로 존재하기 때문에 External Fragmentation 문제가 발생
    - 중간 중간 Free Space가 Hole로 존재하는데, Hole의 총합은 새로운 Process에게 할당하기에 or Heap/Stack 이 커지기에 충분하지만 연속적이지 않아 할당하지 못하는 문제
- 이거는 주기적으로 Memory Space를 Compact하면 해결되지만, 이 작업의 오버헤드는 꽤나 큼
- 결국, Free List Management 알고리즘을 잘 사용하는 방법이 그나마 모든 문제를 줄일 수 있음

### CH17. Free-Space Management

- 가변적인 크기로 Memory를 나누는 경우 (Segmentation), External Fragmentation 문제를 피할 수 없었음
- 어떻게 하면 External Fragmentation 문제를 최소화할 수 있을까? → Free-Space Management
- Splitting and Coalescing
    - Free-Space Management의 가장 기본적인 메커니즘
    - Heap 에 Memory 할당 요청이 오면, Free Region을 쪼갬
    - Memory 해제 요청이 오면, Free Region을 합침
- Free List
    - Memory의 Free Region을 알고 있기 위한 Data Structure
    - Free Region의 시작 Address와 Size를 갖고 있으며 Linked List 형태로 쭉 나아감
- Basic Strategies for Allocation
    - Best Fit
        - 요청한 Memory Size보다 크거나 같은 Size를 갖는 Free Region 을 Free List 에서 전부 다 찾고
        - 가장 Size가 작은 Region에 할당 후 남는 공간만큼 Free Region으로 체크
        - 검색 오버헤드 발생
    - Worst Fit
        - 요청한 Memory Size보다 크거나 같은 Size를 갖는 Free Region 을 Free List 에서 전부 다 찾고
        - 가장 Size가 큰 Region에 할당 후 남는 공간만큼 Free Region으로 체크
        - 검색 오버헤드 발생
    - First Fit
        - 요청한 Memory Size보다 크거나 같은 Size를 갖으며 Free List에서 처음 등장하는 Free Region 을 찾고
        - 할당 후 남은 공간만큼 Free Region으로 체크
    - Next Fit
        - 가장 마지막으로 찾았던 Free Region의 주소부터 시작해서 처음 등장하는 Free Region을 찾음

### CH18. Paging: Introduction

- OS가 Space-Management 문제를 해결하는 방법은 크게 두가지로 나뉨
    - 가변 사이즈로 나누기 → Segmentation
    - 고정된 사이즈로 나누기 → Paging
- Paging
    - Segmentation보다 나은 점
        - Flexibility
            - Address Space를 더 효율적으로 추상화 가능
            - 예를 들어, Heap과 Stack의 확장 방향 등을 고려할 필요가 없음
        - Simplicity
            - Free-Space Management 방법이 훨씬 간단해짐
    - Page라고 불리우는 고정된 사이즈의 단위로 나눔 (Virtual Address)
    - 그리고 그 Page를 Physical Address와 Mapping (Frame)
    - Page - Frame (Virtual Address - Physical Address) Mapping 정보를 저장하기 위해 Page Table 사용
- Page Table
    - Page Table은 Process마다 갖고 있음
        - Context Switching 시, OS가 Page Table을 해당 Process의 것으로 교체함
    - Page Table의 주역할은 Address Translation
        - Process가 갖고 있는 Virtual Address를 두 파트로 분리
            - Virtual Page Number (VPN) + Offset
            - 즉, 어떤 Page에서 (해당 Page의 시작점으로부터) 얼마나 떨어져있는지
        - Page Table을 참고하여 VPN을 Physical Frame Number (PFN)으로 Translate
    - 모든 Process의 Page Table 다 합하면 꽤 크기 때문에, 그냥 Memory의 어딘가에 저장함
    - Page Table Entry (PTE)
        - Page Table의 한 요소 (Mapping 하나)
        - Valid Bit: 현재 Process에서 사용하는 곳인지 / 아닌지
        - Protection Bit: 권한 관리를 위함 (읽기 / 쓰기 / 실행 …)
        - Present Bit: Physical Memory에 있는지 / 없는지 (Swap Memory와 관련)
        - Dirty Bit: 수정된 이력이 있는지
        - Reference Bit: 접근된적이 있는지 (어떤 Page를 많이 사용하는지 판별 가능하게 해줌)

### CH19. Paging: Faster Translations (TLBs)

- 기존 Paging의 단점
    - 오버헤드가 크다는 점 (Memory 까지 갔다오고 연산 과정)
    - 또한 관련 데이터 클 수 밖에 없음
- Translation Lookaside Buffer (TLB)
    - 늘 그래왔듯, OS의 동작을 빠르게 하기 위해 하드웨어의 힘을 빌림
    - MMU의 한 부분임
    - Virtual-to-Physical Address Translation을 위한 하드웨어 Cache
    - Page Table에 접근하기 전, TLB를 먼저 접근하여 찾아봄
    - 이걸 통해서 높은 속도 향상을 이뤘다고 함
- Basic Algorithm
    - Virtual Address 에서 VPN 추출
        
        → TLB에서 해당 VPN의 PFN이 존재하는지 확인
        
        → 있다면? 와! TLB Hit!
        
        → PFN 가져와서 Offset 더함
        
    - TLB Miss 라면?
        
        → 하드웨어는 Exception을 발생시킴
        
        → Kernel mode로 변경되고 Trap Handler에게 넘어감
        
        → Trap Handler는 Page Table을 사용하여 PFN를 찾고 TLB를 업데이트 시킴
        
        → Exception 발생되었던 명령을 재실행 (TLB가 업데이트되었기 때문에 TLB Hit)
        
- 즉, 같은 VPN 일수록 (PFN이 같을 수록) TLB Hit이 많아짐 (Spatial Locality를 활용할수록)
    - 경험적인 예로, 일반적으로 자바에서 ArrayList가 LinkedList보다 좋은 성능을 내는 이유
    - noSQL을 사용할 때 RDB에 비해 정규화를 고려하지 않고 한번에 모든 데이터를 저장하는 것이 좋은 성능을 내는 이유
- Context Switching 시의 문제
    - 다른 Process가 각각 같은 VPN에 다른 PFN이 Mapping 되어 있는 경우, MMU는 어떤 VPN이 어떤 Process의 것인지 알 수 없음
    - 가장 쉬운 방법은 Context Switch 때마다 TLB를 Flush 시키는것
    - 하지만, 이 경우 Context Switch 직후에는 모든 VPN에 대해서 TLB Miss기 때문에 비효율적일 수 있음
    - 그래서 TLB는 그대로 두고, Address Space Identifier (ASID) 필드를 Entry에 추가하여 Process를 특정할 수 있게도 함

### CH20. Paging: Smaller Tables

→ TLB (Cache)를 통해 속도는 해결했으니 Table 크기를 한번 줄여보자

- 단순한 방법
    - Page의 단위 크기를 키움
    - 예를 들어, 단위 크기를 4배하면 VPN은 4배 줄음 → Page Table의 크기도 4배 줄음
    - 하지만 이렇게 되면 Internal Fragmentation의 문제가 발생
- Hybrid Approach
    - Paging과 Segmentation 둘 다 사용
    - 각 Process는 Code / Heap / Stack 에 대한 Segment를 가짐
    - 그리고 Page Table은 각 Segment 영역 내에서만 정의됨
    - Virtual Address 의 Segment Number
        
        → Page Table의 Base Address Get
        
        → 몇번째 Page인지 Base Address에 더함
        
        → Page Offset 더함
        
    - 각 Segment 별로 Page Table을 갖게됨
    - Page Table의 크기는 작아짐 (해당하는 Segment의 Page에 대한 정보만 갖고 있으면 되기 때문)
    - 하지만 Segmentation 의 단점들이 다시 발생..
- Multi-level Page Tables
    - 대부분의 현대 OS에서 채택하는 방식
    - Page Table을 쪼갬 + Page Directory 가 Page Table을 관리
    - Page Directory Entry (PDE) 는 Valid bit 와 PFN 로 구성
        - Valid bit는 해당 Page Table 내의 PTE 가 하나라도 Valid 면 Valid
    - Virtual Address 구성
        - Page Directory Index → PDE 특정
        - Page Table Index → PDE 내부에서 PTE 특정
        - Offset → 더해줌

### CH21. Beyond Physical Memory: Mechanisms

→ 여태까지는 Virtual Address Space 와 Physical Address Space 의 크기가 같다고 가정하였음

⇒ But, 현대의 OS는 Physical 보다 큰 Virtual Address Space를 제공하면서 각 Process에게 Memory Size의 부담을 줄여주었음

- Swap Space
    - Disk (HDD or SSD) 의 도움을 필요로 함
    - Disk에 Swap Space를 설정하고 Memory에 없는 Page의 정보를 저장 (like Page Table)
    - Page 내의 데이터는 Memory와 Disk 사이를 왔다갔다 함 (물론 Unit은 Page size임)
    - Present bit를 사용하여 Page가 Physical Memory에 있는지, Disk에 있는지 확인함
        - Present bit가 1이라면, 기존의 알고리즘대로 봄
        - Present bit가 0이라면, Page Fault가 발생 (당연히 이때는 TLB Miss 임)
            
            → OS의 Page Fault Handler가 처리
            
    - Page Fault
        - Page Fault Handler는 Disk에 있는 Page를 Memory로 옮겨야함 (Swap)
        - Swap space를 통해 Disk에서의 주소를 가져옴
        - Disk로부터 Page정보를 가져와서 Memory로 옮기고
        - Present bit와 PFN(이제는 Physical Adress Space 주소)을 업데이트
    - 만약 Physical Memory가 꽉차있다면?
        - Page Replacement Policy에 의해 Page를 Disk로 내보내야함
    - 하지만 항상 꽉차있을 때까지 유지한다면 계속해서 Page Fault가 발생할 때마다 Replacement Policy가 수행되어야함
        
        → 이걸 해결하기 위해 대부분의 OS는 Memory의 작은 부분을 항상 비워둠
        
    - 언제 비우느냐!
        - High Watermark (HW) 와 Low Watermark (LW) 사용함
        - Free 상태의 Page 수가 LW 아래로 떨어진다면
            
            → Swap daemon or Page daemon 이라고 불리는 Background Thread가 Page 를 Swap Space를 Evict함
            
            → 언제까지? Free Page 수가 HW와 같아질 때까지
            
    - 그리고 하나하나의 Page를 일일히 옮기면 오버헤드가 커지므로
        - Page들을 Cluster or Group 으로 함께 관리하여 한번에 옮김

### CH22. Beyond Physical Memory: Policies

→ Page Replacement Policy에 대해 살펴보자

- Optimal Replacement Policy
    - 가장 최적의 Policy는 교체 시, 가장 먼 미래에 쓰일 Page를 Disk로 보내는 것
    - 하지만, 실제 환경에서는 절대 알 수 없음
    - 해당 Policy를 비교 대상으로 사용
- Simple Policy: FIFO
    - 그냥 단순하게 먼저 Memory 에 할당된 Page를 내보냄
- Using History: LRU
    - 최근에 접근한 Page라면 가까운 미래에도 접근할 것으로 가정
    - 그래서 마지막으로 접근된 Page를 교체함
- 그렇다면 어떻게 History를 관리할까?
    - 또 하드웨어의 도움을 받아 페이지의 time field를 접근되는 시간으로 업데이트
- 하지만 Memory 기술이 발전하며 점점 커져갔고 수백/수천만 개의 Page의 time field를 매번 비교하는건 생각보다 오버헤드가 심해졌음
- 그래서 LRU와 유사하게 만들게 됨
    - 일단 time field가 아닌 use bit를 사용 (사용되었다면 1, 아니면 0)
    - 그리고 Page List를 Circular List로 관리
    - 알고리즘은 Page List의 특정 인덱스를 가르키며, Page 교체가 필요하면 가르키고 있는 인덱스의 use bit를 체크
        
        → 1이라면 0으로 교체하고 다음 Page
        
        → 0이라면 교체
        
- Dirty Page에 대한 고려
    - 만약 교체 대상인 Page가 수정된 이력이 있다면, Disk 에 새로 써야함 (덮어쓰기)
    - 만약 없다면? 그냥 Memory에서만 없애면 됨
    - 그렇기 때문에 Dirty한 Page보다 Clean한 Page가 교체 우선순위를 높게 체크
    - 이 알고리즘에 사용되는게 바로 dirty bit
- Page Replacement 만큼 Page Selection 역시 중요
    - 현대의 OS는 대부분 Demand Paging Policy를 사용
    - 메모리에 한번에 올리는게 아니라, 필요할 때 올린다는 전략
- Thrashing
    - Page Fault Rate이 증가하여 Page 교체 작업이 빈번해져 성능이 감소하는 현상

### CH23. Complete Virtual Memory Systems

- Linux Virtual Memory System
    - Linux Address Space
        - 일단 크게 User Space와 Kernel Space로 나뉨
        - User Space: User prgoram code, stack, heep 등 포함
        - Kernel Space: Kernel code, stacks, heep 등 포함
            - Kernel Logical Address
                - 일반적으로 생각하는 Address Space
                - Page Table, Per-Process Kernel Stacks 등이 위치함
                - Disk로 Swap되지 않음
                - Virtual Address가 Physical Address로 직접적으로 맵핑됨
                    
                    (e.g. `0xC000000` → `ox000000`)
                    
                    - 이렇게되면 Virtual 상에서 연속되어있다면 Physical 에서도 연속적임
                    - Disk로 Swap되지 않기에 가능한 방법이라고 생각됨
            - Kernel Virtual Address
                - 여기는 Physical에 연속적으로 맵핑되지 않음
                    
                    → 큰 사이즈의 메모리를 필요로 해서 연속할당이 힘든 경우에 사용됨
                    
                - 또한, Swap됨 → 좀 더 큰 양의 메모리를 처리할 수 있게 해줌
        - Context Switch 시에, User Space가 교체됨
    - Page Table Structure
        - 64 bit 시스템의 경우, Four-Level Tabled을 가짐
        - 즉, Virtual Address는
            
            P1 (Table 1 인덱스) + P2 (Table 2 인덱스) + P3 (Table 3 인덱스) + P4 (Table 4 인덱스) + Offset 으로 구성됨
            
    - Large Page Support
        - 기본인 4KB 이상 (e.g. 2MB, 1GB, …) 으로 Page Size를 설정할 수 있음
        - Page 사이즈는 TLB 성능에 큰 영향을 끼침
        - 초기엔 DBMS 와 같은 Application 에서만 이러한 필요성을 얘기했지만, 이후 많은 Application에서도 Large Page를 필요로 했고 이를 지원하게 됨
    - Page Cache [[참고](https://brunch.co.kr/@alden/25)]
        - Disk 로 부터 읽었던 데이터를 Memory 에 Caching해둠
        - dirty 해졌다면 (수정되었다면) Persistent Storage (주로 Disk)에도 덮어씀
    - Page Replacement Policy
        - 2Q Algorithm 을 적용
        - Inactive List 와 Active List를 사용함
        - 처음 메모리에 올라오면 Inactive List에 넣고
        - 이후 다시 참조되면 Active List로 옮김
        - 이후 교체해야할 때 Inactive List에서 먼저 찾음
        - 그리고 Active List의 크기를 전체 Page Cache 사이즈의 2/3 크기로 유지함

---

# Part 2. Concurrency

### CH26. Concurrency: An Introduction

- Thread, 왜 써야할까?
    - Parallelism
        - 작업의 병행성을 챙기고 싶었음
        - Multi-Process 로 구성하면 된다고 생각할 수 있음
            
            → 이 경우, 자원을 공유하는 것에 대한 Overhead가 더 클 수 있음
            
        - 즉, 같은 자원을 쉽게 공유하면서 병행적으로 작업을 처리하고 싶기에 사용하게 됨
- Thread
    - 하나의 Process가 여러개의 Program Counter를 갖게 해줌
    - 즉, 여러 실행점을 갖게 함
    - Thread 간에도 Context Switching은 필요함 → Thread Control BLock (TCB)에 Thread 정보 (Register 정보 등)을 저장
    - Process의 Context Switching과의 차이점은 Address Space를 교체할 필요가 없다는 것
        - 하지만 나는 이건 Single Processor 일때의 이야기라고 생각함
        - 현대의 하드웨어는 Multi Processor 이며, JVM 기준 여러 Thread를 Thread Scheduling 알고리즘에 따라 여러 코어에서 동시에 실행시킴
        - 이렇게 되면 한 프로그램의 여러 Thread가 각각 다른 Processor에서 실행되는 것인데 Context Switching Overhead가 적다는 이점이 계속 존재할까?
        - 만약 같은 Processor (코어)에서 Thread 여러개를 실행시킨다면 이득일텐데, 그게 보장되나?
        - 라는 의문이 존재함
        - [StackOverflow 링크](https://stackoverflow.com/questions/41759261/how-jvm-thread-scheduler-control-threads-for-multiprocessors) 를 보면, JVM은 Thread를 Kernel Level로 맵핑하고 OS Scheduler에게 스케쥴링을 맡긴다는거같음
        - 만약 다른 코어에서 실행된다면 → Cache 도 다름 → 공유 자원 관리가 더 골치
            
            (그래서 CAS 알고리즘이나 Concurrent 패키지가 나왔다고 생각됨)
            
    - 각 Thread는 각자의 Stack Space를 가짐 (Program Counter 가 각자 다 다르니 당연히 달라야함)
- 그렇다면 Multi-Thread 그냥 행복할까?
    - 알다시피 절대 행복하지 않음 (공유 자원 관리가 굉장히 까다로움)
    - Race Condition
        - 둘 이상의 Process or Thread가 공유자원에 동시에 접근하는 상태
    - Critical Section
        - Race Condition을 만드는 구간
    - 이걸 해결하기 위해선 Mutual Exclusion이 필요
        - 한번에 하나의 Process or Thread만 Critical Section에 존재하는 것을 허락

### CH28. Locks

- Basic Idea
    - Lock은 Critical Section의 상태를 나타내는 Variable임
- Lock 알고리즘 평가 방법
    - Mutual Exclusion을 확실히 수행하는가?
    - Lock을 얻기 위한 과정이 Thread에게 공정한가?
    - Overhead가 심하지 않은가?
- 가장 간단한 알고리즘: Spin Lock
    - Critical Section에 진입하는 쓰레드는 Lock 상태를 확인
    - Lock이 있다면 바꾸면서 진입
    - 없다면 spin-wait
    - 이 알고리즘을 사용할 수 있을까? → 당연히 없다
    - 첫번째 문제점
        - Critical Section에 진입하며 Lock bit를 수정할 때 하나의 Thread만 해당 권한을 갖는다는 보장이 없음
        
        → Lock을 체크하고 획득하는 과정을 Hardware의 도움으로 Atomic하게 만들면 해결할 수 있음
        
    - 두번째 문제점
        - 대기하는 Thread가 spin-wait한다는것이 Overhead가 큼
        - 특히, 한 Processor에서 둘 이상의 Thread가 존재하면 굉장히 낭비임
        
        → 대기 시작 시마다 Processor 사용을 포기 (yield) 하게 함으로써 해결
        
    - 세번째 문제점
        - Thread가 Lock 대기 후 얻는 과정에서 공정함을 보장하지 않음
        
        → Ticket Lock 이라는 대기 순서를 정하는 방법을 통해 해겴
        
- Compare-And-Swap
    - Hardware의 도움임
    - 주소에 있는 값이 예상하는 값과 같다면 원하는 값으로 수정하고 다르다면 아무것도 하지않는 Hardware가 제공하는 기능을 사용하여 Lock 확인 및 진입
- Yield 방법의 문제점
    - Spinning은 하지않지만, 불필요한 Context Switching이 존재함
- Queue Lock
    - 이를 해결하기 위해
    - Critical Section에 진입하지 못하면, Thread가 Sleep되며, Queue에 추가됨
    - Critical Section에서 빠져나오는 Thread가 Queue의 가장 앞에 있는 Thread를 깨움

### CH31. Semaphores

- Semaphores
    - `sem_wait()` 와 `sem_post()` 두 함수를 사용하여 공유 자원을 관리하는 방법
        - `sem_wait()`
            - semaphore가 양수라면 1 감소시키고 Critical Section에 진입
            - semaphore가 음수라면 wait
        - `sem_post()`
            - semaphore를 1 증가시키고 Critical Section에서 탈출
            - wait 상태의 Thread가 있다면 wake
- Binary Semaphores (Locks)
    - Semaphore value를 1로 설정하는 경우 = Mutex
    - Thread Ordering에도 사용 가능
        - 예를 들어, Parent Thread가 Child Thread를 만들고 Child Thread가 특정 작업을 완료한 후 Parent Thread가 다시 작업하길 원한다고 하자
        - 이 때, Semaphore value를 0으로 설정 후
        - Child Thread생성 후 Parent Thread가 `sem_wait()` 호출 → sleep되고 wait queue에 들어갈 것임
        - Child Thread가 작업 완료 후 `sem_post()` 호출 → sleep 중이던 Parent Thread wake
    - Bounded Buffer (Producer/Consumer) 문제도 해결 가능
        - Full / Empty Semaphore 두개를 만들어 사용
            - Full Value는 0 으로, Empty Value 는 Buffer Size로 초기화
        - Producer는 Buffer에 데이터를 Put 전, Empty Semaphore를 감소시키고 진입
        - 탈출 시 Full Semaphore를 증가시키고 Full 이 0이여서 대기중이던 Consumer Thread wake
        - Consumer의 경우 반대로
- Reader-Writer Lock
    - 데이터를 읽는 경우에는 모든 Thread가 동시에 접근해도 문제가 없음 + 성능 향상됨 → 이를 고려
    - 마찬가지로 두개의 Semaphore 를 사용 (`writelock`, `readlock`)
    - readlock 을 얻기 위해선 writelock이 free 해야함
    - writelock을 얻기 위해선 writelock과 readlock이 free 해야함
- Throttling
    - Thread Throttling 관리에도 Semaphore value를 원하는 Max Thread 수로 정하여 사용할 수 있음

### CH32. Common Concurrency Problems

→ MySQL, Apache, Mozilla, OpenOffice 에서 발견되고 고쳐졌던 Concurrency Problem을 살펴보자

- Non-Deadlock Bugs
    - Atomicity-Violation Bugs
        - “The desired serializability among multiple memory accesses is violated”
            - 즉, Atomic함을 의도하고 작성한 코드가 실행 환경에서 강제되지 못해서 생기는 버그
        - MySQL Case
            
            ```java
            Thread 1::
            if (thd->proc_info) {
                fputs(thd->proc_info, ...);
            }
            
            Thread 2::
            thd-> proc-Info = NULL;
            ```
            
            - 뭐가 문제였을까?
                
                → Thread 1이 사용하는 코드가 Atomic 하지 못했음
                
            - proc_info 값이 존재하는지 체크한 후, interrupt에 의해 Thread 2로 넘겨진다면 `NullPointerException`이 발생할 것임
            - 어떻게 고쳤을까?
                
                → 공유 자원 (proc-info)에 접근하는 구간 (critical section) 앞뒤로 Lock 을 걸어줌
                
    - Order Violation Bugs
        - “The desired order between two memory accesses is flipped”
            - 즉, 의도한 메모리 접근 순서가 지켜지지 않았을 때 생기는 버그
        - Bug Case
            
            ```java
            Thread 1::
            void init() {
                mThread = PR_CreateThread(mMain, ...);
            }
            
            Thread 2::
            void mMain(...) {
                mState = mThread->State;
            }
            ```
            
            - 뭐가 문제였을까?
                
                → Thread 2 는 `mThread`가 초기화되었다고 가정하고 있음
                
            - 어떻게 고쳤을까?
                
                → Condition 체크 변수를 Lock 과 함께 사용
                
- Deadlock Bugs
    - 가장 자주 발생하고 해결하기 복잡한 버그임
    - 자원을 점유하면서 다른 자원을 기다리고 있는 Thread가 많을 때, 더이상 진행되지 않는 상황
    - 발생 조건
        - Mutual Exclusion: 상호배제
        - Hold-and-Wait: 점유대기
        - No preemption: 비선점
        - Circular wait: 원형대기
    - Prevention
        - Circular Wait 방지
            - 가장 실용적인 방법 → Lock 순서를 체크
        - Hold-and-Wait 방지
            - 다수의 Lock을 얻어야한다면, 동시에 Atomic하게 얻게 함으로써 해결
        - No Preemption 방지
            - Lock 점유 시도 실패 시, 점유하던 모든 Lock을 해제하고 다시 시도하게 함
            - LiveLock 이라는 문제가 발생할 수 있지만, try에 딜레이를 거는 등의 방법으로 해결
        - Mutual Exclusion 방지
            - 흠… Critical Section을 정의하고 Lock 을 만들고 한게 다 Mutual Exclusion 을 위한것인데 이걸 방지한다는게 모순임
            - 즉, 이 방법은 Lock 자체를 사용하지 않겠다는 방법임
        - Scheduling 을 통한 방법
            - Thread들의 공유 자원 점유 계획을 보고 Deadlock이 발생하지 않게 Scheduling함
                
                → 너무 이상적인 그래서 불가능할 것만 같은 방법으로 생각됨
                
        - Detect and Recover
            - 주기적으로 Deadlock이 존재하는지 확인하고 자원 할당 그래프를 통해 복구하는 방법

### CH33. Event-based Concurrency

- Thread를 사용하는 것만이 Concurrency를 활용할 수 있는 방법이 아님!
- Event-based Concurrency
    - 등장 배경
        - Multi Thread 구조를 완벽하게 제어하는 시스템을 구성하기 까다로움
        - Multi Thread 구조는 개발자가 Scheduling 제어의 권한을 갖지 않음 (OS가 가짐)
- Event Loop
    - 가장 간단한 방법
    
    ```java
    while (1) {
        events = getEvents();
        for (e in events) processEvent(e);
    }
    ```
    
    - Event가 발생하길 기다리고 발생하면 그에 맞는 handler로 넘기게 됨
- Lock 을 사용하지 않아도 되서 좋은 것
- 하지만 I/O 요청과 같은 Blocking을 유발하는 event의 경우 Single Thread이므로 매우 비효율적임
    
    → 이를 해결하기 위해 Async I/O System call 을 사용
    
- 이럼에도 불구하고, 멀티코어 CPU를 사용하는 현재 시대에는 이 자체만으론 굉장히 비효율적임
    - 결국 여러 코어를 활용해야 성능이 올라가는데
    - 이렇게 하려면 결국 또 동시성 문제가 찾아옴

---

# Part 3. Persistence

### CH36. I/O Devices

- System Architecture
    - 일반적으로 CPU는
        - Memory와는 Memory Bus로
        - 디스플레이와 같은 High-Performance I/O device와는 PCI로
        - Disk와 같은 저장장치와는 Peripheral Bus로
        - 연결됨
- Canonical Protocol
    - Device Interface는 간단하게 세 종류의 Register로 구성됨
        - Status Register → Device의 상태 체크
        - Command Register → Device에게 명령
        - Data Register → Device와 데이터 교환
- Interrupt
    - 기존에는 Device의 상태가 Available 일때까지 Status Register 를 호출하며 기다렸음 → 자원낭비
    - 이를 해결하기 위한 기술이 Interrupt
    - Device에게 일을 맡기고 해당 Process or Thread 는 Sleep
        
        → Device가 작업을 완료하면 Interrupt 발생
        
        → Interrupt Handler가 알맞게 처리
        
    - Interrupt 를 사용하게되면, Handling 과 Context Switching 의 비용을 고려해야함
        - 해당 비용과 Polling했을 때의 비용을 고려하여 사용
        - 무조건 좋은것은 아님
- DMA (Direct Memory Access)
    - Disk I/O 의 경우, CPU가 중요한 연산 등의 작업을 하는 것이 아님
    - DMA는 CPU로 부터 지시를 받고 Memory - Disk 사이의 I/O 작업을 대신함

### CH37. Hard Disk Drives

- Hard Dist Drive
    - 512 byte 사이즈의 n 개의 Block 으로 이루어짐 → n 개의 Sector
        
        → Address Space는 n 개의 Sector의 주소를 포함
        
    - 읽는건 4KB이상 한번에 가능하지만 쓰기는 보통 한 블럭에 atomic함
    - Cache (Track Buffer)
        - Disk는 작은 Memory를 갖고있음
        - Sector를 읽을 때 해당 Sector의 모든 데이터를 Cache에 올림
            
            → 같은 Sector에 연속적으로 들어오는 Read 속도를 빠르게함
            
        - Write시에도 마찬가지로, Cache에 올려두고 실제로 기록함
- Disk Scheduling
    - I/O Request가 많을 때, OS가 맡아서 함
    - I/O task의 예상 소요 시간은 충분히 예측할 수 있음 → SJF 사용

### CH38. Redundant Arrays of Inexpensive Disks (RAIDs)

- Disk에게 바라는 것들
    - 더 빨랐으면…
    - 더 컸으면…
    - 더 안정적이었으면…
- 이를 위해 제안된 것이 RAID System임
    - 여러개의 Disk를 묶어서 관리
    - 병렬로 작업하기 때문에 Performance가 상승함
    - 또한 Capacity 역시 상승함
    - 같은 데이터를 여러 Disk에 중복 저장하는 방법을 통해 Reliability도 챙길 수 있음
- Fault Model
    - Disk Fault를 탐지하고 복구하기 위해 설계됨
- RAID Level 0: Striping
    - 데이터 중복 저장을 하지 않음
    - 하지만 그대신, 최고의 Performance와 Capacity를 보장함
    - N 개의 Disk 를 같은 사이즈의 Block으로 나누고, Block Number는 Disk 마다 RR을 돌며 할당
        
        (같은 Row에 있는 Block의 경우, Disk가 바뀔때마다 Sequential하게 Block Number 증가)
        
        → 같은 Row에 있는 Block들을 Stripe라고 함
        
    - Chunk Size
        - 하나의 Stripe 몇개의 Row로 구성할지 정하는 Parameter
        - Chunk Size가 작으면? → Parallelism 증가 / Positioning Time 증가
        - Chunk Size가 크면? → Parallelism 감소 / Positioning Time 감소
        - 그래서 최적의 Chunk Size를 찾는 것이 어렵지만 중요함
- RAID Level 1: Mirroring
    - Mirrored System이 적용됨 (1개 이상 복사본을 생성)
    - 그리고 복사본은 당연히 다른 Disk에 저장됨
    - 하지만 만약 복사본 저장 직전 RAID System 에 문제가 생긴다면? → Atomic하지않게되고 정합성이 깨짐
    - 이를 해결하기 위해 write-ahead log 라는 것을 사용함
    - Disk에 Write하는 명령을 logging하고 순서대로 작업, 문제가 생기면 재실행시 log에서 실행한된 것을 실행시킴
- RAID Level 4: Saving Space With Parity
    - Mirrored System에서는 모두 복사하므로 Capacity가 좋지 않은데 이걸 Parity로 해결
    - Block 들의 각 Bit들의 1 수가 홀수인지 짝수인지 판단하는 Parity Bit를 저장하고 이를 통해 Failure발생 시 복구함

### CH39. Interlude: FIles and Directories

→ 이제는 OS가 어떻게 Persistent Device를 제어하는지 살펴보자, 즉 어떻게 Storage를 Virtualization하는지!

- Files and Directories
    - File
        - 읽고 쓸 수 있는 Byte의 선형적인 배열임
        - 각 File은 Inode Number라고 하는 Low-level의 이름을 가짐
    - Directory
        - 마찬가지로 Inode Number를 가짐
        - `(user-readable name, low-level name)` Pair의 List를 가짐 (File or Directory)
        - 즉, 해당 Directory에 존재하는 File or Directory의 이름을 List로 갖고있다는 것
    - Directory의 시작은 Root Directory (UNIX기반에서는 `/`)
    - File에 접근하기 위해서는 System Call (open())을 사용해서 OS로부터 권한을 체크받아야함
        - 권한이 있다면, File Descriptor라는 자료구조를 리턴받음
    - File Descriptor는 Process마다 독립적으로 갖게 되는 객체임