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
