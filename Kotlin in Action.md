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