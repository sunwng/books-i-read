> 추가 Ref & 실습: [https://product.kyobobook.co.kr/detail/S000200311846](https://product.kyobobook.co.kr/detail/S000200311846)
> (저 책은 너무 오래된 책이라서 꽤 어려워서 책 하나를 더 참고함)
> 
> 이 책을 읽는 이유: 팩토리 패턴, 빌더 패턴 등 개발하며 알게된 다양한 객체지향 패턴을 체계적으로 습득하기 위함
> 

### CH01. 서론

- 객체지향적 설계
    - 나중에 생길 수 있는 문제나 추가되는 요구 사항들도 수용할 수 있도록 일반적이고 포괄적이어야함
    (말은 쉬움)
    - 결국, 경험에 의해 터득된 효율적인 패턴이 있고 그 패턴을 반복해서 사용함
    - 그 패턴들을 정리했음
- MVC 패턴
    - Model: 응용프로그램 객체
    - View: 스크린에 모델을 디스플레이하는 방법 정의
    - Controller: 사용자 인터페이스가 사용자 입력에 반응하는 방법 정의
    - 으로 나눔으로써 서로간의 결합도를 없애거나 낮춤
        
        → 어떤 변경 사항 발생 시, 해당 객체만 건드려주면 됨
        

* 패턴 공부 다 하고 보는게 맞을듯

### CH02. 사례 연구: 문서 편집기 설계

* 패턴 공부 다 하고 보는게 맞을듯

### CH03. 생성 패턴 (Creational Pattern)

- 생성 패턴은 인스턴스를 만드는 절차를 추상화하는 패턴
- 생성 패턴을 사용하면
    - 무엇이 생성되고
    - 누가 이것을 생성하고
    - 어떻게 생성되고
    - 언제 생성하는지
    - 에 대한 유연함을 확보할 수 있음
- 생성 패턴은 서로 밀접하게 관련되어있음 (경쟁적일수도, 보완적일수도 있음)
- **Abstract Factory Pattern**
    - 의도: 상세화된 하위클래스를 정의하지 않고도, 서로 관련성이 있거나 독립적인 여러 객체 집합을 생성하기 위한 인터페이스 제공
        
        → 즉, 팩토리의 팩토리를 만들어 사용한다는 것 (연관된 객체들을 모음)
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b60c5bb5-90dd-462c-8e3c-aa77779681cb/Untitled.png)
        
        - ProductA와 ProductB 라는 클래스를 주로 사용한다고 가정
        - 그리고 각 클래스는 1, 2 처럼 집단이 있는 상황임 (ProductA1, ProductB1 같이 사용)
        - 이럴 때, 어떤 집단이냐에 따라 모든 클래스를 ~1 or ~2 처럼 일괄적인 형태로 만들어야함
        - 이를 위해 Factory의 Factory인 AbstractFactory를 만들어주고
        - 상황에 맞는 ConcreteFactory들을 AbstractFactory를 상속 받아 만들어주고 사용함
        - 예를 들어, ~1 클래스들을 사용해야한다면 ConcreteFactory1을 사용
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/8feb8ccea029964d322dc89b3fd349610bc5afb3)]
        - 한 Page를 만들 때, Link / Tray 를 Element로 사용한다고 가정
        - 이 때, 페이지를 List 형식으로도 구성할 수 있고 Div 형식으로 구성할 수도 있음
        - 그 형식에 맞게 Link / Tray 를 생성해야함
        - 이를 위해, Link / Tray 를 생성하는 추상 Factory 를 만들고 형식에 맞게 Factory 를 정의 하고 사용하면 깔끔함
    - 내가 생각하는 사용해야 하는 이유
        - 한 서비스에서 주로 사용해야 하는 객체가 50개라고 할 때
        - 만약 그 내부에서 10가지의 유형에 따라 50개 객체가 일괄적으로 조금씩 다르다면
        - 이 패턴을 사용하여 관리하는 것이 편할 것 같음
        - 또한, 새로운 유형이 추가될때 그 유형에 대한 구현체만 만들어주면 되므로 확장에 열려있게 됨
- **Builder Pattern**
    - 의도: 복잡한 객체의 생성을 편하게 하기 위해 생성하는 클래스를 분리함
    (Argument 가 길어질수록 생성자 코드가 복잡해지고 or 생성 시 필요없는 정보가 있을 수 있음)
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ad94ae71-e4a0-4377-a39a-b12ba9d3970a/Untitled.png)
        
        - Director는 객체의 생성을 Builder를 통해 진행
        - Builder는 위 구조에서는 추상클래스
        - Builder가 구현된 ConcreteBuilder에서 만들고자 하는 객체를 차근차근 만들어서
        - Product 객체를 완성시킴
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/9cad0440fea20ed2c3b3f8a6fc9c269d409fae8e)]
        - ComplexClass 라는 복잡한 객체가 있다고 가정
        - 그리고 이 객체의 몇가지 필드 변수는 생성 시점에 추가 되기 어렵다고 가정
        - 이럴 때, Builder 를 통해 최초에 필요한 argument만 넣어주고, 이후에 얻게 되는 추가 정보를 그때 그때 추가해줄 수 있음
        - 다 마무리 되면 .build() 를 통해 얻고자 했던 객체인 ComplexClass 를 얻을 수 있음
        - 롬복이 그립다
    - 나의 경험
        - 개발을 하면서 두 상황에서 빌더패턴을 자주 사용했음 (롬복을 사용해서)
            - 생성자에 넣을 입력값이 5개 이상일 때 → 생성자가 길어지는데, 이러면 어떤 필드에 어떤 변수가 잘 들어가는지 보기가 어려움 → 롬복을 사용해주면 `.필드명()` 으로 보기 편하게 사용할 수 있음
            - 객체 생성 시, 모든 필드에 대한 정보를 알지 못할 때 → 빌더를 사용하여 차근차근 넣어주고 다 넣어졌을 때 객체를 만들어주었음
    - 내가 생각하는 사용하는 이유
        - 객체 생성 시, 아직 생성자에 넣을 인자가 없을 수도 있고 생성자 자체가 복잡할 수도 있음
        - 이럴 때 쓰면 좋을 듯함
        - 이럴 때만 사용하는 것이 아니었음
        - 예를 들어 한 인터페이스를 구현하는 다른 여러 구현체가 있다고 가정
        - 그때 그때 상황에 맞게 해당 객체를 생성해야하는데, 빌더 패턴을 사용하여서 하나의 메소드로 다양한 종료의 구현체를 만들수 있음
        - 즉 빌더패턴을 통해 DI를 이뤄낼 수 있음
- **Factory Method Pattern**
    - 의도: 객체를 생성하기 위한 인터페이스를 정의하지만, 어떤 클래스의 인스턴스를 생성할 지에 대한 결정은 하위 클래스가 내리도록 함
    (즉 Factory Pattern을 통해 객체를 생성할 것인데, Factory 클래스의 하위 클래스를 각 생성될 객체에 맞게 정의후 하위클래스에서 생성)
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1dd3dddf-1662-4933-b937-e4e5080cb8fc/Untitled.png)
        
        - Product: 만들고자 하는 객체들의 인터페이스
        - ConcreteProduct: Product의 하위 클래스 중 하나
        - Creator: 팩토리 메소드를 갖고있는 팩토리의 인터페이스
        - ConcreteCreator: 팩토리에서 객체에 맞게 팩토리 메소드가 재정의된 Creator의 하위 클래스
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/2842a3389d5a7673949177930423703ad851e1f9)]
        - User의 가입경로가 다양하다고 가정, 최초에는 네이버만 존재
        - 만약, 생성자를 팩토리 패턴으로 관리한다면 팩토리의 한 메소드에서 모든 가입경로에 따른 User 객체를 반환해야 할 것임 (type 을 입력받아서 switch or if 를 통해 그에 맞는 객체 반환)
        - 즉, 한 메소드가 모든 종류의 User 객체 생성을 관리한다는 것인데
        - 만약 카카오 유저가 추가된다면? → 그 메소드를 고쳐야 할것 (복잡하다면 더 고칠 곳이 많아짐)
        - 그래서 기본 팩토리를 인터페이스로 만들어두고, 그 팩토리 메소드를 각각 User 종류에 맞게 구현하도록 함
        - 이러면 카카오 유저가 추가되더라도 카카오 팩토리를 만들고 해당 클래스를 사용하여 개발을 진행하면 됨
    - 나의 경험
        - 나는 주로 팩토리 메소드보다는 정적 팩토리 메소드를 사용해왔음
        - 둘의 차이를 생각해보면, 결국 상황에 맞게 그에 맞는 객체를 생성하는 개념은 같지만
            - 팩토리 메소드는 팩토리 자체를 추상화하여 하위 클래스에서 구현하여 사용하도록 하고
            - 정적 팩토리 메소드는 만들 객체에 정적 메소드를 추가하여 구현하여 사용하도록 함
        - 확장성은 팩토리 메소드가 더 좋다고 생각됨
    - 내가 사용하는 사용 이유
        - 결국 기존 코드를 수정하고 싶지 않기 때문에 사용함
- **Prototype Pattern**
    - 의도: 원형이 되는 객체를 사용하여 복사하여 새로운 객체를 생성함
    (마치 OS가 하위 프로세스를 만드는 느낌)
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c561da53-42e2-49ab-8cc9-18a2d5369859/Untitled.png)
        
        - 만들고자 하는 객체와 같은 유형의 객체를 복사하여 생성함
        - 이후 수정하려면 수정
    - 구현
        - 자바에서는 `Cloneable` 인터페이스를 구현하여 `clone()` 메소드를 override 사용하면 됨
    - 내가 사용하는 사용 이유
        - 이미 존재하는 객체와 유사한 객체를 만들 것이라면, 복사하여 수정하는 것이 덜 복잡함
        (그리고 그 객체의 생성 과정이 굉장히 복잡하다면 더더욱 이점)
- **Singleton Pattern**
    - 의도: 클래스가 오직 한개의 객체만을 갖도록 보장하고, 이를 통해 전역 접근을 제공함
    (스프링의 모든 빈들)
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f46c2e4e-7fd0-48b9-922d-99fe955e773c/Untitled.png)
        
        - 오직 하나의 객체만을 반환함
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/1efaa22aa5e814d19a37f2b3f59fb8a4e7052d05)]
        - 생성자를 private으로 선언해줌으로써 새로운 객체의 생성을 막아야 함
    - 내가 사용하는 사용 이유
        - 일단, 한 객체가 크게 변하지 않으면서 자주 생성된다면 메모리적인 측면에서 한 객체를 계속해서 같이 사용하는 것이 이득임
        - 그리고, 그 객체 내부의 필드 변수들을 모두가 공유한다면 더욱 이득
        - 하지만 멀티 스레딩 환경이라면? → 해당 객체에 접근하기 위한 Race Condition이 무조건 발생
        - 이럴 때는, 객체 내부에 공유하는 필드 or 메소드에 대해서 Syncrhonized 나 Concurrent 자료구조를 사용하여 처리를 해줘야 할 듯

### CH04. 구조 패턴 (Structural Pattern)

- 더 큰 구조를 형성하기 위해 어떻게 클래스와 객체를 합성하는지에 대한 패턴
- **Adapter Pattern**
    - 의도: 클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 변환시킴
        
        (서로 일치하지 않는 인터페이스를 갖는 클래스들을 함께 동작시킴)
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/49d5f21c-3758-44ec-917c-82cb82ff80bb/Untitled.png)
        
        - 기존에 만들어진 클래스는 Adaptee
        - Adapter 클래스에서 Adaptee를 사용하고
        - 사용자가 원하는 메소드는 Adapter에서 구현
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/7cf437109232316d206c04ffa8664696df37ec68)]
        - 입력된 String에 괄호를 씌우는 method와 *를 씌우는 method가 있다고 하자
            
            (`showWithParen()`, `showWithAster()`)
            
        - 하지만 Client는 해당 method를 다른 인터페이스를 통해 (`printWeak()`, `printStrong()`) 사용하고 싶어함
        - 이럴 때, 로직을 수행하는 클래스를 Client가 사용하는 클래스 내에 선언 후 인터페이스에 맞게 method를 만들어줌
    - 내가 생각하는 사용 이유
        - 이미 만들어져있던 클래스를 재사용하고 싶지만, 인터페이스가 다를 때 필요할 듯
        - 마찬가지로, legacy system을 유지하고 활용하기 위해서도 사용
- **Bridge Pattern**
    - 의도: 구현에서 추상을 분리하여 이들이 독립적으로 다양성을 가질 수 있도록 함
        - 하위 클래스가 생성될 때는 두가지의 목적이 존재함 → 기능 / 구현
            - 기능의 계층은 기존에서 기능이 추가되는것
            - 구현의 계층은 여러 구현을 추가하는 것
        - 이 두개가 혼재하면 굉장히 복잡해짐
        
        → 기능 확장을 위한 클래스 계층과 구현을 위한 클래스 계층을 분리하고 Bridge로 연결하여 사용
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/32e1bfcb-537e-4a02-a32e-14956a88c0b0/Untitled.png)
        
        - Abstraction: 기능 계층의 최상위 클래스 → Implementor 의존성을 주입받아 사용 (Bridge)
        - RefinedAbstraction: 기능이 추가된 하위 클래스
        - Implementor: 구현 계층의 최상위 클래스
        - ConcreteImplementor: 구현이 구체화된 하위 클래스
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/d08e3f9527b47162f1c7aa359c3822914608444c)]
        - 문자를 표시하는 기능을 담당하는 클래스 (`Display`)가 있다고 하자
        - 하지만 여러번 문자를 표시하고 싶은 기능이 추가될 수 있고 이를 위해 `Display`를 확장한 `CountDisplay` 클래스를 하위클래스로 추가하였음
        - 여기서, 이젠 구현할 클래스가 필요함
        - 구현 계층의 최상위 클래스로 `Implementor` 클래스로 만들고
        - 이를 구현하는 클래스들이 알맞게 구체화하도록 함
    - 내가 생각하는 사용 이유
        - 기능이 다양해짐과 구현이 다양해짐으로 인한 코드 수정을 최소화하기 위함이라고 생각함
        - 예를 들어 위의 예제에서, Display에 여러 추가 기능이 생긴다고 할 때 기능 계층에서만 작업을 해주면 됨
        - 또한 구현이 달라질 때에는 구현 계층에서만 작업해주면 됨 (Interface는 같기 때문)
        - 해당 패턴을 통해 상속을 통한 결합이 아닌 위임을 통한 결합을 가능하게 함 (의존성이 낮아짐)
            - 상속을 통한 결합은 의존도가 너무 높아지기에 유연하지 못함
- **Composite Pattern**
    - 의도: 부분과 전체의 계층을 표현하기 위해 겍체들을 모아 트리 구조로 구성
        
        → 사용자로 하여금 개별 객체와 복합 객체를 모두 동일하게 다룰 수 있도록 함
        
        (내용물과 그릇이 있을 때, 그릇은 내용물과 그릇 모두를 담을 수 있게하고 내용물과 그릇을 하나의 객체 타입으로 사용할 수 있게 함!)
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5091fec7-7c58-46d0-bc9e-7e9f112504b0/Untitled.png)
        
        - Leaf: 내용물 클래스
        - Composite: 내용물을 담을 수 있는 클래스 (Composite 도 담을 수 있음)
        - Component: Leaf와 Composite를 동일시 하기 위한 상위 클래스
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/e63a10744f6d71416594a68400ba60256d9fb54b)]
        - File System을 생각해보자
        - `File` 과 `Directory`가 있는데, `Directory` 안에는 `File`과 `Directory`가 들어갈 수 있으며
        - `File`과 `Directory` 모두 하나의 `Entry`로 취급되는 것이 편함
    - 내가 생각하는 사용 이유
        - 재귀적으로 탐색해야할 때, 여러 클래스를 하나의 타입으로 취급하고 싶을 수 있음
        - 이 때 사용해야한다고 생각

- **Decorator Pattern**
    - 의도: 객체에 동적으로 새로운 책임을 추가할 수 있게 함
        
        (기능을 추가할 때, 서브클래스 생성보다 융통성 있는 방법을 제공함)
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ae1d623-509c-4e07-8ad9-d242f51c5790/Untitled.png)
        
        - Component: 기능을 추가할 때 핵심이 되는 역할 (인터페이스만 정의)
        - ConcreteComponent: Component의 구현체
        - Decorator: 장식하는 기능의 API를 정의 + 장식할 대상 객체를 가짐
        - ConcreteDecorator: 구현된 Decorator
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/ec78b842df58abaedc618d34467a0f59ae083446)]
        - `Display` 클래스를 Component로 정의
        - ConcreteComponent는 `StringDisplay`
        - `StringDisplay`를 장식하기 위한 `Border` 타입의 클래스들 (`SideBorder`, `FullBorder`) 존재
    - 내가 생각하는 사용 이유
        - ConcreteComponent와 Decorator가 같은 Component 클래스를 구현하는 것이 특징이라고 생각함
        - 이것의 이점이 무엇일까?
            
            → Decorator를 통해 ConcreteComponent를 감싼 클래스가 ConcreteComponent와 같은 인터페이스를 갖기 때문에 편함
            
            → Decorator 클래스의 확장이 편리함
            
        - 또한 여러가지 Decorator를 ConcreteComponent에 조합해줄 수 있음
        - 하지만 구현 예제의 `b4` 객체처럼 지저분해질 수 있음…

- **Facade Pattern**
    - 의도: 한 서브시스템 내의 인터페이스 집합에 대한 획일화된 하나의 인터페이스를 제공하는 패턴
        
        (서브시스템을 사용하기 쉽도록 사위 수준의 인터페이스를 정의)
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/07310a14-61fd-4d72-b354-aa1fda7011b2/Untitled.png)
        
        - Facade: 여러 클래스를 활용하여 복잡한 로직을 간단한 API로 제공
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/b55c8bc19f1af49175ba091a1216eb7e0a6920de)]
        - PageMaker 클래스가 Facade역할을 수행
            - 복잡한 로직을 간단한 API 호출로 수행 가능하게 함
    - 내가 생각하는 사용 이유
        - API 호출 순서가 복잡한 경우 or 너무 어려운 경우
            
            → 사용자에게 간단한 API를 제공해줄 수 있게 함
            
- **Flyweight Pattern**
    - 의도: 객체의 공유 or 재활용을 통해 객체들을 효율적으로 관리할 수 있게 함
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/877385e2-0963-488f-af63-faa55ccc4cc0/Untitled.png)
        
        - Flyweight: 공유 or 재활용 대상 클래스
        - FlyweightFactory: Flyweight 관리 클래스
        - ConcreteFlyweight: 공유 속성
        - UnsharedConcreteFlyweight: 공유되면 안되는 속성
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/79d1a3db0378f4f3159d27abc92ba05510dd0504)]
        - `BigCharFactory` 클래스가 객체의 재활용 (or 공유) 를 제어함
        - `BigChar` 클래스는 무거운 클래스의 예시 → 재활용 or 공유의 대상
        - 객체 (인스턴스)를 가져오는 함수에서 경합이 발생할 수 있으므로 `synchronized` 처리
    - 내가 생각하는 사용 이유
        - 불변할 객체 or 공유해야하는 객체 라면 새로운 인스턴스를 매번 만드는 것은 메모리 낭비임
            
            → 이를 해결
            
        - 메모리 뿐만 아니라, 만약 DB I/O 나 DIsk I/O 가 포함되어있다면 전반적인 리소스를 감소시킬 수 있음

- **Proxy Pattern**
    - 의도: 다른 객체에 대한 접근을 제어하기 위한 대리자 또는 자리채움자 역할을 하는 객체를 활용함
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8224d441-19eb-49c0-bac8-8884fb0aacba/Untitled.png)
        
        - Subject: Proxy와 RealSubject를 동일시하기 위한 API가 정의된 인터페이스
            
            → Client가 Proxy와 RealSubject의 차이를 인지하지 못하게 함
            
        - Proxy: Client의 요청을 최대한 처리하고 RealSubject가 필요한 경우에 맡김
        - RealSubject: Proxy가 처리할 수 없는 실제 로직을 수행
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/9f5b44326af0220972e61ce8ff2331ce2127afd1)]
        - `Printable` 인터페이스가 Subject 인터페이스의 역할을 함
        - `Printer` 클래스는 객체가 생성될 때 intensive 작업으로 인해 오래걸린다는 가정이 있음
        - `PrinterProxy` 에서 `Printer` 클래스의 객체가 생성되지 않아도 되는 로직들을 대신 처리해주고
        - 필요한 경우에만 객체를 생성함
    - 내가 생각하는 사용 이유
        - 초기화 시 (객체 생성 시) 오래걸리는 클래스가 있다면 유용할 듯 (진짜 필요할 때만 객체가 생성되므로)
        - 가장 잘 알려진 Proxy Pattern의 예시는 Spring의 AOP임
            
            → 왜?
            
            - AOP는 대상이 되는 로직에 영향 없이, 로직 전 or 중간 or 후 or 전반에 걸친 다른 로직이 수행되게 함
            - 여기서 대상이 되는 로직을 감싸고 AOP 로직을 대상 로직과 독립적으로 실행되게끔 함
            - 흠 근데 Decorator Pattern과도 굉장히 유사한데 왜 Proxy Pattern일까?
            - 두 패턴의 차이는 의도에 있음
                - Proxy Pattern → Proxy의 접근 제한을 살리기 위함
                - Decorator → Proxy의 부가 기능을 살리기 위함
            - 찾아보니 두 패턴이 모두 사용되었다고 볼 수 있다고 함

### CH05. 행동 패턴 (Behavioral Pattern)

- 어떤 처리의 책임을 어느 객체에게 맡기는 것이 좋은지에 대한 패턴임
- 클래스들 간의 교류 방법에 대한 패턴임
- **Chain of Responsibility Pattern**
    - 의도: 메시지를 보내는 객체와 이를 받아 처리하는 객체들 간의 결합도를 없애기 위한 패턴
        
        (하나의 요청에 대한 처리가 반드시 한 객체에서만 되는 것이 아니라 여러 객체에게 처리 기회를 부여)
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d13a41a2-fbd7-4afb-8b5f-696cc04c5080/Untitled.png)
        
        - Handler: 요구를 처리하는 API 정의 (추상 클래스 or 인터페이스)
        - ConcreteHandler: 요구를 구체적으로 처리하는 클래스
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/3b1264e287c57fb373b92c78ef942750f5543bc8)]
        - Support가 Handler 이고 여러 Support (ConcreteHandler)가 존재
        - Client가 요청을 할 때마다, Handler가 자신이 처리할 수 있는지 판단하고 처리할 수 없다면 다음 Handler에게 넘김
    - 내가 생각하는 사용 이유
        - 요구하는 쪽 (Client) 와 처리하는 쪽 (Handler) 의 결합을 약하게 함
            - 어떻게? → Client는 어떤 Handler에게 요청해야하는지 신경 안쓰고 그냥 처음 Handler를 호출하면 됨
        - SRP 원칙을 잘 지킬 수 있음 → 자신의 능력 밖의 일이라면 다른 객체에게 위임
        - 하지만, Handler끼리 결합이 생기게 되는 건 어쩐담? (순서도 문제인데)
- **Command Pattern**
    - 의도: 요청 자체를 캡슐화하여 요청이 서로 다른 사용자를 매개변수로 만들고, 요청을 대기시키거나 되돌릴 수 있는 연산을 지원하게 함
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1fa26299-c4d6-4527-a18a-4ad4afba2120/Untitled.png)
        
        - Command: 명령의 인터페이스
        - ConcreteCommand: 명령의 구체 클래스
        - Reciever: 명령을 실행할 대상
        - Client: 명령을 생성하고 Reciever에게 전달
        - Invoker: 명령을 실행시킴
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/43b75a51e592207e382d2249b2512409e2a1f6aa)]
        - Command를 구현하는 두 클래스는 명령의 내용을 저장함
            
            = 요청 자체를 객체화
            
        - MacroCommand와 같은 history command를 사용하여 요청 내역 관리 가능
    - 내가 생각하는 사용 이유
        - 요청 (명령) 을 객체로 남기는 것이 좋은 경우를 생각해보자
            - 예를 들어 Transaction
        - 요청의 생명 주기가 길다면 객체화하여 주기적으로 상태를 체크하고 이력을 관리해야한다면 필요하다고 생각
- **Interpreter Pattern**
    - 의도: 어떤 언어에 대해, 그 언어의 문법에 대한 표현을 정의하면서 그 표현을 사용하여 해당 언어로 기술된 문장을 해서하는 해석자를 함께 정의
        - 우리가 어떠한 문법을 정의하고 클라이언트가 이를 사용하게 할 때, 그에 맞게 처리하는 패턴임
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/88377a82-096b-4db6-919d-0891334f3845/Untitled.png)
        
        - AbstractExpression: 해석하는 노드들의 공통 API
        - TerminalExpression: 문법에 맞는 종료 해석 방법
        - NonterminalExpression: 문법에 맞는 행동
        - Context: 해석할 구문에 대한 정보
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/730d49ec9193d140392629fdc65909ca9373a33e)]
        - `Node`는 AbstractExpression으로 공통 API를 정의
        - `PrimitiveCommandNode`는 TerminalExpression 역할
        - `ProgramNode`, `CommandNode`, `RepeatCommandNode`, `CommandListNode` 는 NonTerminalExpression 역할
    - 내가 생각하는 사용 이유
        - 상위 수준의 패턴임
        - 우리가 어떤 API (커맨드 or 문법) 을 제공할 때 고민해야하는 영역임
        - 사용하는 예시를 찾자면, DBMS의 엔진이 SQL을 파싱하는 것?
- **Iterator Pattern**
    - 의도: 내부 표현부를 노출하지 않고 어떤 집합 객체에 속한 원소들을 순차적으로 접근할 수 있는 방법을 제공
        
        → 반복을 외부에서 노출하지 않고 내부에서 반복하도록 하겠다
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/74cb67e7-7f62-49fa-986c-f684299b5839/Untitled.png)
        
        - Iterator: `hasNext()`, `next()` API를 제공하는 인터페이스 (`Iterator<E>`)
        - ConcreteIterator: Iterator를 구현한 클래스
        - Aggregate: `iterator()` (Iterator객체를 생성하는) API를 제공하는 인터페이스
        - ConcreteAggregate: Aggregate를 구현한 클래스
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/8177fe7f30582dbcca544c46970030190aedf6ba)]
        - BookShelf 를 통해 명시적 Iterator 활용 / 확장 for문 활용 모두 살펴봄
    - 내가 생각하는 사용 이유
        - 자바에서 많이 접해봤음
        - 내부 반복자를 사용하는 것은 여러모로 장점이 많음
- **Mediator Pattern**
    - 의도:
        
        한 집합에 속해있는 객체의 상호작용을 캡슐화하는 객체를 정의함
        
        객체들이 서로를 직접 참조하지 않도록 하여 객체 사이의 loose coupling을 촉진시킴
        
        개발자가 객체의 상호작용을 독립적으로 다양화시킬 수 있게 함
        
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/957a2de8-3060-49f6-952d-888eb7c9df63/Untitled.png)
        
        - Colleague: 하나의 시스템을 이루는 여러 컴포넌트에 해당하는 클래스들
        - Mediator: 여러 Colleague가 서로 상호작용하기에는 복잡하니 그들을 중재해주는 클래스들 (이 클래스를 거쳐야함)
    - 구현 → 재미없어서 생략
    - 내가 생각하는 사용 이유
        - Facade 패턴과 유사함
        - 하지만, Facade 는 단방향 (Facade 는 호출만함) 이지만, Mediator는 Colleague 클래스들과 양방향으로 상호작용함
        - 즉, 객체들간의 복잡한 관계를 쉽게 풀어내고 싶을 때 사용하기 좋다고 생각함

- **Memento Pattern**
    - 의도: 캡슐화를 위배하지 않은 채 어떤 객체의 내부 상태를 잡아내고 실체화시켜 둠으로써, 이후 해당 객체가 그 상태로 되돌아올 수 있도록 함
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/283e462a-7a34-442e-b01f-6d3132c5b445/Untitled.png)
        
        - Originator: 원할 때 자신의 상태를 Memento로 만들고 + 이전 Memento로 복원
        - Memento: Originator의 내부 정보를 저장하지만 public하게 공개하지 않음
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/fc8924be00bb30fcdc3495a3c45654ce2d72d608)]
        - `Memento`는 현재 `Gamer`의 상태를 저장함
        - 중요한 건, `Memento`의 생성자와 메소드들은 같은 패키지 내에서만 호출 가능함 (`Gamer`만 호출 가능)
        - 특정 시점마다 `Gamer`는 자신의 상태를 `Memento`에 저장하고, `Gamer`는 `Memento`에 저장된 상태로 복원될 수 있음
    - 내가 생각하는 사용 이유
        - 객체의 상태에 대한 스냅샷을 주기적으로 찍고, 원할 때 되돌려야하는 상황에서 써야할 듯
- **Observer Pattern**
    - 의도: 객체 사이에 일 대 다의 의존 관계를 정의해 두어, 어떤 객체의 상태가 변할 때 그 객체에 의존성을 가진 다른 객체들이 그 변화를 통지받고 자동으로 갱신될 수 있게 만듦
    - 구조:
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/aeec9df9-7aae-4f4d-ae77-e4aaedc0aea1/Untitled.png)
        
        - Subject: 관찰 대상자 (전파 시키는 객체)
        - Observer: 관찰자 (전파 받는 객체)
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/ca8cf3ea62330a1cc493018046418b1052cc2a38)]
        - `NumberGenerator` 내부에 서 `Observer` 리스트를 관리함
        - `NumberGenerator` 내부 상태에 변화가 생겼을 때 `notifyObservers()`를 통해 `Observer`에게 전파시킴
    - 내가 생각하는 사용 이유
        - 객체간의 상호작용을 위해??
        - 너무 무의식중에 자주 사용하던 방식인 것 같음
        - 이게 더 나아가서 event-driven 방식으로 진화했을 것 같음