> 이 책을 읽는 이유: 
> 
> Kotlin + Spring 으로 개발하고 있는데, 내 코드는 Java 스타일인 것 같다
> 

- 코틀린의 주요 특징
    - 정적 타입 지정 언어임 (컴파일된다는 뜻)
    - 타입 추론을 지원하기 때문에 변수 타입을 직접 명시할 필요가 없음
    - 함수형 프로그래밍을 지원함
        - 함수형 프로그래밍의 특징
            - 일급 시민 함수 (함수를 변수에 저장할수 있고, 전달할 수 있음)
            - 불변성 (불변 객체를 사용함)
            - 부수 효과 없음 (함수 외부와 상호작용하지 않는 순수 함수를 사용)
- 코틀린의 철학
    - 실용성: 오랜 기간 현업에서 문제가 되었던 것들을 해결하였음
    - 간결성: 간결한 코드는 코드 가독성을 높임
        
        → getter, setter, constructor 등을 개선
        
        → Collection 에 더 다양한 API를 제공
        
    - 안전성: NPE 에 대한 안정성을 높였음 + `ClassCastException`도
    - 상호운용성: 자바의 기존 라이브러리를 사용할 수 있음
- Statement (문) vs Expression (식)
    - Statement → 값을 만들어낼 수 없다 (return 불가)
        - 그래서 자바에서의 `if-else` 에는 `return` 이 필요함
        - `switch` 에도 `return` 필요
    - Expression → 값을 만들어낼 수 있음
        - 코틀린에서의 `if-else` 에는 `return` 이 필요 없음
        - `when` 에도 필요 없음
- 스마트캐스트
    - `is` 를 통해 instance 타입을 검사하면 자동으로 그 인스턴스 타입으로 변환해줌
- Exception → 코틀린의 Exception은 Unchecked Exception임
- 컬렉션
    - 자바 컬렉션을 재활용함
- Class, Interface
    - 기본적으로 public, final
    - 내부 클래스가 외부 클래스의 필드를 참조하고 싶으면 inner class 로 선언
    - primary constructor
        
        ```kotlin
        class Sample (val name: String, val value: Int)
        ```
        
    - secondary constructor: `constructor() : this() {}`
    - 순서: primary constructor → initializer (`init {}`) → secondary constructor
    - static → Object
- Lambda
    - 객체 형태의 함수
    - 컬렉션 연산에 편함
    - 자바는 람다 안에서 외부의 final 변수에만 접근 가능하지만 코틀린은 다 접근 가능
    - 자바 스트림처럼 sequence 로 돌아다니게 할 수 있음 (.asSequence())
- Types
    - `?.` → compatible with null
    - `?:` → call something when it is null
    - `as?` → instead of generate `ClassCastException`, returns null
    - `!!` 를 여러번 중첩하지 말자
    - `lateinit` → 지연 초기화
    - `Any` → 자바의 Object
    - `Unit` → 자바의 Void
    - concurrent 패키지 (e.g. `ConcurrentHashMap`) 은 자바 타입을 사용
- lazy
    - `by lazy` 로 필드 초기화 지연
- Generics
    - `where T : UpperClassOrInterface` 로 제약 걸 수 있음
    - T 는 nullable 하기에, not null 제약을 주고싶다면 `T : Any`
    - inline fun 에서는 reified 를 사용하여 런타임에 타입을 인지할 수 있도록 할 수 있음 (`<reified T>`)
    - out → 공변성 부여 (읽기) / in → 반공변성 부여 (쓰기)
    - star projection (`*`) 는 아직 알 수 없는 제네릭 타입임을 명시하기 위해 사용함 (`Any?` 와 다름)
- Annotations
    - kotlin 에서는 어노테이션 대상 함수를 지정할 수 있음 (e.g. `@get:Bar`)
        - get → getter, set → setter, field → field, …
    - JVM 관련
        - `@JVMName` → 컴파일 될 때의 이름 지정
        - `@JVMStatic` → 컴파일 될 때 정적 메소드로 노출
        - `@JVMField` → 컴파일 될 때 getter/setter 없는 자바 필드로 지정
    - Serialization / Deserialization 관련
        - `@JsonExclude` → 해당 필드 무시
        - `@JsonName` → 필드 이름 지정