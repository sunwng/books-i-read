> Objective: 
> 
> Try to know & acquire the importance of clean architecture (hexagonal architecture) and the difference b/w layered architecture by implementing some actual codes.
> 

## CH01. What’s Wrong With Layers?

- Three-layer architecture
    - very common architecture
    - Web → Domain → Persistence
        - Web: receives requests and routes them to a service in the domain layer (or business layer)
        - Domain: does some business magic and calls components from the persistence layer
        - Persistence: stores data
    - This architecture is a solid architecture
        
        → domain logic can be independent of the web and persistence layers
        
        (we can switch the web or persistence technologies w/o affecting the domain logic)
        
    - But, it has too many open flanks that allow bad habits to creep in
- It promotes Database-Driven Design
    - Foundation of a conventional layered architecture is the database
        
        (Everything builds on top of the persistence layer)
        
    - With using ORM framework, architecture usually looks like this
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/f6c03195-85a8-4abd-8520-c9b3bce3b21c/Untitled.png)
        
    - This creates a strong coupling b/w the persistence layer and the domain layer
    - Persistence code is fused into the domain code
- It’s prone to shortcuts
- It grows hard to test
- It hides the use cases
    - Services usually are too broad

## CH02. Inverting Dependencies

- SRP
    - Actual definition: A component should have only one reason to change
    - Responsibility → Reason to change
    - Complex dependencies make a component hard to change
- DIP
    - Using DIP, we can make the direction of dependency inverse
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/7bdf5271-7f72-4ccb-9b3d-73598ecaf70d/Untitled.png)
        
    - In this architecture, Domain logic is not affected by any change of persistence layer
- Clean Architecture
    - Business rules are
        - testable by design
        - independent of frameworks, databases, … (technologies)
        
        ⇒ Domain code must not have any outward facing dependencies
        
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/973ffe4b-54f6-4713-b081-08193689d027/Untitled.png)
    
    - All dependencies b/w those layers must point inward
    - Domain entities must be accessed only by the surrounding use cases
- Hexagonal Architecture
    - It applies the same principles of Clean Architecture
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/cecb084b-6eba-4914-ae00-6cbb7041777e/Untitled.png)
    
    - By defining Ports (Interfaces) in the hexagon (domain), it satisfies no outgoing dependencies
    - Adapters are related to specific technologies, so located at outside of the hexagon
    - Driver Adapters (which drives our application) use ports
    - Driven Adapter (which are driven by out application) implement ports
    
    ⇒ Why its original name is “Ports and Adapters Architecture”
    

## CH03. Organizing Code

- In greenfield projects, the most important thing is the package structure
- Organizing by layer
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/02308dbf-0ef1-47d0-8150-aaba39f872f0/Untitled.png)
    
    - It satisfies the direction of dependencies
    - There is no package boundary b/w features of the application
    - Can’t know what use cases the `AccountService` or `AccountController` implement
        
        (= Can’t know the purpose of those classes only by their name)
        
- Organizing by feature
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/d103b25d-2065-46cf-92af-271447f065dd/Untitled.png)
    
    - There are all the codes related to accounts into the high-level package `account`
    - Now, there is boundaries b/w the features
        
        (Package boundaries combined w/ package-private visibility enable us to avoid unwanted dependencies b/w features)
        
    - AccountService renamed to `SendMoneyService` to narrow its responsibility
        
        (Can know the purpose of `SendMoneyService`)
        
        → Make the codes scream its intention at us (Screaming Architecture)
        
    - But, it is hard to identify which ones are ports and incoming or outgoing adapters
- Architecturally expressive package structure
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/0476f48b-6f99-4e36-b4f3-7b2846e8e631/Untitled.png)
    
    - `domain` package → contains domain model
    - `application` package → contains a service layer around the domain model
        - `SendMoneyService` implements the incoming port interface `SendMoneyUseCase`
        - uses `LoadAccountPort` and `UpdateAccountStatePort`, which are implemented by the persistence adapter
    - `adapter` package → contains in/out adapters

## CH04. Implementing a Use Case

- What a use case actually does
    1. Take input
        - Why there is no “Validate input” step?
            - domain logic shouldn’t be polluted with input validation
            - input model (`~Command`) contains Validation step
            - input models are located in the incoming port package
    2. Validate business rules
    3. Manipulate model state
    4. Return output
- Different input models for different use cases
    - Might be tempted to use the same input model for different use cases
        - Validation step must be difficult → will pollute sacred business codes
- Different output models for different use cases
    - return as little as possible

## CH05. Implementing a Web Adapter

- Dependency Inversion
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/6010b76c-2534-44fc-84b9-3b9ae4a54214/Untitled.png)
    
    - Web adapter → “driving” or “incoming” adapter
        - takes requests from the outside and translates them into calls to application core
    - Adapters can interact w/ application core only through ports (dependency inversion)
- Responsibilities of a Web Adapter
    - Transform input model of the web adapter into the input model of the use cases
    - Call a certain use case w/ the transformed input model
- Create a separate controller, potentially in a separate package for each operation
    - name the methods and classes as close to our use cases as possible

## CH06. Implementing a Persistence Adapter

- Dependency Inversion
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/41cc558b-e639-4286-8278-088bef18e4c4/Untitled.png)
    
    - Persistence adapter is a “driven” or “outgoing” adapter
- input model to the persistence adapter lies within the application core
- Slicing Port Interfaces
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/4e50f4bf-6441-478e-b9f0-de2a73ca1a6e/Untitled.png)
    
    - Broad port interface makes unnecessary dependencies
    - need to adapt ISP (Interface Segregation Principle)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/400182f2-4e05-4c12-a27d-606f9d722b39/Untitled.png)
    
- Slicing Persistence Adapters
    - One persistence adapter per aggregate (domain) → good foundation

## CH07. Testing Architecture Elements

- The Test Pyramid
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/70bd3b2c-4bfb-4395-a376-956e2635872d/Untitled.png)
    
    - Need high coverage of fine-grained tests, which are cheap to build, easy to maintain, fast-running, and stable → Unit Test
    - Unit Test
        - is the base of the pyramid
        - dependencies are replaced with mocks
    - Integration Test
        - instantiates a network of multiple units
    - System Test
        - spins up the whole network of objects that make up the application
- Unit Tests of Domain entity in hexagonal architecture → easy (no dependency)

## CH08. Mapping Between Boundaries

- ‘No Mapping’ Strategy
    - All layers share the domain model
- ‘Two-Way’ Mapping Strategy
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/59e9fe3e-2389-460e-87e9-49b4e8041a01/Untitled.png)
    
    - Only inner layers know their own model
    - Usually makes a lot of boilerplate code
    - Incoming/Outgoing ports use domain objects as input parameters and return values
- ‘Full’ Mapping Strategy
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/350a8699-189c-4320-af0f-ecf0ee55ddd7/Untitled.png)
    
    - Uses a separate input/output model per operation
- ‘One-Way’ Mapping Strategy
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/d6878191-a26f-40bc-b297-380f3a321679/Untitled.png)
    
    - Models in all layers implement the same interface
    - Domain model can implement a rich behavior