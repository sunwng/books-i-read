### CH9. 리팩터링, 테스팅, 디버깅

- 람다 표현식을 사용하면 코드 가독성이 좋아짐
- 하지만 항상 좋은 것은 아님
    - 람다가 굉장히 복잡해질 때가 있는데, 그건 그냥 메소드로 따로 만들어주고 메소드 참조를 해주면 굿
- for loop 도 stream 으로 처리해주면 가독성이 좋아짐 (파이프라인의 의도가 보임)
- 메소드의 파라미터를 함수형 인터페이스(Consumer, Predicate, Function, …)로 선언해주고 사용 시에 람다로 넣어주는 것도 방법
- 팩토리 메소드 역시 Supplier를 통해 처리해줄 수 있음 (Map에 각각에 맞는 Supplier 추가)
- 복잡한 람다는 당연하게도 유닛 테스팅이 어려움
- 디버깅 시, 람다에서는 스택 트레이스가 보기 어려움

### CH11. null 대신 Optional 클래스

- NullPointerException 은 많은 사람들을 괴롭히는 만큼 피할 수 있다면 최대한 피해야함
- null check 하는 if문들 덕분에 코드의 구조는 엉망이 되고있었음
- 이를 해결하기 위해 Optional 클래스가 도입되었음
- `Optional.empty()` 로 비어있는 Optional 객체 생성
- `Optional.of()` 로 객체를 포함하는 Optional 객체 생성
- `Optional.ofNullable()` 로 객체를 넣으면 null 인지 확인하고 알아서 해줌
- empty 인지 확인은 `.isPresent()`
- empty 아닐 때의 행동을 정의하려면 `.ifPresent()` → consumer 넣어줘야함
- `.ifPresentOrElse()` 를 쓰면 있을 때 없을 때 둘 다 가능
- `.get()` 으로 안에 있는 객체를 가져옴 (empty 아닐때 가능)
- `.orElse()` 를 쓰면 empty 일때의 행동을 정의할 수 있음
- `.orElseGet()` 과의 차이점은 `.orElse()` 는 Supplier가 항상 실행되고 없으면 적용되지만 `.orElseGet()` 은 실행되지 않고 없으면 실행 후 적용됨
- empty 일 때 예외를 발생시키고 싶으면 `orElseThrow()`
- Optional 객체에 `.filter()` 연산을 적용하면 깔끔함 (stream 처리 가능)
- Optional 객체를 try catch 에 사용하여 항상 exception 이 발생할 수 있는 곳에 처리해주면 좋음

### CH12. 새로운 날짜와 시간 API

- 기존의 자바는 `java.util.Date` 클래스 하나만 존재했음
- 이후에는 DateFormat 을 제공했는데 이건 스레드에 안전하지 않았음
- 이젠 `java.time` 패키지를 사용하자
- 자주 봐왔던 `LocalDate`, `LocalTime`, `LocalDateTime` 이 이 패키지에 있음
- LocalDate 는 팩토리 메소드인 `.of()` 에 year, month, day 를 넣어서 생성
- 또는 `.now()` 로 현재 날짜 생성
- 이후에는 `getYear()`, `getMonth()` 등의 메소드 사용
- LocalTime 은 팩토리 메소드인 `.of()` 에 hour, minute, second 를 넣어서 생성
- 두개를 합친게 LocalDateTime → 이걸 쓰자

### CH13. 디폴트 메서드

- 기존의 자바에서는 인터페이스에 메소드를 추가하는 등의 수정이 발생하면 모든 구현 클래스에 문제가 발생했음
- 이를 해결하는 두가지 방법은 1. 정적 메소드 사용 2. 디폴트 메소드 사용 임
- 디폴트 메소드를 사용하면 구현 클래스가 그대로 상속함 → 새로 추가되는 메소드를 구현할 필요 없음
- 디폴트 메소드는 인터페이스의 메소드 제일 앞에 `default` 키워드를 사용
- 하지만 이로 인해서 default 메소드의 다중 상속 문제가 생길 수 있음
(interface 는 여러개를 구현 가능한데, 같은 이름의 default 메소드가 존재할 수 있음)
- 이를 위한 규칙이 존재함
    - 일단 클래스나 슈퍼클래스를 상속 받게될 때 이름이 같은 디폴트 메소드보다 우선권을 가짐
    - 그 다음은 서브인터페이스가 우선권을 가짐
    (디폴트 메소드를 가진 인터페이스 A 를 상속받고 디폴트 메소드를 재정의한 인터페이스 B 가 있을 때 B가 우선권)
    - 그래도 우선순위가 정해지지 않으면 오버라이드해야함 → `target.super.method()` 의 형태

### CH14. 자바 모듈 시스템

- 자바9부터는 관심사분리를 통해 모듈화
- 흠, 근데 왜이미 접근 제한자와 패키지가 있는데 또다른 그룹화가 필요한지 의문
- 음 일단 패키지에 대해서는 캡슐화를 지원하지 않았음
- `module-info.java` 에 module descriptor 를 저장함
- module descriptor 는 module 모듈명 / exports 패키지명 / requires 모듈명 의 구조를 가짐
- Maven or Gradle 을 사용하면 이런건 IDE가 처리함
- module 은 보통 project 단위임

### CH15. CompletableFuture와 리액티브 프로그래밍 컨셉의 기초

- Stream API 를 통해서는 ‘병렬성’ 을 챙길 수 있었음
- 하지만 ‘동시성’ 도 CPU 자원을 효율적으로 사용하기 위해 챙겨야할 요소임
- 스레드에 대해 살펴볼텐데, 이 [링크](https://letsmakemyselfprogrammer.tistory.com/98)로 먼저 훑어보면 좋음
- 초기 자바는 Thread 와 Runnable 을 제공하였는데 쓰레드 관리가 어렵다는 단점이 있었음
(결과를 반환할 수도 없었음)
    - Thread 는 추상클래스, Runnable 은 인터페이스
- 이러한 단점을 보완하기 위해 Callable, Future 가 소개되었음
    - Future 를 통해 비동기로 작업 중인 스레드의 상태를 조회하고 결과를 가져올 수 있음
- 관리를 위해서는 Executor, ExecutorService 등이 추가되었음
    - Executor 는 스레드 풀 구현을 위한 인터페이스임
        - 스레드를 매번 새로 만드는 오버헤드를 줄이기 위해 사용
        - 전달받는 스레드를 실행시켜주는 역할
    - ExecutorService 는 Task (Runnable or Callable) 등록을 위한 인터페이스임
        - Executor를 상속받기때문에 실행의 역할도 수행
- 스레드의 작업을 비동기로 처리해주기 위해 리액티브 방식이 제공됨
- 콜백을 람다로 전달해서 완료되면 호출되는 방식으로
- 자바8부터 지원하는 CompletableFuture 를 사용하면 더욱 편하게 작업의 흐름을 관리할 수 있음

### CH16. CompletableFuture: 안정적 비동기 프로그래밍

- CompletableFuture 와 리액티브 프로그래밍 패러다임 API 를 사용하면 안정적인 병렬 비동기 프로그래밍 설계가 가능함
- 기존에는 (Future 사용) ExecutorService 에 원하는 비동기 작업을 Callable로 전달하고 호출 스레드에서 다른 작업 이후에 `Future.get()` 으로 비동기 작업의 결과를 가져왔음
- 이때 아직 작업이 안끝났다면 호출 스레드는 Block됨
- 만약 해당 작업이 굉장히 오래걸린다면 최대 대기 시간을 설정했음
- 만약 동시에 비동기 작업을 여러개 시작시키고 각 작업이 서로 의존되어있다면 이를 구현하기 까다로웠음
- 이 문제를 람다와 파이프라이닝을 활용하는 CompletableFuture 로 해결함
- 아래 처럼 사용을 해줄 수 있음
    
    ```java
    CompletableFuture<Double> futurePrice = new CompletableFuture<>();
            new Thread(() -> {
                double price = calculatePrice(product);
                futurePrice.complete(price);
            }).start();
    ```
    
- 하지만 이 로직 상에서 예외가 발생할 수 있는데, 발생하면 위 코드 아래의 `.get()` 에서는 영원히 기다릴 것임
→ try catch 로 감싸서 해결해야함
- 저걸 바로 쓸 수 있는 팩토리메소드인 CompletableFuture.supplyAync() 가 있음 → 얘를 쓰면 됨
(변수로 Supplier 람다를 넣어주면 됨, 이 예시에서는 `() → calculatePrice(product)` 가 됨)

### CH17. 리액티브 프로그래밍

- 비동기로 처리해야 할 상황들이 점점 많아짐 → 리액티브 프로그래밍 패러다임
- 컴퓨터 성능이 증가하면서 스레드를 많이 사용할 수 있게 됐고 이를 최대로 활용하기 위한게 비동기라고 이해
- 리액티브 매니패스토의 핵심 원칙
    - Responsive
        - 빠른 반응
        - 일정하고 예상할 수 있는 반응 시간
    - Resilient
        - 장애가 발생해도 반응
    - Elastic
        - 부하에 맞게 자원 할당
    - Message-driven
        - Responsive + Elastic 을 위한 원칙
        - 컴포넌트 (어플리케이션) 의 경계를 명확하게 정의하고 비동기 메시지를 통해 통신
- 이를 위해 자바9부터 Flow 클래스가 추가되었음
    - 내부에는 Publisher, Subscriber, Subscription, Processor 인터페이스를 포함
- 이 [링크](https://sabarada.tistory.com/98)가 책보다 더 잘 이해됨
- `Publisher.subscribe(Subscriber)` 의 형식으로 Publisher - Subscriber 연결 생성
- 최초 연결 시 Subscriber 의 `onSubscribe()` 콜백이 호출
- 이후에는 `onNext()` 가 데이터를 수신시 콜백으로 호출
- Subscription 은 Publisher - Subscriber 사이의 중계 역할
- 그렇다면, Subscriber 의 `onSubscribe()` 와 `onNext()` 는 Subscription 으로부터 오는 것
- Subscription 은 Publisher에 의해 구현됨
- Processor 는 Publisher - Subscriber 사이에서 이벤트를 변환하는 등의 역할
    - 둘 사이에서 데이터 전달 (데이터 전달 or request 전달) 시에 작동

### CH18. 함수형 관점으로 생각하기

- 일반적인 객체지향 프로그래밍의 코드는 ‘어떻게’에 집중함
- ‘무엇을’에 집중하는 방식이 선언형 프로그래밍임 (스트림 API 가 이를 따름)
- 함수형 프로그래밍은 선언형 프로그래밍을 따르는 대표적인 방식임
- 자바에서 완벽한 함수형 프로그래밍을 구현하기 위해서는 한 메소드를 여러번 실행해도 결과가 같아야함
(또한 어떠한 예외도 일으켜선 안됨)
- 지역 변수만을 활용, 참조하는 객체는 불변 객체로 선언, 멀티 스레드 환경에서는 메소드 락 등을 통해 이를 달성할 수 있음
- 하지만 이렇게 되면 이러한 메소드를 병렬로 호출할 수 없음
- 결국 현재의 자바는 객체지향과 함수형의 장점을 잘 골라 사용하는 방향으로 발전했다고 생각됨 (내 생각)
- 참조 객체를 메소드 내부에서 적게 건드는 것이 잠재적인 버그를 줄이는 데에 큰 도움이 됨
(특히 만약 멀티스레드라면 더더욱)

### CH19. 함수형 프로그래밍 기법

- 일반적인 객체처럼 사용되는 함수를 ‘일급 함수’ 라고 함
- 함수 (메소드)를 변수로 사용할 수 있고, 메소드의 인수로 사용 가능하며, 메소드의 결과로 반환할 수 있음
- 커링이란?
    - 함수를 반환하는 함수를 사용하는 기법임
    - 1개 이상의 인수를 받는 함수 하나를 여러 함수로 쪼개고 각 함수가 받는 인수를 줄임
    - 함수의 재활용성을 기대할 수 있음
    
    ```java
    static DoubleUnaryOperator curreidConverter(double f, double b) {
    		return (double x) -> x * f + b;
    }
    ```
    
    - 위의 예시를 보면 단위를 변환해주는 메소드를 구현하고자 한 상황에서, 먼저 f 와 b 만을 가지고 어떤 연산을 할지 연산 함수를 반환하고 실제 값이 들어오면 그때 반환된 연산 함수를 사용함
- 콤비네이터는 둘 이상의 함수를 조합하여 하나의 함수로 만들 수 있게 해줌

### Appendix A. 기타 언어 업데이트

- 어노테이션
    - 어노테이션은 부가 정보를 코드에 쉽게 입힐 수 있게하는 기능임
    - 기존에는 하나의 어노테이션을 반복적으로 사용할 수 없었음 → 어노테이션 인터페이스에 `@Repeatable` 어노테이션을 추가해주면 반복 가능
- 제네릭을 통한 형식 추론이 가능해짐

### Appendix B. 기타 라이브러리 업데이트

- 그 유명한 ConcurrentHashMap 이 추가되었음

### End of Document