## CH02. Java Memory Areas and Memory Overflow

- Runtime Data Area
    - Program Counter (PC) Register
        - bytecode line number indicator of current thread
        - located at thread private memory (independent memory of each thread)
    - JVM Stack
        - push stack frame into JVM Stack when a method starts
        - pop stack frame from JVM Stack when the method ends
        - size of local variables is fixed during compile time
    - Native Method Stack
        - similar to JVM Stack
        - external library codes (os, hardware, ...)
    - Java Heap
        - shared by all threads
        - almost of instances are stored at heap
        - managed by gc
    - Method Area
        - shared by all threads
        - constants, static variables, ...
        - runtime constant pool (strings, literals, ...)

## CH03. Garbage Collector & Memory Allocation Strategy

- Key Questions
    - Which memory to collect
    - When to collect
    - How to collect
- Checking whether target is dead
    - it does not use Reference Counting Algorithm because of drawback on circular reference
    - instead, uses Reachability Analysis Algorithm
        - starting nodes are called GC root
        - candidates of GC root
            - objects that are referenced by JVM Stack
            - objects that are referenced as a static variable
            - objects that are used as a constant
            - objects that are locked with synchronized keyword
    - types of references
        - strong reference: usual reference => not a target of gc
        - soft reference: useful but not neccesary => can survive until memory is available
        - weak reference: weaker than soft reference => will be collected at next gc
        - phantom reference: the weakest reference