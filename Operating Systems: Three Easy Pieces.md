> 책 링크: [https://pages.cs.wisc.edu/~remzi/OSTEP/](https://pages.cs.wisc.edu/~remzi/OSTEP/)


이 책을 읽는 이유: 공룡책을 통해 익혔던 OS 지식을 심화 버전인 이 책을 통해 점검하고 리뷰하기 위함
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