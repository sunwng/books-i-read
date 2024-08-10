## Chapter 1: Safety

- Item 1: Limit mutability
    - Holding state is double-edged sword
        - It is harder to understand and debug a program
            
            (tracking, testing, multi-threading, …)
            
    - Limiting mutability in Kotlin
        - `val` property
        - read-only collections
        - `data class` + `copy()`
    - Having mutable collections as fields can be dangerous
        
        (caller can modify after getting the collections)
- Item 2: Minimize the scope of variables
    - Tightening a variable’s scope (e.g. by using local variables) keeps our program simple to track
    - Need to prefer a variable to be initialized when it is defined
- Items 3: Eliminate platform types as soon as possible
    - Use Kotlin’s types (which are null-safety)
    - Type casting (e.g. `val foo: String`) can check nullability at the time
- Item 4: Do not expose inferred types
    - If it is not sure about the type, it is better to specify it
- Item 5: Specify your expectations on arguments and state
    - `require` → specify expectations on arguments
    - `check` → specify expectations on state
- Item 6: Prefer standard errors to custom ones
    - Exceptions from standard library are known by developers
- Item 7: Prefer `null` or `Failure` result when the lack of result is possible
    - Exceptions should not be used as a standard way to pass information
        
        (Exception propagation is less readable)
        
    - We can use sealed class Result to represent success and failure
- Item 8: Handle nulls properly
    - Use safe call (`?.`), smart casting, elvis (`?:`)
    - Throw an exception
    - Refactor the field to not be nullable
        - use `lateinit` and `delegates.notNull()`
    - Abusing not null assertion (`!!`) is not good

## Chapter 2: Readability

- Item 11: Design for readability
    - developers read code much more than they write it
- Item 12: Operator meaning should be consistent with its function name
    - title is description
- Item 13: Avoid returning or operating on Unit?
    - Using Unit? to represent logical values is misleading
    - Can be mislead let, elvis operations
- Item 14: Specify the variable type when it is not clear
    - Type might be an important information both for developer and for compiler
- Item 15: Consider referencing receivers explicitly
    - title is description
- Item 16: Properties should represent state, not behavior
    - properties in kotlin class represent accessors, not fields
        - that is why it has custom getter and setter
    - but, this fact can mislead us to use properties to have behavior
    - typical situations when we should use functions instead of properties
        - operation is computationally expensive
        - operation involves business logic
        - calling the member twice in succession produces different results
        - the order of execution is important
        - getters should not change property state
- Item 17: Consider naming arguments
    - it definitely increases readability
- Item 18: Respect coding conventions
    - title is description

## Chapter 3: Reusability

- Item 19: Do not repeat knowledge
    - business logics usually changes a lot
    - the biggest enemy of changes is knowledge repetition
    - but, when codes only look similar but represent different knowledge, it needs to be repeated
    - “are they more likely going to change toghether or separately?” → most important question to decide it
    - common algorithms are not
    - SRP → A class should have only one reason to change
- Item 20: Do not repeat common algorithms
    - title is description
- Item 21: Use property delegation to extract common property patterns
    - use `lazy`, `by`, `Delegates.observable,` `Delegates.vetoable`
- Item 22: Use generics when implementing common algorithms
    - title is description
- Item 23: Avoid shadowing type parameters
    - title is description
- Item 24: Consider variance for generic types
    - use `out` and `in`
- Item 25: Reuse between different platforms by extracting common modules
    - title is description

## Chapter 4: Abstraction design

- Item 26: Each function should be written in terms of a single level of abstraction
    - let us clearly see what the general flow of codes is
    - functions should be small and have a minimal number of responsibilities
- Item 27: Use abstraction to protect code against changes
    - abstracting constants making them variable
    - abstracting logics making them function
    - abstracting functions making them class
    - abstracting classes making them interface
- Item 28: Specify API stability
    - we can use annotations like `@Deprecated`
- Item 29: Consider wrapping external API
    - having high coupling w/ external API makes logic unstable
    - use them w/ wrapper (e.g. port and adapter in hexagonal architecture)
- Item 30: Minimize elements visibility
    - title is description
- Item 31: Define contract with documentation
    - use [Kdoc](https://kotlinlang.org/docs/kotlin-doc.html)
- Item 32: Respect abstraction contracts
    - title is description

## Chapter 5: Object creation

- Item 33: Consider factory functions instead of constructors
    - factory functions are functions used as an alternative to constructors
        - advantage 1: they have names
        - advantage 2: they can return an object of any subtype of their return type
        - advantage 3: we can control visibility
        - advantage 4: they can construct objects which might otherwise be complicated to construct
    - companion object factory functions
    - extension factory functions
    - face constructors
    - can make interface look like it has a constructor
- Item 34: Consider a primary constructor with named optional arguments
    - use default arguments instead of telescoping constructor pattern and builder pattern
- Item 35: Consider defining a DSL for complex object creation
    - it can make creating complex object really simple

## Chapter 6: Class design

- Item 36: Prefer composition over inheritance
    - only works for is-a relationship
    - using composition can be an strong alternative
    - it takes everything from the superclass
    - inheritance breaks encapsulation
- Item 37: Use the data modifier to represent a bundle of data
    - it generates `toString`, `equals`, `hashCode`, `copy`, `componentN` functions automatically
    - use data class instead of using tuples (`Pair`, `Triple`)
- Item 38: Use function types instead of interfaces to pass operations and actions
    - Single-Abstract Method
- Item 39: Prefer class hierarchies to tagged classes
    - sealed class instead of heavy enum
- Item 40: Respect the contract of `equals`
    - title is description
- Item 41: Respect the contract of `hashCode`
    - title is description
- Item 42: Respect the contract of `compareTo`
    - title is description
- Item 43: Consider extracting non-essential parts of your API into extensions
    - extensions need to be imported separately
    - extensions are not listed as members in the class reference
- Item 44: Avoid member extensions

## Chapter 7: Make it cheap

- Item 45: Avoid unnecessary object creation
    - use lazy, primitives
- Item 46: Use inline modifier for functions with parameters of functional types
    - slightly better performance
    - non-local return is allowed
    - cannot be recursive
    - cannot control visibility (`internal`, `private`)
- Item 47: Consider using inline classes
    - title is description
- Item 48: Eliminate obsolete object references
    - make it `null` after using it