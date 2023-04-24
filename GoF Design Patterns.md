> 추가 Ref: [https://github.com/RameshMF/gof-java-design-patterns](https://github.com/RameshMF/gof-java-design-patterns)
> 
> 실습: [https://github.com/sunwng/gof-design-patterns-impl](https://github.com/sunwng/gof-design-patterns-impl)
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
    - 구현 [[Link](https://github.com/sunwng/gof-design-patterns-impl/commit/9ecebbd0ac083d9d440be01a8230c18035c8bd4e)]
        - Kingdom 이라는 서비스의 구성요소가 King / Army / Castle 이라고 할 때
        - 서비스의 상황에 따라 Elf / Orc 에 맞게 구성요소가 변경될 수 있음
        - 이럴 때, KingdomFactory 라는 큰 추상 팩토리 클래스를 만들어주고
        - Elf / Orc 에 맞는 Factory를 만들고 사용해주면 됨
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