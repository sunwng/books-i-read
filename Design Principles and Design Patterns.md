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

## Open Closed

“A module should be open for extension but close for modification”

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
    	fun doSomething() {}
    }
    
    class Hayes(): Modem() {
    	override fun doSomething() {}
    }
    ..
    
    fun logOn(modem: Modem) {
    	modem.doSomething()
    }
    ```
    
    - In this version, even if I want to add one more type of `Modem`, `logOn` function doesn’t have to be modified
    - If `Modem` enum class has a corresponding function (`doSomething`), OCP can be also achieved by static polymorphism

## Liskov Substitution

“Subclasses should be substitutable for their base classes”