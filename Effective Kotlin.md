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