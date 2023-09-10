> Reference
> 
> - https://jmgarridopaz.github.io/content/articles.html
> 
> 이 문서를 읽는 이유: DDD의 정수 아키텍쳐를 익혀보자
> 

## Ports and Adapters Pattern

- The Architecture
    - The Hexagon
        - 이 아키텍쳐는 어플리케이션을 closed area 로 묘사함
        - 그리고 이 아키텍쳐의 제안자가 closed area를 hexagon 으로 표현했기에 hexagonal architecture 라고 불림
        - hexagon 내부에는 그 어떤 기술, 프레임워크 등과 연관이 없는 비지니스로직을 담고 있음
        - 그리고 이 아키텍쳐는 그 hexagon 내부의 로직에 대한 제안은 없음
    - Actors
        - Actor 는 Hexagon 의 외부에서 interaction을 담당함
        - Primary Actors (Drivers) → Hexagon 과의 interaction 을 trigger 시키는 존재
        - Secondary Actors (Driven Actors) → Hexagon (Application) 에 의해 trigger 되는 존재
            - e.g. Repository, Recipient, …
        - 누가 interaction을 trigger시키는지 알면 Actor를 구분할 수 있음
    - Ports
        - Interaction의 목적에 맞는 그룹을 Port라고 함
        - Port는 Hexagon의 boundary에 위치함
        - 즉, Actor는 Hexagon Port만을 통해 Hexagon과 Interaction할 수 있음
        - 그렇기에 Port는 Interface임
        - 또한 Port는 엄밀히 Application (Hexagon) 에 포함됨
        - Driver Ports → Application 의 기능을 Driver에게 제공 (API)
        - Driven Port → Application이 비지니스 로직을 구현하기 위해 사용
    - Adapters
        - Port를 사용하는? 구현하는? 구체적인 기술임
        - Hexagon 외부에 위치함
        - 어떤 adapter를 각 port에 사용할 지는 application의 실행 시점에 결정됨 → 유연성을 제공함
    - Configurable Dependency Pattern
        - = Dependency Injection, 실행 시점에 Interface의 Implementation이 정해짐
        - Driver / Driven side 모두에 적용되었음
    - 오해
        - Domain / Port / Adapter 로 나뉘어지는 Layered Architecture 가 아니다!
        - Actor → Port → Adapter → Hexagon 의 흐름은 잘못된 흐름이다!
            - Port 는 Hexagon의 일부임
            - Actor → Adapter → (Port) Hexagon 이 맞음