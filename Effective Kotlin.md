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