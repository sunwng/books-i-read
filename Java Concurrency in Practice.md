> Objective: Get to know how parallelism works in java and use it in a right way
> 

## CH01. Introduction

- Most modern operating systems use threads (not a process) as a basic unit of scheduling

## CH02. Thread Safety

- Writing thread-safe codes ⇒ Managing access to state (shared and mutable)
    - in Java, by using `synchronized` keyword (primary mechanism for synchronization)
- Heart of the definition of Thread-Safety is **“Correctness”**
    - Correctness means that a class conforms to its specification
    - and good specification defines
        - invariants constraining an object’s state
        - postconditions describing the effects of its operation
    
    ⇒ If a class behaves correctly regardless of scheduling, etc, then it is thread-safe
    
- Stateless objects are always thread-safe
- Atomicity
    - to avoid a race condition, it needs to make read-modify-write process atomic
        
        ⇒ Compound Actions (can use Atomic Variables in `java.util.concurrent.atomic`)
        
- Locking
    - What if a behavior needs two or more shared states? → use Lock (w/ Critical Section)
    - use `synchronized` keyword to make a whole method (behavior) atomic
        - only one thread can access
    - or use `synchronized(lock){}` to make a certain critical section in a method