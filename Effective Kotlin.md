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