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