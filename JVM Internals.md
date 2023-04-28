> Reference
> - [https://blog.jamesdbloom.com/JVMInternals.html#operand_stack](https://blog.jamesdbloom.com/JVMInternals.html#operand_stack)
> - [https://d2.naver.com/helloworld/1230](https://d2.naver.com/helloworld/1230)
> 이 문서를 읽는 이유: JVM 내부 동작에 대해 어렴풋이 알고 있었는데, 자세히 들여다보기 위함
> (앞으로 Java, Scala, Kotlin 등 JVM 위에서 작동하는 언어와 오래 볼 것 같음)
> 
- Thread
    - Hotspot JVM 의 경우, Java Thread - OS Thread 의 Mapping이 존재함
        - 즉, OS Thread 가 실행되면 해당 Java Thread를 실행시킴
    - JVM System Threads
        - Main Thread 외에도 백그라운드에 실행되는 Thread는 많음
        - VM Thread
        - Periodic Task Thread
            - Timer event (Interrupt)를 책임 짐
        - GC Thread
            - GC를 담당
        - Compiler Thread
            - 런타임에 byte code → native code 컴파일
        - Signal Dispatcher Thread
            - JVM Process에 Signal을 보냄
    - 구성요소
        - Program Counter (PC)
            - 현재 실행중인 명령(코드)의 주소
            - 그리고 그 명령(코드)는 Method Area에 위치함
        - Stack
            - Stack Frame을 저장/실행 → Stack의 형태로 (LIFO)
            - Stack Frame
                - Local variable array: primitive type의 변수와 reference
                - Operand stack: CPU의 general-purpose register와 같은 역할
        - Native Stack
            - Native 방식 (e.g. C, C++) 으로 작성된 Method Frame이 저장/실행
- Heap
    - 모든 Thread가 공유하는 Memory 공간
    - Class의 객체와 array가 저장되는데, Stack은 데이터가 생기고 크기가 변할수 없게 설게되었기 때문에 필요
    - GC의 대상
- Method area
    - JVM당 한 공간만 생성됨
    - 클래스 정보가 저장됨
    - 위치는 Heap에 저장된다고 함 (하지만 GC의 대상에서는 보통 제외시킴)
    - Byte Code
    - Runtime Constant Pool: 클래스 파일에서 사용되는 Constant (e.g. String, 숫자, 식별자, 타입 등)
    - Field Information: 클래스 내부의 필드 (멤버 변수) 정보 (필드 이름, 타입, 접근 제어자, …)
    - Method Information: 클래스 내의 메소드 정보 (메소드 이름, 파라미터 타입, 반환 타입, 접근제어자, …)
    - Constructor information: 생성자 정보 (생성자 이름, 파라미터 타입, 접근제어자, …)
    - static 변수
- Classloader
    - Runtime 시에 Dynamic Loading을 담당함
    - Loading: Class file을 찾고 Memory에 올림
    - Linking
        
        → Verification: 로드된 Class File을 검증 (Java, JVM Specification을 따르는지)
        
        → Preparation: 클래스 or 인터페이스에 필요한 static field 할당 (필요로 하는 Memory 할당)
        
        → 기본값으로 초기화 (initializer or constructor 가 실행되는 것은 아님)
        
    - Resolution: Symbolic Reference 값을 Method Area 를 통해 Direct Reference (메모리 주소 값)으로 변환
        - 이게 무슨 소리냐면,
            - Java Compiler에 의해 Byte Code로 Compile될 때, 각 클래스는 서로 Symbolic Reference로 참조하도록 표현됨
            - 이후 Class는 실제 메모리에 존재해야하고 주소가 필요하므로 Symbolic Reference 를 Direct Reference로 변환해주는 것임
    - Initialization: 클래스 파일의 코드를 읽음
    - Classloader의 Hierarchy
        - Bootstrap Classloader
            - 가장 상위 Classloader
            - JVM이 실행될때 가장 먼저 생김 → Native code로 되어있음
            - 기본/필수적인 Java API를 로드함
        - Extension Classloader
            - Bootstrap Classloader의 하위 Classloader
            - Standard Java extension API를 로드함
        - System Classloader
            - Extension Classloader의 하위 Classloader
            - Default Application Classloader임 = classpath에 있는 application class를 로드
        - User-Defined Classloader
            - System Classloader의 하위 Classloader
            - 이건 Optional
            - 어플리케이션 사용자가 직접 어플리케이션 코드에서 생성하는 Classloader라고 하는데 아직 잘 모르겠음
    - 위임 모델
        - 처음 클래스를 로드하거나 Execution Engine에 의해 Byte Code를 실행할 때 아래의 순서를 따름
            1. Classloader Cache 확인 (이미 로드된 클래스인지 체크)
            2. 최상위 Classloader 체크 (최상위 Classloader까지 가면서 로드할 수 있는지 체크)
            3. 현재 Classloader 체크
        - 신기한점은, 현재 Classloader나 바로 상위 Classloader를 통해 로드할 수 있어도, 가능하다면 최상위 Classloader에서 찾아서 가져온다는 점
- Class Data Sharing (CDS)
    - 여러 JVM이 한 클래스를 공유할 수 있게함 → 어플리케이션 시작 속도 단축 & 메모리 사용량 감소
    - 대상
        - Bootstrap Classloader에 의해 로드되는 클래스
        - 자주 사용되거나 final 클래스
- Execution Engine
    - Method Area에 있는 Byte Code를 실행시킴
    - Interpreter
        - Byte Code의 Instruction을 한줄 한줄 읽고 실행
    - Just In Time (JIT) Compiler
        - 과거의 Java는 Byte Code를 그때 그때 읽어 실행하는 Interpreting 방식이었음
            
            → 당연히 느린 속도
            
        - 이걸 개선하기 위해, 반복적으로 사용되는 코드를 찾고 미리 Native Code로 Compile
        - Native Code는 Non-Heap Memory에 있는 Code Cache에 저장해두고 사용
        - 이게 적용된게 Hotspot VM (자주 사용되는 곳 = Hotspot 을 찾는다)