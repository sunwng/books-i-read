> Objective: Get to know the real underlying purpose of Design Principles (SOLID) and Design Patterns (OOP)
>
> Reference:
>
> http://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf
>
> https://www.baeldung.com/solid-principles
> 

## Symptoms of Rotting Design

- Rigidity
    - Software is difficult to change
- Fragility
    - Breakage occurs in areas that have no conceptual relationship with the area that was changed
- Immobility
    - Inability to reuse software from other projects or from parts of the same project
- Viscosity
    - Viscosity of Design
        
        → Design preserving methods are harder to employ than the hacks
        (Easy to do the wrong thing, hard to do the right thing)
        
    - Viscosity of Environment
        
        → Development environment is slow and inefficient (e.g. compile time)
        

⇒ Directly or indirectly caused by improper dependencies b/w the modules of the software

“In order to forestall the degradation of the dependency architecture, the dependencies between modules in an application must be managed”

## Single Responsibility (SRP)

“A class should only have one responsibility, and it should only have one reason to change”

- Less functionality in a single class will have fewer dependencies
- A class with one responsibility will have far fewer test cases
- Smaller, well-organized classes are easier to search than monolithic ones

## Open Closed (OCP)

“A module should be open for extension but close for modification.”

- The most import principle
- Write our modules so that they can be extended, without requiring them to be modified
- It can be achieved by abstraction
- Example
    
    ```kotlin
    enum class Modem {
    	hayes, courrier, ernie;
    }
    
    fun logOn(modem: Modem) {
    	if (modem == Modem.hayes) doSomethingForHayes()
    	else if (modem == Modem.courrier) doSomethingForCourrier()
    	else if (modem == Modem.ernie) doSomethingForErnie()
    }
    ```
    
    - In this case, when I want to add one more type of `Modem`, `logOn` must be modified too
    - OCP can be achieved by applying dynamic polymorphism (interface)
    
    ```kotlin
    interface Modem() {
        ..
        fun doSomething() {}
        ..
    }
    
    class Hayes(): Modem() {
        ..
        override fun doSomething() {}
        ..
    }
    ..
    
    fun logOn(modem: Modem) {
        ..
    	modem.doSomething()
        ..
    }
    ```
    
    - In this version, even if I want to add one more type of `Modem`, `logOn` function doesn’t have to be modified
    - If `Modem` enum class has a corresponding function (`doSomething`), OCP can be also achieved by static polymorphism

## Liskov Substitution (LSP)

“Subclasses should be substitutable for their base classes.”

- Circle/Ellipse dilemma (Canonical example) [[Link](https://en.wikipedia.org/wiki/Circle%E2%80%93ellipse_problem)]
    - We cannot pass an instance of `Circle` into the function that uses instance of `Ellipse`
- In order to substitutable, the contract of the base class must be honored by the derived class
- A derived class is substitutable for its base class if
    - Its preconditions, which must be true before the method is called, are no stronger than the base class method
    - Its postconditions, which the method guarantees true, are no weaker than the base class method

## Dependency Inversion (DIP)

“Depend upon Abstractions. Do not depend upon concretions.”

- Difference b/w OCP and DIP
    - OCP → states the goal of OO architecture
    - DIP → states the primary mechanism for it
- Strategy of depending upon interfaces or abstract functions and classes (not concrete functions and classes)
- Example of procedural design
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/8b2dd5ff-e4d4-4e45-8cdd-cb0c43160a57/Untitled.png)
    
    - Usually, high level policies generally care “little” about the details that implement them
    - But, in this design, high level modules directly depend upon those implementation modules
- Object oriented architecture
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/4a709134-8a28-4cf2-949c-b4b8197c562f/Untitled.png)
    
    - Majority of dependencies point towards abstractions
    - Why? (Such a restriction is draconian)
        - Concrete things change a lot
    - Even if detailed implementation modified, High level policy doesn’t have to be modified (OCP)
- There is another motivation to prevent us from depending upon volatile modules
- If there is a super concrete module, depending upon it is not so bad

## Interface Segregation (ISP)

“Many client specific interfaces are better than one general purpose interface”

- Example of fat service
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/4f97c0e0-f67b-4437-bd20-8238517aaafa/Untitled.png)
    
    - Change of service can affect all of Client A, B, C
- Segregated interfaces
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e44a26ef-329f-4598-9429-3f4078727a56/ce06a0de-efb0-430d-b7e2-4e89eb09fb7c/Untitled.png)
    
    - Change of each client’s interface doesn’t affect each other