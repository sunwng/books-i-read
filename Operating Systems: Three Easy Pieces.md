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