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