> 추가 Ref: [https://github.com/RameshMF/gof-java-design-patterns](https://github.com/RameshMF/gof-java-design-patterns)
실습: [https://github.com/sunwng/gof-design-patterns-impl](https://github.com/sunwng/gof-design-patterns-impl)
이 책을 읽는 이유: 팩토리 패턴, 빌더 패턴 등 개발하며 알게된 다양한 객체지향 패턴을 체계적으로 습득하기 위함
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
- Abstract Factory Pattern
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
- Builder Pattern
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